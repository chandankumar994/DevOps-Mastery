# Autoscaling & Resource Management Issues

This file covers Horizontal Pod Autoscaler instability and Cluster Autoscaler scaling failures caused by conflicting constraints.

---

## Issue 18: HPA Flapping Due to Metrics Server Lag

- **Category**: Autoscaling & Resources
- **Severity**: 🟠 High

### Scenario
A checkout service on GKE used a `HorizontalPodAutoscaler` targeting 60% average CPU utilization. During moderate, steady traffic increases, the service began rapidly scaling up and down every 1-2 minutes instead of settling at a stable replica count, causing connection resets as pods were repeatedly terminated mid-request.

### Symptoms
- `kubectl get hpa -w` showed replica counts oscillating: `4 → 12 → 5 → 11 → 4...` over a short window.
- `kubectl describe hpa checkout-hpa` showed rapidly alternating events:
  ```
  Normal  SuccessfulRescale  New size: 12; reason: cpu resource utilization above target
  Normal  SuccessfulRescale  New size: 4; reason: All metrics below target
  ```
- Application-level 502/connection-reset errors correlated exactly with each scale-down event.

### Impact
- Intermittent connection errors for ~8% of checkout requests over a 2-hour period during a moderate traffic increase, directly impacting revenue-generating transactions.

### Root Cause Analysis
1. Checked the HPA configuration:
   ```yaml
   apiVersion: autoscaling/v2
   kind: HorizontalPodAutoscaler
   metadata:
     name: checkout-hpa
   spec:
     minReplicas: 4
     maxReplicas: 20
     metrics:
       - type: Resource
         resource:
           name: cpu
           target:
             type: Utilization
             averageUtilization: 60
   ```
   No custom `behavior` stabilization window was set, meaning the HPA used aggressive default scale-down behavior (stabilizationWindowSeconds: 0 in older API versions/configs).
2. Checked `metrics-server` pod logs and resource usage — it was under-provisioned and occasionally missed its 15-30 second metric collection interval under load, causing the HPA controller to sometimes act on **stale or momentarily-low** CPU readings right after a scale-up (when new pods were still warming up and hadn't picked up their share of traffic yet, making average CPU look artificially low).
3. Root cause: **no scale-down stabilization window configured**, combined with newly-scaled-up pods briefly reporting low CPU before traffic rebalanced to them — causing the HPA to interpret this transient dip as "load has decreased" and immediately scale back down, only to see CPU spike again once the premature scale-down took effect. This created a feedback oscillation (flapping).

### Solution
1. Added an explicit `behavior` block with a meaningful stabilization window for scale-down, so the HPA waits before acting on a metric decrease:
   ```yaml
   spec:
     behavior:
       scaleDown:
         stabilizationWindowSeconds: 300
         policies:
           - type: Percent
             value: 25
             periodSeconds: 60
       scaleUp:
         stabilizationWindowSeconds: 0
         policies:
           - type: Percent
             value: 100
             periodSeconds: 30
   ```
2. Scaled up and right-sized `metrics-server` resources to ensure timely, reliable metric collection under load.
3. Added a `readinessProbe` with a sensible delay to ensure new pods don't count as "ready" (and thus don't get counted in average CPU calculations) until they've actually warmed up and can handle real traffic.

### Prevention / Best Practice
- Always configure explicit `behavior.scaleDown.stabilizationWindowSeconds` (a value of several minutes is typical) to prevent premature scale-down from transient metric dips.
- Ensure `metrics-server` (or the custom metrics pipeline) is adequately resourced and monitored — treat it as critical infrastructure, since HPA decisions are only as good as the metrics feeding them.
- Pair CPU-based HPA with realistic readiness probes so newly-scaled pods don't skew average utilization calculations before they're actually serving traffic.
- Load-test autoscaling behavior explicitly (not just steady-state capacity) as part of pre-production validation for customer-facing services.

---

## Issue 19: Cluster Autoscaler Stuck Due to PodDisruptionBudget Conflict

- **Category**: Autoscaling & Resources
- **Severity**: 🟠 High

### Scenario
A team relied on the Kubernetes Cluster Autoscaler on EKS to scale down underutilized nodes overnight to control costs. Cost reports showed the cluster was not scaling down at all despite very low utilization during off-peak hours for over a week.

### Symptoms
- `kubectl get nodes` consistently showed the same high node count regardless of time of day.
- Cluster Autoscaler logs (`kubectl logs -n kube-system deploy/cluster-autoscaler`) showed repeated entries:
  ```
  Skipping ip-10-0-4-91.ec2.internal: pod monitoring-agent-abcde 
  cannot be evicted: PodDisruptionBudget monitoring-pdb prevents eviction
  ```
- `kubectl get pdb -A` showed a `PodDisruptionBudget` for a monitoring DaemonSet-adjacent deployment set to `minAvailable: 100%` with only a single replica.

### Impact
- Estimated $6,000/month in unnecessary compute spend from nodes that should have been scaled down but couldn't be, due to a single misconfigured PDB blocking eviction cluster-wide.

### Root Cause Analysis
1. Confirmed via Cluster Autoscaler logs that nearly every underutilized node had at least one pod the autoscaler deemed "not safe to evict."
2. Traced the specific blocking pod (a single-replica monitoring agent) and its `PodDisruptionBudget`:
   ```yaml
   apiVersion: policy/v1
   kind: PodDisruptionBudget
   metadata:
     name: monitoring-pdb
   spec:
     minAvailable: 100%
     selector:
       matchLabels:
         app: monitoring-agent
   ```
3. With `replicas: 1` and `minAvailable: 100%`, the PDB mathematically **never allows any voluntary eviction** of that pod — even a single eviction would drop availability below 100%, which is explicitly disallowed. Since this pod was scheduled on many different nodes over time (not a DaemonSet, just a normal Deployment with `replicas: 1`), it silently blocked scale-down on whichever node it happened to be on.
4. Root cause: an overly strict `PodDisruptionBudget` (`minAvailable: 100%` on a single-replica Deployment) unintentionally prevented the Cluster Autoscaler from ever safely evicting that pod, blocking node scale-down on any node hosting it — a subtle but common PDB misconfiguration.

### Solution
1. Corrected the PDB to allow for the operational reality of a single-replica deployment — since some disruption must be tolerable for scaling/maintenance to ever occur:
   ```yaml
   spec:
     maxUnavailable: 1
     selector:
       matchLabels:
         app: monitoring-agent
   ```
   (or, better, increased replicas to 2 with `minAvailable: 1` for actual redundancy plus safe evictability).
2. Verified Cluster Autoscaler logs no longer showed this pod as a blocker, and confirmed nodes began scaling down appropriately during the next low-utilization window.
3. Audited all other PDBs cluster-wide for similar `100%`/`replicas=1` combinations:
   ```bash
   kubectl get pdb -A -o json | jq '.items[] | select(.spec.minAvailable=="100%")'
   ```

### Prevention / Best Practice
- Never set `minAvailable: 100%` on a PDB backing a single-replica workload — it mathematically forbids any voluntary disruption, which conflicts with both autoscaling and routine node maintenance/upgrades.
- For genuinely critical single-replica services, favor increasing replica count over an unenforceable PDB — availability should come from redundancy, not from blocking necessary cluster operations.
- Regularly audit PDBs cluster-wide as part of routine hygiene checks, specifically looking for combinations that make eviction mathematically impossible.
- Monitor Cluster Autoscaler's "unable to scale down" events/logs as a first-class signal, not just overall node count trends, since the failure mode is silent unless logs are actively reviewed.
