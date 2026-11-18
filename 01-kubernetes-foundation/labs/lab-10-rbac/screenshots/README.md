# Lab 10 – Screenshots

Evidence captured during the lab. See the lab's [README](../README.md) and [observations](../observations.md) for what each step shows.

- [FInal-verication](FInal-verication.png)
- [role](role.png)
- [rolebinding](rolebinding.png)
- [serviceaccount](serviceaccount.png)

## Recommended Commands

```bash id="mjlwmg"
kubectl get serviceaccounts

kubectl describe serviceaccount pod-reader

kubectl get roles

kubectl describe role pod-reader-role

kubectl get rolebindings

kubectl describe rolebinding pod-reader-binding

kubectl auth can-i get pods --as=system:serviceaccount:default:pod-reader

kubectl auth can-i delete pods --as=system:serviceaccount:default:pod-reader
```

---
