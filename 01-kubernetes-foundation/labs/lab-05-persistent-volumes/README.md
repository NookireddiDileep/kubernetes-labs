# Lab 05 – Kubernetes Persistent Storage

## Objective

The objective of this lab is to understand how Kubernetes provides persistent storage for applications using Persistent Volumes (PV), Persistent Volume Claims (PVC), and StorageClasses.

This lab demonstrates both **Static Provisioning** and **Dynamic Provisioning**, verifies data persistence across Pod recreation, and introduces common storage troubleshooting scenarios encountered in production Kubernetes clusters.

---

# Learning Objectives

By completing this lab, you will learn how to:

* Create Persistent Volumes (PV)
* Create Persistent Volume Claims (PVC)
* Mount persistent storage inside Pods
* Verify data persistence across Pod recreation
* Understand Static vs Dynamic Provisioning
* Understand StorageClasses
* Learn Volume Binding Modes
* Troubleshoot common PV/PVC issues
* Apply production storage best practices

---

# Lab Environment

| Component               | Value                       |
| ----------------------- | --------------------------- |
| Kubernetes Distribution | K3s                         |
| Storage Provisioner     | local-path                  |
| Provisioning Methods    | Static & Dynamic            |
| Container Runtime       | containerd                  |
| Cloud Platform          | Google Cloud Platform (GCP) |

---

# Lab Architecture

## Part A – Static Provisioning

```text
Application
     │
     ▼
PersistentVolumeClaim
     │
     ▼
PersistentVolume
     │
     ▼
HostPath Storage
```

---

## Part B – Dynamic Provisioning

```text
Application
     │
     ▼
PersistentVolumeClaim
     │
     ▼
StorageClass (local-path)
     │
     ▼
Local Path Provisioner
     │
     ▼
PersistentVolume (Created Automatically)
```

---

# Repository Structure

```text
lab-05-persistent-volumes/
├── deployment.yaml
├── observations.md
├── persistent-volume-claim.yaml
├── persistent-volume.yaml
├── README.md
├── screenshots/
│   ├── 01-pv-created.png
│   ├── 02-pvc-pending-storageclass-mismatch.png
│   ├── 03-pvc-bound.png
│   ├── 04-deployment-created.png
│   ├── 05-volume-mounted.png
│   ├── 06-index-html-created.png
│   ├── 07-pod-deleted.png
│   ├── 08-data-persisted-after-pod-recreation.png
│   ├── 09-dynamic-pvc-created.png
│   ├── 10-dynamic-pv-auto-created.png
│   ├── 11-dynamic-pod-running -part1.png
│   ├── 11-dynamic-pod-running-part2.png
│   ├── 11-storageclass-details.png
│   ├── 12-pvc-events.png
│   └── README.md
└── dynamic/
    ├── deployment-dynamic.yaml
    └── pvc-dynamic.yaml
```

---

# Part A – Static Provisioning

## Step 1

Create a PersistentVolume.

```bash
kubectl apply -f persistent-volume.yaml
```

---

## Step 2

Create a PersistentVolumeClaim.

```bash
kubectl apply -f persistent-volume-claim.yaml
```

---

## Step 3

Verify binding.

```bash
kubectl get pv

kubectl get pvc
```

---

## Step 4

Deploy an application using the PVC.

```bash
kubectl apply -f deployment.yaml
```

---

## Step 5

Create a test file.

```bash
echo "<h1>Persistent Storage Working</h1>" > /usr/share/nginx/html/index.html
```

---

## Step 6

Delete the Pod.

```bash
kubectl delete pod <pod-name>
```

---

## Step 7

Verify the file still exists after the Pod is recreated.

```bash
cat /usr/share/nginx/html/index.html
```

---

# Part B – Dynamic Provisioning

Create only a PVC.

```bash
kubectl apply -f dynamic/pvc-dynamic.yaml
```

Deploy an application.

```bash
kubectl apply -f dynamic/deployment-dynamic.yaml
```

Verify.

```bash
kubectl get pvc

kubectl get pv
```

Observe that Kubernetes automatically creates a new PersistentVolume using the default StorageClass.

---

# Static vs Dynamic Provisioning

| Feature                  | Static   | Dynamic     |
| ------------------------ | -------- | ----------- |
| PV created manually      | ✅        | ❌           |
| PV created automatically | ❌        | ✅           |
| Uses StorageClass        | Optional | Required    |
| Common in production     | Rare     | Very Common |

---

# Troubleshooting Scenarios

## Scenario 1 – PVC Pending

Cause:

* StorageClass mismatch

Resolution:

```yaml
storageClassName: ""
```

---

## Scenario 2 – WaitForFirstConsumer

The PVC remained in **Pending** until a Pod was created because the default K3s StorageClass uses:

```text
VolumeBindingMode: WaitForFirstConsumer
```

Once the Pod was scheduled, Kubernetes dynamically provisioned the PersistentVolume and the PVC transitioned to **Bound**.

---

# Production Best Practices

* Use StorageClasses for dynamic provisioning.
* Prefer CSI-based storage in production.
* Select appropriate access modes for workloads.
* Choose reclaim policies based on application requirements.
* Monitor PVC events during troubleshooting.
* Avoid using `hostPath` for production workloads.

---

# Key Concepts Learned

* Persistent Volume (PV)
* Persistent Volume Claim (PVC)
* StorageClass
* Static Provisioning
* Dynamic Provisioning
* Volume Binding Mode
* WaitForFirstConsumer
* ReadWriteOnce (RWO)
* Reclaim Policies
* Data Persistence
* CSI Provisioning
* Persistent Storage Troubleshooting

---

# Screenshots

```text
lab-05-persistent-volumes/
├── deployment.yaml
├── observations.md
├── persistent-volume-claim.yaml
├── persistent-volume.yaml
├── README.md
├── screenshots/
│   ├── 01-pv-created.png
│   ├── 02-pvc-pending-storageclass-mismatch.png
│   ├── 03-pvc-bound.png
│   ├── 04-deployment-created.png
│   ├── 05-volume-mounted.png
│   ├── 06-index-html-created.png
│   ├── 07-pod-deleted.png
│   ├── 08-data-persisted-after-pod-recreation.png
│   ├── 09-dynamic-pvc-created.png
│   ├── 10-dynamic-pv-auto-created.png
│   ├── 11-dynamic-pod-running -part1.png
│   ├── 11-dynamic-pod-running-part2.png
│   ├── 11-storageclass-details.png
│   ├── 12-pvc-events.png
│   └── README.md
└── dynamic/
    ├── deployment-dynamic.yaml
    └── pvc-dynamic.yaml
```

---

# Interview Questions

**1. What is a PersistentVolume?**

A piece of storage in the cluster with its own lifecycle, independent of any Pod, created by an admin or by a provisioner.

**2. What is a PersistentVolumeClaim?**

A request for storage by a user: size, access mode and StorageClass. Kubernetes binds it to a matching PV.

**3. Explain Static vs Dynamic Provisioning.**

Static: an admin creates PVs in advance and claims bind to them. Dynamic: a claim names a StorageClass and its provisioner creates the PV on demand. In this lab, the static PVC needed `storageClassName: ""` to bind to the hand-made PV.

**4. What is a StorageClass?**

A template for dynamic provisioning: which provisioner to use, parameters, reclaim policy, binding mode and whether volumes can be expanded. On K3s the default is `local-path`.

**5. What is a CSI Driver?**

A Container Storage Interface plugin that lets a storage system (cloud disks, SAN, Ceph and so on) provision, attach and mount volumes for Kubernetes without changes to Kubernetes itself.

**6. What is `WaitForFirstConsumer`?**

A binding mode that delays provisioning and binding until a Pod using the claim is scheduled, so the volume is created where the Pod will run. That's why the dynamic PVC stayed Pending until the Deployment existed.

**7. Explain PersistentVolume reclaim policies.**

`Delete` removes the underlying storage when the claim is deleted (the default for dynamic PVs); `Retain` keeps the PV and data for manual recovery. `Recycle` is deprecated.

**8. What are Kubernetes access modes?**

`ReadWriteOnce` (one node), `ReadOnlyMany`, `ReadWriteMany` (many nodes) and `ReadWriteOncePod` (exactly one Pod). What is supported depends on the storage backend; `local-path` supports RWO.

**9. Why might a PVC remain in `Pending`?**

No matching PV, a StorageClass mismatch or a missing StorageClass, a provisioner that is down, size or access-mode mismatch, or `WaitForFirstConsumer` waiting for a Pod.

**10. How do you verify that data persists after Pod recreation?**

Write a file into the mounted path, delete the Pod, let the controller recreate it, and read the file again. This lab did exactly that.

---

# References

* Kubernetes Persistent Volumes Documentation
* Kubernetes StorageClass Documentation
* K3s Storage Documentation
* CNCF Kubernetes Best Practices

---

# Conclusion

This lab demonstrated Kubernetes persistent storage using both Static and Dynamic Provisioning. You successfully mounted persistent storage into Pods, verified that data survives Pod recreation, diagnosed and resolved a StorageClass mismatch, and observed dynamic volume provisioning with the `local-path` StorageClass. These are foundational production skills for Kubernetes and Platform Engineering.

