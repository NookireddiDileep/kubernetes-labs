# Lab 12 – Screenshots Guide

This directory contains screenshots demonstrating the successful implementation of **Kubernetes Ingress** using the **Traefik Ingress Controller**.

---

# Screenshot Naming Convention

```text
01-traefik-controller.png
02-ingressclass.png
03-deployment-created.png
04-pods-running.png
05-service-created.png
06-service-endpoints.png
07-ingress-created.png
08-ingress-describe.png
09-traefik-service.png
10-curl-output.png
```

---

# Screenshot Details

## 01-traefik-controller.png

**Command**

```bash
kubectl get pods -A | grep -Ei "traefik|ingress"
```

**Capture**

* Traefik controller Pod
* Running status
* Helm installation Jobs (Completed)

Purpose:

Verify that the Ingress Controller is installed and healthy.

---

## 02-ingressclass.png

**Command**

```bash
kubectl get ingressclass
```

**Capture**

* Default IngressClass
* Controller name (`traefik`)

Purpose:

Verify that Kubernetes knows which Ingress Controller will manage Ingress resources.

---

## 03-deployment-created.png

**Command**

```bash
kubectl get deployments
```

**Capture**

* `nginx-web`
* READY replicas
* AVAILABLE replicas

Purpose:

Verify successful Deployment creation.

---

## 04-pods-running.png

**Command**

```bash
kubectl get pods
```

**Capture**

* Two `nginx-web` Pods
* READY `1/1`
* STATUS `Running`

Purpose:

Confirm backend application Pods are healthy.

---

## 05-service-created.png

**Command**

```bash
kubectl get svc
```

**Capture**

* `nginx-service`
* Type: `ClusterIP`
* Cluster IP
* Port `80`

Purpose:

Verify that the Service exposes the Pods internally.

---

## 06-service-endpoints.png

**Command**

```bash
kubectl get endpoints nginx-service
```

**Capture**

* Both backend Pod IP addresses

Purpose:

Confirm that the Service selector correctly discovered the backend Pods.

---

## 07-ingress-created.png

**Command**

```bash
kubectl get ingress
```

**Capture**

* `nginx-ingress`
* Ingress Class: `traefik`
* Address: `10.160.0.2`
* Port `80`

Purpose:

Verify that the Ingress resource has been created and assigned to the Traefik controller.

---

## 08-ingress-describe.png

**Command**

```bash
kubectl describe ingress nginx-ingress
```

**Capture**

Focus on:

* Ingress Class
* Path `/`
* Backend Service (`nginx-service:80`)
* Backend Pod endpoints
* Address

Purpose:

Verify the complete routing configuration.

---

## 09-traefik-service.png

**Command**

```bash
kubectl get svc -n kube-system traefik
```

**Capture**

* Service Type: `LoadBalancer`
* External IP / Node IP
* Ports `80` and `443`

Purpose:

Verify the external entry point managed by the Traefik Ingress Controller.

---

## 10-curl-output.png

**Command**

```bash
curl http://10.160.0.2/
```

**Capture**

* **Welcome to nginx!** page

Purpose:

Confirm successful end-to-end connectivity from the client through the Ingress Controller to the backend Pods.

---

# Recommended Capture Order

1. Traefik Controller
2. IngressClass
3. Deployment
4. Pods
5. Service
6. Endpoints
7. Ingress
8. Ingress Details
9. Traefik Service
10. Browser or `curl` Output

---

# Outcome

These screenshots provide complete evidence of:

* Ingress Controller verification
* Application deployment
* Service creation
* Endpoint discovery
* Ingress configuration
* External application accessibility
* End-to-end HTTP request routing

Together, they document a complete Kubernetes Ingress implementation suitable for production-style environments and technical portfolio demonstrations.

