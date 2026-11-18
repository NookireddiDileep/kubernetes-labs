# Lab 08 – Screenshots

Evidence captured during the lab. See the lab's [README](../README.md) and [observations](../observations.md) for what each step shows.

- [01.liveness-probe-created](01.liveness-probe-created.png)
- [02.readiness-probe-created](02.readiness-probe-created.png)
- [03.startp-probe-created](03.startp-probe-created.png)
- [nginx-liveness pod describe](nginx-liveness%20pod%20describe.txt)
- [nginx-readiness pod describe](nginx-readiness%20pod%20describe.txt)
- [nginx-startup pod describe](nginx-startup%20pod%20describe.txt)

## Recommended Commands

```bash
kubectl get pods
kubectl describe pod -l app=nginx-liveness
kubectl describe pod -l app=nginx-readiness
kubectl describe pod -l app=nginx-startup
```
