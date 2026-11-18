# Lab 05 – Screenshots

Evidence captured during the lab. See the lab's [README](../README.md) and [observations](../observations.md) for what each step shows.

- [01-pv-created](01-pv-created.png)
- [02-pvc-pending-storageclass-mismatch](02-pvc-pending-storageclass-mismatch.png)
- [03-pvc-bound](03-pvc-bound.png)
- [04-deployment-created](04-deployment-created.png)
- [05-volume-mounted](05-volume-mounted.png)
- [06-index-html-created](06-index-html-created.png)
- [07-pod-deleted](07-pod-deleted.png)
- [08-data-persisted-after-pod-recreation](08-data-persisted-after-pod-recreation.png)
- [09-dynamic-pvc-created](09-dynamic-pvc-created.png)
- [10-dynamic-pv-auto-created](10-dynamic-pv-auto-created.png)
- [11-dynamic-pod-running -part1](11-dynamic-pod-running%20-part1.png)
- [11-dynamic-pod-running-part2](11-dynamic-pod-running-part2.png)
- [11-storageclass-details](11-storageclass-details.png)
- [12-pvc-events](12-pvc-events.png)

## Recommended Commands

```bash
kubectl get pv
kubectl get pvc
kubectl get pods
kubectl describe pvc nginx-dynamic-pvc
kubectl describe storageclass local-path
```
