# Lab 11 – Screenshots Guide

This folder contains screenshots demonstrating each major step completed during **Lab 11 – Namespaces & Resource Quotas**.

---

# Screenshot Naming Convention

```text
01-namespace-created.png
02-namespace-describe.png
03-resourcequota-created.png
04-resourcequota-describe.png
05-limitrange-created.png
06-limitrange-describe.png
07-deployment-created.png
08-pod-running.png
09-pod-describe.png
10-resourcequota-usage.png
```

---

# Screenshot Details

## 01-namespace-created.png

**Command**

```bash
kubectl get namespaces
```

**Capture**

* `development` namespace listed
* Namespace status **Active**

---

## 02-namespace-describe.png

**Command**

```bash
kubectl describe namespace development
```

**Capture**

* Namespace information
* Labels
* Status
* Initial message showing:

  * No ResourceQuota
  * No LimitRange

---

## 03-resourcequota-created.png

**Command**

```bash
kubectl get resourcequota -n development
```

**Capture**

* `development-quota`
* Current resource usage
* Configured limits

---

## 04-resourcequota-describe.png

**Command**

```bash
kubectl describe resourcequota development-quota -n development
```

**Capture**

* Hard limits
* Used resources
* CPU requests
* Memory requests
* CPU limits
* Memory limits
* Pod quota

---

## 05-limitrange-created.png

**Command**

```bash
kubectl get limitrange -n development
```

**Capture**

* `development-limits`
* Creation timestamp

---

## 06-limitrange-describe.png

**Command**

```bash
kubectl describe limitrange development-limits -n development
```

**Capture**

* Default CPU Request (250m)
* Default Memory Request (256Mi)
* Default CPU Limit (500m)
* Default Memory Limit (512Mi)

---

## 07-deployment-created.png

**Command**

```bash
kubectl get deployments -n development
```

**Capture**

* `nginx-demo`
* READY replicas
* AVAILABLE replicas
* Deployment status

---

## 08-pod-running.png

**Command**

```bash
kubectl get pods -n development
```

**Capture**

* Pod name
* READY `1/1`
* STATUS `Running`
* RESTARTS
* AGE

---

## 09-pod-describe.png

**Command**

```bash
kubectl describe pod -n development
```

**Capture**

Focus on:

* `Requests`
* `Limits`
* `QoS Class`
* `LimitRanger` annotation
* Container status

These confirm that the **LimitRange** automatically applied default resource values.

---

## 10-resourcequota-usage.png

**Command**

```bash
kubectl describe resourcequota development-quota -n development
```

**Capture**

Verify updated usage:

* Pods: `1 / 10`
* Requests CPU: `250m / 2`
* Requests Memory: `256Mi / 2Gi`
* Limits CPU: `500m / 4`
* Limits Memory: `512Mi / 4Gi`

This demonstrates that the ResourceQuota tracks namespace resource consumption after the Deployment is created.

---

# Recommended Capture Order

1. Namespace created
2. Namespace details
3. ResourceQuota created
4. ResourceQuota details
5. LimitRange created
6. LimitRange details
7. Deployment created
8. Pod running
9. Pod description
10. ResourceQuota usage

---

# Outcome

These screenshots provide visual evidence of:

* Namespace isolation
* ResourceQuota enforcement
* LimitRange configuration
* Automatic default resource assignment
* Deployment success
* Pod health
* Namespace resource accounting

Together, they document the complete lifecycle of implementing resource governance within a Kubernetes namespace.

