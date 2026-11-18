# Lab 01 – First Kubernetes Deployment

## Objective

Deploy an NGINX application on a Kubernetes cluster using a declarative Deployment manifest and understand how Kubernetes controllers maintain the desired state.

---

## Lab Architecture

```
Deployment
      │
      ▼
ReplicaSet
      │
      ▼
Pod
      │
      ▼
Container (nginx:stable)
```

---

## Prerequisites

- Ubuntu 24.04 LTS
- K3s Kubernetes Cluster
- kubectl configured
- Namespace: demo

---

## Files Used

```
lab-01-first-deployment/
├── README.md
├── nginx.yaml
├── observations.md
└── screenshots/
```

---

## Deployment Manifest

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx
  namespace: demo
  labels:
    app: nginx

spec:
  replicas: 1

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
      - name: nginx
        image: nginx:stable
        ports:
        - containerPort: 80
```

---

## Deployment Steps

Create namespace

```bash
kubectl create namespace demo
```

Deploy application

```bash
kubectl apply -f nginx.yaml
```

Verify Deployment

```bash
kubectl get deployment -n demo
```

Verify ReplicaSet

```bash
kubectl get rs -n demo
```

Verify Pod

```bash
kubectl get pods -n demo -o wide
```

---

## Verification

Deployment

```
READY        1/1
AVAILABLE    1
```

ReplicaSet

```
DESIRED    1
CURRENT    1
READY      1
```

Pod

```
STATUS    Running
READY     1/1
```

---

## Self-Healing Demonstration

Delete the Pod

```bash
kubectl delete pod -n demo --all
```

Observe

```bash
kubectl get pods -n demo -w
```

Result

- Original Pod deleted.
- ReplicaSet detected missing replica.
- New Pod created automatically.
- Deployment continued maintaining one healthy replica.

---

## Key Learnings

- Deployment manages ReplicaSets.
- ReplicaSet manages Pods.
- Pods are ephemeral resources.
- Kubernetes continuously reconciles actual state with desired state.
- Declarative configuration simplifies application lifecycle management.

---

## Troubleshooting Commands

```bash
kubectl get all -n demo

kubectl describe deployment nginx -n demo

kubectl describe rs -n demo

kubectl describe pod -n demo

kubectl logs <pod-name> -n demo
```

---

## Interview Questions

**1. Why use a Deployment instead of creating Pods directly?**

So the workload heals and scales itself. A bare Pod that dies or whose node fails is gone; a Deployment keeps the desired replica count through a ReplicaSet and adds rolling updates and rollbacks.

**2. What is the responsibility of a ReplicaSet?**

To keep the specified number of Pods matching its selector running at all times: it creates Pods when there are too few and deletes them when there are too many.

**3. What is the reconciliation loop?**

Controllers continuously watch the desired state (spec) in the API server, compare it with the actual state, and act to close the gap. In this lab the ReplicaSet controller saw 0 of 1 Pods after the delete and created a new one.

**4. Why did the Pod name change after deletion?**

The replacement is a brand-new Pod, not a restart. ReplicaSet Pods are named `<replicaset-name>-<random-suffix>`, so a new suffix is generated; the ReplicaSet name stays the same because the Pod template didn't change.

**5. How does Kubernetes perform self-healing?**

Through controllers: if a Pod or its node disappears, the ReplicaSet creates a new Pod, and the scheduler places it. Inside a Pod, the kubelet restarts failed containers according to `restartPolicy` and liveness probes.

**6. What is the difference between desired state and actual state?**

Desired state is what you declared in the spec (for example, 1 replica of nginx). Actual state is what is running now, reported in `status`. Kubernetes' job is to make actual match desired.

**7. What is the default Deployment strategy?**

`RollingUpdate`, with `maxSurge: 25%` and `maxUnavailable: 25%`. The alternative is `Recreate`, which stops all old Pods before starting new ones.

---

## References

- Kubernetes Deployment
- ReplicaSet
- Pod
- kubectl
