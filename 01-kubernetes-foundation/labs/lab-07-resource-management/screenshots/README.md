# Lab 07 – Screenshots

Evidence captured during the lab. See the lab's [README](../README.md) and [observations](../observations.md) for what each step shows.

- [01.nginx-no-resources created](01.nginx-no-resources%20created.png)
- [02.ngnix no-resource pod describe part-1](02.ngnix%20no-resource%20pod%20describe%20part-1.png)
- [02.ngnix no-resource pod describe part-2 with QoS class besteffort](02.ngnix%20no-resource%20pod%20describe%20part-2%20with%20QoS%20class%20besteffort.png)
- [03. QoS class with Burstable](03.%20QoS%20class%20with%20Burstable.png)
- [04.oom-killed](04.oom-killed.png)
- [05.delete oom-kill pod](05.delete%20oom-kill%20pod.png)

## Recommended Commands

```bash
kubectl get deployment
kubectl get pods
kubectl describe pod <pod-name>
kubectl get pod oom-test -w
kubectl top nodes
kubectl top pods
kubectl get events --sort-by=.metadata.creationTimestamp
```
