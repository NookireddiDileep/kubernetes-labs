# Lab 13 - Scenario 07: Persistent Volume Troubleshooting

## Overview

This troubleshooting scenario demonstrates how to diagnose and resolve Persistent Volume Claim (PVC) issues caused by an incorrect or unavailable StorageClass.

Persistent storage is essential for stateful applications such as databases, monitoring systems, CI/CD servers, and enterprise workloads. A misconfigured StorageClass prevents PVCs from binding, leaving Pods in a `Pending` state.

This lab simulates a production storage provisioning issue and walks through identifying the root cause, resolving the configuration, and validating successful volume binding.

---

## Learning Objectives

After completing this scenario, you will be able to:

- Understand Persistent Volumes (PV) and Persistent Volume Claims (PVC)
- Troubleshoot PVCs stuck in `Pending`
- Diagnose StorageClass-related issues
- Understand PV/PVC binding workflow
- Differentiate between Static and Dynamic Provisioning
- Apply production storage troubleshooting practices

---

## Prerequisites

- Kubernetes cluster (K3s)
- kubectl configured
- Default `local-path` StorageClass available
- Basic understanding of Kubernetes storage concepts

---

## Repository Structure

```text
scenario-07-persistent-volume/
├── README.md
├── pvc.yaml
├── deployment.yaml
└── screenshots/
```

---

# Production Scenario

A web application requires persistent storage through a Persistent Volume Claim.

The PVC requests a StorageClass that does not exist in the cluster, preventing Kubernetes from provisioning storage.

As a result:

- PVC remains Pending
- Pod remains Pending
- Application cannot start

---

# Architecture

```
Application
     │
     ▼
PersistentVolumeClaim
     │
     ▼
StorageClass
     │
     ▼
Storage Provisioner
     │
     ▼
PersistentVolume
```

---

# Symptoms

- PVC stuck in Pending
- Pod stuck in Pending
- Application unavailable
- Storage provisioning fails

---

# Investigation

Check PVC status:

```bash
kubectl get pvc
```

Describe the PVC:

```bash
kubectl describe pvc <pvc-name>
```

Verify available StorageClasses:

```bash
kubectl get storageclass
```

Check Persistent Volumes:

```bash
kubectl get pv
```

Review Pod events:

```bash
kubectl describe pod <pod-name>
```

---

# Root Cause Analysis

The PVC requested an invalid StorageClass.

Example:

```yaml
storageClassName: invalid-storage
```

The Kubernetes cluster only provided:

```text
local-path
```

Since no matching StorageClass existed, Kubernetes could not dynamically provision a Persistent Volume.

---

# Resolution

Update the PVC to use a valid StorageClass.

Example:

```yaml
storageClassName: local-path
```

Recreate the PVC (StorageClass is immutable):

```bash
kubectl delete deployment scenario07-pv-demo
kubectl delete pvc scenario07-invalid-sc-pvc

kubectl apply -f pvc.yaml
kubectl apply -f deployment.yaml
```

---

# Validation

Verify PVC:

```bash
kubectl get pvc
```

Expected:

```text
STATUS
Bound
```

Verify Pod:

```bash
kubectl get pods
```

Expected:

```text
Running
```

---

# PV/PVC Binding Workflow

```
Create PVC
     │
     ▼
StorageClass Selected
     │
     ▼
Provisioner Creates PV
     │
     ▼
PV Bound to PVC
     │
     ▼
Pod Starts
```

---

# Static vs Dynamic Provisioning

| Static Provisioning | Dynamic Provisioning |
|---------------------|----------------------|
| Administrator creates PV manually | Kubernetes provisions PV automatically |
| Suitable for pre-allocated storage | Preferred for cloud-native environments |
| Requires manual management | Automated and scalable |
| Common in legacy environments | Standard in modern Kubernetes clusters |

---

# Production Troubleshooting Workflow

```
Pod Pending
      │
      ▼
Check PVC
      │
      ▼
PVC Pending
      │
      ▼
Describe PVC
      │
      ▼
Review Events
      │
      ▼
Verify StorageClass
      │
      ▼
Correct Configuration
      │
      ▼
PVC Bound
      │
      ▼
Pod Running
```

---

# Why Persistent Storage Matters in Production

Most enterprise workloads require data persistence beyond the lifecycle of individual Pods.

Examples include:

- PostgreSQL
- MySQL
- MongoDB
- Prometheus
- Grafana
- Jenkins
- Elasticsearch
- Kafka

Persistent Volumes keep application data across Pod restarts and rolling updates. Surviving a **node** failure depends on the storage backend: network or replicated storage (CSI) does; node-local storage such as K3s `local-path` does not.

Incorrect storage configuration can delay deployments, impact application availability, and increase recovery time during incidents.

---

# Production Best Practices

- Use dynamic provisioning whenever possible.
- Verify StorageClass names before deployment.
- Monitor PVC binding failures.
- Keep StorageClass definitions under version control.
- Use meaningful names for PVs and PVCs.
- Regularly monitor storage capacity.
- Clean up unused PVCs and PVs to avoid resource waste.

---

# Common Interview Questions

### Why is my Pod stuck in Pending?

Possible reasons include:

- PVC Pending
- Missing StorageClass
- No available Persistent Volume
- Capacity mismatch
- AccessMode mismatch

---

### How do you troubleshoot a PVC stuck in Pending?

1. Check PVC status.
2. Describe the PVC.
3. Review Kubernetes Events.
4. Verify StorageClass.
5. Check available Persistent Volumes.
6. Confirm storage provisioner health.
7. Correct configuration and redeploy.

---

### Can the StorageClass of an existing PVC be modified?

No.

`storageClassName` is immutable after the PVC has been created. A new PVC must be created with the desired StorageClass.

---

# Key Commands

```bash
kubectl get pvc

kubectl describe pvc <pvc-name>

kubectl get pv

kubectl get storageclass

kubectl describe pod <pod-name>

kubectl apply -f pvc.yaml

kubectl apply -f deployment.yaml
```

---

# Screenshots

Evidence captured during this scenario:

- [pvc pending](screenshots/01-pvc-pending.png)
- [pod pending](screenshots/02-pod-pending.png)
- [describe pvc](screenshots/03-describe-pvc.png)
- [pvc bound](screenshots/04-pvc-bound.png)
- [pod running](screenshots/05-pod-running.png)

---

# Key Takeaways

- PVCs depend on valid StorageClasses.
- Pods cannot start until PVCs are successfully bound.
- `kubectl describe pvc` is the primary troubleshooting command.
- Dynamic provisioning simplifies storage management.
- Storage troubleshooting is a fundamental Platform Engineering skill.

---

## Lab Status

**Status:** ✅ Completed

**Scenario:** Persistent Volume Troubleshooting

**Difficulty:** Intermediate

**Production Relevance:** ⭐⭐⭐⭐⭐
