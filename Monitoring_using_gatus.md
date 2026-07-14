# Mini Project: Configuring Uptime Monitoring Using Gatus

## Introduction

Ensuring that applications, APIs, and websites remain available is essential for delivering reliable services and maintaining user trust. In this mini project, I configured **Gatus**, an open-source uptime monitoring solution, to monitor the availability and health of web services.

Gatus periodically checks configured endpoints, records uptime statistics, and provides a web dashboard for visualizing service health. It also supports alert notifications through services such as Slack, email, Discord, Microsoft Teams, and other notification platforms.

This project demonstrates how to deploy Gatus using Docker, configure endpoint monitoring, test monitoring behavior, set up alerting, and customize the monitoring dashboard.

---

# Project Objectives

The objectives of this project were to:

- Understand the purpose of Gatus for uptime monitoring.
- Deploy Gatus using Docker.
- Configure Gatus to monitor one or more HTTP endpoints.
- Test endpoint monitoring using live websites.
- Configure downtime alerts.
- Explore and customize the Gatus dashboard.

---

# Prerequisites

Before beginning the project, I ensured the following requirements were met:

- Docker installed
- Internet connection
- Basic knowledge of HTTP and REST APIs
- Basic understanding of YAML configuration files
- A text editor (Visual Studio Code)

I verified that Docker was installed by running:

```bash
docker --version
```

---

# What is Gatus?

Gatus is a lightweight uptime monitoring application that continuously checks the availability and response status of websites, APIs, and other HTTP services.

Some of its key features include:

- Endpoint monitoring
- Response status validation
- Response time monitoring
- Web dashboard
- Alert notifications
- Docker support
- YAML-based configuration

Gatus provides an easy way to monitor services without requiring complex infrastructure.

---

# Task 1: Installing and Running Gatus

## Pull the Gatus Docker Image

I downloaded the official Gatus Docker image.

```bash
docker pull twinproduction/gatus
```

Docker successfully downloaded the latest image from Docker Hub.

---

## Create the Configuration Directory

Next, I created a directory to store the Gatus configuration.

```bash
mkdir gatus
```

I then navigated into the project directory.

```bash
cd gatus
```

Inside the project folder, I created a configuration directory.

```bash
mkdir config
```

---

## Start the Gatus Container

I launched the Gatus container using Docker.

```bash
docker run -d \
-p 8080:8080 \
--name gatus \
-v $(pwd)/config:/config \
twinproduction/gatus
```

This command:

- Runs Gatus in detached mode
- Maps port **8080**
- Mounts the local configuration folder into the container

After deployment, the container started successfully.

---

## Accessing the Dashboard

Once the container was running, I accessed the Gatus dashboard using:

```text
http://localhost:8080
```

The dashboard displayed the monitoring interface where configured endpoints would later appear.

---

# Task 2: Creating a Configuration File

Inside the configuration directory, I created a YAML configuration file.

```text
config/config.yaml
```

The configuration defines the endpoints that Gatus monitors.

Example configuration:

```yaml
endpoints:

  - name: Google

    url: https://www.google.com

    interval: 1m

    conditions:

      - "[STATUS] == 200"
```

This configuration instructs Gatus to:

- Monitor Google's homepage
- Perform a health check every minute
- Consider the endpoint healthy only when the HTTP response code is **200**

---

## Restarting Gatus

After saving the configuration file, I restarted the Docker container.

```bash
docker restart gatus
```

Gatus automatically loaded the updated configuration.

The monitored endpoint immediately appeared on the dashboard.

---

# Task 3: Monitoring Multiple Endpoints

To monitor additional services, I added another endpoint.

Example:

```yaml
- name: GitHub

  url: https://github.com

  interval: 1m

  conditions:

    - "[STATUS] == 200"
```

After updating the configuration, I restarted Gatus again.

```bash
docker restart gatus
```

The dashboard now displayed multiple monitored endpoints.

Each endpoint included:

- Current status
- Response time
- Uptime percentage
- Health history

---

# Simulating a Failure

To verify that Gatus correctly detected downtime, I added a non-existent endpoint.

Example:

```yaml
- name: Broken Service

  url: https://nonexistent-example-site.xyz

  interval: 30s

  conditions:

    - "[STATUS] == 200"
```

After restarting Gatus, the dashboard reported the endpoint as **Down**.

This confirmed that Gatus successfully detected service failures.

---

# Task 4: Configuring Alerts

One of Gatus' most useful features is automated alerting.

Gatus supports several notification providers, including:

- Slack
- Email
- Discord
- Microsoft Teams
- PagerDuty
- Telegram
- Webhooks

For this project, I configured Slack notifications.

Example configuration:

```yaml
alerting:

  slack:

    webhook-url: https://hooks.slack.com/services/XXXXXXXX

endpoints:

  - name: Google

    url: https://www.google.com

    interval: 1m

    conditions:

      - "[STATUS] == 200"

    alerts:

      - type: slack
```

Whenever an endpoint becomes unavailable, Gatus automatically sends a notification to the configured Slack channel.

To verify the configuration, I temporarily monitored an unavailable endpoint and confirmed that the alert was triggered.

---

# Task 5: Exploring the Dashboard

The Gatus dashboard provides a clear overview of monitored services.

The dashboard displays:

- Service availability
- Current health status
- Historical uptime
- Response times
- Success and failure history

This makes it easy to quickly identify unhealthy services.

---

# Customizing the Dashboard

Gatus allows several dashboard customizations.

Examples include:

- Changing themes
- Adding a custom logo
- Modifying page titles
- Adjusting monitoring intervals
- Configuring response conditions

Monitoring intervals can be optimized depending on application requirements.

Example:

```yaml
interval: 30s
```

or

```yaml
interval: 5m
```

More frequent checks provide faster detection but generate additional requests.

---

# Verifying the Monitoring Results

After configuring multiple endpoints, I confirmed that:

- Healthy endpoints displayed a green status.
- Unavailable endpoints displayed a red status.
- Response times were recorded correctly.
- Historical uptime percentages were calculated automatically.
- The dashboard updated continuously as endpoint status changed.

---

# Observations

During the project, I observed that:

- Docker makes deploying Gatus extremely simple.
- YAML configuration is easy to understand and modify.
- Gatus detects endpoint failures quickly.
- Multiple endpoints can be monitored simultaneously.
- The web dashboard provides an intuitive visualization of service health.
- Built-in alerting allows rapid notification of downtime events.

---

# Challenges Encountered

Some challenges encountered during the project included:

### Configuration Changes Not Applying

After editing the configuration file, changes did not immediately appear.

**Solution**

Restarted the Docker container.

```bash
docker restart gatus
```

---

### Invalid YAML Syntax

Incorrect indentation prevented Gatus from loading the configuration.

**Solution**

Verified YAML formatting and corrected the indentation.

---

### Endpoint Always Showing Down

Some monitored endpoints consistently failed.

**Solution**

Verified:

- Correct URL
- Internet connectivity
- Expected HTTP status code

---

### Slack Notifications Not Working

Notifications were not delivered initially.

**Solution**

Verified that the Slack webhook URL was valid and correctly configured.

---

# Key Concepts Learned

Through this project, I learned how to:

- Deploy Gatus using Docker.
- Configure endpoint monitoring.
- Monitor multiple HTTP services.
- Validate response status codes.
- Detect service failures automatically.
- Configure Slack notifications.
- Customize the Gatus dashboard.
- Troubleshoot monitoring configurations.
- Monitor service uptime using open-source tools.

---

# Conclusion

This mini project provided practical experience configuring **Gatus** as an uptime monitoring solution. I successfully deployed Gatus using Docker, configured multiple endpoint health checks, monitored website availability, simulated service failures, configured alert notifications, and explored the built-in monitoring dashboard.

Overall, this project demonstrated how lightweight monitoring tools like Gatus can improve service reliability by continuously tracking application availability and providing immediate alerts whenever monitored services become unavailable.
