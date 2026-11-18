# Lab 13 - Scenario 06: Liveness & Readiness Probe Failures

## Overview

This troubleshooting scenario demonstrates how to identify and resolve application instability caused by misconfigured Kubernetes health probes.

Health probes are critical for ensuring application availability and reliability in Kubernetes. Incorrect probe configuration can cause unnecessary container restarts, delayed traffic routing, or applications being marked as unhealthy even when they are functioning correctly.

This lab focuses on diagnosing and resolving Liveness and Readiness Probe failures using Kubernetes troubleshooting techniques.

---

## Learning Objectives

After completing this scenario, you will be able to:

- Understand the purpose of Liveness and Readiness Probes
- Differentiate between Liveness, Readiness, and Startup Probes
- Troubleshoot probe-related application failures
- Interpret Kubernetes Events
- Verify application health endpoints
- Configure health probes correctly
- Apply production troubleshooting practices

---

## Prerequisites

- Kubernetes cluster (K3s)
- kubectl configured
- Basic understanding of Deployments and Pods

---

## Repository Structure

```text
scenario-06-liveness-readiness/
├── README.md
├── deployment.yaml
└── screenshots/
```

---

## Production Scenario

A web application is deployed successfully, but Kubernetes continuously restarts the container.

Investigation reveals that the application itself is healthy, but the Liveness Probe is checking an endpoint that does not exist.

Typical production causes include:

- Incorrect health endpoint
- Wrong application port
- Slow application startup
- Aggressive probe timing
- Incorrect HTTP path

---

## Kubernetes Health Probes

### Liveness Probe

Determines whether a container is still alive.

If the probe fails repeatedly:

- Kubernetes restarts the container.

---

### Readiness Probe

Determines whether the application is ready to receive traffic.

If the probe fails:

- Pod remains running.
- Pod is removed from Service endpoints.
- No traffic is routed to the Pod.

---

### Startup Probe

Used for slow-starting applications.

Startup Probe disables Liveness and Readiness checks until the application finishes starting.

---

## Architecture

```
Application Starts
        │
        ▼
Liveness Probe
        │
        ▼
Wrong Endpoint (/health)
        │
        ▼
HTTP 404
        │
        ▼
Probe Failure
        │
        ▼
Kubelet Restarts Container
        │
        ▼
Crash Loop Continues
```

---

# Step 1 – Deploy Application

Apply the Deployment.

```bash
kubectl apply -f deployment.yaml
```

---

# Step 2 – Observe Pod Status

```bash
kubectl get pods -w
```

Expected behavior:

- Pod starts
- Restart count increases
- Pod repeatedly restarts

---

# Step 3 – Investigate

Describe the Pod.

```bash
kubectl describe pod <pod-name>
```

Expected Events:

```text
Liveness probe failed:
HTTP probe failed with statuscode: 404
```

Review recent events.

```bash
kubectl get events --sort-by=.lastTimestamp
```

---

# Root Cause Analysis

The Deployment defines:

```text
Liveness Probe
Path:
/health
```

However, the NGINX application serves:

```text
/
```

Since `/health` does not exist, Kubernetes continuously receives HTTP 404 responses.

After multiple failures, kubelet restarts the container.

The application is healthy.

The probe configuration is incorrect.

---

# Resolution

Update the Liveness Probe.

Change:

```yaml
path: /health
```

to

```yaml
path: /
```

Apply the updated Deployment.

```bash
kubectl apply -f deployment.yaml
```

Wait for rollout.

```bash
kubectl rollout status deployment probe-demo
```

---

# Validation

Verify Pod status.

```bash
kubectl get pods
```

Verify probe configuration.

```bash
kubectl describe pod <pod-name>
```

Verify application endpoint manually.

```bash
kubectl exec -it <pod-name> -- curl localhost/
```

Expected:

```text
Welcome to nginx!
```

---

# Production Troubleshooting Workflow

```
Application Restarting
          │
          ▼
kubectl get pods
          │
          ▼
High Restart Count
          │
          ▼
kubectl describe pod
          │
          ▼
Liveness Probe Failed
          │
          ▼
Verify Application Endpoint
          │
          ▼
Correct Probe Configuration
          │
          ▼
Redeploy
          │
          ▼
Application Stable
```

---

# Why Correct Health Probe Configuration Matters

Health probes directly impact application availability.

Incorrect probe configuration can cause:

- Continuous container restarts
- Application downtime
- Increased recovery time
- False health reports
- Service disruptions

Properly configured probes help Kubernetes make accurate decisions about when to restart containers and when to route traffic.

---

# Production Best Practices

- Always validate application health endpoints before configuring probes.
- Configure realistic `initialDelaySeconds` for application startup.
- Use Readiness Probes to prevent traffic from reaching unready applications.
- Use Startup Probes for applications with long initialization times.
- Avoid aggressive probe intervals that may cause unnecessary restarts.
- Regularly review probe events during incident investigations.

---
# Why Health Probes Matter in Production

Health probes are one of Kubernetes' most powerful self-healing mechanisms. They enable Kubernetes to automatically determine whether an application is healthy, ready to receive traffic, or still starting up.

Properly configured probes improve application availability, reliability, and operational resilience. However, incorrectly configured probes can cause healthy applications to be restarted unnecessarily, leading to service disruptions.

---

## Liveness Probe

A Liveness Probe answers the question:

> **"Is the application still alive?"**

If the probe fails repeatedly, Kubernetes assumes the application has become unhealthy and automatically restarts the container.

### Common Use Cases

- Detect application deadlocks
- Recover hung processes
- Restart applications that stop responding
- Enable Kubernetes self-healing

---

## Readiness Probe

A Readiness Probe answers the question:

> **"Is the application ready to serve user requests?"**

If the probe fails:

- The container continues running.
- Kubernetes removes the Pod from the Service endpoints.
- No new traffic is sent to the Pod until it becomes ready again.

This prevents users from accessing applications that are still initializing or temporarily unavailable.

---

## Startup Probe

A Startup Probe answers the question:

> **"Has the application finished starting?"**

It is designed for applications that require a long initialization time.

While the Startup Probe is running:

- Liveness Probe is disabled.
- Readiness Probe is disabled.

Once the Startup Probe succeeds, Kubernetes begins executing the configured Liveness and Readiness Probes.

Typical examples include:

- Java Spring Boot applications
- Elasticsearch
- Kafka
- Jenkins
- Large enterprise applications

---

## Real Production Example

Consider a Java application that requires approximately **90 seconds** to initialize.

If the Liveness Probe starts checking after only **10 seconds**, Kubernetes may repeatedly restart the container before the application has completed startup.

This creates a restart loop, even though the application is functioning correctly.

A better configuration is:

- Startup Probe handles the initialization phase.
- Readiness Probe waits until the application is ready to receive traffic.
- Liveness Probe monitors the application after startup for long-term health.

---

## How Platform Engineers Troubleshoot Probe Failures

When investigating probe-related issues, Platform Engineers typically follow this sequence:

1. Check Pod status and restart count.
2. Describe the Pod and review probe configuration.
3. Examine Kubernetes Events for probe failures.
4. Verify the application's health endpoint manually.
5. Compare the configured probe path and port with the application's actual behavior.
6. Adjust probe timing (`initialDelaySeconds`, `timeoutSeconds`, `periodSeconds`, `failureThreshold`) if necessary.
7. Redeploy and monitor the application's stability.

---

## Production Best Practices

- Verify application health endpoints before configuring probes.
- Use Readiness Probes to control traffic routing.
- Use Startup Probes for applications with long startup times.
- Configure realistic probe intervals and timeouts.
- Avoid aggressive probe settings that can create false failures.
- Monitor probe failures using observability platforms such as Prometheus and Grafana.
- Review Kubernetes Events during incident investigations before restarting workloads.

---

## Interview Perspective

One common interview question is:

> **Why did Kubernetes keep restarting a healthy application?**

A strong answer is:

> The application itself was healthy, but the Liveness Probe was incorrectly configured. Kubernetes relies on probe responses to determine container health. Because the probe repeatedly failed, kubelet restarted the container as part of its self-healing behavior. The correct solution was to fix the probe configuration rather than modifying the application.

This demonstrates an understanding of Kubernetes health management rather than simply identifying application issues.

---

## Key Takeaway

A healthy application can still become unavailable if its health probes are misconfigured.

Understanding how Kubernetes evaluates application health—and configuring probes appropriately—is a fundamental skill for Platform Engineers responsible for operating production Kubernetes and OpenShift environments.




# Common Interview Questions

### What is the difference between Liveness and Readiness Probes?

| Liveness Probe | Readiness Probe |
|----------------|-----------------|
| Checks whether the container is alive | Checks whether the application is ready to receive traffic |
| Failure causes container restart | Failure removes Pod from Service endpoints |
| Used for self-healing | Used for traffic management |

---

### What is a Startup Probe?

A Startup Probe is designed for slow-starting applications. It delays Liveness and Readiness checks until the application has successfully initialized.

---

### How do you troubleshoot probe failures?

1. Check Pod status.
2. Review restart count.
3. Describe the Pod.
4. Review Events.
5. Verify application endpoint manually.
6. Compare probe configuration with application behavior.
7. Update probe settings.
8. Redeploy and validate.

---

## Key Commands

```bash
kubectl get pods

kubectl describe pod <pod-name>

kubectl get events --sort-by=.lastTimestamp

kubectl exec -it <pod-name> -- curl localhost/

kubectl apply -f deployment.yaml

kubectl rollout status deployment probe-demo
```

---

# Screenshots

Store screenshots in:

```text
screenshots/
├── 01-deployment-created.png
├── 02-pod-restarting.png
├── 03-describe-pod.png
├── 04-events.png
├── 05-curl-health-check.png
├── 06-fixed-probe.png
└── 07-pod-running.png
```

---

# Key Takeaways

- Health probes are essential for application reliability.
- Incorrect probe configuration can restart healthy applications.
- `kubectl describe pod` is the primary command for diagnosing probe failures.
- Kubernetes Events provide detailed probe failure information.
- Proper probe tuning improves application stability and minimizes downtime.

---

## Lab Status

**Status:** ✅ Completed

**Scenario:** Liveness & Readiness Probe Failures

**Difficulty:** Intermediate

**Production Relevance:** ⭐⭐⭐⭐⭐
