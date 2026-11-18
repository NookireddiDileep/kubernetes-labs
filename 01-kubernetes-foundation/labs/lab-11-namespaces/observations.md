# Lab 11 – Observations

## Lab Objective

The objective of this lab was to understand how Kubernetes uses **Namespaces**, **ResourceQuotas**, and **LimitRanges** to isolate workloads and control resource consumption in a shared cluster.

---

# Environment

| Component               | Details          |
| ----------------------- | ---------------- |
| Kubernetes Distribution | K3s              |
| Namespace               | development      |
| Runtime                 | containerd       |
| Test Application        | NGINX Deployment |

---

# Step 1 – Namespace Creation

Created Namespace:

```text
development
```

Verification:

* Namespace status was **Active**
* Namespace was successfully added to the cluster
* Initially there were no ResourceQuotas or LimitRanges configured

Observation:

A Namespace provides logical isolation for Kubernetes resources. Although all namespaces share the same cluster, they allow workloads to be organized and managed independently.

---

# Step 2 – ResourceQuota

Configured:

* Pods: 10
* CPU Requests: 2 cores
* Memory Requests: 2Gi
* CPU Limits: 4 cores
* Memory Limits: 4Gi

Verification:

Immediately after creation:

```text
Pods:              0 / 10
Requests CPU:      0 / 2
Requests Memory:   0 / 2Gi
Limits CPU:        0 / 4
Limits Memory:     0 / 4Gi
```

Observation:

The quota was successfully applied to the namespace and began tracking resource usage. Since no workloads existed yet, all usage values were zero.

---

# Step 3 – LimitRange

Configured default values:

| Resource | Default Request | Default Limit |
| -------- | --------------: | ------------: |
| CPU      |            250m |          500m |
| Memory   |           256Mi |         512Mi |

Verification:

The LimitRange was successfully created and associated with the `development` namespace.

Observation:

The LimitRange ensures that containers created without explicit resource requests or limits automatically receive predefined values. This promotes consistent resource allocation and helps prevent poorly configured workloads.

---

# Step 4 – Deployment Verification

An NGINX Deployment was created without specifying a `resources:` section.

Verification:

* Deployment created successfully
* Pod reached the **Running** state
* Pod scheduled successfully
* No manual resource configuration was required

Observation:

The Deployment confirmed that Kubernetes automatically applied the default CPU and memory values from the configured LimitRange.

---

# Step 5 – Automatic Resource Assignment

Pod description showed:

```text
Requests

CPU      250m
Memory   256Mi

Limits

CPU      500m
Memory   512Mi
```

The Pod also contained the annotation:

```text
kubernetes.io/limit-ranger
```

Observation:

This verified that the **LimitRanger admission controller** injected the default resource requests and limits during Pod creation.

---

# Step 6 – ResourceQuota Usage

After the Deployment:

```text
Pods              1
Requests CPU      250m
Requests Memory   256Mi
Limits CPU        500m
Limits Memory     512Mi
```

Observation:

The ResourceQuota accurately tracked the namespace's resource consumption. As workloads were deployed, the usage values increased automatically while remaining within the configured limits.

---

# QoS Class

The deployed Pod received the following Quality of Service (QoS) class:

```text
Burstable
```

Reason:

* Resource requests were defined.
* Resource limits were defined.
* Requests and limits were different.

Observation:

Pods with the Burstable QoS class receive a balance between efficient resource utilization and scheduling guarantees.

---

# Key Learnings

* Namespaces logically isolate workloads within a Kubernetes cluster.
* ResourceQuotas limit the total resources that a namespace can consume.
* LimitRanges provide default resource requests and limits for containers.
* Kubernetes automatically injects default values through the LimitRanger admission controller.
* ResourceQuota usage updates dynamically as workloads consume resources.
* Resource governance is essential for operating shared Kubernetes clusters.

---

# Production Relevance

These Kubernetes features are widely used in enterprise environments to:

* Separate Development, Testing, and Production workloads.
* Isolate different application teams.
* Prevent resource exhaustion.
* Enforce organizational resource policies.
* Improve cluster stability.
* Support multi-tenant Kubernetes and OpenShift platforms.

---

# Common Troubleshooting

| Issue                         | Possible Cause                 | Resolution                              |
| ----------------------------- | ------------------------------ | --------------------------------------- |
| Pod rejected due to quota     | Namespace quota exceeded       | Increase quota or free resources        |
| Pod missing resource requests | No LimitRange configured       | Create a LimitRange                     |
| Namespace not found           | Namespace not created          | Create or specify the correct namespace |
| Pod stays Pending             | Insufficient node capacity     | `kubectl describe pod`, check node allocatable |
| Deployment shows 0 Ready, no Pods at all | Quota would be exceeded, so the ReplicaSet can't create Pods | `kubectl describe rs` → `FailedCreate ... exceeded quota` |
| Pod rejected: `must specify limits.cpu` | Quota covers CPU/memory but the Pod sets no requests/limits and there is no LimitRange | Add resources to the Pod or a LimitRange with defaults |

---

# Conclusion

This lab demonstrated how Kubernetes combines **Namespaces**, **ResourceQuotas**, and **LimitRanges** to provide resource isolation, governance, and predictable workload behavior. These mechanisms are fundamental for managing production Kubernetes and OpenShift environments where multiple applications and teams share the same cluster infrastructure.

