# Lab 05 – Observations

## Objective

Understand Kubernetes persistent storage using both Static and Dynamic Provisioning while validating data persistence and troubleshooting common storage issues.

---

# Observations

## Static Provisioning

* Created a PersistentVolume (PV) manually.
* Created a PersistentVolumeClaim (PVC).
* Initially observed the PVC remaining in the **Pending** state due to a StorageClass mismatch.
* Updated the PVC by setting:

```yaml
storageClassName: ""
```

* The PVC successfully transitioned to the **Bound** state.
* Deployed an NGINX application using the PVC.
* Created a test HTML file inside the mounted volume.
* Deleted the Pod and verified that Kubernetes recreated it automatically.
* Confirmed that the HTML file remained available after Pod recreation, demonstrating persistent storage.

---

## Dynamic Provisioning

* Created a PVC using the default `local-path` StorageClass.
* Observed the PVC initially remain in the **Pending** state.
* Verified that the StorageClass uses:

```
VolumeBindingMode: WaitForFirstConsumer
```

* Created a Deployment that consumed the PVC.
* The Local Path Provisioner automatically created a PersistentVolume.
* The PVC transitioned to the **Bound** state.
* Verified that the Pod reached the **Running** state with the dynamically provisioned volume mounted successfully.

---

# Troubleshooting Performed

### Issue 1

**Problem**

PVC remained in `Pending`.

**Root Cause**

StorageClass mismatch between the manually created PV and the PVC.

**Resolution**

Removed the StorageClass from the PVC (`storageClassName: ""`) so it matched the static PV.

---

### Issue 2

**Problem**

Dynamic PVC remained in `Pending`.

**Root Cause**

The `local-path` StorageClass uses `WaitForFirstConsumer`, delaying provisioning until a Pod is scheduled.

**Resolution**

Created a Deployment that referenced the PVC. Kubernetes automatically provisioned a PersistentVolume and bound the claim.

---

# Production Learnings

* Dynamic provisioning is the preferred approach in modern Kubernetes environments.
* StorageClasses abstract the underlying storage implementation.
* CSI provisioners automate storage lifecycle management.
* `kubectl describe pvc` and `kubectl describe pod` are essential commands for diagnosing storage issues.
* Event messages provide the fastest path to identifying provisioning failures.

---

# Commands Frequently Used

```bash
kubectl get pv
kubectl get pvc
kubectl describe pv
kubectl describe pvc
kubectl get storageclass
kubectl describe storageclass
kubectl get events
kubectl describe pod
```

---

# Key Takeaways

* Understand the relationship between PV, PVC, StorageClass, and Pods.
* Recognize the difference between static and dynamic provisioning.
* Verify storage persistence through Pod recreation.
* Interpret Kubernetes Events for storage troubleshooting.
* Understand how `WaitForFirstConsumer` influences provisioning behavior.

