# Networking in Kubernetes

## Introduction

Networking in Kubernetes enables communication between pods, services, and other resources within a cluster. Kubernetes provides a flexible networking model that allows containers and services to communicate whether they are running on the same node or across multiple nodes.

Understanding Kubernetes networking is essential because applications are often composed of multiple microservices that must communicate reliably and securely.

---

# Objectives

By completing this project, you will learn how to:

- Understand Kubernetes networking concepts.
- Learn how pods communicate with each other.
- Deploy a multi-container pod.
- Demonstrate how containers inside the same pod share the same network namespace.
- Verify communication between containers using `localhost`.

---

# Prerequisites

Before starting, ensure you have:

- A working Kubernetes cluster (Minikube, Kind, EKS, AKS, or GKE)
- `kubectl` installed and configured
- Basic understanding of Kubernetes Pods
- A text editor (VS Code, Nano, Vim, etc.)

---

# Kubernetes Networking Concepts

## 1. Pod Networking

Every Pod receives its own unique IP address.

Containers inside the same Pod:

- Share the same network namespace
- Share the same IP address
- Communicate using `localhost`

This allows tightly coupled containers to exchange data without exposing ports externally.

---

## 2. Service Networking

A Kubernetes Service provides a stable endpoint for accessing one or more Pods.

Features include:

- Stable ClusterIP
- Load balancing across Pods
- Internal or external access

Service Types:

- ClusterIP
- NodePort
- LoadBalancer
- ExternalName

---

## 3. Pod-to-Pod Communication

Pods communicate directly using their IP addresses.

Kubernetes networking ensures:

- Every Pod can communicate with every other Pod.
- Communication works even when Pods are scheduled on different worker nodes.
- No Network Address Translation (NAT) is required between Pods.

---

## 4. Ingress

Ingress allows external HTTP/HTTPS traffic to reach applications running inside Kubernetes.

Ingress provides:

- Host-based routing
- Path-based routing
- SSL/TLS termination
- Centralized entry point for multiple services

---

## 5. Network Policies

Network Policies control traffic between Pods.

Benefits include:

- Restricting Pod communication
- Improving cluster security
- Implementing Zero Trust networking

---

## 6. Container Network Interface (CNI)

Kubernetes networking is implemented through Container Network Interface (CNI) plugins.

Common CNI plugins include:

- Calico
- Flannel
- Cilium
- Weave Net

These plugins assign Pod IP addresses and manage network communication across the cluster.

---

# Project

## Pod Networking Using a Multi-Container Pod

This project demonstrates how multiple containers inside the same Pod share the same network namespace and communicate using `localhost`.

---

# Project Tasks

## Task 1 — Create a Multi-Container Pod

Create a YAML file:

```bash
touch multi-container-pod.yaml
```

Open the file:

```bash
vim multi-container-pod.yaml
```

Add the following configuration:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod

spec:
  containers:

  - name: container-1
    image: nginx:alpine
    ports:
      - containerPort: 80

  - name: container-2
    image: busybox
    command:
      - /bin/sh
      - -c
      - |
        while true
        do
          echo "Hello from Container 2 - $(date)" > /usr/share/nginx/html/index.html
          sleep 10
        done
    volumeMounts:
      - name: nginx-data
        mountPath: /usr/share/nginx/html

  volumes:
    - name: nginx-data
      emptyDir: {}
```

---

## Explanation of the YAML

### apiVersion

Specifies the Kubernetes API version.

```yaml
apiVersion: v1
```

---

### kind

Creates a Pod resource.

```yaml
kind: Pod
```

---

### metadata

Defines the Pod name.

```yaml
metadata:
  name: multi-container-pod
```

---

### spec

Defines the desired state of the Pod.

---

### Container 1

Runs an Nginx web server.

```yaml
image: nginx:alpine
```

---

### Container 2

Runs BusyBox and continuously updates the Nginx web page.

```yaml
image: busybox
```

Every 10 seconds it writes:

```
Hello from Container 2
```

into:

```
/usr/share/nginx/html/index.html
```

---

### emptyDir Volume

The `emptyDir` volume is shared between both containers.

This allows:

- BusyBox to write the HTML file.
- Nginx to serve the same file.

---

# Task 2 — Deploy the Pod

Apply the configuration:

```bash
kubectl apply -f multi-container-pod.yaml
```

Expected output:

```
pod/multi-container-pod created
```

---

# Task 3 — Verify the Pod

Check the Pod status:

```bash
kubectl get pods
```

Example:

```
NAME                  READY   STATUS    RESTARTS   AGE
multi-container-pod   2/2     Running   0          30s
```

Both containers should be running.

---

# Task 4 — View Logs

Logs from the Nginx container:

```bash
kubectl logs multi-container-pod -c container-1
```

Logs from the BusyBox container:

```bash
kubectl logs multi-container-pod -c container-2
```

The BusyBox logs should show the repeated execution of the update loop.

---

# Task 5 — Access the BusyBox Container

Open a shell inside BusyBox:

```bash
kubectl exec -it multi-container-pod -c container-2 -- /bin/sh
```

Navigate to the shared directory:

```bash
cd /usr/share/nginx/html
```

Display the file contents:

```bash
cat index.html
```

Example output:

```
Hello from Container 2
```

---

# Task 6 — Verify Localhost Communication

Still inside the BusyBox container, install wget if necessary (BusyBox usually includes it).

Run:

```bash
wget -qO- http://localhost
```

or

```bash
wget http://localhost
```

You should receive the HTML page served by the Nginx container.

Example:

```
Hello from Container 2
```

This demonstrates that both containers communicate using:

```
localhost
```

because they share the same network namespace.

---

# How It Works

```
                +----------------------------------+
                |          Kubernetes Pod          |
                |                                  |
                |  +----------------------------+  |
                |  |     Nginx Container        |  |
                |  |   Serving index.html       |  |
                |  +-------------▲--------------+  |
                |                │                 |
                |      Shared emptyDir Volume      |
                |                │                 |
                |  +-------------▼--------------+  |
                |  |    BusyBox Container       |  |
                |  | Updates index.html         |  |
                |  +----------------------------+  |
                |                                  |
                +----------------------------------+
```

Both containers:

- Share the same IP address
- Share localhost
- Share the mounted volume

---

# Verification Commands

View Pods:

```bash
kubectl get pods
```

Describe Pod:

```bash
kubectl describe pod multi-container-pod
```

View logs:

```bash
kubectl logs multi-container-pod -c container-1
```

```bash
kubectl logs multi-container-pod -c container-2
```

Execute commands inside BusyBox:

```bash
kubectl exec -it multi-container-pod -c container-2 -- /bin/sh
```

---

# Expected Results

After completing this project, you should have:

- A multi-container Pod running successfully.
- Nginx serving a webpage.
- BusyBox continuously updating the webpage.
- Successful communication between containers using `localhost`.
- A practical understanding of Pod networking in Kubernetes.

---

# Observations

### Key Learnings

- Every Pod receives its own IP address.
- Containers inside the same Pod share the same network namespace.
- Containers communicate using `localhost`.
- Shared volumes enable data exchange between containers.
- Multi-container Pods are useful for tightly coupled applications.

---

# Troubleshooting

### Pod stuck in Pending

Check:

```bash
kubectl describe pod multi-container-pod
```

---

### Image Pull Error

Verify internet connectivity and image names:

```yaml
nginx:alpine
busybox
```

---

### Cannot Access localhost

Ensure both containers are running:

```bash
kubectl get pods
```

---

### Volume Not Shared

Verify that both containers mount the same `emptyDir` volume.

---

# Cleanup

Delete the Pod:

```bash
kubectl delete -f multi-container-pod.yaml
```

or

```bash
kubectl delete pod multi-container-pod
```

---

# Conclusion

In this project, you explored Kubernetes networking fundamentals by deploying a multi-container Pod. You learned how containers within the same Pod share a network namespace, communicate using `localhost`, and exchange data through a shared volume. These concepts form the foundation for designing containerized applications that require close inter-container communication within Kubernetes.

---
```
