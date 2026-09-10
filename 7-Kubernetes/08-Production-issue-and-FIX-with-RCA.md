# Kubernetes Production Troubleshooting Guide

This repository contains a reference guide for common Kubernetes issues encountered in production environments, their root causes, and how to fix them.

## Common Production Issues Reference

| S.No. | Issue | Possible reason | Fix | RCA |
| :--- | :--- | :--- | :--- | :--- |
| **1** | **`OOMKilled` (Exit Code 137)** | Container memory usage exceeded the explicitly defined `limits.memory` in the pod manifest. | Increase the `limits.memory` threshold or profile the application code to fix memory leaks. | The application attempted to allocate more memory than its cgroup boundary allowed, triggering the Linux kernel out-of-memory killer. |
| **2** | **`CrashLoopBackOff` (Exit Code 1)** | Missing environment variables, incorrect DB credentials, or wrong container `entrypoint` command. | Check `kubectl logs --previous` to find the runtime exception and update the respective `ConfigMap` or `Secret`. | The application encountered a fatal initialization error during startup, causing the process to terminate immediately and loop continuously. |
| **3** | **`ImagePullBackOff`** | The Kubernetes worker nodes lack authorization to pull from a private container registry, or the image tag has a typo. | Create a Docker registry secret (`kubernetes.io/dockerconfigjson`) and reference it using `imagePullSecrets` in the pod spec. | The Kubelet daemon failed to authenticate with the container registry or pull the specified image digest. |
| **4** | **`Pod Stuck in Pending` (FailedScheduling)** | There are no available worker nodes with sufficient unallocated CPU/Memory requests to host the pod. | Add a Cluster Autoscaler to provision new instances, or optimize the pod’s `requests.cpu` / `requests.memory` specs downwards. | The `kube-scheduler` component could not find a cluster node matching all resource requirements and scheduling constraints. |
| **5** | **`Service 503 / Endpoints Empty`** | The `selector` labels inside the Kubernetes Service do not match the labels assigned to the underlying Pods. | Match the labels in the Service’s `spec.selector` identically to the Pod's `metadata.labels`. | The endpoint controller removed all backend routing IPs because no active pods matched the service's selector criteria. |
| **6** | **`Liveness/Readiness Probe Failure`** | The application took longer to boot up than allowed by the probe configurations, or the health check URL path changed. | Adjust `initialDelaySeconds`, add a `startupProbe`, or extend `failureThreshold` parameters. | The container runtime terminated the pod because the application failed to respond to the health check probe endpoints within the designated time limit. |
| **7** | **`NodeNotReady`** | The worker node is experiencing severe disk, memory, or CPU pressure, forcing the `kubelet` service to freeze. | Safely cordon and drain the node, investigate the underlying VM/host logs, and restart the `kubelet` agent. | The control plane lost connection with the worker node because the node's system resources were completely exhausted, terminating its heartbeat. |

## Quick Diagnostics Cheat Sheet

To debug these issues in real-time, you can use the following core commands:

```bash
# 1. Check pod status and events (Useful for Pending, ImagePullBackOff, OOMKilled)
kubectl describe pod <pod-name> -n <namespace>

# 2. View current logs
kubectl logs <pod-name> -n <namespace>

# 3. View logs of a crashed container before it restarted (Useful for CrashLoopBackOff)
kubectl logs <pod-name> -n <namespace> --previous

# 4. Check if endpoints are correctly attached to your service
kubectl get endpoints <service-name> -n <namespace>
```
