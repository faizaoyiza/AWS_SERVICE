# Working with Kubernetes Resources

# Introduction to YAML

A Kubernetes YAML file is a configuration file written in YAML syntax that defines Kubernetes resources such as Pods, Deployments, Services, ConfigMaps, and more. Kubernetes follows a **declarative approach**, meaning you describe the desired state of your application, and Kubernetes works to maintain that state automatically.

YAML (YAML Ain't Markup Language) is a human-readable data serialization language widely used for configuration files because of its simple syntax and readability.

---

# Objectives

By completing this project, you will learn how to:

- Understand the fundamentals of YAML syntax.
- Create Kubernetes resources using YAML files.
- Deploy an application to a Kubernetes cluster.
- Expose the application using a Kubernetes Service.
- Verify and access the deployed application.
- Understand the relationship between Deployments and Services.

---

# Prerequisites

Before starting, ensure you have:

- A working Kubernetes cluster (Minikube, Kind, EKS, AKS, or GKE)
- `kubectl` installed and configured
- Minikube installed (for local testing)
- Docker installed (if using the Docker driver)
- Basic understanding of containers and Kubernetes

---

# Understanding YAML

YAML uses indentation (spaces only) to represent data hierarchy.

Example:

```yaml
name: myapp
version: 1.0
enabled: true
```

---

# YAML Data Types

## 1. Strings

```yaml
name: Akinwale Idowu
```

---

## 2. Numbers

```yaml
age: 20
```

---

## 3. Boolean Values

```yaml
is_student: true
```

Possible values:

```yaml
true
false
yes
no
on
off
```

---

# Collections

## Lists (Arrays)

```yaml
fruits:
  - Apple
  - Orange
  - Mango
```

---

## Maps (Key-Value Pairs)

```yaml
person:
  name: John
  age: 25
  city: Lagos
```

---

# Nested Structures

YAML supports nested objects.

```yaml
student:
  name: Mary
  age: 22
  courses:
    - Kubernetes
    - Docker
    - Terraform
```

---

# Comments

Comments begin with `#`.

```yaml
# This is a comment

name: Kubernetes
```

---

# Multiline Strings

Using `|`

```yaml
description: |
  This is line one.
  This is line two.
  This is line three.
```

Using `>`

```yaml
description: >
  This sentence
  will appear
  on one line.
```

---

# Anchors and Aliases

Anchors allow values to be reused.

```yaml
first: &username admin

second: *username
```

Output:

```
first = admin
second = admin
```

---

# Deploying Applications in Kubernetes

Deployments are the standard Kubernetes resource used to deploy and manage applications.

A Deployment automatically:

- Creates Pods
- Replaces failed Pods
- Performs rolling updates
- Scales applications
- Maintains the desired number of replicas

---

# Kubernetes Services

A Service provides a stable network endpoint for Pods.

Without a Service:

- Pod IP addresses change when Pods restart.
- Applications cannot reliably communicate.

Services solve this problem.

---

# Types of Kubernetes Services

## ClusterIP

- Default Service type
- Accessible only inside the cluster

Example:

```yaml
type: ClusterIP
```

---

## NodePort

Exposes the application through a port on every Kubernetes node.

Example:

```yaml
type: NodePort
```

---

## LoadBalancer

Creates a cloud provider load balancer.

Common on:

- AWS
- Azure
- Google Cloud

Example:

```yaml
type: LoadBalancer
```

---

# Project 1 — Deploying an Application with kubectl

## Step 1 — Create a Deployment

Run:

```bash
kubectl create deployment hello-minikube \
--image=kicbase/echo-server:1.0
```

Expected output:

```
deployment.apps/hello-minikube created
```

---

## Step 2 — Start Minikube (If Required)

If you receive connection errors:

```bash
minikube start --driver=docker
```

Verify:

```bash
kubectl get nodes
```

---

## Step 3 — Expose the Deployment

Create a NodePort Service.

```bash
kubectl expose deployment hello-minikube \
--type=NodePort \
--port=8080
```

---

## Step 4 — Verify the Service

```bash
kubectl get services
```

Example output:

```
NAME               TYPE       CLUSTER-IP
hello-minikube     NodePort   10.96.x.x
```

---

## Step 5 — Access the Application

Open it automatically:

```bash
minikube service hello-minikube
```

Or retrieve the URL:

```bash
minikube service hello-minikube --url
```

---

# Project 2 — Deploying an Application with YAML

Using YAML provides a repeatable and version-controlled way of managing Kubernetes resources.

---

# Step 1 — Create a Project Directory

```bash
mkdir my-nginx-yaml
```

Enter the directory:

```bash
cd my-nginx-yaml
```

---

# Step 2 — Create Deployment YAML

Create the file:

```bash
touch nginx-deployment.yaml
```

Open it:

```bash
vim nginx-deployment.yaml
```

Paste:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: my-nginx-deployment

spec:
  replicas: 1

  selector:
    matchLabels:
      app: my-nginx

  template:
    metadata:
      labels:
        app: my-nginx

    spec:
      containers:
      - name: my-nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

> **Note:** Replace `nginx:latest` with your own Docker image if desired.

---

# Deployment YAML Explanation

### apiVersion

Defines the Kubernetes API version.

```yaml
apiVersion: apps/v1
```

---

### kind

Creates a Deployment resource.

```yaml
kind: Deployment
```

---

### metadata

Names the Deployment.

```yaml
metadata:
  name: my-nginx-deployment
```

---

### replicas

Specifies the desired number of Pods.

```yaml
replicas: 1
```

---

### selector

Matches Pods managed by the Deployment.

```yaml
selector:
  matchLabels:
    app: my-nginx
```

---

### template

Defines the Pod template.

---

### containers

Defines container settings.

```yaml
image: nginx:latest
```

---

### containerPort

Exposes port 80 inside the Pod.

---

# Step 3 — Create Service YAML

Create:

```bash
touch nginx-service.yaml
```

Open:

```bash
vim nginx-service.yaml
```

Paste:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: my-nginx-service

spec:
  selector:
    app: my-nginx

  ports:
    - protocol: TCP
      port: 80
      targetPort: 80

  type: NodePort
```

---

# Service YAML Explanation

### apiVersion

Defines the Service API version.

```yaml
apiVersion: v1
```

---

### kind

Creates a Kubernetes Service.

```yaml
kind: Service
```

---

### selector

Routes traffic to Pods matching:

```yaml
app: my-nginx
```

---

### ports

Maps external traffic to the Pod.

```yaml
port: 80
targetPort: 80
```

---

### type

Creates a NodePort Service.

```yaml
type: NodePort
```

---

# Step 4 — Deploy the Resources

Deploy the Deployment:

```bash
kubectl apply -f nginx-deployment.yaml
```

Deploy the Service:

```bash
kubectl apply -f nginx-service.yaml
```

Expected output:

```
deployment.apps/my-nginx-deployment created

service/my-nginx-service created
```

---

# Step 5 — Verify Deployment

Check Deployments:

```bash
kubectl get deployments
```

Check Pods:

```bash
kubectl get pods
```

Check Services:

```bash
kubectl get services
```

Example:

```
NAME                 READY

my-nginx-deployment  1/1

NAME

my-nginx-service
```

---

# Step 6 — Access the Application

Retrieve the URL:

```bash
minikube service my-nginx-service --url
```

Example:

```
http://192.168.49.2:31000
```

Open the URL in your browser.

You should see the default Nginx welcome page.

---

# Useful kubectl Commands

List Pods:

```bash
kubectl get pods
```

List Deployments:

```bash
kubectl get deployments
```

List Services:

```bash
kubectl get services
```

Describe Deployment:

```bash
kubectl describe deployment my-nginx-deployment
```

Describe Service:

```bash
kubectl describe service my-nginx-service
```

View Pod Logs:

```bash
kubectl logs <pod-name>
```

Delete Deployment:

```bash
kubectl delete deployment my-nginx-deployment
```

Delete Service:

```bash
kubectl delete service my-nginx-service
```

Delete using YAML:

```bash
kubectl delete -f nginx-deployment.yaml
kubectl delete -f nginx-service.yaml
```

---

# Expected Results

After completing this project, you should have:

- A running Kubernetes Deployment.
- One or more Pods created automatically.
- A NodePort Service exposing the application.
- Successful browser access to the Nginx web server.
- Practical experience creating Kubernetes resources using YAML.

---

# Observations

## Key Learnings

- YAML is the preferred format for Kubernetes resource definitions.
- Deployments manage application lifecycle and scaling.
- Services provide stable network access to Pods.
- Labels and selectors connect Services to Pods.
- Kubernetes automatically maintains the desired application state.

---

# Troubleshooting

## Minikube Not Running

Start Minikube:

```bash
minikube start --driver=docker
```

---

## Pods Stuck in Pending

Check Pod details:

```bash
kubectl describe pod <pod-name>
```

---

## Image Pull Error

Verify the Docker image exists:

```yaml
image: nginx:latest
```

or replace it with your own valid image.

---

## Service Not Accessible

Verify:

```bash
kubectl get services
```

Retrieve the correct URL:

```bash
minikube service my-nginx-service --url
```

---

## Deployment Not Creating Pods

Describe the Deployment:

```bash
kubectl describe deployment my-nginx-deployment
```

---

# Cleanup

Delete the resources:

```bash
kubectl delete -f nginx-service.yaml
```

```bash
kubectl delete -f nginx-deployment.yaml
```

Or:

```bash
kubectl delete deployment my-nginx-deployment
```

```bash
kubectl delete service my-nginx-service
```

---

# Conclusion

In this project, you learned the fundamentals of YAML and how Kubernetes uses declarative configuration files to manage resources. You deployed an Nginx application using both imperative (`kubectl create`) and declarative (`kubectl apply`) approaches, exposed it with a NodePort Service, and verified successful deployment. These foundational skills prepare you for deploying more complex applications and managing Kubernetes infrastructure using Infrastructure as Code (IaC) principles.

---
