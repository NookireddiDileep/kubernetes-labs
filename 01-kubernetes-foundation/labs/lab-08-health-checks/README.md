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

1. What is a Liveness Probe?
2. What is a Readiness Probe?
3. What is a Startup Probe?
4. When should Startup Probes be used?
5. Difference between Liveness and Readiness?
6. Can a Pod be Running but Not Ready?
7. What happens when a Liveness Probe fails?
8. What happens when a Readiness Probe fails?
9. Why are health probes important in production?
10. What are the best practices for configuring probes?

---

# Conclusion

This lab demonstrated Kubernetes health monitoring using Liveness, Readiness, and Startup Probes. These mechanisms are fundamental for building resilient, self-healing, and highly available applications in Kubernetes and OpenShift environments.

