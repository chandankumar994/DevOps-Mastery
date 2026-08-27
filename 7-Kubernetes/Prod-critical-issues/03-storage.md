# Storage Issues (PV/PVC, StatefulSets, Volume Mounts)

This file covers persistent storage issues: scheduling/zone mismatches, multi-attach errors, and data loss from reclaim policy misconfiguration.

---

## Issue 8: StatefulSet PVCs Stuck Pending Due to Zone Mismatch

- **Category**: Storage
- **Severity**: 🟠 High

### Scenario
A team deployed a 3-replica Kafka `StatefulSet` on GKE using dynamically provisioned `PersistentVolumeClaims` backed by zonal persistent disks. After a node pool scaling event redistributed pods across zones, one Kafka broker pod got stuck permanently in `Pending`.

### Symptoms
- `kubectl get pods` showed:
  ```
  kafka-broker-2   0/1   Pending   0   35m
  ```
- `kubectl describe pod kafka-broker-2` showed:
  ```
  Warning  FailedScheduling  0/6 nodes are available: 3 node(s) had volume node affinity conflict
  ```
- The associated PVC was already `Bound`, which confused the team since normally `Pending` pods correlate with `Pending` PVCs.

### Impact
- Kafka cluster ran at reduced replication factor (2 of 3 brokers) for 6+ hours, increasing risk of data loss if another broker failed during that window.

### Root Cause Analysis
1. Checked the PV bound to the stuck PVC:
   ```bash
   kubectl get pv <pv-name> -o yaml
   ```
   and found a `nodeAffinity` field pinning the volume to `topology.kubernetes.io/zone: us-central1-a` — because the underlying disk was a **zonal** persistent disk, physically tied to that zone.
2. The node pool scaling event had shifted available capacity mostly to `us-central1-b` and `us-central1-c`; no schedulable nodes remained in `us-central1-a` with sufficient resources.
3. Root cause: **zonal PD placement conflicting with node pool zone distribution** — the PVC/PV was bound to a specific zone at creation time, but cluster autoscaling changed which zones had available capacity, and Kubernetes cannot move a zonal disk to follow the pod.

### Solution
1. Immediate fix: manually scaled up a node in `us-central1-a` to give the stuck pod somewhere to schedule:
   ```bash
   gcloud container clusters resize <cluster> --node-pool <pool> --num-nodes <n> --zone us-central1-a
   ```
2. Long-term fix: migrated the StorageClass to use **regional persistent disks**, which replicate across two zones and aren't subject to this single-zone pinning problem:
   ```yaml
   apiVersion: storage.k8s.io/v1
   kind: StorageClass
   metadata:
     name: regional-pd
   provisioner: pd.csi.storage.gke.io
   parameters:
     type: pd-ssd
     replication-type: regional-pd
   volumeBindingMode: WaitForFirstConsumer
   ```
3. Set `volumeBindingMode: WaitForFirstConsumer` (instead of `Immediate`) so the PV is only provisioned after the scheduler picks a node — avoiding this class of zone-mismatch entirely for future PVCs.

### Prevention / Best Practice
- Always use `volumeBindingMode: WaitForFirstConsumer` for zonal storage classes to align provisioning with actual scheduling decisions.
- Prefer regional/replicated disks for critical stateful workloads where availability matters more than the marginal cost increase.
- Ensure node pools used by StatefulSets span the same zones the StorageClass can provision into, and keep balanced capacity across zones.
- Monitor `FailedScheduling` events with alerting specifically for `volume node affinity conflict` reason strings.

---

## Issue 9: Multi-Attach Volume Error After Node Failure

- **Category**: Storage
- **Severity**: 🔴 Critical

### Scenario
An on-prem Kubernetes cluster using Rook-Ceph for block storage experienced a sudden hard node failure (hardware fault) hosting a critical PostgreSQL pod backed by a `ReadWriteOnce` PVC. Kubernetes attempted to reschedule the pod to a healthy node, but it failed to start.

### Symptoms
- New pod stuck in `ContainerCreating`:
  ```
  Warning  FailedAttachVolume  ... Multi-Attach error for volume "pvc-8f21..." Volume is already exclusively attached to one node and can't be attached to another
  ```
- The old pod on the failed node still showed as `Running` or `Terminating` in `kubectl get pods`, despite the node being unreachable.

### Impact
- Database unavailable for 22 minutes — full outage for all dependent services, breaching the internal RPO/RTO target for this tier-1 workload.

### Root Cause Analysis
1. Confirmed the failed node was truly down (unreachable via SSH, not responding to `kubelet` heartbeats):
   ```bash
   kubectl get nodes
   # node-7   NotReady   47m
   ```
2. Because the node was hard-down (not gracefully drained), Kubernetes could not confirm the volume had been safely detached — the `VolumeAttachment` object for that PV remained in place, since the CSI driver requires the original node to acknowledge detachment, which never happens if the kubelet is unreachable.
3. Root cause: **stale VolumeAttachment object** referencing a dead node blocked the CSI driver from attaching the `ReadWriteOnce` volume elsewhere — a known limitation with `RWO` volumes and non-graceful node failures, since Kubernetes defaults to a conservative ~6 minute pod eviction timeout (`node.kubernetes.io/unreachable` taint) but the storage layer's attachment state can lag further behind.

### Solution
1. Immediate recovery: manually verified the node was truly dead (not a network partition risking split-brain) then force-deleted the stale `VolumeAttachment`:
   ```bash
   kubectl get volumeattachment | grep pvc-8f21
   kubectl delete volumeattachment <name> --force
   ```
2. Also force-deleted the old pod object that was stuck terminating:
   ```bash
   kubectl delete pod <old-pod> --grace-period=0 --force
   ```
3. This released the exclusive attach lock and allowed the CSI driver to reattach the volume to the new node; the PostgreSQL pod started successfully within 90 seconds.
4. Followed up by reducing `default-unreachable-toleration-seconds` for critical workloads and evaluating a switch to Ceph's `ReadWriteOncePod`/replicated RWX-compatible setup where feasible.

### Prevention / Best Practice
- For tier-1 stateful workloads on `RWO` volumes, have a documented, tested runbook for force-deleting stale `VolumeAttachment` objects — **only after confirming the node is truly dead**, to avoid split-brain data corruption.
- Consider storage solutions/architectures (e.g., Postgres with streaming replication + automatic failover via Patroni) that don't depend on a single RWO volume being reattached.
- Implement node health monitoring with faster hardware-failure detection (e.g., out-of-band IPMI/BMC alerts) to reduce time-to-confirmation before manual intervention.
- Tune `pod-eviction-timeout` and related taint-based eviction settings deliberately for critical namespaces, balancing failover speed against risk of premature action on a merely-slow (not dead) node.

---

## Issue 10: Data Loss from PV Reclaim Policy Misconfiguration

- **Category**: Storage
- **Severity**: 🔴 Critical

### Scenario
A DevOps engineer was cleaning up unused namespaces as part of a cost-optimization sprint on an AKS cluster. They deleted a namespace they believed only contained decommissioned test workloads — it also contained a `PersistentVolumeClaim` for a staging database that a data science team was still actively using for model training data.

### Symptoms
- After namespace deletion, the data science team reported their training dataset (stored in the PVC-backed volume) was completely gone.
- `kubectl get pv` showed no trace of the volume — the underlying Azure Disk had been deleted along with the PVC.

### Impact
- Permanent loss of ~3 weeks of curated training data with no backup, requiring the data science team to redo significant data collection and labeling work — estimated 2 weeks of lost productivity.

### Root Cause Analysis
1. Investigated the StorageClass used by the deleted PVC:
   ```bash
   kubectl get storageclass -o yaml
   ```
   Found:
   ```yaml
   reclaimPolicy: Delete
   ```
2. With `reclaimPolicy: Delete` (the default for most dynamic provisioners including Azure Disk CSI), deleting the PVC automatically triggers deletion of the underlying cloud disk — there is no "trash" or recovery window.
3. No backup or snapshot policy existed for this "staging" classified namespace, since it wasn't in scope for the production backup process.
4. Root cause: **default `Delete` reclaim policy** on a StorageClass used for a workload that was operationally important (despite being labeled "staging"), combined with a namespace deletion performed without verifying active usage or checking for existing PVCs/snapshots first.

### Solution
1. Immediate: attempted disk recovery via Azure's soft-delete/backup features — none were enabled, confirming the data was unrecoverable.
2. Created a new StorageClass with `reclaimPolicy: Retain` for any namespace not explicitly marked as ephemeral/test:
   ```yaml
   apiVersion: storage.k8s.io/v1
   kind: StorageClass
   metadata:
     name: retain-standard
   provisioner: disk.csi.azure.com
   reclaimPolicy: Retain
   volumeBindingMode: WaitForFirstConsumer
   ```
3. Implemented mandatory Velero-based backups (including PV snapshots) for all namespaces, with a scheduled daily snapshot policy.
4. Instituted a namespace deletion process requiring a pre-deletion checklist: list all PVCs, confirm no active consumers, and take a manual snapshot before deletion.

### Prevention / Best Practice
- Default to `reclaimPolicy: Retain` for any StorageClass backing data that isn't explicitly disposable; require conscious opt-in to `Delete`.
- Enable cloud-provider-level soft-delete or recycle-bin features for disks/snapshots where available (e.g., Azure Disk soft-delete, AWS EBS snapshot lifecycle policies).
- Use Velero or a similar backup tool with scheduled PV snapshots for all namespaces containing stateful workloads, regardless of "staging" vs. "production" labeling.
- Require a `kubectl get pvc,pv -n <namespace>` audit step (or automated pre-delete admission webhook check) before any namespace deletion in a runbook or CI/CD pipeline.
