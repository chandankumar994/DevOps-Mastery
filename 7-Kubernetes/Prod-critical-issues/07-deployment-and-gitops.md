# Deployment, GitOps & Cluster Upgrade Issues

This file covers cluster upgrade risks, API version skew, and control-plane (etcd) performance degradation during major operations.

---

## Issue 20: Version Skew + etcd Latency Spike During Cluster Upgrade

- **Category**: Deployment & GitOps
- **Severity**: 🔴 Critical

### Scenario
A platform team performed a two-minor-version Kubernetes upgrade (e.g., 1.26 → 1.28) on a large self-managed cluster in a single maintenance window to "save time," upgrading the control plane first and planning to upgrade worker nodes and workloads over the following days. Midway through the control plane upgrade, a custom admission webhook began rejecting all pod creations cluster-wide, and etcd latency spiked sharply.

### Symptoms
- New pod creation across the entire cluster failed with:
  ```
  Error from server: error when creating "deployment.yaml": 
  Internal error occurred: failed calling webhook "validate.mywebhook.io": 
  Post "https://webhook-svc.default.svc:443/validate?timeout=10s": 
  no endpoints available for service "webhook-svc"
  ```
- Simultaneously, `etcdctl endpoint status` showed a sharp increase in `dbSizeInUse` and request latency:
  ```
  etcdctl endpoint status --write-out=table
  # DB Size: 3.8 GB (up from 1.1 GB baseline)
  ```
- `kube-apiserver` logs showed frequent `context deadline exceeded` errors on various resource watches.

### Impact
- ~50 minutes of degraded cluster-wide deployment capability (existing workloads kept running, but no new pods, scale-ups, or rollouts could succeed), during which an unrelated incident requiring an emergency scale-up couldn't be resolved quickly, compounding the outage.

### Root Cause Analysis
1. **Webhook failure**: the custom admission webhook's Deployment used an old `admissionregistration.k8s.io/v1beta1` API — which was **removed** in the target Kubernetes version. Once the control plane finished upgrading past the version supporting that API, the `ValidatingWebhookConfiguration` object itself became invalid/orphaned, and more critically, the webhook's own pods (built against an older client-go version incompatible with the new API server) crashed on startup, leaving `no endpoints available` for the webhook service — and since the webhook's `failurePolicy` was set to `Fail` (the safe default), **every** pod creation cluster-wide was blocked because the API server couldn't reach the webhook to validate them.
2. **etcd latency**: investigated separately and found it was a **compounding, related issue** — the upgrade process's automatic CRD conversion webhooks (for internally-used CRDs) were also failing for the same version-skew reason, causing the API server to repeatedly retry and cache large numbers of failed conversion attempts, alongside a backlog of pending object writes that couldn't complete due to the blocked admission webhook — significantly increasing etcd's working set and write/watch load during the incident.
3. Root cause: **the custom admission webhook and its underlying client libraries were not upgraded/tested for compatibility with the target Kubernetes version before the control plane upgrade**, and the **two-minor-version jump skipped intermediate compatibility checks** that would normally surface deprecated API usage earlier. This is compounded by Kubernetes' well-known **N-2 version skew support policy** — jumping multiple minor versions at once significantly increases the risk of hitting exactly this kind of breaking change with no rollback path once the control plane is upgraded.

### Solution
1. Immediate mitigation: temporarily set the webhook's `failurePolicy` to `Ignore` to unblock pod creation cluster-wide while the underlying fix was prepared:
   ```bash
   kubectl patch validatingwebhookconfiguration my-webhook \
     --type=json -p='[{"op": "replace", "path": "/webhooks/0/failurePolicy", "value": "Ignore"}]'
   ```
   *(Used only as a temporary, time-boxed measure — `Ignore` bypasses validation entirely, which carries its own risk.)*
2. Rebuilt and redeployed the admission webhook using an updated `admissionregistration.k8s.io/v1` manifest and an updated `client-go`/`controller-runtime` version compatible with the new Kubernetes API server:
   ```yaml
   apiVersion: admissionregistration.k8s.io/v1
   kind: ValidatingWebhookConfiguration
   ```
3. Verified webhook pods started successfully and had healthy endpoints:
   ```bash
   kubectl get endpoints webhook-svc -n default
   ```
4. Restored `failurePolicy: Fail` once the webhook was confirmed healthy and validated end-to-end.
5. Monitored etcd metrics (`etcd_disk_backend_commit_duration_seconds`, `etcd_server_slow_apply_total`) until they returned to baseline, and ran `etcdctl defrag` during a low-traffic window to reclaim space from the temporary bloat.

### Prevention / Best Practice
- **Never skip minor versions** during Kubernetes upgrades — always upgrade one minor version at a time, per the official supported skew policy, even if it takes longer.
- Before any control plane upgrade, run `kubectl-convert`/`pluto`/`kube-no-trouble` (`kubent`) against the cluster to detect any use of deprecated or soon-to-be-removed API versions, including in custom webhooks, CRDs, and third-party controllers.
- Test all admission webhooks and CRD conversion webhooks against the **target** Kubernetes version in a staging cluster before touching production — webhook compatibility is one of the most common silent breaking points in upgrades.
- Set conservative `failurePolicy` and `timeoutSeconds` values on webhooks, and maintain a documented, tested emergency procedure for temporarily disabling a misbehaving webhook without introducing unacceptable risk.
- Monitor etcd health (db size, latency, defrag status) proactively during any cluster-wide operation that touches large numbers of objects (upgrades, mass CRD migrations, bulk relabeling), not just during steady-state operation.
