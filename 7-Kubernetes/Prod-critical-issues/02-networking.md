# Networking Issues (CNI, DNS, Ingress, Service Mesh, NetworkPolicy)

This file covers networking-layer production issues: DNS resolution failures, IP exhaustion, service mesh misconfigurations, and NetworkPolicy problems.

---

## Issue 4: Intermittent CoreDNS Resolution Failures Under Load

- **Category**: Networking
- **Severity**: 🔴 Critical

### Scenario
A high-traffic e-commerce application on AKS began experiencing intermittent `5xx` errors during flash-sale events. The failures were sporadic and non-reproducible in staging, which had much lower traffic.

### Symptoms
- Application logs showed:
  ```
  java.net.UnknownHostException: inventory-service.prod.svc.cluster.local
  ```
  appearing intermittently, roughly 2–3% of requests during peak load.
- `kubectl logs -n kube-system -l k8s-app=kube-dns` showed CoreDNS pods logging:
  ```
  [ERROR] plugin/errors: 2 inventory-service.prod.svc.cluster.local. A: read udp 10.244.1.5:53->10.244.0.10:53: i/o timeout
  ```
- CoreDNS pod CPU usage was pegged at 100% of its limit during peak traffic.

### Impact
- ~2–3% request failure rate during each flash sale, translating to an estimated $18K in lost transactions per event.
- Increased retry storms further amplified DNS query volume, worsening the problem in a feedback loop.

### Root Cause Analysis
1. Ran `kubectl top pods -n kube-system -l k8s-app=kube-dns` and confirmed CoreDNS pods were CPU-throttled at peak — only 2 replicas were running with `limits.cpu: 100m`, sized for a much smaller cluster.
2. Checked `kubectl get hpa -n kube-system` — CoreDNS had no HorizontalPodAutoscaler configured, so replica count never scaled with query volume.
3. Also found application-side misconfiguration: the app's HTTP client wasn't caching DNS lookups (`networkaddress.cache.ttl` unset in the JVM, defaulting to cache every lookup), multiplying DNS query volume unnecessarily per request instead of per connection.
4. Root cause: **under-provisioned CoreDNS** (fixed 2 replicas, low CPU limits, no autoscaling) combined with **no client-side DNS caching**, causing CoreDNS saturation and dropped queries during traffic spikes.

### Solution
1. Increased CoreDNS resources and replica count:
   ```yaml
   resources:
     limits:
       cpu: "500m"
       memory: "256Mi"
     requests:
       cpu: "250m"
       memory: "128Mi"
   ```
2. Added a `HorizontalPodAutoscaler` for CoreDNS based on CPU:
   ```yaml
   apiVersion: autoscaling/v2
   kind: HorizontalPodAutoscaler
   metadata:
     name: coredns-hpa
     namespace: kube-system
   spec:
     scaleTargetRef:
       apiVersion: apps/v1
       kind: Deployment
       name: coredns
     minReplicas: 3
     maxReplicas: 10
     metrics:
       - type: Resource
         resource:
           name: cpu
           target:
             type: Utilization
             averageUtilization: 70
   ```
3. Enabled the `NodeLocal DNSCache` add-on to reduce load on central CoreDNS by caching DNS responses on each node.
4. Fixed the JVM DNS caching TTL (`networkaddress.cache.ttl=30`) to avoid excessive per-request lookups.

### Prevention / Best Practice
- Always monitor CoreDNS-specific metrics (`coredns_dns_responses_total`, `coredns_panics_total`, request latency) as first-class SLIs, not just generic kube-system health.
- Deploy `NodeLocal DNSCache` by default in any cluster expecting significant traffic.
- Set explicit DNS cache TTLs in application runtimes (JVM, Node.js, Python) to reduce unnecessary DNS query volume.
- Load-test DNS query volume as part of pre-event capacity planning for known traffic spikes (sales, launches).

---

## Issue 5: CNI IP Address Exhaustion Blocking New Pod Scheduling

- **Category**: Networking
- **Severity**: 🔴 Critical

### Scenario
An EKS cluster using the AWS VPC CNI plugin hit a wall during a large-scale rollout of a new microservice — new pods stopped being created entirely, even though nodes showed available CPU/memory capacity.

### Symptoms
- New pods stuck in `ContainerCreating` indefinitely.
- `kubectl describe pod <pod>` showed:
  ```
  Warning  FailedCreatePodSandBox  ... failed to assign an IP address to container
  ```
- `kubectl describe node <node>` showed plenty of free CPU/memory, contradicting the scheduling failure.

### Impact
- New deployments and scale-up events across the entire cluster were blocked for ~1 hour, delaying an important feature launch and blocking unrelated teams' deployments.

### Root Cause Analysis
1. Since CPU/memory weren't the bottleneck, checked ENI/IP allocation via the AWS VPC CNI:
   ```bash
   kubectl describe node <node> | grep -A5 "Allocatable"
   ```
   and cross-referenced with the subnet's available IPs in the AWS console — the subnet used for pod networking was nearly fully allocated.
2. Each node type (`m5.large`) supports a limited number of ENIs × IPs-per-ENI (per AWS VPC CNI limits), and the **subnet CIDR itself** (a `/24`) was undersized for the number of nodes × max pods per node in the cluster.
3. Root cause: **VPC subnet IP exhaustion** — not a Kubernetes-level resource constraint, but an AWS networking-layer constraint. The CNI plugin could not allocate any more secondary IPs to nodes for new pod sandboxes because the subnet had run out of free addresses.

### Solution
1. Immediate mitigation: identified and cleaned up unused/orphaned ENIs from previously terminated nodes that hadn't released IPs promptly:
   ```bash
   aws ec2 describe-network-interfaces --filters Name=status,Values=available
   ```
2. Added a secondary, larger CIDR block to the VPC and associated new subnets with more available IP space, then migrated new node groups to use them.
3. Enabled `WARM_IP_TARGET` and `WARM_ENI_TARGET` tuning on the VPC CNI to reduce over-allocation of IPs per node while still maintaining reasonable pod-launch latency.
4. Longer-term: migrated to **prefix delegation mode** for the VPC CNI, which allocates /28 prefixes instead of individual IPs, dramatically increasing pod density per subnet.

### Prevention / Best Practice
- Size VPC subnets for pod networking with significant headroom — calculate `max_nodes × max_pods_per_node` and compare against subnet CIDR capacity before cluster design.
- Monitor subnet IP utilization as a cluster-health metric (via AWS CloudWatch or a custom exporter), not just Kubernetes-native metrics.
- Enable CNI prefix delegation mode where supported to avoid this class of issue at scale.
- Set `maxPods` per node conservatively relative to available IP capacity, and validate during cluster capacity planning exercises.

---

## Issue 6: Service Mesh mTLS Misconfiguration Causing 503s

- **Category**: Networking
- **Severity**: 🟠 High

### Scenario
A platform team rolled out Istio with strict mTLS enforcement cluster-wide as a security hardening initiative. Immediately after, several services — some in the mesh, some not yet migrated — began returning intermittent `503 Upstream connect error`.

### Symptoms
- Envoy sidecar logs showed:
  ```
  upstream connect error or disconnect/reset before headers. reset reason: connection termination
  ```
- Some service-to-service calls worked fine; others failed consistently — pointing to a partial rather than total networking failure.
- `istioctl analyze` had not been run before the PeerAuthentication rollout.

### Impact
- ~30% of internal API calls between mesh and non-mesh services failed for roughly 2 hours, causing degraded functionality across several dependent features (though not a full outage).

### Root Cause Analysis
1. Identified that the failing calls were specifically between services **inside** the mesh (with Envoy sidecars) and services **not yet onboarded** to Istio (no sidecar injected).
2. Checked the `PeerAuthentication` policy applied cluster-wide:
   ```yaml
   apiVersion: security.istio.io/v1beta1
   kind: PeerAuthentication
   metadata:
     name: default
     namespace: istio-system
   spec:
     mtls:
       mode: STRICT
   ```
3. `STRICT` mode was applied mesh-wide before all services had sidecars injected — non-mesh services couldn't present a valid mTLS certificate, so the mesh-side Envoy proxies rejected their plaintext connections.
4. Root cause: **premature enforcement of STRICT mTLS** during a partial/incremental service mesh rollout, breaking communication with services that hadn't yet been migrated into the mesh.

### Solution
1. Immediately changed the policy to `PERMISSIVE` mode to allow both plaintext and mTLS traffic during the migration window:
   ```yaml
   spec:
     mtls:
       mode: PERMISSIVE
   ```
2. Used `istioctl analyze -A` to identify all namespaces/services still missing sidecar injection.
3. Completed sidecar injection rollout for remaining services, verified via:
   ```bash
   kubectl get pods -n <ns> -o jsonpath='{.items[*].spec.containers[*].name}'
   ```
4. Only after 100% of relevant services had sidecars did the team re-enable `STRICT` mode, this time scoped per-namespace and rolled out incrementally rather than cluster-wide.

### Prevention / Best Practice
- Roll out mTLS enforcement **incrementally per-namespace**, never cluster-wide in one step, especially during partial mesh adoption.
- Always run `istioctl analyze` before applying mesh-wide security policies — it flags exactly this kind of gap.
- Use `PERMISSIVE` mode as the default during migration and only tighten to `STRICT` once sidecar coverage is verified at 100%.
- Add synthetic canary requests between mesh and non-mesh services as a pre-deployment gate for mesh policy changes.

---

## Issue 7: Cross-Namespace NetworkPolicy Silently Blocking Traffic

- **Category**: Networking
- **Severity**: 🟠 High

### Scenario
A platform security team implemented default-deny `NetworkPolicy` rules across all namespaces as part of a compliance initiative. Weeks later, a batch reporting service in the `analytics` namespace began silently failing to reach a database proxy in the `data` namespace — with no clear error at deploy time since the policy rollout and the failure were separated by time and by team.

### Symptoms
- Application logs showed generic connection timeouts:
  ```
  dial tcp 10.96.14.22:5432: i/o timeout
  ```
- No changes had been made to the reporting service itself recently, making the cause non-obvious.
- `kubectl get networkpolicy -A` was not part of the reporting team's usual debugging toolkit, since they didn't own that resource.

### Impact
- Nightly analytics reports failed to generate for 4 days before being noticed (low visibility due to non-customer-facing nature), delaying business reporting and causing stale dashboards for leadership.

### Root Cause Analysis
1. Confirmed pod-to-pod connectivity was the issue (not DNS or service discovery) using `kubectl exec` into the reporting pod and testing raw TCP connectivity:
   ```bash
   kubectl exec -it reporting-pod -- nc -zv db-proxy.data.svc.cluster.local 5432
   ```
   Result: timeout.
2. Checked `NetworkPolicy` objects in the `data` namespace:
   ```bash
   kubectl get networkpolicy -n data -o yaml
   ```
   Found a default-deny-all ingress policy with a namespace-selector allow rule that only permitted traffic from namespaces labeled `team: data-consumers` — the `analytics` namespace lacked this label.
3. Root cause: the default-deny rollout used **namespace label selectors** for allow-listing, but the `analytics` namespace was never labeled correctly, silently cutting off legitimate cross-namespace traffic with no explicit error surfaced to the consuming team.

### Solution
1. Labeled the `analytics` namespace correctly:
   ```bash
   kubectl label namespace analytics team=data-consumers
   ```
2. Verified connectivity was restored:
   ```bash
   kubectl exec -it reporting-pod -- nc -zv db-proxy.data.svc.cluster.local 5432
   ```
3. Audited all namespaces against the required label taxonomy to catch any other silently-broken cross-namespace dependencies before they caused incidents.

### Prevention / Best Practice
- Treat `NetworkPolicy` changes as cross-team changes requiring a dependency map — maintain a service-to-service communication matrix before applying default-deny policies.
- Use tools like `kubectl-np-viewer`, Cilium's Hubble, or Calico's flow logs to visualize and validate allowed/denied traffic before and after policy changes.
- Add explicit NetworkPolicy "dry-run" tooling (e.g., Cilium's policy audit mode) to catch unintended blocks before enforcing.
- Alert on connection-level metrics (e.g., `envoy_cluster_upstream_cx_connect_fail` or CNI-level drop counters) to catch policy-related failures faster than log-diving.
