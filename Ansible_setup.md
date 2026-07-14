# Setting Up Ansible on a Linux Server

## Introduction

Ansible is an open-source automation tool used for configuration management, application deployment, and infrastructure automation. It enables system administrators and DevOps engineers to manage multiple Linux servers efficiently using simple YAML-based playbooks and SSH.

In this project, we will install and configure Ansible on a Linux control node, establish passwordless SSH authentication with target servers, create an inventory file, and verify connectivity using Ansible ad-hoc commands.

---

# Objectives

By the end of this project, you will be able to:

- Understand what Ansible is and how it works.
- Install and configure Ansible on a Linux control node.
- Configure passwordless SSH authentication.
- Create an Ansible inventory file.
- Test connectivity between the control node and managed nodes.
- Execute basic Ansible ad-hoc commands.

---

# Prerequisites

Before beginning this project, ensure you have the following:

- Ubuntu Linux Control Node (AWS EC2)
- Ubuntu Target Node (AWS EC2)
- AWS Account
- SSH Key Pair
- Internet connectivity
- Basic Linux command-line knowledge

---

# Project Overview

This project consists of the following tasks:

1. Install Ansible
2. Configure SSH key-based authentication
3. Create an inventory file
4. Test Ansible connectivity
5. Execute Ansible ad-hoc commands

---

# Task 1 — Install Ansible on the Control Node

For this project, I used an **Ubuntu EC2 instance on AWS** as the Ansible Control Node.

## Step 1: Update the Package Repository

```bash
sudo apt update
```

**Output**

*(Insert Screenshot: aptupdate)*

---

## Step 2: Install Ansible

```bash
sudo apt install ansible -y
```

**Output**

*(Insert Screenshot: InstallAnsible)*

---

## Step 3: Verify Installation

```bash
ansible --version
```

Example output:

```text
ansible [core 2.x.x]
python version = 3.x.x
```

**Output**

*(Insert Screenshot: VerifyAnsible)*

---

# Task 2 — Configure SSH Key-Based Authentication

Generate an SSH key pair on the control node.

```bash
ssh-keygen -t rsa
```

Press **Enter** to accept the default file location and leave the passphrase empty (optional).

Alternatively, generate an Ed25519 key:

```bash
cd ~/.ssh

ls

ssh-keygen

cat id_ed25519.pub
```

Copy the public key to the target server.

Test passwordless SSH login.

```bash
ssh ubuntu@98.93.9.134
```

If successful, the server should allow login without prompting for a password.

**Screenshots**

- SSHKeygen
- keygen
- addkeytoNode
- logintoNodeServer

---

# Task 3 — Create an Ansible Inventory File

Create a working directory for Ansible.

```bash
mkdir ~/ansible

cd ~/ansible
```

**Screenshot**

*(Insert Screenshot: AnsibleFile)*

---

Create an inventory file.

```bash
vim inventory.ini
```

Add the managed host information.

```ini
[linux_servers]
target1 ansible_host=98.93.9.134 ansible_user=ubuntu
```

Save and close the file.

**Screenshots**

- inventoryfile
- AnsibleTarget

---

# Verify Inventory

Display the inventory.

```bash
ansible-inventory -i inventory.ini --list
```

This confirms that Ansible recognizes the configured host.

---

# Task 4 — Test Ansible Connectivity

Verify communication between the control node and the managed node.

```bash
ansible -i inventory.ini linux_servers -m ping
```

Expected output:

```text
target1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

A successful **pong** response confirms that Ansible can communicate with the remote server.

**Screenshot**

*(Insert Screenshot: Ansibleconnection)*

---

# Task 5 — Execute Ansible Ad-Hoc Commands

## Check System Uptime

Run the following command:

```bash
ansible -i inventory.ini linux_servers -m command -a "uptime"
```

Example output:

```text
target1 | CHANGED | rc=0 >>
14:25:15 up 2 days, 4 users, load average: 0.00, 0.01, 0.05
```

**Screenshot**

*(Insert Screenshot: AdHOCuptimecommand)*

---

## Check Disk Usage

Execute:

```bash
ansible -i inventory.ini linux_servers -m shell -a "df -h"
```

Example output:

```text
Filesystem      Size  Used Avail Use%
/dev/root        20G  4.1G   15G  22%
```

**Screenshot**

*(Insert Screenshot: CommandforDiskUsage)*

---

## Check Available Memory (Optional)

```bash
ansible -i inventory.ini linux_servers -m shell -a "free -h"
```

---

## Check Hostname (Optional)

```bash
ansible -i inventory.ini linux_servers -m command -a "hostname"
```

---

# Project Structure

```text
ansible/
└── inventory.ini
```

---

# Validation Checklist

- ✅ Ubuntu control node created
- ✅ Ubuntu target node created
- ✅ Ansible installed successfully
- ✅ SSH key pair generated
- ✅ Passwordless SSH configured
- ✅ Inventory file created
- ✅ Managed host added
- ✅ Connectivity verified with `ping`
- ✅ Ad-hoc commands executed successfully

---

# Troubleshooting

## SSH Permission Denied

Verify that:

- The public SSH key has been copied to the target server.
- The correct private key is being used.
- The target server allows SSH access.

---

## Host Unreachable

Check:

- Public IP address
- Security Group rules (TCP Port 22)
- Internet connectivity
- SSH service is running

---

## Inventory Errors

Verify the inventory file.

```bash
ansible-inventory -i inventory.ini --list
```

---

## Ping Failed

Increase verbosity for troubleshooting.

```bash
ansible -i inventory.ini linux_servers -m ping -vvv
```

---

# Useful Ansible Ad-Hoc Commands

Check uptime:

```bash
ansible -i inventory.ini linux_servers -m command -a "uptime"
```

Check disk usage:

```bash
ansible -i inventory.ini linux_servers -m shell -a "df -h"
```

Check memory usage:

```bash
ansible -i inventory.ini linux_servers -m shell -a "free -h"
```

Check hostname:

```bash
ansible -i inventory.ini linux_servers -m command -a "hostname"
```

Update package cache:

```bash
ansible -i inventory.ini linux_servers -m apt -a "update_cache=yes" --become
```

---

# Key Learnings

During this project, I learned how to:

- Install and configure Ansible on a Linux server.
- Configure passwordless SSH authentication.
- Create and manage an Ansible inventory.
- Verify communication with managed nodes.
- Execute remote commands using Ansible ad-hoc modules.
- Automate Linux administration tasks without manually logging into each server.

---

# Conclusion

In this project, we successfully set up **Ansible** on a Linux control node and configured it to manage remote Linux servers. We established passwordless SSH authentication, created an inventory file, verified connectivity using the **ping** module, and executed several ad-hoc commands to gather system information.

This foundational setup prepares us for more advanced automation tasks, including writing Ansible playbooks, deploying applications, managing configurations, and automating infrastructure across multiple servers.

---

**End of Project**
