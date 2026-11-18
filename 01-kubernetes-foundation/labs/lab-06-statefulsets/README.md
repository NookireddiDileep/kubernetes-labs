# Lab 06 – Kubernetes StatefulSets

## Objective

The objective of this lab is to understand how Kubernetes StatefulSets manage stateful applications by providing stable Pod identities, stable network identities, dedicated persistent storage, ordered deployment, ordered scaling, and ordered termination.

This lab demonstrates why StatefulSets are preferred over Deployments for databases and distributed systems.

---

# Learning Objectives

After completing this lab, you will be able to:

* Understand StatefulSets
* Understand Headless Services
* Deploy Stateful Applications
* Understand Stable Pod Identity
* Understand Stable DNS
* Understand volumeClaimTemplates
* Verify Automatic PVC Creation
* Verify Persistent Storage
* Scale StatefulSets
* Understand Production Use Cases

---

# Lab Environment

| Component               | Value                       |
| ----------------------- | --------------------------- |
| Kubernetes Distribution | K3s                         |
| Storage Provisioner     | local-path                  |
| Container Runtime       | containerd                  |
| Cloud Platform          | Google Cloud Platform (GCP) |

---

# Architecture

```text
                    Headless Service
                           │
                           │
          ┌────────────────┴────────────────┐
          │                                 │
 nginx-stateful-0                   nginx-stateful-1
          │                                 │
 Stable DNS                         Stable DNS
          │                                 │
 Dedicated PVC                     Dedicated PVC
          │                                 │
 Dedicated PV                      Dedicated PV
```

---

# Repository Structure

```text
lab-06-statefulsets/
│
├── README.md
├── observations.md
├── headless-service.yaml
├── statefulset.yaml
└── screenshots/
    └── README.md
```

---

# Part A – Headless Service

Create the Headless Service.

```bash
kubectl apply -f headless-service.yaml
```

Verify:

```bash
kubectl get svc
```

Expected:

```text
ClusterIP: None
```

---

# Part B – Deploy StatefulSet

Deploy the StatefulSet.

```bash
kubectl apply -f statefulset.yaml
```

Verify:

```bash
kubectl get statefulset

kubectl get pods
```

Observe:

* Ordered Pod creation
* Stable Pod names
* Stateful replicas

---

# Part C – Verify Persistent Storage

Create different data inside each Pod.

Pod 0

```bash
kubectl exec -it nginx-stateful-0 -- /bin/sh
```

```bash
echo "<h1>Stateful Pod 0</h1>" > /usr/share/nginx/html/index.html
```

Pod 1

```bash
kubectl exec -it nginx-stateful-1 -- /bin/sh
```

```bash
echo "<h1>Stateful Pod 1</h1>" > /usr/share/nginx/html/index.html
```

Verify:

```bash
kubectl exec nginx-stateful-0 -- cat /usr/share/nginx/html/index.html

kubectl exec nginx-stateful-1 -- cat /usr/share/nginx/html/index.html
```

---

# Part D – Verify Pod Recreation

Delete Pod 0.

```bash
kubectl delete pod nginx-stateful-0
```

Verify that Kubernetes recreates:

```text
nginx-stateful-0
```

Confirm the data is still available.

---

# Part E – Scaling

Scale up.

```bash
kubectl scale statefulset nginx-stateful --replicas=3
```

Verify:

```bash
kubectl get pods

kubectl get pvc
```

Observe automatic creation of:

```text
nginx-storage-nginx-stateful-2
```

Scale down.

```bash
kubectl scale statefulset nginx-stateful --replicas=2
```

Verify:

* Pod removed
* PVC retained

---

# Part F – Stable DNS

Verify hostname.

```bash
kubectl exec nginx-stateful-0 -- hostname
```

Verify FQDN.

```bash
kubectl exec nginx-stateful-0 -- hostname -f
```

Expected:

```text
nginx-stateful-0.nginx-headless.default.svc.cluster.local
```

---

# Deployment vs StatefulSet

| Feature      | Deployment       | StatefulSet   |
| ------------ | ---------------- | ------------- |
| Pod Name     | Random           | Stable        |
| DNS          | Dynamic          | Stable        |
| Storage      | Shared/Ephemeral | Dedicated PVC |
| Scaling      | Parallel         | Ordered       |
| Suitable For | Stateless Apps   | Stateful Apps |

---

# Production Use Cases

* PostgreSQL
* MySQL
* MongoDB
* Kafka
* Cassandra
* ZooKeeper
* Elasticsearch
* Redis Sentinel

---

# Key Concepts Learned

* StatefulSets
* Headless Services
* Stable Identity
* Stable DNS
* Ordered Deployment
* Ordered Scaling
* Ordered Termination
* volumeClaimTemplates
* Dynamic PVC Creation
* Persistent Storage
* Stateful Applications

---

# Interview Questions

**1. Why use StatefulSets instead of Deployments?**

For workloads that need a stable identity: fixed Pod names (`-0`, `-1`), stable DNS per Pod, a dedicated PVC per replica and ordered start, scale and update. Deployments give interchangeable Pods with shared or no storage.

**2. What is a Headless Service?**

A Service with `clusterIP: None`. Instead of one virtual IP, DNS returns the Pod IPs and creates a record per Pod.

**3. Why does StatefulSet require a Headless Service?**

The StatefulSet's `serviceName` points at it to give each Pod a stable DNS name like `nginx-stateful-0.nginx-headless.default.svc.cluster.local`, so peers can find each other by name.

**4. What are volumeClaimTemplates?**

PVC templates in the StatefulSet spec. Each replica gets its own claim, named `<template>-<pod>`, such as `nginx-storage-nginx-stateful-2` in this lab.

**5. Why does every StatefulSet Pod have its own PVC?**

So each replica keeps its own data, which databases and quorum systems need. The PVC follows the Pod's identity: `pod-0` always gets the same claim back after it is recreated.

**6. What happens during StatefulSet scale-down?**

Pods are removed in reverse order, highest ordinal first, one at a time; their PVCs are kept by default.

**7. Why are PVCs retained?**

To protect data. By default (`persistentVolumeClaimRetentionPolicy` = `Retain`), PVCs survive scale-down and deletion, so scaling up again reattaches the same data. You can set `Delete` instead.

**8. How does StatefulSet provide stable DNS?**

Each Pod gets a predictable hostname and an A record through the headless Service: `<pod>.<service>.<namespace>.svc.cluster.local`. The name stays the same even when the Pod's IP changes.

**9. Explain OrderedReady.**

The default `podManagementPolicy`: Pods are created one at a time in order, and each must be Running and Ready before the next starts; deletion happens in reverse order. `Parallel` skips the waiting.

**10. Which production applications require StatefulSets?**

Databases and clustered systems that need stable identity or storage: PostgreSQL, MySQL, MongoDB, Kafka, ZooKeeper, Elasticsearch, etcd, Redis with persistence.

---

# Conclusion

This lab demonstrated Kubernetes StatefulSets by deploying stateful workloads with stable identities, dedicated persistent storage, stable DNS, and ordered lifecycle management. These capabilities make StatefulSets the preferred workload controller for databases and distributed systems running in production Kubernetes clusters.

