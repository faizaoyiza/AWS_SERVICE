# Mini Project: Monitoring a Linux Server Using Prometheus Node Exporter

## Introduction

Monitoring Linux servers is essential for maintaining system performance, availability, and reliability. **Prometheus Node Exporter** is an open-source monitoring agent that exposes hardware and operating system metrics, allowing Prometheus to collect and store performance data for analysis.

In this project, I deployed **Prometheus Node Exporter** on an AWS Linux server, configured it to run as a system service, integrated it with Prometheus, and explored the collected metrics using the Prometheus web interface. This setup provides real-time visibility into server resources such as CPU, memory, disk, and network utilization.

---

# Project Objectives

The objectives of this project were to:

- Install Prometheus Node Exporter on a Linux server.
- Configure Node Exporter as a systemd service.
- Integrate Node Exporter with Prometheus.
- Verify metric collection through the Prometheus UI.
- Query and analyze server performance metrics.
- Configure basic monitoring for Linux server health.

---

# Prerequisites

Before beginning the project, I ensured the following requirements were met:

- AWS EC2 Linux server
- SSH access to the server
- Prometheus installed and running
- Internet connectivity
- Sudo privileges
- Terminal access (Git Bash)
- Text editor (Nano or Vim)

---

# Project Architecture

```
                    +----------------------+
                    |     Prometheus       |
                    |   Metrics Database   |
                    +----------+-----------+
                               |
                         Scrapes Metrics
                               |
                     Port 9100 (HTTP)
                               |
                    +----------+-----------+
                    |   Node Exporter      |
                    |    Linux Server      |
                    +----------+-----------+
                               |
                 CPU • Memory • Disk • Network
```

---

# Task 1: Install Prometheus Node Exporter

For this project, I launched an **AWS EC2 Linux instance** that served as the monitoring target.

After connecting to the server via SSH using Git Bash, I downloaded the latest Node Exporter release.

```bash
curl -LO https://github.com/prometheus/node_exporter/releases/download/v1.10.2/node_exporter-1.10.2.linux-amd64.tar.gz
```

The download completed successfully.

---

## Extract the Archive

Next, I extracted the downloaded archive.

```bash
tar -xvf node_exporter-1.10.2.linux-amd64.tar.gz
```

This created a directory containing the Node Exporter binary.

---

## Install the Binary

I moved the executable into a directory included in the system PATH.

```bash
sudo mv node_exporter-1.10.2.linux-amd64/node_exporter /usr/local/bin/
```

To verify the installation, I checked the version.

```bash
node_exporter --version
```

The version information confirmed that Node Exporter had been installed successfully.

---

# Task 2: Configure Node Exporter as a Service

To ensure Node Exporter starts automatically whenever the server boots, I configured it as a **systemd service**.

## Create the Service File

```bash
sudo nano /etc/systemd/system/node_exporter.service
```

I added the following configuration.

```ini
[Unit]
Description=Prometheus Node Exporter
After=network.target

[Service]
User=root
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

After saving the file, I reloaded systemd.

```bash
sudo systemctl daemon-reload
```

---

## Start the Service

I started the Node Exporter service.

```bash
sudo systemctl start node_exporter
```

Then configured it to start automatically during system boot.

```bash
sudo systemctl enable node_exporter
```

---

## Verify the Service

To confirm that Node Exporter was running correctly, I checked its status.

```bash
sudo systemctl status node_exporter
```

Expected output:

```
Active: active (running)
```

This confirmed that the service was operating successfully.

---

## Verify the Metrics Endpoint

Node Exporter exposes metrics over HTTP on port **9100**.

I verified that the endpoint was accessible by opening:

```
http://<EC2-Public-IP>:9100/metrics
```

The page displayed hundreds of Prometheus metrics, confirming that Node Exporter was functioning correctly.

---

# Task 3: Configure Prometheus

Next, I configured Prometheus to scrape metrics from the Linux server.

## Open the Prometheus Configuration

```bash
sudo vim /etc/prometheus/prometheus.yml
```

Inside the **scrape_configs** section, I added a new job.

```yaml
scrape_configs:

  - job_name: "node_exporter"

    static_configs:

      - targets:

          - "<EC2-Public-IP>:9100"
```

This instructs Prometheus to collect metrics from the Node Exporter endpoint.

---

## Restart Prometheus

After saving the configuration file, I restarted Prometheus.

```bash
sudo systemctl restart prometheus
```

The updated scrape configuration was loaded successfully.

---

# Task 4: Verify Metric Collection

## Open the Prometheus Dashboard

I opened the Prometheus web interface.

```
http://<Prometheus-Server-IP>:9090
```

---

## Verify the Target

Inside Prometheus:

**Status → Targets**

The Node Exporter target appeared with the status:

```
UP
```

This confirmed that Prometheus could successfully communicate with Node Exporter.

---

## Test Queries

I tested several Node Exporter metrics using PromQL.

### CPU Usage

```text
node_cpu_seconds_total
```

This metric displays cumulative CPU usage for each processor.

---

### Memory Usage

```text
node_memory_MemAvailable_bytes
```

This metric reports the amount of available system memory.

---

### Disk Space

```text
node_filesystem_avail_bytes
```

This metric shows the available disk space for mounted filesystems.

---

### Network Traffic

```text
node_network_receive_bytes_total
```

This metric displays the total number of bytes received over the network.

---

# Task 5: Analyze Performance Metrics

Using Prometheus, I explored several useful system metrics.

## CPU Utilization

To calculate CPU usage over the previous five minutes:

```text
rate(node_cpu_seconds_total[5m])
```

This query provides a real-time view of processor utilization.

---

## Memory Availability

```text
node_memory_MemAvailable_bytes
```

This metric helped determine whether sufficient RAM remained available.

---

## Filesystem Capacity

```text
node_filesystem_avail_bytes
```

This metric reports remaining free disk space.

---

## Network Throughput

```text
rate(node_network_receive_bytes_total[5m])
```

This query measures average network traffic received during the last five minutes.

---

# Optional Alert Configuration

Prometheus also supports alert rules.

Example alert for high CPU utilization:

```yaml
groups:

- name: node_alerts

  rules:

  - alert: HighCPUUsage

    expr: rate(node_cpu_seconds_total[5m]) > 0.8

    for: 5m

    labels:

      severity: warning

    annotations:

      summary: High CPU Usage Detected
```

Alerts like this can later be integrated with **Alertmanager** to notify administrators through email, Slack, or Microsoft Teams.

---

# Observations

During this project, I observed that:

- Node Exporter installation was straightforward.
- Running Node Exporter as a systemd service ensures persistence after server reboots.
- Prometheus automatically began collecting metrics once the scrape job was configured.
- The Prometheus dashboard provided immediate access to live server metrics.
- Hundreds of operating system metrics became available without additional configuration.
- PromQL made it easy to analyze server performance.

---

# Challenges Encountered

## Node Exporter Service Failed to Start

Initially, the service failed because the binary path was incorrect.

**Solution**

Verified the installation location.

```bash
which node_exporter
```

Updated the service file accordingly.

---

## Prometheus Could Not Reach Node Exporter

Initially, the target appeared as **DOWN**.

**Solution**

Verified:

- Security Group allowed port **9100**
- Node Exporter service was running
- Correct IP address configured

---

## Metrics Endpoint Not Accessible

Opening:

```
http://<EC2-IP>:9100/metrics
```

initially failed.

**Solution**

Opened TCP port **9100** in the AWS Security Group.

---

## Prometheus Configuration Errors

After editing the configuration, Prometheus failed to restart.

**Solution**

Validated the YAML formatting and corrected indentation errors.

---

# Key Concepts Learned

This project provided practical experience with:

- Prometheus monitoring
- Node Exporter installation
- Linux system monitoring
- systemd services
- Prometheus scrape configuration
- PromQL queries
- CPU monitoring
- Memory monitoring
- Filesystem monitoring
- Network monitoring
- Infrastructure observability

---

# Conclusion

In this mini project, I successfully installed **Prometheus Node Exporter** on an AWS Linux server and configured it as a persistent system service. I integrated the exporter with Prometheus, verified metric collection, and explored real-time server performance using PromQL queries.

This monitoring solution provides comprehensive visibility into Linux server health, including CPU utilization, memory availability, disk capacity, and network traffic. The project established a solid foundation for infrastructure monitoring and can be extended by integrating **Grafana** for advanced dashboards and **Alertmanager** for automated alert notifications.
