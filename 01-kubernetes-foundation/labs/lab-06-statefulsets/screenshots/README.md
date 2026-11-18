# Lab 06 – Screenshots

Evidence captured during the lab. See the lab's [README](../README.md) and [observations](../observations.md) for what each step shows.

- [01-headless-service-created](01-headless-service-created.png)
- [02-statefulset-created_pods-running-pvcs-pvcs_created](02-statefulset-created_pods-running-pvcs-pvcs_created.png)
- [03-stateful-data-persisted](03-stateful-data-persisted.png)
- [04.Scaling StatefulSets](04.Scaling%20StatefulSets.png)

## Recommended Commands

```bash
kubectl get statefulset
kubectl get pods
kubectl get pvc
kubectl get svc
kubectl exec nginx-stateful-0 -- hostname
kubectl exec nginx-stateful-0 -- hostname -f
```
