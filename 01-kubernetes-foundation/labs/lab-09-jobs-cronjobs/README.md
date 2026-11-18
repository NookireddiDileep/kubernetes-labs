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

1. What is a Kubernetes Job?
2. What is a Kubernetes CronJob?
3. Difference between Job and Deployment?
4. Difference between Job and CronJob?
5. What is `backoffLimit`?
6. What is `restartPolicy`?
7. What is `concurrencyPolicy`?
8. What cron expression runs every two minutes?
9. How do you check Job logs?
10. What are common production use cases for CronJobs?

---

# Conclusion

This lab demonstrated the execution of one-time and scheduled workloads using Kubernetes Jobs and CronJobs. These resources are fundamental for automating operational tasks such as backups, maintenance, reporting, and batch processing in production Kubernetes and OpenShift environments.

