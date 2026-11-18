# Lab 09 – Screenshots

Capture the following screenshots while performing the lab.

| Screenshot              | Description                                        |
| ----------------------- | -------------------------------------------------- |
| 01-job-created.png      | Kubernetes Job created successfully                |
| 02-job-status.png       | Output of `kubectl get jobs`                       |
| 03-job-logs.png         | Output of `kubectl logs job/hello-job`             |
| 04-job-describe.png     | Output of `kubectl describe job hello-job`         |
| 05-cronjob-created.png  | Output of `kubectl get cronjobs`                   |
| 06-cronjob-jobs.png     | Jobs created by the CronJob                        |
| 07-cronjob-logs.png     | Logs of a generated CronJob execution              |
| 08-cronjob-describe.png | Output of `kubectl describe cronjob hello-cronjob` |

---

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

## Tips

* Capture both the command and its output in each screenshot.
* Follow the numbering convention to keep screenshots in chronological order.
* Ensure the generated CronJob name is visible in the logs and Job list.
* Verify timestamps are visible where applicable.

