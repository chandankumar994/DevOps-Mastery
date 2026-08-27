# Scheduling, Eviction & Node Pressure Issues

This file covers issues related to pod scheduling decisions, node resource pressure, eviction behavior, and noisy-neighbor problems in multi-tenant clusters.

---

## Issue 1: Cascading Pod Evictions from Node Memory Pressure

- **Category**: Scheduling & Nodes
- **Severity**: 🔴 Critical

### Scenario
A microservices application running on an EKS cluster (managed node groups, `m5.xlarge` instances) began experiencing rolling outages during a traffic spike from a marketing campaign. Multiple unrelated services — not just the one under load — started getting terminated within a 10-minute window.

### Symptoms
- Pods across multiple namespaces showed `Status: Evicted` with reason `The node was low on resource: memory`.
- `kubectl get events -A` showed repeated `FailedScheduling` and `Evicted` events.
- Node conditions showed `MemoryPressure: True`:
  ```bash
  kubectl describe node ip-10-0-3-142.ec2.internal
  # Conditions:
  #   MemoryPressure   True    KubeletHasInsufficientMemory
  ```
- Grafana node-exporter dashboards showed memory usage climbing to >95% before evictions began.

### Impact
- 14 pods across 6 services evicted simultaneously, causing a 9-minute partial outage.
- Downstream retries amplified load on the database tier, nearly causing a secondary incident.

### Root Cause Analysis
1. Checked node capacity vs. requested resources: `kubectl describe node <node>` showed **Allocated resources** far below actual usage — meaning most pods had **no memory requests/limits set**, so the scheduler had no visibility into real consumption.
2. Cross-referenced with `kubectl top pods -A --sort-by=memory` and found one service (an image-processing sidecar) consuming unbounded memory during the traffic spike — a classic memory leak under load, unconstrained because no `limits.memory` was set.
3. Because that pod had no limit, it wasn't OOMKilled individually — instead, the **kubelet's eviction manager** kicked in at the node level and evicted *other* pods based on QoS class (`BestEffort` and `Burstable` pods without limits were evicted first, regardless of which pod actually caused the pressure).
4. Root cause: **missing resource requests/limits** across most workloads, combined with a memory leak in one specific service, triggered node-level eviction that punished innocent pods due to QoS classification.

### Solution
1. Set explicit requests/limits on all workloads, prioritizing the leaking service:
   ```yaml
   resources:
     requests:
       memory: "256Mi"
       cpu: "250m"
     limits:
       memory: "512Mi"
       cpu: "500m"
   ```
2. This moved the leaking pod to a `Burstable`/`Guaranteed` QoS class so it would be OOMKilled individually instead of triggering node-wide eviction.
3. Patched the actual memory leak in the image-processing sidecar (unbounded in-memory cache).
4. Added a `PriorityClass` for critical services so, even during pressure, low-priority batch jobs are evicted first:
   ```yaml
   apiVersion: scheduling.k8s.io/v1
   kind: PriorityClass
   metadata:
     name: high-priority
   value: 1000000
   globalDefault: false
   ```

### Prevention / Best Practice
- Enforce resource requests/limits via a `LimitRange` or OPA/Kyverno admission policy — reject pods without them.
- Set up node-level memory pressure alerts (`node_memory_MemAvailable_bytes`) well before kubelet's hard eviction threshold (default 100Mi available).
- Use Vertical Pod Autoscaler in "recommendation" mode to right-size requests/limits based on real usage.
- Regularly audit workloads with `kubectl get pods -A -o json | jq` for missing `resources` fields.

---

## Issue 2: Noisy Neighbor CPU Throttling in Multi-Tenant Cluster

- **Category**: Scheduling & Nodes
- **Severity**: 🟠 High

### Scenario
A shared GKE cluster hosted multiple internal teams' applications in separate namespaces with no dedicated node pools. One team's batch analytics job (CPU-intensive, run nightly) started causing latency spikes in an unrelated customer-facing API team's service co-located on the same nodes.

### Symptoms
- API latency (p99) spiked from 120ms to 2.4s every night between 1–3 AM.
- `kubectl top pod` showed the API pods' CPU usage looked *normal* (under their request), yet response times were degraded.
- Checking `/sys/fs/cgroup/cpu/cpu.stat` inside the affected containers showed high `nr_throttled` and `throttled_time` values.

### Impact
- Nightly SLA breaches (p99 latency SLA of 500ms) for 3 consecutive weeks before root cause was found.
- Customer complaints logged against the API team despite the fault originating from a different team's workload.

### Root Cause Analysis
1. Initial investigation focused on the API service itself — no code changes correlated with the timing, ruling out application regression.
2. Used `kubectl describe node` to identify co-located pods and found the analytics batch job scheduled on the same nodes, requesting CPU but with no `limits.cpu` set — allowed it to burst far beyond its request.
3. Confirmed via cgroup CFS throttling metrics (`container_cpu_cfs_throttled_periods_total` in Prometheus) that the API pods were being throttled despite having "room" on paper — because the *node's total CPU* was oversubscribed by the bursting batch job.
4. Root cause: **CPU requests without limits** on the batch job allowed it to consume all available node CPU during its run, causing CFS (Completely Fair Scheduler) throttling on co-located pods — a classic "noisy neighbor" problem exacerbated by lack of node-level isolation.

### Solution
1. Set a hard CPU limit on the batch job to cap its burst behavior:
   ```yaml
   resources:
     requests:
       cpu: "2"
     limits:
       cpu: "2"
   ```
2. Migrated the batch workload to a dedicated node pool using taints/tolerations so it can no longer share nodes with latency-sensitive services:
   ```yaml
   tolerations:
     - key: "workload-type"
       operator: "Equal"
       value: "batch"
       effect: "NoSchedule"
   ```
3. Applied `PodAntiAffinity` as a secondary safeguard to keep the API pods away from batch-labeled nodes.

### Prevention / Best Practice
- Use dedicated node pools (with taints) to separate latency-sensitive workloads from batch/bursty workloads.
- Alert on `container_cpu_cfs_throttled_periods_total` rate, not just raw CPU usage — throttling is invisible in basic usage dashboards.
- Adopt `ResourceQuota` per namespace to cap total CPU/memory a team can consume cluster-wide.
- Consider Kubernetes `PriorityClass` + `PreemptionPolicy` to ensure critical workloads can preempt lower-priority ones under contention.

---

## Issue 3: Silent CrashLoopBackOff Masking OOMKilled Root Cause

- **Category**: Scheduling & Nodes
- **Severity**: 🔴 Critical

### Scenario
A Java-based payment processing service on an on-prem Kubernetes cluster (kubeadm, bare metal nodes) entered `CrashLoopBackOff` after a routine deployment that bumped the JVM heap size configuration. The on-call engineer assumed it was an application bug based on generic crash logs.

### Symptoms
- `kubectl get pods` showed:
  ```
  payment-service-7d9f8b6c5-xk2lp   0/1   CrashLoopBackOff   14   22m
  ```
- `kubectl logs payment-service-7d9f8b6c5-xk2lp` showed truncated/empty logs — the JVM was killed before flushing its own error output.
- No obvious application-level stack trace was present, leading the team to initially suspect a bad deployment rollback rather than a resource issue.

### Impact
- 40 minutes of full service unavailability for payment processing.
- Engineers spent the majority of that time chasing application-level causes instead of the actual infrastructure cause, extending MTTR significantly.

### Root Cause Analysis
1. `kubectl logs` alone was insufficient since the container was killed abruptly. The key diagnostic step was:
   ```bash
   kubectl describe pod payment-service-7d9f8b6c5-xk2lp
   ```
   which revealed under **Last State**:
   ```
   Last State:  Terminated
     Reason:    OOMKilled
     Exit Code: 137
   ```
2. Cross-checked the container's memory limit vs. the newly configured JVM heap:
   - Container `limits.memory`: `512Mi`
   - New JVM flag introduced in the deploy: `-Xmx768m`
3. Root cause: the JVM was configured to use more heap (768Mi) than the container's memory limit (512Mi) allowed. Once heap usage approached the JVM max, the container was OOMKilled by the kernel cgroup controller — well before the JVM itself could throw a graceful `OutOfMemoryError` or log anything useful, because the **entire container**, not just the JVM process, was killed.
4. This is a common and easily-missed issue: `-Xmx` must always be set comfortably *below* the container's memory limit to leave room for JVM overhead (metaspace, thread stacks, native memory), typically 75-80% of the limit.

### Solution
1. Immediate rollback: reverted the JVM flag change and the memory limit to their previously working configuration.
2. Correct fix applied afterward:
   ```yaml
   resources:
     limits:
       memory: "1Gi"
   env:
     - name: JAVA_OPTS
       value: "-Xmx768m -Xms512m"
   ```
   (leaving ~256Mi headroom above `-Xmx` for JVM native memory overhead).
3. Verified via `kubectl top pod` and JVM `-XX:+PrintGCDetails` output that memory usage stayed within container limits under load testing before re-deploying.

### Prevention / Best Practice
- Always size container memory limits with **at least 20–30% headroom** above JVM `-Xmx` (or equivalent runtime heap settings for other languages/runtimes).
- Add a Grafana panel specifically for `kube_pod_container_status_last_terminated_reason` to make OOMKilled events immediately visible, separate from generic restart counts.
- Use `kubectl describe pod` (not just `logs`) as a mandatory first triage step for any CrashLoopBackOff — exit code 137 is a strong OOMKilled signal.
- Load-test any JVM heap/memory limit changes in staging before production rollout.
