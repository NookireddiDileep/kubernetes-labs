# Lab 09 – Kubernetes Jobs & CronJobs

## Objective

The objective of this lab is to understand how Kubernetes executes one-time batch workloads using **Jobs** and recurring scheduled workloads using **CronJobs**. This lab demonstrates how Kubernetes manages task execution, retries failed workloads, schedules recurring jobs, and automates operational activities commonly used in production environments.

---

# Learning Objectives

After completing this lab, you will be able to:

* Create Kubernetes Jobs
* Create Kubernetes CronJobs
* Execute one-time batch workloads
* Schedule recurring workloads
* Understand Job lifecycle
* Understand CronJob scheduling
* Configure Job retry behavior
* Configure Job history retention
* Understand Concurrency Policies
* Apply production batch processing best practices

---

# Lab Environment

| Component               | Value                       |
| ----------------------- | --------------------------- |
| Kubernetes Distribution | K3s                         |
| Container Runtime       | containerd                  |
| Cloud Platform          | Google Cloud Platform (GCP) |
| Operating System        | Ubuntu 24.04 LTS            |

---

# Repository Structure

```text
lab-09-jobs-cronjobs/
│
├── README.md
├── observations.md
├── job.yaml
├── cronjob.yaml
└── screenshots/
    └── README.md
```

---

# Part A – Kubernetes Job

A Kubernetes **Job** is used to execute a task until it completes successfully. Unlike Deployments, Jobs are not intended to run continuously.

## Job Manifest

The Job used the **BusyBox** container to execute a simple shell script.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: hello-job
spec:
  template:
    spec:
      restartPolicy: Never
      containers:
      - name: hello
        image: busybox:latest
        command:
        - /bin/sh
        - -c
        - |
          echo "Hello from Kubernetes Job"
          date
          sleep 10
          echo "Job Completed Successfully"
```

---

## Deploy the Job

```bash
kubectl apply -f job.yaml
```

---

## Verification

Commands executed:

```bash
kubectl get jobs
kubectl get pods
kubectl logs job/hello-job
kubectl describe job hello-job
```

Observed results:

* Job created successfully.
* One Pod was created.
* Script executed successfully.
* Job completed with **1/1** completion.
* No failures occurred.
* Restart Policy remained **Never**.

---

# Part B – Kubernetes CronJob

A **CronJob** automatically creates Jobs according to a defined schedule using standard cron syntax.

The schedule configured for this lab was:

```yaml
schedule: "*/2 * * * *"
```

This executes the workload every **2 minutes**.

---

## CronJob Manifest

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello-cronjob
spec:
  schedule: "*/2 * * * *"
  successfulJobsHistoryLimit: 3
  failedJobsHistoryLimit: 1
  jobTemplate:
    spec:
      template:
        spec:
          restartPolicy: Never
          containers:
          - name: hello
            image: busybox:latest
            command:
            - /bin/sh
            - -c
            - |
              echo "CronJob executed"
              date
```

---

## Deploy the CronJob

```bash
kubectl apply -f cronjob.yaml
```

---

## Verification

Commands executed:

```bash
kubectl get cronjobs
kubectl get jobs
kubectl logs job/<generated-job-name>
kubectl describe cronjob hello-cronjob
```

Observed results:

* CronJob created successfully.
* Jobs were automatically created every two minutes.
* Generated Jobs completed successfully.
* CronJob maintained execution history.
* Controller events confirmed successful scheduling.

---

# Job vs CronJob

| Job                    | CronJob                                |
| ---------------------- | -------------------------------------- |
| Executes once          | Executes repeatedly on a schedule      |
| Manual execution       | Automatic execution                    |
| Batch processing       | Scheduled batch processing             |
| Stops after completion | Waits for the next scheduled execution |

---

# Cron Schedule Used

| Expression    | Meaning         |
| ------------- | --------------- |
| `*/2 * * * *` | Every 2 minutes |

---

# Production Use Cases

Jobs

* Database migration
* ETL processing
* Certificate generation
* Cache warm-up
* One-time initialization
* Report generation

CronJobs

* Database backups
* Log cleanup
* Scheduled maintenance
* Temporary file cleanup
* Data synchronization
* Certificate renewal
* Automated reporting

---

# Key Kubernetes Concepts

* Jobs
* CronJobs
* Batch Workloads
* Scheduled Workloads
* Job Controller
* CronJob Controller
* Retry Mechanism
* Job History
* Concurrency Policy
* Restart Policy
* Batch Processing

---

# Best Practices

* Design Jobs to be idempotent.
* Configure appropriate retry limits.
* Use CronJobs only for recurring workloads.
* Limit retained Job history to reduce resource usage.
* Select the correct `concurrencyPolicy` based on workload requirements.
* Monitor Job failures using Kubernetes events and logs.

---

# Interview Questions

**1. What is a Kubernetes Job?**

A controller that runs Pods until a set number of them complete successfully, for one-off batch work. This lab's Job finished `1/1` with exit code 0.

**2. What is a Kubernetes CronJob?**

A controller that creates Jobs on a cron schedule, like `*/2 * * * *` (every two minutes) in this lab.

**3. Difference between Job and Deployment?**

A Deployment keeps Pods running forever and restarts them if they exit. A Job expects Pods to finish, and is done once enough have succeeded.

**4. Difference between Job and CronJob?**

A Job runs once when you create it. A CronJob creates a new Job at each scheduled time and keeps a limited history of them.

**5. What is `backoffLimit`?**

How many times a Job retries failed Pods before marking itself Failed. The default is 6, with exponentially increasing delays between retries.

**6. What is `restartPolicy`?**

What the kubelet does when a container exits: `Always` (Deployments), `OnFailure` or `Never`. Job Pods must use `OnFailure` or `Never`; with `Never`, each retry is a new Pod.

**7. What is `concurrencyPolicy`?**

What a CronJob does if the previous Job is still running at the next scheduled time: `Allow` (default, run both), `Forbid` (skip the new one) or `Replace` (stop the old one, start the new one).

**8. What cron expression runs every two minutes?**

`*/2 * * * *`.

**9. How do you check Job logs?**

`kubectl logs job/<job-name>`, or find its Pod with `kubectl get pods -l job-name=<job-name>` and run `kubectl logs` on it. Logs are available as long as the Pod still exists.

**10. What are common production use cases for CronJobs?**

Backups, report generation, log or temporary-file clean-up, certificate checks and periodic data syncs.

---

# Conclusion

This lab demonstrated the execution of one-time and scheduled workloads using Kubernetes Jobs and CronJobs. These resources are fundamental for automating operational tasks such as backups, maintenance, reporting, and batch processing in production Kubernetes and OpenShift environments.

