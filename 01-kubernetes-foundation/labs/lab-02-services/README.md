# Lab 02 – Kubernetes Services

## Objective

The objective of this lab is to understand how Kubernetes Services provide stable network access to Pods. You will deploy an NGINX application, expose it using a ClusterIP Service, and verify service discovery and load balancing within the Kubernetes cluster.

---

## Learning Objectives

By completing this lab, you will learn how to:

* Create Kubernetes Services
* Understand the purpose of ClusterIP Services
* Expose Deployments using Services
* Understand Labels and Selectors
* Verify Endpoints and EndpointSlices
* Test Service connectivity
* Understand Kubernetes Service Discovery
* Learn production networking concepts

---

## Lab Architecture

```text
                   Client Pod
                        │
                        │
                 ClusterIP Service
                 nginx-service
                        │
          ┌─────────────┴─────────────┐
          │                           │
      NGINX Pod 1                NGINX Pod 2
                        │
                   Deployment
```

---

## Files

```text
lab-02-services/
├── README.md
├── deployment.yaml
├── service.yaml
├── observations.md
└── screenshots/
    └── README.md
```

---

## Prerequisites

* Running Kubernetes cluster (K3s, Minikube, Kind, or OpenShift)
* kubectl configured
* Basic understanding of Pods and Deployments

---

## Lab Steps

### Step 1 – Deploy the Application

Create the Deployment.

```bash
kubectl apply -f deployment.yaml
```

Verify the Deployment.

```bash
kubectl get deployments
```

Verify the Pods.

```bash
kubectl get pods -o wide
```

---

### Step 2 – Create the Service

Expose the Deployment using a ClusterIP Service.

```bash
kubectl apply -f service.yaml
```

Verify the Service.

```bash
kubectl get services
```

Describe the Service.

```bash
kubectl describe service nginx-service
```

---

### Step 3 – Verify Endpoints

Check the Service endpoints.

```bash
kubectl get endpoints
```

Verify EndpointSlices.

```bash
kubectl get endpointslices
```

---

### Step 4 – Test Service Connectivity

Create a temporary test Pod.

```bash
kubectl run test-client --rm -it --image=busybox -- sh
```

Inside the Pod, test connectivity.

```bash
wget -qO- http://nginx-service
```

Exit the test Pod.

```bash
exit
```

---

### Step 5 – Cleanup

Delete the resources.

```bash
kubectl delete -f service.yaml

kubectl delete -f deployment.yaml
```

---

## Expected Output

After completing this lab, you should observe:

* Deployment successfully created
* Pods in Running state
* ClusterIP Service created
* Service assigned a stable cluster IP
* Endpoints mapped to running Pods
* Successful communication using the Service DNS name

---

## Screenshots

```text
screenshots/
├── 01-deployment-created.png
├── 02-pods-running.png
├── 03-service-created.png
├── 04-service-describe.png
├── 05-endpoints.png
├── 06-endpointslices.png
├── 07-service-connectivity-test.png
```

---

## Production Best Practices

* Use Services instead of accessing Pods directly.
* Select meaningful labels for Service selectors.
* Prefer ClusterIP for internal application communication.
* Expose applications externally using Ingress or LoadBalancer where appropriate.
* Regularly verify Endpoints during troubleshooting.
* Use DNS-based Service discovery rather than Pod IP addresses.

---

## Key Concepts Learned

* Kubernetes Services
* ClusterIP
* Labels & Selectors
* Endpoints
* EndpointSlices
* Service Discovery
* Internal DNS
* Network Abstraction
* Load Balancing

---

## Interview Questions

1. What is a Kubernetes Service?
2. Why do Pods require a Service?
3. What are the different Service types in Kubernetes?
4. What is the default Service type?
5. How do Labels and Selectors work with Services?
6. What are Endpoints?
7. What are EndpointSlices?
8. How does Kubernetes perform Service discovery?
9. What happens if a Pod is recreated?
10. When would you use ClusterIP, NodePort, or LoadBalancer?

---

## References

* Official Kubernetes Documentation
* Kubernetes Services Documentation
* CNCF Kubernetes Best Practices

---

## Conclusion

This lab demonstrated how Kubernetes Services provide stable networking for applications running inside a cluster. You learned how Services decouple clients from individual Pods, enable service discovery, distribute traffic across healthy Pods, and provide a reliable networking abstraction that is fundamental to production Kubernetes environments.

