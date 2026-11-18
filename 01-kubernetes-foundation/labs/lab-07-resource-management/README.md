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

**1. What is the difference between requests and limits?**

Requests are what the scheduler reserves for the container and uses to pick a node. Limits are the most the container may use at runtime: CPU above the limit is throttled, memory above the limit gets the container OOM-killed.

**2. How does the Kubernetes Scheduler use resource requests?**

It only places a Pod on a node whose allocatable CPU and memory, minus the requests of Pods already there, can fit the new Pod's requests. Actual usage is not considered.

**3. What is the purpose of CPU limits?**

To cap how much CPU time a container can use per scheduling period (CFS quota), so one busy container can't starve others. Going over causes throttling, not a kill.

**4. What causes OOMKilled?**

The container used more memory than its memory limit, so the kernel's cgroup OOM killer killed it. It can also happen when the whole node runs out of memory.

**5. Why is Exit Code 137 associated with OOMKilled?**

137 = 128 + 9: the process was ended by SIGKILL, which is what the OOM killer sends. The `OOMKilled` reason in the container status is what proves it was memory.

**6. Explain BestEffort, Burstable, and Guaranteed QoS classes.**

BestEffort: no requests or limits at all, evicted first. Burstable: some requests or limits set but not meeting Guaranteed. Guaranteed: every container has CPU and memory requests equal to limits, evicted last. In this lab: no resources gave BestEffort, 100m/500m and 128Mi/256Mi gave Burstable.

**7. What is CrashLoopBackOff?**

A container keeps failing after starting, so the kubelet restarts it with an increasing delay (10 s, 20 s, 40 s… up to 5 minutes). In this lab it followed each OOMKill.

**8. Difference between `kubectl top` and `kubectl describe`?**

`kubectl top` shows live usage from metrics-server. `kubectl describe` shows what was configured (requests, limits, QoS class) plus status and events.

**9. Why should production workloads define requests and limits?**

Requests make scheduling accurate and protect Pods from noisy neighbours; limits contain leaks and runaway processes. Without them you get overcommitted nodes, surprise evictions and unpredictable latency.

**10. How would you troubleshoot a Pod repeatedly restarting?**

Check `kubectl get pod` (restart count), `kubectl describe pod` (last state, reason, exit code, events), `kubectl logs --previous`, then decide: OOMKilled points to memory, a failing liveness probe to probe config, a non-zero exit code to the app or its config.

---

# Conclusion

This lab demonstrated Kubernetes resource management by configuring CPU and memory requests and limits, understanding QoS classes, reproducing an OOMKilled scenario, analyzing CrashLoopBackOff behavior, and monitoring resource utilization. These concepts are fundamental for operating production Kubernetes and OpenShift environments.

