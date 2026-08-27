# Kubernetes Production Issues — Scenario-Based Troubleshooting Guide

A curated collection of **20 critical, real-world Kubernetes production issues**, each with a full scenario, symptoms, business impact, root cause analysis (RCA), solution, and prevention strategy. Intended as a reference/runbook for SRE and DevOps engineers operating production Kubernetes clusters (EKS/GKE/AKS/on-prem).

## 📁 File Structure

| File | Category |
|---|---|
| [01-scheduling-and-nodes.md](./01-scheduling-and-nodes.md) | Pod scheduling, eviction, node pressure, noisy neighbors |
| [02-networking.md](./02-networking.md) | CNI, DNS, Ingress, service mesh, NetworkPolicy |
| [03-storage.md](./03-storage.md) | PV/PVC, StatefulSets, volume mounts, reclaim policy |
| [04-security-and-rbac.md](./04-security-and-rbac.md) | RBAC, Secrets, certificates, private registries |
| [05-workloads-and-controllers.md](./05-workloads-and-controllers.md) | Probes, custom controllers/operators, orphaned resources |
| [06-autoscaling-and-resources.md](./06-autoscaling-and-resources.md) | HPA/VPA, Cluster Autoscaler, resource limits |
| [07-deployment-and-gitops.md](./07-deployment-and-gitops.md) | Cluster upgrades, version skew, etcd performance |

## 📋 Issue Index

| # | Title | Category | Severity | Link |
|---|---|---|---|---|
| 1 | Cascading Pod Evictions from Node Memory Pressure | Scheduling & Nodes | 🔴 Critical | [Go →](./01-scheduling-and-nodes.md#issue-1-cascading-pod-evictions-from-node-memory-pressure) |
| 2 | Noisy Neighbor CPU Throttling in Multi-Tenant Cluster | Scheduling & Nodes | 🟠 High | [Go →](./01-scheduling-and-nodes.md#issue-2-noisy-neighbor-cpu-throttling-in-multi-tenant-cluster) |
| 3 | Silent CrashLoopBackOff Masking OOMKilled Root Cause | Scheduling & Nodes | 🔴 Critical | [Go →](./01-scheduling-and-nodes.md#issue-3-silent-crashloopbackoff-masking-oomkilled-root-cause) |
| 4 | Intermittent CoreDNS Resolution Failures Under Load | Networking | 🔴 Critical | [Go →](./02-networking.md#issue-4-intermittent-coredns-resolution-failures-under-load) |
| 5 | CNI IP Address Exhaustion Blocking New Pod Scheduling | Networking | 🔴 Critical | [Go →](./02-networking.md#issue-5-cni-ip-address-exhaustion-blocking-new-pod-scheduling) |
| 6 | Service Mesh mTLS Misconfiguration Causing 503s | Networking | 🟠 High | [Go →](./02-networking.md#issue-6-service-mesh-mtls-misconfiguration-causing-503s) |
| 7 | Cross-Namespace NetworkPolicy Silently Blocking Traffic | Networking | 🟠 High | [Go →](./02-networking.md#issue-7-cross-namespace-networkpolicy-silently-blocking-traffic) |
| 8 | StatefulSet PVCs Stuck Pending Due to Zone Mismatch | Storage | 🟠 High | [Go →](./03-storage.md#issue-8-statefulset-pvcs-stuck-pending-due-to-zone-mismatch) |
| 9 | Multi-Attach Volume Error After Node Failure | Storage | 🔴 Critical | [Go →](./03-storage.md#issue-9-multi-attach-volume-error-after-node-failure) |
| 10 | Data Loss from PV Reclaim Policy Misconfiguration | Storage | 🔴 Critical | [Go →](./03-storage.md#issue-10-data-loss-from-pv-reclaim-policy-misconfiguration) |
| 11 | RBAC Misconfiguration Silently Breaking CI/CD Pipeline | Security & RBAC | 🟠 High | [Go →](./04-security-and-rbac.md#issue-11-rbac-misconfiguration-silently-breaking-cicd-pipeline) |
| 12 | Rotated Secret Not Propagated to Running Pods | Security & RBAC | 🟠 High | [Go →](./04-security-and-rbac.md#issue-12-rotated-secret-not-propagated-to-running-pods) |
| 13 | Unnoticed TLS Certificate Expiry Causing Full Outage | Security & RBAC | 🔴 Critical | [Go →](./04-security-and-rbac.md#issue-13-unnoticed-tls-certificate-expiry-causing-full-outage) |
| 14 | Private Registry ImagePullBackOff from Expired Token | Security & RBAC | 🟡 Medium | [Go →](./04-security-and-rbac.md#issue-14-private-registry-imagepullbackoff-from-expired-token) |
| 15 | Liveness Probe Misconfiguration Causing Cascading Restarts | Workloads & Controllers | 🔴 Critical | [Go →](./05-workloads-and-controllers.md#issue-15-liveness-probe-misconfiguration-causing-cascading-restarts) |
| 16 | Custom Operator Stuck in Infinite Reconcile Loop | Workloads & Controllers | 🟠 High | [Go →](./05-workloads-and-controllers.md#issue-16-custom-operator-stuck-in-infinite-reconcile-loop) |
| 17 | Zombie/Orphaned Resources After Failed Helm Rollback | Workloads & Controllers | 🟡 Medium | [Go →](./05-workloads-and-controllers.md#issue-17-zombieorphaned-resources-after-failed-helm-rollback) |
| 18 | HPA Flapping Due to Metrics Server Lag | Autoscaling & Resources | 🟠 High | [Go →](./06-autoscaling-and-resources.md#issue-18-hpa-flapping-due-to-metrics-server-lag) |
| 19 | Cluster Autoscaler Stuck Due to PodDisruptionBudget Conflict | Autoscaling & Resources | 🟠 High | [Go →](./06-autoscaling-and-resources.md#issue-19-cluster-autoscaler-stuck-due-to-poddisruptionbudget-conflict) |
| 20 | Version Skew + etcd Latency Spike During Cluster Upgrade | Deployment & GitOps | 🔴 Critical | [Go →](./07-deployment-and-gitops.md#issue-20-version-skew--etcd-latency-spike-during-cluster-upgrade) |

## How to Use This Guide

Each issue follows a consistent structure: **Scenario → Symptoms → Impact → Root Cause Analysis → Solution → Prevention**. Jump directly to any issue via the index above — each is self-contained and does not require reading others.

## Severity Legend

- 🔴 **Critical** — Production outage, data loss, or SLA breach
- 🟠 **High** — Significant degradation, partial outage, or high risk of escalation
- 🟡 **Medium** — Contained impact, but recurring or operationally costly if unaddressed
