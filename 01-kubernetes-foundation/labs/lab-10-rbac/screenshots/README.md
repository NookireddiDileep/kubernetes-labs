# Lab 10 – Screenshots

Capture the following screenshots while performing the lab.

| Screenshot                     | Description                             |
| ------------------------------ | --------------------------------------- |
| 01-serviceaccount-created.png  | Output of `kubectl get serviceaccounts` |
| 02-serviceaccount-describe.png | Details of `pod-reader` ServiceAccount  |
| 03-role-created.png            | Output of `kubectl get roles`           |
| 04-role-describe.png           | Details of `pod-reader-role`            |
| 05-rolebinding-created.png     | Output of `kubectl get rolebindings`    |
| 06-rolebinding-describe.png    | Details of `pod-reader-binding`         |
| 07-auth-can-i-get.png          | `kubectl auth can-i get pods` result    |
| 08-auth-can-i-delete.png       | `kubectl auth can-i delete pods` result |

---

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

## Tips

* Include both the executed command and its output in every screenshot.
* Ensure the ServiceAccount, Role, and RoleBinding names are clearly visible.
* Capture the `yes` and `no` results from the permission checks.
* Keep screenshots in numerical order to match the documentation.

