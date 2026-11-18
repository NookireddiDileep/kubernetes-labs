# Lab 07 – Kubernetes Resource Requests & Limits

## Objective

The objective of this lab is to understand how Kubernetes manages CPU and memory resources using requests and limits. This lab also demonstrates Kubernetes Quality of Service (QoS) classes, Out of Memory (OOMKilled) troubleshooting, CrashLoopBackOff behavior, and live resource monitoring.

---

# Learning Objectives

After completing this lab, you will be able to:

* Understand CPU Requests
* Understand CPU Limits
* Understand Memory Requests
* Understand Memory Limits
* Understand Kubernetes Scheduler decisions
* Configure Resource Requests & Limits
* Understand QoS Classes
* Troubleshoot OOMKilled Pods
* Monitor resource usage using `kubectl top`
* Apply production resource management best practices

---

# Lab Environment

| Component               | Value                       |
| ----------------------- | --------------------------- |
| Kubernetes Distribution | K3s                         |
| Container Runtime       | containerd                  |
| Storage                 | local-path                  |
| Cloud Platform          | Google Cloud Platform (GCP) |

---

# Repository Structure

```text
lab-07-resource-management/
│
├── README.md
├── observations.md
├── nginx-no-resources.yaml
├── nginx-with-resources.yaml
├── oom-test.yaml
└── screenshots/
    └── README.md
```

---

# Part A – Deploy Without Resources

Deploy an application without requests or limits.

```bash
kubectl apply -f nginx-no-resources.yaml
```

Verify:

```bash
kubectl get deployment
kubectl get pods
kubectl describe pod <pod-name>
```

Observation:

* No CPU requests
* No Memory requests
* No CPU limits
* No Memory limits
* QoS Class = **BestEffort**

---

# Part B – Configure Requests & Limits

Deploy an application with resource constraints.

```bash
kubectl apply -f nginx-with-resources.yaml
```

Configured resources:

| Resource | Request | Limit |
| -------- | ------- | ----- |
| CPU      | 100m    | 500m  |
| Memory   | 128Mi   | 256Mi |

Verify:

```bash
kubectl describe pod <pod-name>
```

Observation:

* Requests configured
* Limits configured
* QoS Class = **Burstable**

---

# Part C – OOMKilled Demonstration

Deploy the memory stress Pod.

```bash
kubectl apply -f oom-test.yaml
```

Observe:

```bash
kubectl get pod oom-test -w
```

Expected lifecycle:

```text
ContainerCreating
↓
Running
↓
OOMKilled
↓
Restart
↓
CrashLoopBackOff
```

Verify:

```bash
kubectl describe pod oom-test
```

Key findings:

* Reason = OOMKilled
* Exit Code = 137
* Restart Count increased
* CrashLoopBackOff observed

---

# Part D – Resource Monitoring

Monitor node usage.

```bash
kubectl top nodes
```

Monitor Pod usage.

```bash
kubectl top pods
```

Observation:

* Live CPU usage
* Live Memory usage
* Resource consumption differs from configured requests and limits

---

# Kubernetes QoS Classes

| QoS        | Requests | Limits               | Typical Usage                 |
| ---------- | -------- | -------------------- | ----------------------------- |
| BestEffort | None     | None                 | Test workloads                |
| Burstable  | At least one request or limit set | Not equal to requests, or not set for every container | Most production applications  |
| Guaranteed | CPU and memory set for every container | Equal to requests | Critical production workloads |

---

# Production Concepts

* Kubernetes Scheduler uses **requests** during scheduling.
* CPU limits throttle containers when exceeded.
* Memory limits trigger OOMKilled when exceeded.
* Burstable QoS is commonly used for production workloads.
* BestEffort Pods are the first candidates for eviction.
* `kubectl top` provides live resource utilization.
* `kubectl describe` displays configured requests, limits, events, and QoS.

---

# Troubleshooting Workflow

```text
Application Restarting
        │
        ▼
kubectl get pods
        │
        ▼
kubectl describe pod
        │
        ▼
Reason: OOMKilled
        │
        ▼
Review Requests & Limits
        │
        ▼
Increase Memory Limit or Optimize Application
```

---

# Interview Questions

1. What is the difference between requests and limits?
2. How does the Kubernetes Scheduler use resource requests?
3. What is the purpose of CPU limits?
4. What causes OOMKilled?
5. Why is Exit Code 137 associated with OOMKilled?
6. Explain BestEffort, Burstable, and Guaranteed QoS classes.
7. What is CrashLoopBackOff?
8. Difference between `kubectl top` and `kubectl describe`?
9. Why should production workloads define requests and limits?
10. How would you troubleshoot a Pod repeatedly restarting?

---

# Conclusion

This lab demonstrated Kubernetes resource management by configuring CPU and memory requests and limits, understanding QoS classes, reproducing an OOMKilled scenario, analyzing CrashLoopBackOff behavior, and monitoring resource utilization. These concepts are fundamental for operating production Kubernetes and OpenShift environments.

