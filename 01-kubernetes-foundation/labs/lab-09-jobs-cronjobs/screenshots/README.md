# Lab 09 – Screenshots

Evidence captured during the lab. See the lab's [README](../README.md) and [observations](../observations.md) for what each step shows.

- [01-job-created and job-status](01-job-created%20and%20job-status.png)
- [02-job-describe](02-job-describe.png)
- [03-job-logs](03-job-logs.png)
- [04-cronjob-created](04-cronjob-created.png)
- [05-cronjob-logs](05-cronjob-logs.png)
- [06-cronjob-describe](06-cronjob-describe.png)

## Recommended Commands

```bash
kubectl get jobs

kubectl logs job/hello-job

kubectl describe job hello-job

kubectl get cronjobs

kubectl get jobs

kubectl logs job/<generated-job-name>

kubectl describe cronjob hello-cronjob
```

---
