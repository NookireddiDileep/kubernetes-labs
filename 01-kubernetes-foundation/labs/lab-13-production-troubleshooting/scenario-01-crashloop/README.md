# Lab 13 – Kubernetes Production Troubleshooting

## Scenario 01 – CrashLoopBackOff

---

## Objective

The objective of this lab is to understand how Kubernetes handles containers that repeatedly fail during startup and how to troubleshoot a `CrashLoopBackOff` state using standard Kubernetes debugging commands.

This scenario simulates a production application that immediately exits after starting, causing Kubernetes to continuously restart the container with an exponential back-off delay.

---

## Learning Objectives

After completing this lab, you will be able to:

* Understand the `CrashLoopBackOff` lifecycle.
* Identify common causes of container startup failures.
* Troubleshoot failed Pods using `kubectl`.
* Interpret Pod events and container exit codes.
* Recover a failed workload by correcting the application startup command.
* Understand why Pods are immutable and when they must be recreated.

---

## Prerequisites

* Kubernetes Cluster (K3s)
* kubectl configured
* BusyBox container image
* Basic knowledge of Pods and container lifecycle

---

## Directory Structure

```text
scenario-01-crashloop/
├── crashloop.yaml
├── README.md
└── screenshots/
    ├── 01 02-pod-created and crashloop status.png
    ├── 03-describe-pod.png
    ├── 04-events.png
    ├── 05-pod-update-error.png
    └── 06-pod-running-after-error-fix.png
```

---

# Scenario

A Pod is deployed with the following startup command:

```bash
echo Starting application... && exit 1
```

The application exits immediately with a non-zero exit code.

Kubernetes interprets this as a container failure and continuously restarts the Pod.

---

# Step 1 – Create the Pod

Apply the manifest:

```bash
kubectl apply -f crashloop.yaml
```

Verify the Pod:

```bash
kubectl get pods
```

Initially you may observe:

```text
ContainerCreating
```

Within a few seconds:

```text
Error
```

Finally:

```text
CrashLoopBackOff
```

---

# Step 2 – Inspect the Pod

Describe the Pod:

```bash
kubectl describe pod crash-demo
```

Important observations:

```text
State:
  Terminated

Reason:
  Error

Exit Code:
  1

Restart Count:
  2
```

Events:

```text
Back-off restarting failed container
```

These indicate that Kubernetes is attempting to restart the failed container using an exponential back-off strategy.

---

# Step 3 – View Container Logs

Retrieve the logs:

```bash
kubectl logs crash-demo
```

Output:

```text
Starting application...
```

The application starts successfully but immediately exits with status code `1`.

---

# Root Cause Analysis

The container executes:

```bash
echo Starting application... && exit 1
```

The command explicitly returns a non-zero exit code.

Kubernetes considers any non-zero exit code as a container failure and restarts the container according to the configured restart policy.

After multiple failed restart attempts, the Pod enters the `CrashLoopBackOff` state.

---

# Step 4 – Fix the Application

Modify the command:

```bash
echo Application running && sleep 3600
```

Updated manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: crash-demo
spec:
  containers:
    - name: crash-container
      image: busybox
      command:
        - /bin/sh
        - -c
        - "echo Application running && sleep 3600"
```

---

# Pod Immutability

Attempting to update the existing Pod using:

```bash
kubectl apply -f crashloop.yaml
```

results in:

```text
The Pod "crash-demo" is invalid:
spec: Forbidden:
pod updates may not change fields...
```

### Why?

Pods are immutable.

Only a limited number of Pod fields (such as the container image) can be modified after creation.

Changing the startup command requires deleting and recreating the Pod.

Delete the existing Pod:

```bash
kubectl delete pod crash-demo
```

Recreate it:

```bash
kubectl apply -f crashloop.yaml
```

Verify:

```bash
kubectl get pods
```

Expected:

```text
NAME         READY   STATUS    RESTARTS   AGE
crash-demo   1/1     Running   0          10s
```

Verify logs:

```bash
kubectl logs crash-demo
```

Output:

```text
Application running
```

---

# Troubleshooting Workflow

```text
Application exits

        │

        ▼

Container terminates

        │

        ▼

Kubernetes restarts container

        │

        ▼

Repeated failures detected

        │

        ▼

Exponential restart delay

        │

        ▼

CrashLoopBackOff
```

---

# Production Best Practices

* Always inspect Pod events before making changes.
* Use `kubectl logs` to identify application startup failures.
* Check the container exit code.
* Validate ConfigMaps and Secrets used by the application.
* Verify database and external service connectivity.
* Configure appropriate liveness, readiness, and startup probes.
* Use Deployments instead of standalone Pods in production environments.
* Update Deployments or StatefulSets instead of modifying Pods directly.

---

# Common Causes of CrashLoopBackOff

* Incorrect application startup command
* Application crashes during initialization
* Missing ConfigMap
* Missing Secret
* Invalid environment variables
* Database connection failures
* Port conflicts
* Permission issues
* Out-of-Memory (OOMKilled)
* Misconfigured liveness or startup probes

---

# Interview Questions

### Basic

**1. What is CrashLoopBackOff?**

A Pod status meaning a container keeps exiting after it starts, so the kubelet keeps restarting it with a growing delay between attempts.

**2. What causes CrashLoopBackOff?**

Anything that makes the main process exit: a bad command or entrypoint, an app crash, missing config or dependencies, failing liveness probes, or OOMKills.

**3. Which command do you execute first?**

`kubectl describe pod <pod>`: it shows the last state, exit code, reason and the events. Then `kubectl logs <pod> --previous`.

**4. How do you retrieve container logs?**

`kubectl logs <pod>` for the current container, `-c <container>` for multi-container Pods, `--previous` for the instance that crashed.

**5. What does Exit Code 1 indicate?**

The process exited with a general error. The cause is in the application or its command; in this scenario the command was literally `exit 1`.

### Intermediate

**6. Why does Kubernetes wait before restarting the container?**

To avoid hammering the node and dependencies with a container that fails instantly, and to give transient problems time to clear.

**7. What is exponential back-off?**

The delay doubles after each failure: 10 s, 20 s, 40 s and so on, capped at 5 minutes, and resets after the container has run cleanly for 10 minutes.

**8. How do you inspect previous container logs?**

`kubectl logs <pod> --previous`.

**9. How would you troubleshoot if the application exits before logs are generated?**

Use `kubectl describe` for the exit code, reason and events; override the command to keep it alive (`sleep infinity`) and exec in; use `kubectl debug` with an ephemeral container or a copy of the Pod; check config and mounts.

**10. What information is available in `kubectl describe`?**

Container state, last state, exit code and reason, restart count, image, command, probes, mounts, resources, node, QoS class and the recent events.

### Advanced

**11. Why are Pods immutable?**

So that what's running always matches what was scheduled and admitted; changes go through a new Pod, which keeps scheduling, security checks and rollouts consistent.

**12. Which Pod fields can be updated?**

Only a few, such as container `image`, `activeDeadlineSeconds` and added tolerations, plus resources through the resize subresource. The command, env vars and volumes need a new Pod.

**13. How would you recover a Deployment experiencing CrashLoopBackOff?**

Find the cause from events and previous logs, then `kubectl rollout undo` to the last good revision, or fix the manifest and apply it so the Deployment rolls out new Pods.

**14. How do liveness and startup probes influence restart behavior?**

A failing liveness probe kills and restarts a container, so a probe that's too strict can itself cause CrashLoopBackOff. A startup probe protects slow starts by holding liveness checks until the app is up.

**15. Describe a real production incident involving CrashLoopBackOff and your troubleshooting approach.**

Use the format symptom → evidence (`describe`, `logs --previous`, events) → root cause → fix → prevention. Example: a release changed an environment variable name, the app exited 1 on start; rolled back, fixed the config, added a pre-deploy config check.

---

# Screenshots

Evidence captured during this scenario:

- [02 pod created and crashloop status](screenshots/01%2002-pod-created%20and%20crashloop%20status.png)
- [describe pod](screenshots/03-describe-pod.png)
- [events](screenshots/04-events.png)
- [pod update error](screenshots/05-pod-update-error.png)
- [pod running after error fix](screenshots/06-pod-running-after-error-fix.png)

---

# Key Takeaways

* CrashLoopBackOff indicates repeated container startup failures.
* `kubectl describe` provides container state, events, and exit codes.
* `kubectl logs` helps identify application startup issues.
* Kubernetes uses an exponential back-off strategy before restarting failed containers.
* Pods are immutable; changes to most Pod fields require deletion and recreation.
* Production workloads should be managed through Deployments or StatefulSets rather than standalone Pods.

