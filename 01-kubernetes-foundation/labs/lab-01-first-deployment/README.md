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

1. Why use a Deployment instead of creating Pods directly?
2. What is the responsibility of a ReplicaSet?
3. What is the reconciliation loop?
4. Why did the Pod name change after deletion?
5. How does Kubernetes perform self-healing?
6. What is the difference between desired state and actual state?
7. What is the default Deployment strategy?

---

## References

- Kubernetes Deployment
- ReplicaSet
- Pod
- kubectl
