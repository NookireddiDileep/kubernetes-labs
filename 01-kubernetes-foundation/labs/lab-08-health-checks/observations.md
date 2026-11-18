# Lab 08 – Observations

## Objective

Learn how Kubernetes monitors application health and availability using health probes.

---

# Observations

## Liveness Probe

* Configured an HTTP liveness probe.
* Verified successful health checks.
* Container remained healthy with no restarts.

---

## Readiness Probe

* Configured an HTTP readiness probe.
* Verified the Pod reached Ready status.
* Confirmed readiness determines whether the Pod receives traffic.

---

## Startup Probe

* Configured an HTTP startup probe.
* Verified successful startup before liveness checks began.
* Observed that startup probes are useful for slow-starting applications.

---

# Key Learnings

* Liveness probes restart unhealthy containers.
* Readiness probes control Service traffic.
* Startup probes prevent premature restarts during initialization.
* Health probes improve application resilience and availability.
* Proper probe configuration is essential for production workloads.

