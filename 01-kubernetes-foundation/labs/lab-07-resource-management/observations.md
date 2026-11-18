# Lab 07 – Observations

## Objective

Understand Kubernetes resource management and troubleshoot resource-related issues in production workloads.

---

# Observations

## Deployment Without Resources

* Created a Deployment without CPU or memory requests and limits.
* Verified the Pod was assigned the **BestEffort** QoS class.
* Observed that no resource guarantees were configured.

---

## Deployment With Resources

* Configured CPU requests (100m) and limits (500m).
* Configured memory requests (128Mi) and limits (256Mi).
* Verified the Pod was assigned the **Burstable** QoS class.

---

## OOMKilled Demonstration

* Created a stress test Pod with a 128Mi memory limit.
* The application attempted to allocate approximately 300MB of memory.
* Kubernetes terminated the container with:

  * Reason: OOMKilled
  * Exit Code: 137
* Observed automatic restarts and CrashLoopBackOff behavior.

---

## Resource Monitoring

* Used `kubectl top nodes` to monitor node resource usage.
* Used `kubectl top pods` to monitor live Pod CPU and memory usage.
* Confirmed that idle NGINX Pods consumed very little CPU and memory.

---

# Key Learnings

* Requests influence scheduling decisions.
* Limits restrict maximum runtime resource consumption.
* CPU exceeding the limit results in throttling.
* Memory exceeding the limit results in OOMKilled.
* Burstable QoS is the most common class for production applications.
* `kubectl describe` and `kubectl top` provide complementary information during troubleshooting.

