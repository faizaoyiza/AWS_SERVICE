# Deploy and Configure Nginx Web Server using Ansible

## Introduction

Nginx is a high-performance, open-source web server widely used for hosting websites, reverse proxying, and load balancing. Deploying and configuring Nginx manually across multiple servers can be repetitive and time-consuming. With **Ansible**, these tasks can be automated using reusable playbooks, ensuring consistency and reducing manual effort.

In this project, we will deploy and configure an Nginx web server on a Linux machine using Ansible.

---

# Objectives

By the end of this project, you will be able to:

- Understand how Ansible automates application deployment.
- Install and configure Ansible on a control node.
- Configure passwordless SSH authentication.
- Create an Ansible inventory file.
- Write an Ansible playbook to install Nginx.
- Deploy a custom Nginx website using Ansible.
- Verify the Nginx deployment through a web browser.

---

# Prerequisites

Before beginning, ensure you have the following:

- Ubuntu Linux Control Node (AWS EC2)
- Ubuntu Target Server (AWS EC2)
- AWS Account
- SSH Key Pair
- Internet connectivity
- Basic Linux knowledge
- Basic understanding of Ansible

---

# Project Overview

This project covers the following tasks:

1. Install and configure Ansible
2. Configure passwordless SSH
3. Create an inventory file
4. Install Nginx using Ansible
5. Deploy a custom website
6. Verify the deployment

---

# Task 1 — Install and Configure Ansible

For this project, I used an Ubuntu EC2 instance on AWS as the **Ansible Control Node**.

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

# Configure Passwordless SSH Authentication

Generate an SSH key pair on the control node.

```bash
cd ~/.ssh

ls

ssh-keygen
```

Display the public key.

```bash
cat id_ed25519.pub
```

Copy the public key to the target server.

Test SSH access.

```bash
ssh ubuntu@54.221.50.28
```

If configured correctly, the login should not prompt for a password.

**Screenshots**

- keygen
- addkeytoNode
- AccessNode1Server

---

# Task 2 — Configure the Inventory File

Create a working directory.

```bash
mkdir ~/ansible

cd ~/ansible
```

**Screenshot**

*(Insert Screenshot: AnsibleFile)*

---

Create the inventory file.

```bash
vim inventory.ini
```

Add the target server.

```ini
[web_servers]
target ansible_host=54.221.50.28 ansible_user=ubuntu
```

Save the file.

**Screenshot**

*(Insert Screenshot: inventoryfile)*

---

## Verify Connectivity

Test communication between Ansible and the target server.

```bash
ansible -i inventory.ini web_servers -m ping
```

Expected output:

```text
target | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

---

# Task 3 — Create the Nginx Installation Playbook

Create the playbook.

```bash
vim install_nginx.yml
```

**Screenshot**

*(Insert Screenshot: Createnginxfile)*

---

## Playbook

```yaml
---
- name: Install Nginx
  hosts: web_servers
  become: yes

  tasks:

    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install Nginx
      apt:
        name: nginx
        state: present

    - name: Enable Nginx
      service:
        name: nginx
        enabled: yes

    - name: Start Nginx
      service:
        name: nginx
        state: started
```

Save the file.

**Screenshots**

- nginxfile
- nginxfile

---

# Task 4 — Configure a Custom Nginx Website

Create another playbook.

```bash
vim configure_nginx.yml
```

**Screenshot**

*(Insert Screenshot: ConfigureNginc)*

---

## Playbook

```yaml
---
- name: Configure Nginx Website
  hosts: web_servers
  become: yes

  tasks:

    - name: Deploy custom index page
      copy:
        dest: /var/www/html/index.html
        content: |
          <!DOCTYPE html>
          <html>
          <head>
              <title>Ansible Nginx Deployment</title>
          </head>
          <body>
              <h1>Congratulations!</h1>
              <p>This website was deployed using Ansible.</p>
          </body>
          </html>

    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
```

Save the playbook.

**Screenshots**

- ConfigFile
- ConfigFileAdded

---

# Task 5 — Deploy the Website

Run the installation playbook.

```bash
ansible-playbook -i inventory.ini install_nginx.yml
```

**Screenshot**

*(Insert Screenshot: NginxInstalled)*

---

Run the configuration playbook.

```bash
ansible-playbook -i inventory.ini configure_nginx.yml
```

**Screenshot**

*(Insert Screenshot: NginxDeployed)*

---

# Configure AWS Security Group

Before accessing the web server, edit the EC2 Security Group.

Allow the following inbound rule:

| Type | Protocol | Port | Source |
|------|----------|------|--------|
| HTTP | TCP | 80 | 0.0.0.0/0 |

**Screenshot**

*(Insert Screenshot: SGAccess)*

---

# Verify the Website

Test from the terminal.

```bash
curl http://54.221.50.28
```

Expected output:

```html
<h1>Congratulations!</h1>
<p>This website was deployed using Ansible.</p>
```

**Screenshot**

*(Insert Screenshot: WebAccess)*

---

Open the server's public IP in your browser.

```text
http://54.221.50.28
```

The custom webpage should be displayed.

**Screenshot**

*(Insert Screenshot: WebAccess)*

---

# Project Structure

```text
ansible/
├── inventory.ini
├── install_nginx.yml
└── configure_nginx.yml
```

---

# Validation Checklist

- ✅ Ansible installed successfully
- ✅ Passwordless SSH configured
- ✅ Inventory file created
- ✅ Nginx installed
- ✅ Nginx service started
- ✅ Custom webpage deployed
- ✅ HTTP allowed in Security Group
- ✅ Website accessible from browser

---

# Troubleshooting

## SSH Connection Failed

Verify:

- Public IP address
- SSH key
- Security Group
- SSH service running

---

## Host Unreachable

Run:

```bash
ansible -i inventory.ini web_servers -m ping
```

---

## Nginx Not Running

Check the service status.

```bash
sudo systemctl status nginx
```

Restart if necessary.

```bash
sudo systemctl restart nginx
```

---

## Verify Port 80

Check if Nginx is listening.

```bash
sudo ss -tulpn | grep :80
```

---

## Website Not Accessible

Verify:

- Security Group allows HTTP (Port 80)
- Instance has a public IP
- Nginx service is running
- Browser is using the correct public IP

---

# Useful Commands

Check Nginx version.

```bash
nginx -v
```

Check service status.

```bash
sudo systemctl status nginx
```

Restart Nginx.

```bash
sudo systemctl restart nginx
```

Reload configuration.

```bash
sudo systemctl reload nginx
```

Test configuration.

```bash
sudo nginx -t
```

---

# Key Learnings

Throughout this project, I learned how to:

- Install and configure Ansible.
- Configure passwordless SSH authentication.
- Create an Ansible inventory.
- Automate Nginx installation.
- Deploy a custom website using Ansible.
- Manage Linux services remotely.
- Configure AWS Security Groups for web access.
- Verify successful deployment using both the terminal and a web browser.

---

# Conclusion

In this project, we automated the deployment and configuration of an **Nginx web server** using **Ansible**. By creating reusable playbooks, we simplified the installation, configuration, and deployment process, making it consistent and repeatable across multiple servers.

This automation approach greatly reduces manual administration and provides a solid foundation for managing larger web infrastructures. The same playbooks can be extended to configure virtual hosts, SSL certificates, reverse proxies, or deploy complete web applications across multiple environments.

---

**End of Project**
