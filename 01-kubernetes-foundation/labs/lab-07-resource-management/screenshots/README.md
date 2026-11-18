# Lab 07 – Screenshots

Capture the following screenshots during the lab.

| Screenshot                    | Description                           |
| ----------------------------- | ------------------------------------- |
| 01-no-resource-deployment.png | Deployment without requests or limits |
| 02-besteffort-qos.png         | Pod showing BestEffort QoS            |
| 03-resource-deployment.png    | Deployment with requests and limits   |
| 04-burstable-qos.png          | Pod showing Burstable QoS             |
| 05-oom-pod-running.png        | OOM test Pod lifecycle                |
| 06-oomkilled-status.png       | OOMKilled status                      |
| 07-crashloopbackoff.png       | CrashLoopBackOff state                |
| 08-describe-oom-pod.png       | `kubectl describe` showing OOMKilled  |
| 09-events.png                 | Kubernetes events                     |
| 10-top-nodes.png              | `kubectl top nodes` output            |
| 11-top-pods.png               | `kubectl top pods` output             |

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

## Tips

* Include both the command and output in screenshots.
* Capture screenshots in execution order.
* Use descriptive filenames matching the table above.

