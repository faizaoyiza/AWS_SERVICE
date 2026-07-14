# Mini Project: Backup and Restore Files on a Linux Server Using Ansible

## Introduction

Regular data backup and restoration are essential practices for maintaining business continuity and protecting critical information on Linux servers. Performing these tasks manually can be repetitive and prone to human error. **Ansible** simplifies backup and recovery by automating the entire process through reusable playbooks.

In this mini project, I configured Ansible on an Ubuntu control node hosted on AWS, established passwordless SSH authentication with a managed Linux server, created an inventory file, and developed Ansible playbooks to automate file backup and restoration.

---

# Project Objectives

The objectives of this project were to:

- Understand the role of Ansible in Linux automation.
- Install and configure Ansible on a control machine.
- Configure passwordless SSH authentication.
- Create an Ansible inventory for managed servers.
- Develop an Ansible playbook to back up files.
- Develop an Ansible playbook to restore files from backups.
- Test and verify the backup and restore processes.

---

# Prerequisites

Before beginning the project, I ensured the following requirements were available:

- AWS EC2 Ubuntu instance (Control Node)
- AWS EC2 Ubuntu/Linux instance (Managed Node)
- SSH access to both servers
- Sudo privileges
- Internet connectivity
- Basic Linux command-line knowledge
- A text editor (Nano or Vim)

---

# Project Architecture

```text
                  +----------------------+
                  |   Ansible Control    |
                  |     Ubuntu (AWS)     |
                  +----------+-----------+
                             |
                      SSH (Key-Based)
                             |
                             |
                  +----------+-----------+
                  |     Target Server    |
                  |    Ubuntu/Linux EC2  |
                  +----------------------+
```

The control node executes Ansible playbooks over SSH to automate backup and restoration on the managed server.

---

# Task 1: Install and Configure Ansible

## Update the Package Repository

After launching the Ubuntu EC2 instance that would serve as the Ansible control node, I connected to it via SSH and updated the package repository.

```bash
sudo apt update
```

---

## Install Ansible

I installed Ansible using the Ubuntu package manager.

```bash
sudo apt install ansible -y
```

The installation completed successfully.

---

## Verify the Installation

To confirm that Ansible was installed correctly, I checked the installed version.

```bash
ansible --version
```

The output displayed the installed Ansible version and Python runtime, confirming a successful installation.

---

# Configure Passwordless SSH Authentication

Ansible communicates with managed servers over SSH. To enable passwordless authentication, I generated an SSH key pair on the control node.

Navigate to the SSH directory.

```bash
cd ~/.ssh
```

Generate a new SSH key pair.

```bash
ssh-keygen
```

Display the public key.

```bash
cat id_ed25519.pub
```

I copied the public key and added it to the managed server's `authorized_keys` file.

To verify passwordless authentication, I connected to the target server.

```bash
ssh ubuntu@<TARGET_SERVER_PUBLIC_IP>
```

The connection succeeded without requiring a password, confirming that SSH key-based authentication was configured correctly.

---

# Task 2: Configure the Ansible Inventory

I created a dedicated working directory for the Ansible project.

```bash
mkdir ~/ansible
```

Navigate into the directory.

```bash
cd ~/ansible
```

---

## Create the Inventory File

```bash
vim inventory.ini
```

Inventory configuration:

```ini
[linux_servers]
target ansible_host=<TARGET_SERVER_PUBLIC_IP> ansible_user=ubuntu
```

The inventory file defines the managed Linux server that Ansible will administer.

---

## Verify Connectivity

Before creating playbooks, I confirmed that the managed server was reachable.

```bash
ansible all -i inventory.ini -m ping
```

Expected output:

```text
target | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

This confirmed successful communication between the control node and the managed server.

---

# Task 3: Create the Backup Playbook

I created a playbook to automate file backups.

```bash
nano backup.yml
```

Example playbook:

```yaml
---
- name: Backup Application Files
  hosts: linux_servers
  become: true

  tasks:

    - name: Create backup directory
      file:
        path: /backup
        state: directory
        mode: '0755'

    - name: Copy application file to backup directory
      copy:
        src: /home/ubuntu/file.txt
        dest: /backup/file.txt
        remote_src: yes
```

This playbook performs the following tasks:

- Creates a backup directory if it does not exist.
- Copies the specified file into the backup directory.
- Preserves the original file.

---

## Determine the File Path

To identify the full path of the file being backed up, I used:

```bash
pwd
```

or

```bash
echo "$PWD/file.txt"
```

This ensured the correct source path was specified in the playbook.

---

# Task 4: Create the Restore Playbook

Next, I created a playbook to restore files from the backup location.

```bash
vim restore.yml
```

Example playbook:

```yaml
---
- name: Restore Application Files
  hosts: linux_servers
  become: true

  tasks:

    - name: Restore file from backup
      copy:
        src: /backup/file.txt
        dest: /home/ubuntu/file.txt
        remote_src: yes
```

The restore playbook copies the backup file back to its original location.

---

# Task 5: Execute the Backup Playbook

To back up the selected file, I ran:

```bash
ansible-playbook -i inventory.ini backup.yml
```

Ansible successfully executed all tasks and reported the playbook status.

---

## Verify the Backup

I logged into the managed server and confirmed that the backup directory had been created.

```bash
ls /backup
```

The backed-up file was present, confirming that the backup operation completed successfully.

---

# Execute the Restore Playbook

To restore the backed-up file, I executed:

```bash
ansible-playbook -i inventory.ini restore.yml
```

Ansible restored the file to its original location.

---

## Verify the Restoration

Finally, I confirmed that the file had been restored successfully.

```bash
ls /home/ubuntu
```

The original file was available again, demonstrating that the restoration process worked correctly.

---

# Project Workflow

```text
Install Ansible
        │
        ▼
Configure SSH Keys
        │
        ▼
Create Inventory
        │
        ▼
Create Backup Playbook
        │
        ▼
Run Backup
        │
        ▼
Verify Backup
        │
        ▼
Create Restore Playbook
        │
        ▼
Run Restore
        │
        ▼
Verify Restoration
```

---

# Observations

During this project, I observed that:

- Ansible greatly simplifies repetitive administration tasks.
- Passwordless SSH authentication is essential for seamless automation.
- Inventory files make it easy to manage multiple servers.
- Playbooks are reusable and can be executed repeatedly without manual intervention.
- File backups can be automated consistently across multiple Linux systems.

---

# Challenges Encountered

## SSH Authentication Failed

Initially, Ansible could not connect to the managed server.

**Solution**

Verified that the SSH public key had been copied correctly into the managed server's `authorized_keys` file.

---

## Permission Denied During Backup

The backup playbook initially failed because it required elevated privileges.

**Solution**

Enabled privilege escalation using:

```yaml
become: true
```

---

## Incorrect File Path

The playbook failed because the specified file path did not exist.

**Solution**

Verified the file location using:

```bash
pwd
```

and

```bash
ls
```

before updating the playbook.

---

## Inventory Connection Errors

Ansible initially reported the host as unreachable.

**Solution**

Verified:

- Correct public IP address
- Username
- SSH connectivity
- AWS Security Group rules allowing SSH (port 22)

---

# Key Concepts Learned

This project provided practical experience with:

- Infrastructure automation using Ansible
- Installing Ansible
- SSH key authentication
- Inventory configuration
- Ansible playbooks
- File backup automation
- File restoration automation
- Linux server administration
- Configuration management
- Infrastructure as Code (IaC)

---

# Conclusion

In this mini project, I successfully installed and configured **Ansible** on an Ubuntu control node, established secure passwordless SSH communication with a managed Linux server, and automated file backup and restoration using Ansible playbooks.

The project demonstrated how Ansible can simplify repetitive administrative tasks through reusable automation. This solution can be extended to support multiple managed servers, scheduled backups using cron jobs, compressed backup archives, encrypted backups, and integration with cloud storage services for disaster recovery.
