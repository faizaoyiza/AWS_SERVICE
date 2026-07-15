# Working with Kubernetes Nodes

## Introduction

Now that we have our Minikube cluster set up, let's explore **Kubernetes Nodes** and understand their role in a Kubernetes cluster.

---

# What Is a Node?

In Kubernetes, think of a **Node** as a dedicated worker, similar to a dependable employee in an office, responsible for executing tasks and hosting containers to ensure seamless application performance.

A **Kubernetes Node** is a physical or virtual machine that:

- Runs the Kubernetes software.
- Serves as a worker machine in the cluster.
- Hosts Pods (the smallest deployable units in Kubernetes).
- Provides compute resources such as CPU, memory, storage, and networking.

Each node in a Kubernetes cluster typically represents a single host system.

---

# Managing Nodes in Kubernetes

Minikube simplifies Kubernetes management for development and testing. Before interacting with the cluster, Minikube must be running.

---

## Start the Minikube Cluster

### On Ubuntu Server (AWS)

```bash
minikube start
```

**Screenshot Placeholder:** `StartMinikube`

### On Windows (Local Computer)

```bash
minikube start
```

**Screenshot Placeholder:** `StartMinikubeOnWindows`

### What this command does

- Starts a local Kubernetes cluster.
- Creates a single-node Minikube environment.
- Provisions the Kubernetes worker node.
- Starts all required Kubernetes components.

---

## Stop the Minikube Cluster

```bash
minikube stop
```

**Screenshot Placeholder:** `StopMinikube`

This command stops the running Minikube cluster while preserving its state for future use.

---

## Delete the Minikube Cluster

```bash
minikube delete
```

This command removes the Minikube cluster along with all associated resources.

---

# View Kubernetes Nodes

To display all nodes in the Kubernetes cluster:

```bash
kubectl get nodes
```

### On Windows

**Screenshot Placeholder:** `GetNodes`

### On Ubuntu

**Screenshot Placeholder:** `OnUbuntu`

Example output:

```text
NAME        STATUS   ROLES           AGE   VERSION
minikube    Ready    control-plane   5m    v1.xx.x
```

This command lists:

- Node name
- Status
- Assigned role
- Cluster age
- Kubernetes version

---

# Inspect a Node

To view detailed information about a specific node:

```bash
kubectl describe node <node-name>
```

Example:

```bash
kubectl describe node minikube
```

### On Windows

**Screenshot Placeholder:** `InspectNode`

### On Ubuntu Linux

**Screenshot Placeholder:** `InspectNodeUbuntu`

The output includes:

- Node capacity (CPU and memory)
- Allocated resources
- Labels
- Taints
- Running Pods
- Network configuration
- Conditions and events

This information is useful for troubleshooting and monitoring node health.

---

# Node Scaling and Maintenance

Although Minikube is generally used as a **single-node cluster**, it's still important to understand node management concepts used in production environments.

## Node Scaling

Minikube normally runs with one worker node.

In production Kubernetes clusters, additional nodes can be added to:

- Increase compute capacity
- Improve fault tolerance
- Handle higher workloads
- Support high availability

---

## Node Upgrades

Minikube allows upgrading the local Kubernetes version to match production environments.

Keeping Kubernetes versions aligned helps:

- Reduce compatibility issues
- Test new features safely
- Validate deployments before production

---

# Common kubectl Node Commands

| Command | Description |
|----------|-------------|
| `minikube start` | Starts the Minikube cluster |
| `minikube stop` | Stops the cluster while preserving state |
| `minikube delete` | Deletes the entire Minikube cluster |
| `kubectl get nodes` | Lists all cluster nodes |
| `kubectl describe node <node-name>` | Displays detailed information about a node |

---

# Key Learnings

- Kubernetes Nodes are worker machines that run application Pods.
- Minikube creates a local single-node Kubernetes cluster for development and testing.
- `kubectl get nodes` displays all available nodes.
- `kubectl describe node` provides detailed node information for monitoring and troubleshooting.
- Understanding node management prepares you for working with production Kubernetes clusters.

---

# Conclusion

In this project, we explored **Kubernetes Nodes** using Minikube. We learned how to start, stop, and delete a Minikube cluster, inspect nodes, and understand the responsibilities of Kubernetes worker nodes. Although Minikube is designed for local development with a single node, the concepts learned here directly apply to larger production Kubernetes environments, making it an excellent foundation for Kubernetes administration.

---

**End of Project**
