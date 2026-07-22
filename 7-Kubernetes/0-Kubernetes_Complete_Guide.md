# 🚀 Kubernetes Complete Guide for DevOps Engineers

> *Learn Kubernetes from Zero to Hero with Real-World Examples*

---

## 📋 Table of Contents

1. [What is Kubernetes?](#what-is-kubernetes)
2. [Basic Concepts](#basic-concepts)
3. [Architecture Overview](#architecture-overview)
4. [Getting Started](#getting-started)
5. [Core Objects with Sample Code](#core-objects-with-sample-code)
6. [Real-World Examples](#real-world-examples)
7. [Useful kubectl Commands](#useful-kubectl-commands)
8. [Trending Interview Questions & Answers](#trending-interview-questions--answers)

---

## 🤔 What is Kubernetes?

**Simple Explanation:**
Imagine you own a restaurant 🍕. You have:
- Multiple chefs (containers) cooking food
- A head manager (Kubernetes) who decides which chef does what
- If one chef gets sick, the manager immediately assigns another chef
- During rush hour, the manager hires more chefs automatically

> **Kubernetes (K8s)** is an open-source container orchestration platform that automates deployment, scaling, and management of containerized applications.

**Why Do We Need It?**

| Problem Without K8s | Solution With K8s |
|---------------------|-------------------|
| App crashes, no auto-recovery | Auto-restarts failed containers |
| Manual scaling during traffic spikes | Auto-scales based on load |
| Manual load balancing | Built-in load balancer |
| Complex multi-container management | Simplified orchestration |
| Downtime during deployments | Rolling updates with zero downtime |

---

## 🧱 Basic Concepts

### 1. 🫛 Pod
> The **smallest unit** in Kubernetes

**Real-life analogy:** A pod is like a **single apartment unit** in an apartment building. It contains one or more containers (roommates) who share the same resources.

```
Pod
 ├── Container 1 (Main App - Nginx)
 └── Container 2 (Sidecar - Log collector)
```

### 2. 🏗️ Node
> A **physical or virtual machine** that runs pods

**Real-life analogy:** A node is like a **floor in the apartment building**. Multiple apartments (pods) live on each floor.

```
Node (Machine)
 ├── Pod 1
 ├── Pod 2
 └── Pod 3
```

### 3. 🏙️ Cluster
> A **group of nodes** managed together

**Real-life analogy:** A cluster is the **entire apartment complex** with multiple buildings (nodes).

### 4. 📦 Deployment
> Manages **how your app runs** and ensures desired state

**Real-life analogy:** A deployment is like a **building contract** that says "I always want 3 apartments occupied. If one becomes empty, fill it immediately."

### 5. 🌐 Service
> Provides **stable networking** to access pods

**Real-life analogy:** A service is like the **reception desk** — no matter which chef is cooking, customers always call the same number (IP/DNS).

### 6. 🗂️ Namespace
> **Virtual clusters** within a cluster for organization

**Real-life analogy:** Namespaces are like **different departments** in a company — HR, Finance, IT — each with their own resources.

### 7. ⚙️ ConfigMap & Secret
- **ConfigMap:** Non-sensitive configuration (like app settings)
- **Secret:** Sensitive data (like passwords, API keys)

**Real-life analogy:** ConfigMap = Employee handbook, Secret = Employee's personal PIN

### 8. 📊 ReplicaSet
> Ensures a **specified number of pod copies** are always running

**Real-life analogy:** Like a staffing agency that ensures you always have exactly 3 support staff on duty.

### 9. 📈 HorizontalPodAutoscaler (HPA)
> **Automatically scales** pods based on CPU/memory usage

**Real-life analogy:** Like a restaurant that automatically calls in more waiters when it gets crowded.

### 10. 💾 PersistentVolume (PV) & PersistentVolumeClaim (PVC)
- **PV:** Actual storage (like a hard disk)
- **PVC:** A request for storage (like requesting a specific-size hard disk)

---

## 🏛️ Architecture Overview

```
┌─────────────────────────────────────────────────┐
│                  KUBERNETES CLUSTER              │
│                                                  │
│  ┌──────────────────────────────────────────┐   │
│  │           CONTROL PLANE (Master)          │   │
│  │                                          │   │
│  │  ┌──────────┐  ┌──────────────────────┐  │   │
│  │  │  API     │  │  etcd (Database)     │  │   │
│  │  │  Server  │  │  Stores cluster state│  │   │
│  │  └──────────┘  └──────────────────────┘  │   │
│  │                                          │   │
│  │  ┌──────────┐  ┌──────────────────────┐  │   │
│  │  │Scheduler │  │  Controller Manager  │  │   │
│  │  │(Assigns  │  │  (Watches & heals)   │  │   │
│  │  │ pods)    │  └──────────────────────┘  │   │
│  │  └──────────┘                            │   │
│  └──────────────────────────────────────────┘   │
│                                                  │
│  ┌──────────────┐  ┌──────────────┐             │
│  │  WORKER NODE │  │  WORKER NODE │             │
│  │              │  │              │             │
│  │  ┌────────┐  │  │  ┌────────┐  │             │
│  │  │ kubelet│  │  │  │kubelet │  │             │
│  │  └────────┘  │  │  └────────┘  │             │
│  │  ┌────────┐  │  │  ┌────────┐  │             │
│  │  │kube-   │  │  │  │kube-   │  │             │
│  │  │proxy   │  │  │  │proxy   │  │             │
│  │  └────────┘  │  │  └────────┘  │             │
│  │  ┌────────┐  │  │  ┌────────┐  │             │
│  │  │  Pod   │  │  │  │  Pod   │  │             │
│  │  │  Pod   │  │  │  │  Pod   │  │             │
│  │  └────────┘  │  │  └────────┘  │             │
│  └──────────────┘  └──────────────┘             │
└─────────────────────────────────────────────────┘
```

### Control Plane Components:

| Component | Role | Analogy |
|-----------|------|---------|
| **API Server** | Entry point for all commands | 📞 Receptionist |
| **etcd** | Stores all cluster data | 📚 Company database |
| **Scheduler** | Decides which node runs a pod | 📅 Resource manager |
| **Controller Manager** | Ensures desired state | 👮 Supervisor |

### Worker Node Components:

| Component | Role | Analogy |
|-----------|------|---------|
| **kubelet** | Manages pods on the node | 👷 Site supervisor |
| **kube-proxy** | Handles networking | 🌐 Network manager |
| **Container Runtime** | Runs containers (Docker/containerd) | 🏃 The actual worker |

---

## 🚀 Getting Started

### Step 1: Install Required Tools

```bash
# Install kubectl (Kubernetes CLI)
# On Mac
brew install kubectl

# On Linux
curl -LO "https://dl.k8s.io/release/$(curl -L -s \
  https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Verify installation
kubectl version --client
```

```bash
# Install Minikube (Local Kubernetes for development)
# On Mac
brew install minikube

# On Linux
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Start Minikube
minikube start

# Check cluster status
minikube status
kubectl get nodes
```

### Step 2: Verify Your Setup

```bash
# Check cluster info
kubectl cluster-info

# See all nodes
kubectl get nodes

# Expected output:
# NAME       STATUS   ROLES           AGE   VERSION
# minikube   Ready    control-plane   5m    v1.27.0
```

---

## 💻 Core Objects with Sample Code

### 1. 🫛 Creating a Pod

```yaml
# pod.yaml
# Real-world example: Running a simple web server

apiVersion: v1          # API version to use
kind: Pod               # Type of Kubernetes object
metadata:
  name: my-web-app      # Name of the pod
  labels:
    app: web            # Labels for identification
    environment: dev    # Useful for filtering
spec:
  containers:
  - name: nginx-container        # Container name
    image: nginx:1.21            # Docker image to use
    ports:
    - containerPort: 80          # Port the container exposes
    resources:
      requests:                  # Minimum resources needed
        memory: "64Mi"
        cpu: "250m"
      limits:                    # Maximum resources allowed
        memory: "128Mi"
        cpu: "500m"
```

```bash
# Apply the pod
kubectl apply -f pod.yaml

# Check if pod is running
kubectl get pods

# Get detailed info
kubectl describe pod my-web-app

# View logs
kubectl logs my-web-app

# Access pod shell
kubectl exec -it my-web-app -- /bin/bash

# Delete pod
kubectl delete pod my-web-app
```

---

### 2. 📦 Creating a Deployment

> **Best practice:** Always use Deployments instead of bare Pods!

```yaml
# deployment.yaml
# Real-world example: Deploying an e-commerce web app with 3 replicas

apiVersion: apps/v1
kind: Deployment
metadata:
  name: ecommerce-app             # Deployment name
  namespace: production           # Namespace
  labels:
    app: ecommerce
    version: "2.0"
spec:
  replicas: 3                     # Always keep 3 pods running
  selector:
    matchLabels:
      app: ecommerce              # Manage pods with this label
  strategy:
    type: RollingUpdate           # Update strategy
    rollingUpdate:
      maxSurge: 1                 # Max extra pods during update
      maxUnavailable: 0           # No downtime!
  template:                       # Pod template
    metadata:
      labels:
        app: ecommerce
    spec:
      containers:
      - name: ecommerce-container
        image: myrepo/ecommerce:v2.0
        ports:
        - containerPort: 8080
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:         # Get from Secret (secure!)
              name: db-secret
              key: database-url
        - name: APP_ENV
          valueFrom:
            configMapKeyRef:      # Get from ConfigMap
              name: app-config
              key: environment
        livenessProbe:            # Is container alive?
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:           # Is container ready for traffic?
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
        resources:
          requests:
            memory: "256Mi"
            cpu: "500m"
          limits:
            memory: "512Mi"
            cpu: "1000m"
```

```bash
# Deploy the application
kubectl apply -f deployment.yaml

# Check deployment status
kubectl get deployments

# Watch rollout
kubectl rollout status deployment/ecommerce-app

# Scale up manually
kubectl scale deployment ecommerce-app --replicas=5

# Update image (triggers rolling update)
kubectl set image deployment/ecommerce-app \
  ecommerce-container=myrepo/ecommerce:v2.1

# Rollback if something goes wrong
kubectl rollout undo deployment/ecommerce-app

# See rollout history
kubectl rollout history deployment/ecommerce-app
```

---

### 3. 🌐 Creating a Service

```yaml
# service.yaml
# Real-world example: Exposing the ecommerce app to users

---
# ClusterIP - Internal access only (default)
apiVersion: v1
kind: Service
metadata:
  name: ecommerce-internal-svc
spec:
  type: ClusterIP
  selector:
    app: ecommerce               # Routes to pods with this label
  ports:
  - protocol: TCP
    port: 80                     # Service port
    targetPort: 8080             # Container port

---
# NodePort - External access via node IP
apiVersion: v1
kind: Service
metadata:
  name: ecommerce-nodeport-svc
spec:
  type: NodePort
  selector:
    app: ecommerce
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
    nodePort: 30080              # Access via NodeIP:30080

---
# LoadBalancer - Cloud load balancer (AWS/GCP/Azure)
apiVersion: v1
kind: Service
metadata:
  name: ecommerce-lb-svc
spec:
  type: LoadBalancer
  selector:
    app: ecommerce
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

```
Service Types Summary:
┌─────────────────────────────────────────────────┐
│  ClusterIP    → Only inside cluster             │
│  NodePort     → Via Node IP + Port (Dev/Test)   │
│  LoadBalancer → Public URL via cloud provider   │
│  ExternalName → Maps to external DNS            │
└─────────────────────────────────────────────────┘
```

---

### 4. ⚙️ ConfigMap & Secret

```yaml
# configmap.yaml
# Non-sensitive configuration data

apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  environment: "production"
  log_level: "INFO"
  max_connections: "100"
  app.properties: |              # Multi-line config file
    server.port=8080
    server.timeout=30
    feature.darkmode=true
```

```yaml
# secret.yaml
# Sensitive data - always use Secrets for passwords!

apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  # Values must be base64 encoded
  # echo -n "mypassword" | base64
  database-url: bXlwYXNzd29yZA==
  api-key: c3VwZXJzZWNyZXRrZXk=
```

```bash
# Create secret directly (easier)
kubectl create secret generic db-secret \
  --from-literal=database-url=postgresql://user:pass@db:5432/mydb \
  --from-literal=api-key=supersecretkey123

# Create configmap from file
kubectl create configmap app-config \
  --from-file=app.properties

# View configmap
kubectl get configmap app-config -o yaml

# View secret (base64 encoded)
kubectl get secret db-secret -o yaml
```

---

### 5. 💾 PersistentVolume & PersistentVolumeClaim

```yaml
# persistent-volume.yaml
# Real-world example: Database storage that survives pod restarts

---
# Step 1: Create PersistentVolume (Admin creates this)
apiVersion: v1
kind: PersistentVolume
metadata:
  name: postgres-pv
spec:
  capacity:
    storage: 10Gi               # 10 GB storage
  accessModes:
  - ReadWriteOnce               # One node can read/write
  persistentVolumeReclaimPolicy: Retain
  storageClassName: standard
  hostPath:
    path: /data/postgres        # On the host machine

---
# Step 2: Create PersistentVolumeClaim (Developer requests storage)
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-pvc
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi              # Request 5 GB
  storageClassName: standard

---
# Step 3: Use PVC in a Pod
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres-db
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:14
        env:
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: postgres-password
        volumeMounts:
        - name: postgres-storage
          mountPath: /var/lib/postgresql/data   # Mount point
      volumes:
      - name: postgres-storage
        persistentVolumeClaim:
          claimName: postgres-pvc              # Use the PVC
```

---

### 6. 📈 HorizontalPodAutoscaler (HPA)

```yaml
# hpa.yaml
# Real-world example: Auto-scale ecommerce app during sale events

apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: ecommerce-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: ecommerce-app         # Target deployment
  minReplicas: 2                # Never go below 2 pods
  maxReplicas: 20               # Never go above 20 pods
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70  # Scale when CPU > 70%
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80  # Scale when memory > 80%
```

```bash
# Apply HPA
kubectl apply -f hpa.yaml

# Watch HPA in action
kubectl get hpa --watch

# Check current status
kubectl describe hpa ecommerce-hpa
```

---

### 7. 🔄 Ingress Controller

```yaml
# ingress.yaml
# Real-world example: Route traffic to different services based on URL

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ecommerce-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - myshop.com
    secretName: tls-secret       # SSL Certificate
  rules:
  - host: myshop.com
    http:
      paths:
      - path: /                  # myshop.com/ → frontend
        pathType: Prefix
        backend:
          service:
            name: frontend-svc
            port:
              number: 80
      - path: /api               # myshop.com/api → backend
        pathType: Prefix
        backend:
          service:
            name: backend-svc
            port:
              number: 8080
      - path: /payments          # myshop.com/payments → payment-svc
        pathType: Prefix
        backend:
          service:
            name: payment-svc
            port:
              number: 3000
```

---

### 8. 🕐 CronJob

```yaml
# cronjob.yaml
# Real-world example: Daily database backup at midnight

apiVersion: batch/v1
kind: CronJob
metadata:
  name: db-backup-job
spec:
  schedule: "0 0 * * *"         # Every day at midnight
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            image: postgres:14
            command:
            - /bin/sh
            - -c
            - |
              pg_dump $DATABASE_URL > /backup/db-$(date +%Y%m%d).sql
              echo "Backup completed!"
            env:
            - name: DATABASE_URL
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: database-url
            volumeMounts:
            - name: backup-storage
              mountPath: /backup
          restartPolicy: OnFailure
          volumes:
          - name: backup-storage
            persistentVolumeClaim:
              claimName: backup-pvc
  successfulJobsHistoryLimit: 3  # Keep last 3 successful jobs
  failedJobsHistoryLimit: 1      # Keep last 1 failed job
```

---

## 🏭 Real-World Examples

### Complete Microservices App Deployment

```
Real-World Scenario: Deploy an Online Food Delivery App
─────────────────────────────────────────────────────
├── Frontend (React) → 3 replicas
├── Backend API (Node.js) → 5 replicas
├── Database (PostgreSQL) → 1 replica with PVC
├── Cache (Redis) → 1 replica
└── Message Queue (RabbitMQ) → 1 replica
```

```yaml
# Complete namespace setup
---
apiVersion: v1
kind: Namespace
metadata:
  name: foodapp
  labels:
    environment: production

---
# Redis Cache Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-cache
  namespace: foodapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:7-alpine
        ports:
        - containerPort: 6379
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "200m"
---
apiVersion: v1
kind: Service
metadata:
  name: redis-svc
  namespace: foodapp
spec:
  selector:
    app: redis
  ports:
  - port: 6379
    targetPort: 6379

---
# Backend API Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-server
  namespace: foodapp
spec:
  replicas: 5
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
      - name: api
        image: foodapp/api:v1.5
        ports:
        - containerPort: 3000
        env:
        - name: REDIS_URL
          value: "redis://redis-svc:6379"
        - name: NODE_ENV
          value: "production"
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 20
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: api-svc
  namespace: foodapp
spec:
  selector:
    app: api
  ports:
  - port: 80
    targetPort: 3000

---
# HPA for API
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-hpa
  namespace: foodapp
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api-server
  minReplicas: 3
  maxReplicas: 15
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 65
```

---

## ⌨️ Useful kubectl Commands

```bash
# ═══════════════════════════════
# 📋 GET INFORMATION
# ═══════════════════════════════

# Get all resources in default namespace
kubectl get all

# Get resources in specific namespace
kubectl get all -n production

# Get resources across ALL namespaces
kubectl get pods --all-namespaces
kubectl get pods -A                    # Short form

# Get with more details
kubectl get pods -o wide

# Get in YAML format
kubectl get deployment myapp -o yaml

# Watch in real-time
kubectl get pods --watch

# ═══════════════════════════════
# 🔍 DEBUGGING
# ═══════════════════════════════

# Describe a resource (most useful command!)
kubectl describe pod my-pod
kubectl describe node my-node
kubectl describe service my-service

# View logs
kubectl logs my-pod
kubectl logs my-pod -c my-container   # Specific container
kubectl logs my-pod --previous        # Previous crashed container
kubectl logs my-pod -f                # Follow (tail) logs
kubectl logs my-pod --tail=100        # Last 100 lines

# Execute command in running pod
kubectl exec -it my-pod -- /bin/bash
kubectl exec -it my-pod -- /bin/sh    # If bash not available

# Copy files to/from pod
kubectl cp my-pod:/app/logs/error.log ./error.log
kubectl cp ./config.yaml my-pod:/app/config.yaml

# Port forward (access pod locally)
kubectl port-forward pod/my-pod 8080:80
kubectl port-forward service/my-svc 8080:80

# ═══════════════════════════════
# 🛠️ MANAGE RESOURCES
# ═══════════════════════════════

# Apply configuration
kubectl apply -f deployment.yaml
kubectl apply -f ./k8s/              # Apply entire directory

# Delete resources
kubectl delete -f deployment.yaml
kubectl delete pod my-pod
kubectl delete pod my-pod --grace-period=0 --force  # Force delete

# Scale deployment
kubectl scale deployment my-app --replicas=5

# Update image
kubectl set image deployment/my-app container=image:v2

# Rollout commands
kubectl rollout status deployment/my-app
kubectl rollout history deployment/my-app
kubectl rollout undo deployment/my-app
kubectl rollout undo deployment/my-app --to-revision=2

# ═══════════════════════════════
# 🏷️ LABELS & SELECTORS
# ═══════════════════════════════

# Add label to pod
kubectl label pod my-pod environment=production

# Select pods by label
kubectl get pods -l app=ecommerce
kubectl get pods -l environment=prod,app=web

# ═══════════════════════════════
# 🔧 CONTEXT & CONFIG
# ═══════════════════════════════

# View current context
kubectl config current-context

# List all contexts (clusters)
kubectl config get-contexts

# Switch cluster
kubectl config use-context production-cluster

# Set default namespace
kubectl config set-context --current --namespace=production

# ═══════════════════════════════
# 🚀 QUICK CREATE (Imperative)
# ═══════════════════════════════

# Create deployment quickly
kubectl create deployment my-app --image=nginx:latest

# Expose deployment as service
kubectl expose deployment my-app --port=80 --type=LoadBalancer

# Create namespace
kubectl create namespace staging

# Generate YAML without applying (dry-run)
kubectl create deployment my-app --image=nginx \
  --dry-run=client -o yaml > deployment.yaml
```

---

## 🎯 Trending Interview Questions & Answers

---

### Q1: What is the difference between a Pod and a Deployment?

> **Answer:**

```
Pod:
├── Single instance of running containers
├── No auto-restart if it crashes
├── No scaling capability
└── Temporary (ephemeral) by nature

Deployment:
├── Manages multiple pods (replicas)
├── Auto-restarts failed pods
├── Supports rolling updates & rollbacks
└── Maintains desired state
```

**Real Answer to Give:**

*"A Pod is the smallest deployable unit — it's just a wrapper around one or more containers. But if a Pod crashes, it's gone forever! A Deployment is a higher-level abstraction that manages pods for you. If I say `replicas: 3`, the Deployment ensures exactly 3 pods are always running. If one dies, it creates a new one immediately. In production, we never run bare pods — always use Deployments."*

---

### Q2: How does Kubernetes handle rolling updates with zero downtime?

> **Answer:**

```yaml
# This is how zero-downtime rolling update works:
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1        # Create 1 extra pod before killing old one
    maxUnavailable: 0  # Never kill old pod until new one is ready
```

**Flow:**
```
Before Update: [v1] [v1] [v1]  (3 pods)

Step 1:        [v1] [v1] [v1] [v2]  (creates new v2 pod)
Step 2:        [v1] [v1] [v2]       (kills one v1 after v2 is ready)
Step 3:        [v1] [v2] [v2]       (continues...)
Step 4:        [v2] [v2] [v2]       (update complete!)
```

---

### Q3: What is the difference between Liveness and Readiness Probes?

> **Answer:**

| Probe | Purpose | Action if Fails |
|-------|---------|-----------------|
| **Liveness** | Is container alive? | Restart the container |
| **Readiness** | Is container ready for traffic? | Remove from load balancer |
| **Startup** | Has container started? | Wait or restart |

```yaml
# Real example with explanation
livenessProbe:          # If app is deadlocked → RESTART it
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 30  # Wait 30s before first check
  periodSeconds: 10        # Check every 10s
  failureThreshold: 3      # Restart after 3 failures

readinessProbe:         # If app still loading → STOP sending traffic
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
```

**Real Answer to Give:**

*"Liveness probe checks if the container is alive — if it fails, Kubernetes restarts the container. Readiness probe checks if the container is ready to serve traffic — if it fails, Kubernetes removes it from the Service endpoints. A practical example: during app startup, the app might take 60 seconds to load. Readiness probe prevents traffic from hitting an app that's still warming up, while liveness probe would restart it if it gets stuck in a deadlock."*

---

### Q4: How does Kubernetes Service Discovery work?

> **Answer:**

*Kubernetes has two built-in service discovery mechanisms:*

```bash
# 1. Environment Variables (automatically injected)
# When a pod starts, K8s injects service URLs as env vars
echo $MY_SERVICE_SERVICE_HOST  # → 10.96.0.1
echo $MY_SERVICE_SERVICE_PORT  # → 80

# 2. DNS (recommended and most used)
# Format: service-name.namespace.svc.cluster.local
curl http://api-svc.production.svc.cluster.local:80
curl http://api-svc.production     # Short form
curl http://api-svc                # If same namespace
```

---

### Q5: What is the difference between StatefulSet and Deployment?

> **Answer:**

| Feature | Deployment | StatefulSet |
|---------|-----------|-------------|
| Pod names | Random (my-app-xyz123) | Ordered (my-app-0, my-app-1) |
| Scaling | Simultaneous | Sequential |
| Storage | Shared or none | Each pod gets own PVC |
| Use case | Stateless apps (web servers) | Stateful apps (databases) |

```yaml
# StatefulSet for MongoDB cluster
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mongodb
spec:
  serviceName: "mongodb"
  replicas: 3
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo:6
        ports:
        - containerPort: 27017
  volumeClaimTemplates:           # Each pod gets its own PVC!
  - metadata:
      name: mongo-storage
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi
```

---

### Q6: What is the role of etcd in Kubernetes?

> **Answer:**

*"etcd is a distributed key-value store that acts as Kubernetes' brain or database. It stores the entire cluster state — all objects, configurations, secrets, and current status.*

*Think of it like this: if you run `kubectl get pods`, that data comes from etcd. If etcd goes down, the cluster still runs (existing pods keep working), but you can't make any changes. This is why in production, we always run etcd in a cluster of 3 or 5 nodes for high availability.*

*It uses the Raft consensus algorithm to ensure data consistency across all etcd nodes."*

---

### Q7: How do you troubleshoot a pod stuck in CrashLoopBackOff?

> **Answer (Step by step):**

```bash
# Step 1: Check pod status
kubectl get pods
# NAME         READY   STATUS             RESTARTS
# my-app       0/1     CrashLoopBackOff   5

# Step 2: Check pod events
kubectl describe pod my-app
# Look for: Events section at the bottom

# Step 3: Check logs (current)
kubectl logs my-app

# Step 4: Check logs (previous crashed container)
kubectl logs my-app --previous

# Step 5: Common causes and fixes
```

```
Common Causes:
├── ❌ Wrong Docker image → Check image name and tag
├── ❌ Missing env variables → Check ConfigMap/Secret
├── ❌ Out of memory → Increase memory limits
├── ❌ Wrong command → Check container command/args
├── ❌ Application bug → Check application logs
├── ❌ Missing config file → Check volume mounts
└── ❌ Permission issues → Check security context
```

---

### Q8: What is the difference between Ingress and LoadBalancer Service?

> **Answer:**

```
LoadBalancer Service:
├── One cloud Load Balancer per service = EXPENSIVE 💰
├── One external IP per service
└── Example: 3 services = 3 load balancers = 3 IPs

Ingress:
├── One Load Balancer for ALL services = COST EFFECTIVE 💚
├── Routes based on URL path or hostname
├── Supports SSL termination
└── Example: 3 services = 1 load balancer with URL rules
```

```
With LoadBalancer:
User → lb1.aws.com → frontend-svc
User → lb2.aws.com → api-svc
User → lb3.aws.com → payment-svc

With Ingress:
User → myapp.com/      → frontend-svc   ✅ ONE endpoint
User → myapp.com/api   → api-svc        ✅ for everything
User → myapp.com/pay   → payment-svc    ✅
```

---

### Q9: Explain Kubernetes RBAC

> **Answer:**

```yaml
# RBAC = Role-Based Access Control
# Real example: Give a developer read-only access to production

# Step 1: Create a Role (what actions are allowed)
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: production
rules:
- apiGroups: [""]
  resources: ["pods", "logs"]
  verbs: ["get", "list", "watch"]  # Read-only

---
# Step 2: Bind the Role to a user
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: production
subjects:
- kind: User
  name: john-dev              # The developer
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

```
RBAC Components:
├── Role → Permissions within a namespace
├── ClusterRole → Permissions across entire cluster
├── RoleBinding → Attach Role to User/Group/ServiceAccount
└── ClusterRoleBinding → Attach ClusterRole cluster-wide
```

---

### Q10: What is a DaemonSet and when would you use it?

> **Answer:**

*"A DaemonSet ensures that one copy of a pod runs on EVERY node in the cluster (or selected nodes). Unlike Deployments where you specify a replica count, DaemonSets automatically add a pod to new nodes as they join.*

**Common real-world use cases:**
- 📊 Log collection (Fluentd, Filebeat) — collect logs from every node
- 📈 Monitoring agents (Prometheus Node Exporter, Datadog) — monitor every node
- 🔒 Security agents — run security scanning on every node
- 🌐 Network plugins (CNI) — needed on every node for networking"

```yaml
# DaemonSet for log collection on every node
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: log-collector
spec:
  selector:
    matchLabels:
      app: log-collector
  template:
    metadata:
      labels:
        app: log-collector
    spec:
      containers:
      - name: fluentd
        image: fluentd:latest
        volumeMounts:
        - name: varlog
          mountPath: /var/log       # Read node logs
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
```

---

## 📚 Quick Reference Cheat Sheet

```
┌─────────────────────────────────────────────────────────┐
│              KUBERNETES OBJECT CHEAT SHEET              │
├─────────────────┬───────────────────────────────────────┤
│ Pod             │ Smallest unit, runs containers         │
│ Deployment      │ Manages stateless app replicas         │
│ StatefulSet     │ Manages stateful apps (DBs)            │
│ DaemonSet       │ One pod per node                       │
│ Job             │ Run-to-completion tasks                │
│ CronJob         │ Scheduled tasks                        │
│ Service         │ Network access to pods                 │
│ Ingress         │ HTTP routing rules                     │
│ ConfigMap       │ Non-sensitive config                   │
│ Secret          │ Sensitive data                         │
│ PV              │ Actual storage                         │
│ PVC             │ Storage request                        │
│ HPA             │ Auto-scale on CPU/memory               │
│ Namespace       │ Virtual cluster isolation              │
│ ServiceAccount  │ Identity for pods                      │
│ Role/RoleBinding│ Access control                         │
└─────────────────┴───────────────────────────────────────┘
```

---

## 🎓 Learning Path

```
Beginner
   │
   ├── ✅ Understand Pods, Deployments, Services
   ├── ✅ Learn kubectl basics
   ├── ✅ Deploy simple apps on Minikube
   │
Intermediate
   │
   ├── ✅ ConfigMaps, Secrets, Volumes
   ├── ✅ Ingress, HPA
   ├── ✅ RBAC, Namespaces
   ├── ✅ StatefulSets, DaemonSets
   │
Advanced
   │
   ├── ✅ Helm Charts (Package manager for K8s)
   ├── ✅ Service Mesh (Istio/Linkerd)
   ├── ✅ GitOps (ArgoCD/Flux)
   ├── ✅ Custom Resource Definitions (CRDs)
   └── ✅ Kubernetes Operators
```

---

> 💡 **Pro Tip:** Practice on [Killercoda](https://killercoda.com) or [Play with Kubernetes](https://labs.play-with-k8s.com) for FREE!

> 🏆 **Certification Path:** CKA (Certified Kubernetes Administrator) → CKAD (Developer) → CKS (Security)

---

*Happy Learning! 🚀 Kubernetes may seem complex at first, but once you understand it's just about managing containers at scale, everything clicks!*
