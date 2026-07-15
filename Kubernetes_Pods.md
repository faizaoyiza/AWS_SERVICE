# Working with Kubernetes Pods

## Introduction

Pods are the smallest deployable units in Kubernetes. Every application running in a Kubernetes cluster is deployed inside one or more Pods. A Pod can contain one or multiple containers that work together as a single unit.

Understanding Pods is one of the most fundamental concepts in Kubernetes because all higher-level resources such as Deployments, ReplicaSets, StatefulSets, and Jobs ultimately manage Pods.

This project introduces Kubernetes Pods, explains how they work, and demonstrates how to create, inspect, manage, and delete them using `kubectl`.

---

# Objectives

By completing this project, you will learn how to:

- Understand what a Kubernetes Pod is.
- Learn the purpose of Pods in Kubernetes.
- Understand the relationship between Pods and Containers.
- Create and manage Pods using `kubectl`.
- Inspect Pod details.
- Delete Pods.
- Deploy containers inside Pods using YAML.

---

# Prerequisites

Before starting, ensure you have:

- A working Kubernetes cluster (Minikube, Kind, EKS, AKS, or GKE)
- `kubectl` installed and configured
- Docker installed (if using Minikube with the Docker driver)
- Basic knowledge of containers and Docker

---

# What is a Kubernetes Pod?

A **Pod** is the smallest deployable object in Kubernetes.

A Pod serves as a wrapper around one or more containers that need to work together.

Containers inside the same Pod:

- Share the same network namespace.
- Share the same IP address.
- Share storage volumes.
- Can communicate using `localhost`.

Pods provide an execution environment for applications while Kubernetes manages their lifecycle.

---

# Purpose of Pods

Pods are responsible for:

- Running application containers.
- Sharing networking between containers.
- Sharing storage volumes.
- Grouping tightly coupled containers.
- Providing the basic unit of deployment and scaling.

---

# Pod Architecture

```
+--------------------------------------+
|              Kubernetes Pod          |
|                                      |
|  +------------------------------+    |
|  |        Container 1           |    |
|  +------------------------------+    |
|                                      |
|  +------------------------------+    |
|  |        Container 2           |    |
|  +------------------------------+    |
|                                      |
| Shared Network (localhost)           |
| Shared Storage Volumes               |
+--------------------------------------+
```

---

# Pods in Minikube

Minikube is a lightweight Kubernetes environment designed for local development and learning.

Pods in Minikube behave exactly the same as Pods running in production Kubernetes clusters.

You can:

- Deploy Pods
- Scale applications
- Inspect Pod status
- Delete Pods
- View Pod logs

using the `kubectl` command-line tool.

---

# What is kubectl?

`kubectl` is the official Kubernetes command-line interface (CLI).

It allows you to:

- Deploy applications
- Create Pods
- Inspect cluster resources
- View logs
- Scale workloads
- Delete resources
- Troubleshoot Kubernetes applications

---

# Project Tasks

## Task 1 — List All Pods

To display every Pod running in all namespaces, execute:

```bash
kubectl get pods -A
```

or

```bash
kubectl get po -A
```

Example output:

```
NAMESPACE     NAME                       READY   STATUS
default       nginx-pod                  1/1     Running
kube-system   coredns-xxxx               1/1     Running
```

---

## Task 2 — List Pods in the Default Namespace

```bash
kubectl get pods
```

Example:

```
NAME          READY   STATUS
nginx-pod     1/1     Running
```

---

## Task 3 — Inspect a Pod

To view detailed information about a Pod:

```bash
kubectl describe pod <pod-name>
```

Example:

```bash
kubectl describe pod nginx-pod
```

The output includes:

- Pod name
- Namespace
- Labels
- Node assignment
- Container image
- Resource limits
- Events
- IP address
- Mounted volumes

This command is extremely useful for troubleshooting.

---

## Task 4 — View Pod Logs

Display logs from a Pod:

```bash
kubectl logs <pod-name>
```

Example:

```bash
kubectl logs nginx-pod
```

If multiple containers exist:

```bash
kubectl logs <pod-name> -c <container-name>
```

---

## Task 5 — Delete a Pod

Delete an existing Pod:

```bash
kubectl delete pod <pod-name>
```

Example:

```bash
kubectl delete pod nginx-pod
```

Expected output:

```
pod "nginx-pod" deleted
```

---

# Containers in Kubernetes

## Definition

A container is a lightweight, portable package that contains:

- Application code
- Runtime
- Libraries
- Dependencies
- System tools

Containers provide consistent execution across different environments.

---

# Relationship Between Pods and Containers

Containers do not run directly inside Kubernetes.

Instead:

```
Cluster
   │
   ▼
Node
   │
   ▼
Pod
   │
   ├── Container A
   └── Container B
```

Pods act as the execution environment for one or more containers.

---

# Why Multiple Containers?

Sometimes an application requires helper containers.

Examples include:

- Logging sidecars
- Monitoring agents
- Reverse proxies
- Backup containers

All these containers share:

- Network
- Storage
- Lifecycle

---

# Deploying Containers Inside a Pod

Containers are defined inside a Pod YAML file.

Create a file:

```bash
touch nginx-pod.yaml
```

Open it:

```bash
vim nginx-pod.yaml
```

Paste the following configuration:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx
      image: nginx:latest

      ports:
        - containerPort: 80
```

---

# YAML Explanation

## apiVersion

Specifies the Kubernetes API version.

```yaml
apiVersion: v1
```

---

## kind

Creates a Pod resource.

```yaml
kind: Pod
```

---

## metadata

Assigns the Pod name.

```yaml
metadata:
  name: nginx-pod
```

---

## spec

Defines the desired Pod configuration.

---

## containers

Specifies one or more containers.

```yaml
containers:
```

---

## image

Defines the container image.

```yaml
image: nginx:latest
```

---

## containerPort

Exposes port 80 inside the container.

```yaml
containerPort: 80
```

---

# Deploy the Pod

Create the Pod:

```bash
kubectl apply -f nginx-pod.yaml
```

Expected output:

```
pod/nginx-pod created
```

---

# Verify the Pod

Check Pod status:

```bash
kubectl get pods
```

Example:

```
NAME        READY   STATUS
nginx-pod   1/1     Running
```

---

# Describe the Pod

```bash
kubectl describe pod nginx-pod
```

---

# View Logs

```bash
kubectl logs nginx-pod
```

---

# Execute Commands Inside the Pod

Open a shell:

```bash
kubectl exec -it nginx-pod -- /bin/bash
```

If Bash is unavailable:

```bash
kubectl exec -it nginx-pod -- /bin/sh
```

---

# Common kubectl Commands

List Pods:

```bash
kubectl get pods
```

List all Pods:

```bash
kubectl get pods -A
```

Describe a Pod:

```bash
kubectl describe pod nginx-pod
```

View logs:

```bash
kubectl logs nginx-pod
```

Execute inside a Pod:

```bash
kubectl exec -it nginx-pod -- /bin/sh
```

Delete Pod:

```bash
kubectl delete pod nginx-pod
```

Delete using YAML:

```bash
kubectl delete -f nginx-pod.yaml
```

---

# Expected Results

After completing this project, you should have:

- Successfully created a Kubernetes Pod.
- Understood the relationship between Pods and Containers.
- Viewed Pod details.
- Examined Pod logs.
- Executed commands inside the running container.
- Deleted a Pod successfully.

---

# Observations

## Key Learnings

- Pods are the smallest deployable units in Kubernetes.
- Every Pod receives its own IP address.
- Containers inside the same Pod share networking and storage.
- `kubectl` is the primary tool for managing Kubernetes resources.
- Pods can be created imperatively using commands or declaratively using YAML.

---

# Troubleshooting

## Pod Stuck in Pending

Check:

```bash
kubectl describe pod nginx-pod
```

Possible causes:

- Insufficient resources
- Image pull issues
- Scheduling problems

---

## ImagePullBackOff

Verify the image exists:

```yaml
image: nginx:latest
```

---

## Pod CrashLoopBackOff

View logs:

```bash
kubectl logs nginx-pod
```

---

## Cannot Execute Shell

Some minimal images do not include Bash.

Use:

```bash
/bin/sh
```

instead.

---

# Cleanup

Delete the Pod:

```bash
kubectl delete pod nginx-pod
```

or

```bash
kubectl delete -f nginx-pod.yaml
```

---

# Conclusion

In this project, you learned the fundamentals of Kubernetes Pods and their role as the smallest deployable units in a cluster. You explored how Pods encapsulate one or more containers, share networking and storage resources, and provide a consistent execution environment for applications. By using `kubectl`, you created, inspected, managed, and deleted Pods, laying the foundation for working with higher-level Kubernetes resources such as Deployments, ReplicaSets, and StatefulSets.

---
