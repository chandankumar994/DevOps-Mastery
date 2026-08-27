# Workloads, Probes & Custom Controller Issues

This file covers liveness/readiness probe misconfigurations, custom operator/controller bugs, and orphaned resource cleanup failures.

---

## Issue 15: Liveness Probe Misconfiguration Causing Cascading Restarts

- **Category**: Workloads & Controllers
- **Severity**: 🔴 Critical

### Scenario
A team deployed a new version of a Node.js API with a heavier startup sequence (loading ML model weights into memory at boot). The liveness probe configuration, inherited unchanged from the previous lightweight version, caused an unexpected cascading failure during rollout.

### Symptoms
- `kubectl get pods` showed continuous restarts across all replicas simultaneously:
  ```
  api-service-5f7c9d8b6-abc12   0/1   Running   6   4m
  ```
- `kubectl describe pod` showed:
  ```
  Liveness probe failed: HTTP probe failed with statuscode: 000
  Warning  Killing  Container api failed liveness probe, will be restarted
  ```
- The application logs showed the model was still loading (~45 seconds) when the container was killed and restarted — creating an infinite restart loop since it never finished loading before being killed again.

### Impact
- Full service outage for the API for 20 minutes — every replica was stuck in a permanent restart loop, never reaching a healthy state, effectively a self-inflicted denial of service.

### Root Cause Analysis
1. Reviewed the probe configuration:
   ```yaml
   livenessProbe:
     httpGet:
       path: /healthz
       port: 8080
     initialDelaySeconds: 10
     periodSeconds: 5
     failureThreshold: 3
   ```
2. Calculated the effective grace period before the first kill: `initialDelaySeconds (10s) + periodSeconds × failureThreshold (5s × 3 = 15s)` ≈ 25 seconds — far shorter than the new ~45-second model load time.
3. Root cause: the **liveness probe's grace period was not updated to reflect the new, longer startup time** introduced by the ML model loading change. The probe began killing containers before they ever became healthy, and since this affected all replicas simultaneously (deployed together), there was no healthy replica left to serve traffic — a textbook cascading self-inflicted outage.
4. Confirmed no `startupProbe` was configured, which is the correct mechanism for handling variable/slow startup separately from steady-state liveness checks.

### Solution
1. Immediate fix: rolled back to the previous image version to restore service.
2. Correct fix for the new version — added a dedicated `startupProbe` to handle the slow boot sequence, decoupling it from the liveness probe:
   ```yaml
   startupProbe:
     httpGet:
       path: /healthz
       port: 8080
     failureThreshold: 30
     periodSeconds: 2
   livenessProbe:
     httpGet:
       path: /healthz
       port: 8080
     periodSeconds: 10
     failureThreshold: 3
   ```
   This gives up to 60 seconds (30 × 2s) for startup before liveness checks even begin, while keeping steady-state liveness checks responsive to genuine hangs.
3. Re-deployed and confirmed pods reached `Running`/`Ready` state successfully without restart loops.

### Prevention / Best Practice
- Always use a `startupProbe` for any workload with meaningful or variable startup time, rather than inflating `initialDelaySeconds` on the liveness probe (which then also delays detection of genuine post-startup hangs).
- Treat probe configuration as part of the application's deployment contract — review and update it explicitly whenever startup behavior changes (new dependencies, larger models/datasets, cold-cache warmup, etc.).
- Use `maxUnavailable`/`maxSurge` rolling update strategy settings conservatively so a bad rollout doesn't take down all replicas simultaneously; combine with automated rollout health checks (e.g., Argo Rollouts analysis steps) that halt a bad rollout automatically.
- Load-test startup behavior under realistic conditions (not just fast local dev environments) before finalizing probe thresholds.

---

## Issue 16: Custom Operator Stuck in Infinite Reconcile Loop

- **Category**: Workloads & Controllers
- **Severity**: 🟠 High

### Scenario
An internal platform team built a custom Kubernetes Operator (using Kubebuilder) to manage the lifecycle of tenant-specific database instances via a `TenantDatabase` CRD. After a minor update to the operator, the cluster's control plane began experiencing unusually high API server load.

### Symptoms
- `kubectl top pods -n operator-system` showed the operator pod consuming abnormally high CPU continuously, not just during actual reconciliation events.
- API server audit logs showed an extremely high rate of `PATCH` and `GET` requests against `tenantdatabases.platform.io` resources — far more than the number of actual `TenantDatabase` objects would justify.
- `kubectl get tenantdatabases -A` showed the `status.phase` field for several objects flapping between `Provisioning` and `Ready` repeatedly, visible via `kubectl get tenantdatabases -w`.

### Impact
- Elevated API server latency cluster-wide (p99 API latency up from 50ms to 400ms), degrading responsiveness for `kubectl` and other controllers competing for API server capacity — a "noisy operator" affecting the entire control plane.

### Root Cause Analysis
1. Pulled the operator's controller logs and found it was reconciling the same objects thousands of times per minute rather than settling into a steady state.
2. Reviewed the recent code change and found the reconcile loop's status-update logic:
   ```go
   status.Phase = "Ready"
   status.LastUpdated = time.Now()  // <-- bug
   r.Status().Update(ctx, &tenantDB)
   ```
3. Root cause: the reconciler unconditionally wrote `status.LastUpdated = time.Now()` **on every reconcile**, even when nothing meaningful had changed. Since this always produced a diff in the object's `status` subresource, it triggered a new watch event, which triggered another reconcile, which updated the timestamp again — an **infinite reconcile loop caused by a non-idempotent status update** that always "changes" the object even when there's nothing new to do.
4. This is a classic operator anti-pattern: any field that changes on every reconcile (timestamps, non-deterministic ordering, etc.) inside a `Status().Update()` call defeats Kubernetes' event-driven reconciliation model and turns it into a tight busy-loop.

### Solution
1. Immediate mitigation: scaled the operator deployment to 0 replicas to stop the API load, restoring normal API server latency.
2. Fixed the reconcile logic to only update status when something has actually changed:
   ```go
   if tenantDB.Status.Phase != desiredPhase {
       tenantDB.Status.Phase = desiredPhase
       tenantDB.Status.LastUpdated = metav1.Now()
       if err := r.Status().Update(ctx, &tenantDB); err != nil {
           return ctrl.Result{}, err
       }
   }
   ```
3. Added a `RequeueAfter` with a sane interval instead of relying purely on watch-triggered reconciles for periodic health checks, avoiding tight loops:
   ```go
   return ctrl.Result{RequeueAfter: 5 * time.Minute}, nil
   ```
4. Re-deployed the fixed operator and confirmed reconcile counts dropped to expected levels via controller-runtime metrics (`controller_runtime_reconcile_total`).

### Prevention / Best Practice
- Never write fields that always change (timestamps, random IDs, etc.) into a `Status().Update()` call unless the change is genuinely meaningful — this breaks the idempotency assumption central to Kubernetes' reconciliation model.
- Monitor `controller_runtime_reconcile_total` and `controller_runtime_reconcile_time_seconds` per controller as standard operator health metrics, alerting on abnormally high reconcile rates relative to the number of managed objects.
- Load-test custom operators against realistic object counts and simulate steady-state behavior (no external changes) to confirm reconciliation settles rather than loops.
- Use `controller-runtime`'s built-in rate limiting and exponential backoff on requeues as a safety net, but treat a persistently high reconcile rate as a bug to fix, not just something to rate-limit.

---

## Issue 17: Zombie/Orphaned Resources After Failed Helm Rollback

- **Category**: Workloads & Controllers
- **Severity**: 🟡 Medium

### Scenario
A team attempted to roll back a Helm release after a bad deployment introduced a breaking config change. The `helm rollback` command reported success, but several resources from the bad release remained active in the cluster, causing confusing, inconsistent behavior.

### Symptoms
- `helm status my-app` showed the release back at the previous, "good" revision.
- However, `kubectl get configmaps,secrets -n prod -l app=my-app` revealed a `ConfigMap` from the bad release still present and still referenced by some pods, alongside the old, correct `ConfigMap`, with confusingly similar names (`my-app-config-v2` vs `my-app-config-v3`).
- Some pods were reading stale config, others correct config, depending on which `ReplicaSet` they belonged to — an inconsistent, hard-to-diagnose state.

### Impact
- Intermittent, inconsistent application behavior for ~1 hour as different pods within the same Deployment served different behavior depending on which config they'd picked up, complicating triage.

### Root Cause Analysis
1. Compared `helm get manifest my-app --revision <good>` against the actual live cluster state (`kubectl get all,cm,secret -n prod -l app=my-app`) and found extra resources in the cluster that weren't part of the "good" revision's manifest.
2. Investigated Helm's release history:
   ```bash
   helm history my-app -n prod
   ```
   and found the bad release had introduced a **renamed** `ConfigMap` (a common pattern when using Helm's checksum-based config hashing to force pod restarts on config change) — because it was a *new* resource name rather than an update to an existing one, `helm rollback` correctly removed resources it explicitly tracked as changed, but a `ReplicaSet` from the bad release (retained by Kubernetes' default revision history limit) was still referencing the orphaned ConfigMap and had not been fully scaled down due to a stuck `Deployment` rollout status.
3. Root cause: **Helm rollback restores the Helm release's tracked manifest state, but does not guarantee immediate, complete cleanup of transient/orphaned objects from a partially-completed prior rollout**, especially when combined with leftover `ReplicaSets` from `revisionHistoryLimit` retention that hadn't finished scaling down.

### Solution
1. Manually identified and removed the orphaned `ReplicaSet` and its associated `ConfigMap`:
   ```bash
   kubectl get replicasets -n prod -l app=my-app
   kubectl delete replicaset my-app-7f9c8b6d5 -n prod
   kubectl delete configmap my-app-config-v2 -n prod
   ```
2. Verified all pods were now running under a single, correct `ReplicaSet` referencing the correct `ConfigMap`.
3. Reduced `revisionHistoryLimit` on the Deployment to limit how many old ReplicaSets are retained, reducing surface area for this kind of drift:
   ```yaml
   spec:
     revisionHistoryLimit: 3
   ```

### Prevention / Best Practice
- After any `helm rollback`, explicitly verify live cluster state matches the expected manifest (`helm get manifest` diffed against `kubectl get -o yaml`), don't just trust the reported Helm status.
- Use `helm rollback --wait` to ensure Helm waits for all resources to reach a ready state before considering the rollback complete, surfacing stuck rollouts immediately rather than silently.
- Consider adopting GitOps (ArgoCD/Flux) with automated drift detection, which continuously reconciles live cluster state against the declared Git state and will flag orphaned resources like this automatically.
- Set a reasonable `revisionHistoryLimit` and periodically audit for orphaned ConfigMaps/Secrets not referenced by any active pod (`kubectl get configmap -A -o json | jq` cross-referenced with pod specs).
