# Lab 08 – Kubernetes Health Checks

## Objective

The objective of this lab is to understand how Kubernetes monitors application health using Liveness, Readiness, and Startup Probes. These probes enable Kubernetes to build self-healing applications, control traffic routing, and support zero-downtime deployments.

---

# Learning Objectives

After completing this lab, you will be able to:

* Configure Liveness Probes
* Configure Readiness Probes
* Configure Startup Probes
* Understand HTTP Health Checks
* Understand Probe Timing Parameters
* Differentiate between Liveness, Readiness, and Startup Probes
* Understand Kubernetes Self-Healing
* Apply Production Health Check Best Practices

---

# Lab Environment

| Component               | Value                       |
| ----------------------- | --------------------------- |
| Kubernetes Distribution | K3s                         |
| Container Runtime       | containerd                  |
| Cloud Platform          | Google Cloud Platform (GCP) |

---

# Repository Structure

```text
lab-08-health-checks/
│
├── README.md
├── observations.md
├── liveness.yaml
├── readiness.yaml
├── startup.yaml
└── screenshots/
    └── README.md
```

---

# Part A – Liveness Probe

Configured an HTTP liveness probe to verify that the NGINX application remained healthy.

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
```

Verified using:

```bash
kubectl describe pod -l app=nginx-liveness
```

Observation:

* Liveness probe executed successfully.
* Container remained healthy.
* Restart Count remained 0.

---

# Part B – Readiness Probe

Configured an HTTP readiness probe to determine when the application was ready to receive traffic.

```yaml
readinessProbe:
  httpGet:
    path: /
    port: 80
```

Verified using:

```bash
kubectl describe pod -l app=nginx-readiness
```

Observation:

* Pod reached Ready state.
* Readiness checks succeeded.
* Pod became eligible to receive Service traffic.

---

# Part C – Startup Probe

Configured a Startup Probe to support slow-starting applications.

```yaml
startupProbe:
  httpGet:
    path: /
    port: 80
```

Verified using:

```bash
kubectl describe pod -l app=nginx-startup
```

Observation:

* Startup probe completed successfully.
* Liveness probe started only after startup succeeded.
* Suitable for applications with long initialization times.

---

# Health Probe Comparison

| Probe     | Purpose                               | Action on Failure                             |
| --------- | ------------------------------------- | --------------------------------------------- |
| Liveness  | Verify application is alive           | Restart container                             |
| Readiness | Verify application can serve traffic  | Remove Pod from Service endpoints             |
| Startup   | Allow slow applications to initialize | Restart container after `failureThreshold` failures; until it succeeds, liveness and readiness checks don't run |

---

# Production Concepts

* Self-healing workloads
* Zero-downtime deployments
* Traffic management using readiness probes
* Protection for slow-starting applications
* Automatic container recovery
* Kubernetes health monitoring

---

# Best Practices

* Use dedicated health endpoints (`/health`, `/ready`, `/live`) where possible.
* Configure realistic timing values to avoid unnecessary failures.
* Use Startup Probes for slow-starting applications.
* Use Readiness Probes to control traffic.
* Use Liveness Probes to recover from application hangs.

---

# Interview Questions

**1. What is a Liveness Probe?**

A periodic check that the container is still working. If it fails `failureThreshold` times, the kubelet kills and restarts the container.

**2. What is a Readiness Probe?**

A check that the container can serve traffic. While it fails, the Pod is marked Not Ready and removed from Service endpoints; the container is not restarted.

**3. What is a Startup Probe?**

A check for slow-starting containers. Until it succeeds, liveness and readiness probes don't run; if it never succeeds within `failureThreshold × periodSeconds`, the container is restarted.

**4. When should Startup Probes be used?**

When start-up time is long or varies (JVM apps, large caches, migrations), so you don't have to set a huge `initialDelaySeconds` on the liveness probe.

**5. Difference between Liveness and Readiness?**

Liveness answers "should this container be restarted?"; readiness answers "should this Pod get traffic right now?". Failing liveness restarts; failing readiness only removes it from load balancing.

**6. Can a Pod be Running but Not Ready?**

Yes. The container is running but its readiness probe is failing, for example while warming up or when a dependency is down. `kubectl get pod` shows `0/1` READY with STATUS `Running`.

**7. What happens when a Liveness Probe fails?**

After `failureThreshold` consecutive failures, the kubelet kills the container and restarts it according to `restartPolicy`, increasing the restart count.

**8. What happens when a Readiness Probe fails?**

The Pod's Ready condition turns false and it is removed from the EndpointSlices of every Service that selects it, until the probe passes again.

**9. Why are health probes important in production?**

They let Kubernetes recover hung apps automatically, keep traffic away from Pods that aren't ready, and make rolling updates safe, because new Pods only receive traffic once ready.

**10. What are the best practices for configuring probes?**

Probe a cheap endpoint that reflects the app's own health. Don't make liveness depend on databases or other services. Use a startup probe for slow starts, set realistic timeouts and thresholds, and test the endpoint by hand first.

---

# Conclusion

This lab demonstrated Kubernetes health monitoring using Liveness, Readiness, and Startup Probes. These mechanisms are fundamental for building resilient, self-healing, and highly available applications in Kubernetes and OpenShift environments.

