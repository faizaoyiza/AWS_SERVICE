# Mini Project: Setting Up Prometheus Node Exporter on Kubernetes

## Introduction

Monitoring is a critical component of Kubernetes administration because it provides visibility into the health, performance, and resource utilization of cluster nodes. **Prometheus** is one of the most popular open-source monitoring systems, while **Node Exporter** is a lightweight exporter that collects hardware and operating system metrics from Linux hosts.

In this mini project, I deployed **Prometheus** and **Node Exporter** on a Kubernetes cluster running **Minikube**. Node Exporter was deployed as a **DaemonSet**, ensuring that one monitoring pod runs on every node in the cluster. Prometheus was then configured to scrape metrics from Node Exporter, allowing node-level performance data to be queried and visualized through the Prometheus web interface.

---

# Project Objectives

The objectives of this project were to:

- Understand the purpose of Prometheus Node Exporter.
- Install and configure a Kubernetes cluster using Minikube.
- Deploy Prometheus in Kubernetes.
- Deploy Node Exporter as a DaemonSet.
- Configure Prometheus to scrape Node Exporter metrics.
- Verify metric collection through the Prometheus UI.
- Explore common node performance metrics.

---

# Prerequisites

Before starting the project, I ensured the following requirements were met:

- Ubuntu Linux
- Docker installed
- Kubernetes CLI (`kubectl`)
- Minikube
- Internet connectivity
- Basic knowledge of Kubernetes resources

---

# Understanding Prometheus Node Exporter

Node Exporter is an official Prometheus exporter that collects system-level metrics from Linux hosts.

It exposes metrics such as:

- CPU utilization
- Memory usage
- Disk I/O
- Filesystem statistics
- Network traffic
- System load
- Boot time
- Hardware information

When deployed as a **DaemonSet**, Kubernetes automatically schedules one Node Exporter pod on every node, allowing Prometheus to collect metrics from the entire cluster.

---

# Project Architecture

```
                 +----------------------+
                 |    Prometheus UI     |
                 +----------+-----------+
                            |
                            |
                     Scrapes Metrics
                            |
        +-------------------+-------------------+
        |                                       |
+-------+--------+                     +--------+-------+
| Node Exporter  |                     | Node Exporter  |
| (Node 1)       |                     | (Node 2)       |
+-------+--------+                     +--------+-------+
        |                                       |
        +-------------------+-------------------+
                            |
                     Kubernetes Cluster
                      (Minikube)
```

---

# Part 1: Install Kubernetes (Minikube)

## Step 1: Install Required Dependencies

First, I updated the package repository.

```bash
sudo apt update
```

Next, I installed the required dependencies.

```bash
sudo apt install -y curl wget apt-transport-https ca-certificates conntrack
```

---

## Step 2: Install Docker

Since Minikube uses Docker as its container runtime, I installed Docker.

```bash
sudo apt install -y docker.io
```

Enable Docker at boot.

```bash
sudo systemctl enable docker
```

Start the Docker service.

```bash
sudo systemctl start docker
```

Grant the current user permission to use Docker.

```bash
sudo usermod -aG docker $USER
```

After this step, I logged out and logged back in for the permission changes to take effect.

---

## Step 3: Install Minikube

Download Minikube.

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

Install it.

```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Verify the installation.

```bash
minikube version
```

---

## Step 4: Install kubectl

Download kubectl.

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Install kubectl.

```bash
sudo install kubectl /usr/local/bin/
```

Verify the installation.

```bash
kubectl version --client
```

---

## Step 5: Start Minikube

Start the Kubernetes cluster.

```bash
minikube start --driver=docker --cpus=4 --memory=4096
```

Verify that the node is running.

```bash
kubectl get nodes
```

Expected output:

```
NAME        STATUS   ROLES           AGE
minikube    Ready    control-plane   xxm
```

This confirmed that the Kubernetes cluster was operational.

---

# Part 2: Install Prometheus

## Create a Monitoring Namespace

To keep monitoring resources organized, I created a dedicated namespace.

```bash
kubectl create namespace monitoring
```

---

## Deploy Prometheus

I created the deployment file.

```bash
vim prometheus-deploy.yaml
```

The deployment YAML included:

- Prometheus Deployment
- ConfigMap
- Service
- Scrape configuration

After saving the file, I deployed Prometheus.

```bash
kubectl apply -f prometheus-deploy.yaml
```

---

## Verify the Deployment

I confirmed that the Prometheus pod was running.

```bash
kubectl get pods -n monitoring
```

Expected output:

```
NAME                          READY   STATUS
prometheus-xxxxxxxxx-xxxxx    1/1     Running
```

---

# Part 3: Deploy Node Exporter

## Create the DaemonSet

I created the Node Exporter DaemonSet.

```bash
vim node-exporter-daemonset.yaml
```

The DaemonSet ensures that one Node Exporter instance runs on every Kubernetes node.

After saving the file, I deployed it.

```bash
kubectl apply -f node-exporter-daemonset.yaml
```

---

## Verify the DaemonSet

I confirmed that the DaemonSet was running.

```bash
kubectl get daemonset -n monitoring
```

Expected output:

```
NAME             DESIRED   CURRENT   READY
node-exporter    1         1         1
```

---

# Create the Node Exporter Service

Next, I created a Kubernetes Service so Prometheus could scrape the Node Exporter metrics.

```bash
vim node-exporter-service.yaml
```

Deploy the service.

```bash
kubectl apply -f node-exporter-service.yaml
```

Verify the service.

```bash
kubectl get svc -n monitoring
```

At this stage, Prometheus could successfully discover the Node Exporter endpoint.

---

# Part 4: Configure Prometheus Scraping

Inside the Prometheus configuration, I added a new scrape job.

Example configuration:

```yaml
scrape_configs:

  - job_name: node-exporter

    static_configs:

      - targets:

          - node-exporter.monitoring.svc.cluster.local:9100
```

This instructs Prometheus to collect metrics from the Node Exporter service every scrape interval.

After updating the configuration, I restarted the Prometheus deployment.

```bash
kubectl rollout restart deployment prometheus -n monitoring
```

---

# Part 5: Access the Prometheus Dashboard

To access Prometheus locally, I used port forwarding.

```bash
kubectl port-forward svc/prometheus 9090:9090 -n monitoring
```

Open a browser and navigate to:

```
http://localhost:9090
```

The Prometheus dashboard loaded successfully.

---

# Part 6: Verify Node Exporter Metrics

Inside the Prometheus query interface, I tested several Node Exporter metrics.

## CPU Usage

```text
node_cpu_seconds_total
```

This metric displays CPU time spent in different operating modes.

---

## Memory Usage

```text
node_memory_MemAvailable_bytes
```

This metric reports the available system memory.

---

## Disk Space

```text
node_filesystem_avail_bytes
```

This metric shows the available storage space on mounted filesystems.

---

## Network Traffic

```text
rate(node_network_receive_bytes_total[5m])
```

This metric calculates the average network receive rate over the previous five minutes.

---

# Observations

During the project, I observed that:

- Minikube provided a simple Kubernetes environment for testing.
- Node Exporter automatically collected node-level metrics.
- Deploying Node Exporter as a DaemonSet ensured every node was monitored.
- Prometheus successfully scraped metrics after the scrape configuration was added.
- The Prometheus UI provided an easy interface for querying system metrics.
- Metrics updated continuously as system activity changed.

---

# Challenges Encountered

## Docker Permission Errors

Initially, Docker commands failed because the current user lacked permission.

**Solution**

Added the current user to the Docker group.

```bash
sudo usermod -aG docker $USER
```

Logged out and logged back in.

---

## Prometheus Could Not Scrape Metrics

Initially, Prometheus did not detect Node Exporter.

**Solution**

Verified:

- Node Exporter Service
- Namespace
- Scrape configuration
- Target endpoint

---

## Pod Not Running

Some pods initially remained in the Pending state.

**Solution**

Checked cluster resources.

```bash
kubectl describe pod <pod-name>
```

Confirmed sufficient CPU and memory allocation for Minikube.

---

## Port Forwarding Failed

Port forwarding occasionally terminated unexpectedly.

**Solution**

Restarted the port-forward command.

```bash
kubectl port-forward svc/prometheus 9090:9090 -n monitoring
```

---

# Key Concepts Learned

This project provided practical experience with:

- Kubernetes monitoring
- Prometheus architecture
- Node Exporter deployment
- Kubernetes DaemonSets
- Kubernetes Services
- Prometheus scrape configuration
- Node performance metrics
- Prometheus Query Language (PromQL)
- Cluster health monitoring

---

# Conclusion

In this mini project, I successfully deployed **Prometheus** and **Node Exporter** on a Kubernetes cluster using **Minikube**. Node Exporter was configured as a DaemonSet, allowing each Kubernetes node to expose hardware and operating system metrics. Prometheus was then configured to scrape these metrics, enabling real-time monitoring through the Prometheus web interface.

The project provided hands-on experience with Kubernetes monitoring, metric collection, and Prometheus configuration. This monitoring stack can be further enhanced by integrating **Grafana** for dashboards and **Alertmanager** for automated alert notifications, creating a complete cloud-native monitoring solution.
