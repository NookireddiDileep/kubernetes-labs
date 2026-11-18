# Lab 08 – Screenshots

Capture the following screenshots during the lab.

| Screenshot                  | Description                   |
| --------------------------- | ----------------------------- |
| 01-liveness-deployment.png  | Deploy Liveness Probe         |
| 02-liveness-describe.png    | Liveness Probe details        |
| 03-readiness-deployment.png | Deploy Readiness Probe        |
| 04-readiness-describe.png   | Readiness Probe details       |
| 05-startup-deployment.png   | Deploy Startup Probe          |
| 06-startup-describe.png     | Startup Probe details         |
| 07-pod-status.png           | Running Pods after deployment |

## Recommended Commands

```bash
kubectl get pods
kubectl describe pod -l app=nginx-liveness
kubectl describe pod -l app=nginx-readiness
kubectl describe pod -l app=nginx-startup
```

## Tips

* Include both the executed command and its output.
* Capture screenshots in chronological order.
* Use the suggested filenames for consistency.

