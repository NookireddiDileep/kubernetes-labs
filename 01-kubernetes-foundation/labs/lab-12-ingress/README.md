# Lab 12 – Kubernetes Ingress

## Objective

The objective of this lab is to understand how Kubernetes **Ingress** exposes HTTP/HTTPS applications using a single entry point. This lab demonstrates deploying an application, exposing it with a ClusterIP Service, and routing external traffic through the **Traefik Ingress Controller**.

---

# Learning Objectives

After completing this lab, you will be able to:

* Understand the purpose of Kubernetes Ingress
* Understand the role of an Ingress Controller
* Deploy applications behind a ClusterIP Service
* Configure an Ingress resource
* Verify Ingress routing
* Test application accessibility
* Understand production traffic flow
* Apply Ingress best practices

---

# Lab Environment

| Component               | Details          |
| ----------------------- | ---------------- |
| Kubernetes Distribution | K3s              |
| Operating System        | Ubuntu 24.04 LTS |
| Container Runtime       | containerd       |
| Ingress Controller      | Traefik          |
| Application             | NGINX            |
| Service Type            | ClusterIP        |

---

# Repository Structure

```text
lab-12-ingress/
│
├── README.md
├── observations.md
├── deployment.yaml
├── service.yaml
├── ingress.yaml
└── screenshots/
```

---

# Files Used

## deployment.yaml

Deploys an NGINX application with two replicas.

Features:

* Deployment
* Replica count: 2
* Container image: nginx:latest
* Port: 80

---

## service.yaml

Creates a ClusterIP Service.

Features:

* Internal communication
* Service Port: 80
* Target Port: 80
* Selector:

  * app=nginx-web

---

## ingress.yaml

Creates an Ingress resource using the Traefik Ingress Controller.

Configuration:

* IngressClass: traefik
* Path: /
* Backend Service: nginx-service
* Backend Port: 80

---

# Lab Steps

## Step 1 – Verify Ingress Controller

Commands:

```bash
kubectl get pods -A | grep -Ei "traefik|ingress"

kubectl get ingressclass

kubectl get svc -A | grep -Ei "traefik|ingress"
```

Verification:

* Traefik Controller running
* Default IngressClass available
* LoadBalancer Service available

---

## Step 2 – Deploy Application

Commands:

```bash
kubectl apply -f deployment.yaml

kubectl get deployments

kubectl get pods
```

Verification:

* Deployment created
* Two replicas available
* Pods running successfully

---

## Step 3 – Create ClusterIP Service

Commands:

```bash
kubectl apply -f service.yaml

kubectl get svc

kubectl get endpoints nginx-service
```

Verification:

* ClusterIP Service created
* Endpoints automatically discovered
* Service connected to both Pods

---

## Step 4 – Create Ingress

Commands:

```bash
kubectl apply -f ingress.yaml

kubectl get ingress

kubectl describe ingress nginx-ingress
```

Verification:

* Ingress created successfully
* Traefik IngressClass assigned
* Backend Service configured correctly

---

## Step 5 – Test Access Through the Ingress

Command:

```bash
curl http://10.160.0.2/
```

Verification:

* Received the default **Welcome to nginx!** page
* Confirmed successful routing from the Ingress Controller to the application (tested with `curl` against the node's private IP from inside the VM)

---

# Request Flow

```text
Client
   │
   ▼
Traefik LoadBalancer
   │
   ▼
Traefik Ingress Controller
   │
   ▼
Ingress Resource
   │
   ▼
ClusterIP Service
   │
   ▼
NGINX Pods
```

---

# Service vs Ingress

| Service                       | Ingress                                    |
| ----------------------------- | ------------------------------------------ |
| Exposes a single application  | Routes traffic to multiple applications    |
| Operates at Layer 4 (TCP/UDP) | Operates at Layer 7 (HTTP/HTTPS)           |
| Provides connectivity to Pods | Provides HTTP/HTTPS routing rules          |
| Does not inspect URLs         | Supports path-based and host-based routing |

---

# Production Use Cases

* Hosting multiple applications behind one public endpoint
* Reverse proxy implementation
* API Gateway style routing
* TLS termination
* Host-based routing
* Path-based routing
* Centralized traffic management
* Kubernetes and OpenShift application exposure

---

# Best Practices

* Use ClusterIP Services behind an Ingress.
* Explicitly define the `ingressClassName`.
* Configure TLS for production environments.
* Use meaningful hostnames instead of wildcard rules.
* Monitor the Ingress Controller.
* Protect public applications with authentication where appropriate.

---

# Key Learnings

* An Ingress resource defines routing rules only.
* An Ingress Controller enforces those rules.
* Traefik automatically discovered backend Service endpoints.
* ClusterIP Services provide internal connectivity to Pods.
* External traffic successfully reached the application through the Ingress.

---

# Interview Questions

**1. What is a Kubernetes Ingress?**

An API object with HTTP(S) routing rules (hosts, paths, TLS) that send traffic to Services.

**2. What is an Ingress Controller?**

The software that reads Ingress objects and actually routes traffic, such as Traefik (built into K3s), NGINX or HAProxy. Without a controller, an Ingress does nothing.

**3. How does Ingress differ from a LoadBalancer Service?**

A LoadBalancer Service exposes one Service at layer 4 with its own external IP. An Ingress is layer 7 and lets many Services share one entry point using host and path rules and TLS.

**4. What is path-based routing?**

Sending requests to different Services based on the URL path, such as `/api` to one Service and `/` to another.

**5. What is host-based routing?**

Sending requests to different Services based on the HTTP `Host` header, such as `app.example.com` and `api.example.com` on the same IP.

**6. What is TLS termination?**

Decrypting HTTPS at the Ingress controller using a certificate stored in a Secret, then forwarding plain HTTP (or re-encrypted HTTPS) to the backends.

**7. Why are ClusterIP Services commonly used behind an Ingress?**

The controller already handles external exposure, so backends only need an internal, stable address. ClusterIP keeps them off node ports and out of direct reach.

---

# Conclusion

In this lab, we deployed an NGINX application, exposed it internally with a ClusterIP Service, and configured a Traefik-managed Ingress resource to route external HTTP requests to the application. This demonstrated the standard architecture used in production Kubernetes environments for exposing web applications securely and efficiently.

