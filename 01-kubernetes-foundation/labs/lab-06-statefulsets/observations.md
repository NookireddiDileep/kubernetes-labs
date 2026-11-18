# Lab 06 – Observations

## Objective

Learn how StatefulSets differ from Deployments by implementing stable identities, stable networking, dedicated storage, and ordered lifecycle management.

---

# Observations

## Headless Service

* Created a Headless Service using `clusterIP: None`.
* Verified that Kubernetes assigned no virtual IP.
* Confirmed it enables stable DNS records for StatefulSet Pods.

---

## StatefulSet Deployment

* Deployed a StatefulSet with two replicas.
* Observed ordered Pod creation:

  * `nginx-stateful-0`
  * `nginx-stateful-1`
* Verified stable Pod names without random suffixes.

---

## Persistent Storage

* Used `volumeClaimTemplates`.
* Kubernetes automatically created a dedicated PVC for each Pod.
* Verified independent storage for each replica.

---

## Data Persistence

* Stored different HTML content in each Pod.
* Deleted `nginx-stateful-0`.
* Kubernetes recreated the Pod with the same name.
* Verified the original data remained available.

---

## Scaling

* Scaled from 2 replicas to 3 replicas.
* Kubernetes automatically created:

  * `nginx-stateful-2`
  * `nginx-storage-nginx-stateful-2`
* Scaled back to 2 replicas.
* Verified that the Pod was removed but the PVC was retained.

---

## DNS Verification

Verified:

* Hostnames:

  * `nginx-stateful-0`
  * `nginx-stateful-1`
* Fully Qualified Domain Names:

  * `nginx-stateful-0.nginx-headless.default.svc.cluster.local`
  * `nginx-stateful-1.nginx-headless.default.svc.cluster.local`

---

# Key Learnings

* StatefulSets provide stable Pod identities.
* Each replica receives its own PersistentVolumeClaim.
* Headless Services provide stable DNS.
* StatefulSets create and terminate Pods in order.
* PVCs are preserved during scale-down to prevent data loss.
* StatefulSets are designed for databases and distributed systems.

