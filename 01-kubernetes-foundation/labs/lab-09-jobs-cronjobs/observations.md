# Lab 09 – Observations

## Objective

To understand how Kubernetes executes one-time batch workloads using **Jobs** and recurring scheduled workloads using **CronJobs**.

---

# Observations

## Part A – Kubernetes Job

### Job Creation

* Successfully created a Kubernetes Job named **hello-job**.
* Kubernetes created one Pod to execute the workload.
* The container executed the configured shell commands successfully.
* The Job completed with a status of **Complete (1/1)**.

---

### Job Execution

The Job performed the following actions:

* Printed a greeting message.
* Displayed the current system date and time.
* Waited for 10 seconds.
* Printed a completion message.
* Exited successfully with exit code **0**.

---

### Job Status

Observed using:

```bash
kubectl get jobs
kubectl describe job hello-job
```

Findings:

* Completion Status: **1/1**
* Failed Pods: **0**
* Active Pods: **0**
* Succeeded Pods: **1**
* Restart Policy: **Never**
* Backoff Limit: **6**

---

## Part B – Kubernetes CronJob

### CronJob Creation

Successfully created a CronJob named **hello-cronjob**.

Configured schedule:

```text
*/2 * * * *
```

The CronJob automatically created a new Job every **2 minutes**.

---

### Scheduled Executions

Observed multiple Jobs being created automatically.

Example:

* hello-cronjob-29745600
* hello-cronjob-29745602

Each Job:

* Created one Pod
* Executed successfully
* Completed without failures

---

### CronJob Configuration

Observed using:

```bash
kubectl describe cronjob hello-cronjob
```

Configuration verified:

* Schedule: Every 2 minutes
* Suspend: False
* Concurrency Policy: Allow
* Successful Job History Limit: 3
* Failed Job History Limit: 1

---

### Logs Verification

Verified execution using:

```bash
kubectl logs job/<generated-job-name>
```

Output confirmed:

* CronJob executed successfully.
* Timestamp displayed correctly.
* Workload finished normally.

---

# Key Learnings

* Jobs execute workloads one time until successful completion.
* CronJobs automatically create Jobs based on a cron schedule.
* Jobs terminate after completion and do not continue running.
* CronJobs are ideal for recurring operational tasks.
* Kubernetes controllers automatically manage Job execution and history.
* Job logs are available even after successful completion (while the Job and Pod remain).
* Proper history limits help control resource usage in production clusters.

---

# Production Relevance

Typical production use cases include:

* Database backups
* Scheduled maintenance
* Log rotation
* Report generation
* Data synchronization
* ETL pipelines
* Certificate renewal
* Batch processing

