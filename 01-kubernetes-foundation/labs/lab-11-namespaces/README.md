# Lab 11 – Namespaces & Resource Quotas

## Objective

The objective of this lab is to understand how Kubernetes uses **Namespaces**, **ResourceQuotas**, and **LimitRanges** to isolate workloads and manage resource consumption in a shared cluster. These features are essential for running multi-team Kubernetes and OpenShift environments securely and efficiently.

---

# Learning Objectives

After completing this lab, you will be able to:

* Create and manage Kubernetes Namespaces
* Understand namespace isolation
* Configure ResourceQuotas
* Configure LimitRanges
* Understand default CPU and Memory Requests/Limits
* Deploy workloads inside a namespace
* Verify automatic resource assignment
* Monitor namespace resource consumption
* Apply production best practices

---

# Lab Environment

| Component               | Details                     |
| ----------------------- | --------------------------- |
| Kubernetes Distribution | K3s                         |
| Operating System        | Ubuntu 24.04 LTS            |
| Container Runtime       | containerd                  |
| Cloud Platform          | Google Cloud Platform (GCP) |
| Namespace               | development                 |

---

# Repository Structure

```text
lab-11-namespaces/
│
├── README.md
├── observations.md
├── namespace.yaml
├── quota.yaml
├── limitrange.yaml
├── deployment.yaml
└── screenshots/
```

---

# Files Used

## namespace.yaml

Creates the **development** namespace.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: development
```

---

## quota.yaml

Defines the maximum resources allowed within the namespace.

Configured limits:

* Pods: 10
* CPU Requests: 2 cores
* Memory Requests: 2Gi
* CPU Limits: 4 cores
* Memory Limits: 4Gi

---

## limitrange.yaml

Defines default CPU and Memory Requests/Limits for containers.

Default values:

| Resource | Request | Limit |
| -------- | ------: | ----: |
| CPU      |    250m |  500m |
| Memory   |   256Mi | 512Mi |

---

## deployment.yaml

Deploys an NGINX application into the **development** namespace without explicitly specifying resource requests or limits.

Kubernetes automatically applies the defaults from the configured LimitRange.

---

# Lab Steps

## Step 1 – Create Namespace

Commands:

```bash
kubectl apply -f namespace.yaml

kubectl get namespaces

kubectl describe namespace development
```

Verification:

* Namespace created successfully
* Status: Active

---

## Step 2 – Configure ResourceQuota

Commands:

```bash
kubectl apply -f quota.yaml

kubectl get resourcequota -n development

kubectl describe resourcequota development-quota -n development
```

Verification:

* Maximum Pods: 10
* CPU Requests: 2
* Memory Requests: 2Gi
* CPU Limits: 4
* Memory Limits: 4Gi

---

## Step 3 – Configure LimitRange

Commands:

```bash
kubectl apply -f limitrange.yaml

kubectl get limitrange -n development

kubectl describe limitrange development-limits -n development
```

Verification:

Default Requests:

* CPU: 250m
* Memory: 256Mi

Default Limits:

* CPU: 500m
* Memory: 512Mi

---

## Step 4 – Deploy Application

Commands:

```bash
kubectl apply -f deployment.yaml

kubectl get pods -n development

kubectl describe pod -n development
```

Verification:

* Deployment created successfully
* Pod running
* Default resources automatically assigned

---

## Step 5 – Verify ResourceQuota Usage

Command:

```bash
kubectl describe resourcequota development-quota -n development
```

Verification:

Observed resource usage:

* Pods: 1
* CPU Request: 250m
* Memory Request: 256Mi
* CPU Limit: 500m
* Memory Limit: 512Mi

---

# ResourceQuota vs LimitRange

| ResourceQuota                                          | LimitRange                                                                        |
| ------------------------------------------------------ | --------------------------------------------------------------------------------- |
| Controls total resources available within a namespace  | Defines default, minimum, and maximum resources for individual containers or Pods |
| Prevents excessive namespace-wide resource consumption | Ensures workloads have appropriate resource requests and limits                   |
| Applied at the namespace level                         | Applied to containers or Pods                                                     |

---

# Production Use Cases

* Multi-team Kubernetes clusters
* Namespace isolation
* Development, Testing, and Production environments
* Resource governance
* Cost optimization
* Fair resource allocation
* Platform engineering
* OpenShift multi-tenancy

---

# Best Practices

* Create separate namespaces for each application or team.
* Always configure ResourceQuotas in shared clusters.
* Use LimitRanges to enforce default resource requests and limits.
* Monitor namespace resource usage regularly.
* Combine Namespaces with RBAC for secure multi-tenant environments.
* Review quotas periodically as workloads grow.

---

# Key Learnings

* Namespaces provide logical isolation within a Kubernetes cluster.
* ResourceQuotas restrict the total resources available to a namespace.
* LimitRanges automatically assign default CPU and memory values.
* Resource usage is tracked dynamically as workloads are deployed.
* These features improve cluster stability, fairness, and operational control.

---

# Interview Questions

1. What is the purpose of a Kubernetes Namespace?
2. How does ResourceQuota differ from LimitRange?
3. What happens when a Pod does not specify resource requests or limits?
4. What are the Kubernetes QoS classes?
5. Why are ResourceQuotas important in enterprise clusters?
6. How do Namespaces support multi-tenancy?
7. How does the LimitRanger admission controller work?

---

# Conclusion

In this lab, we successfully implemented namespace isolation and resource governance using **Namespaces**, **ResourceQuotas**, and **LimitRanges**. We verified that Kubernetes automatically applied default CPU and memory requests and limits to a workload through the LimitRanger admission controller and that namespace resource usage was accurately tracked by the ResourceQuota. These are foundational concepts for operating production-grade Kubernetes and OpenShift clusters.

