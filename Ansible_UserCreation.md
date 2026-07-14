# Automate User Creation on Linux Server using Ansible

## Introduction

Managing user accounts is one of the most common administrative tasks on Linux servers. As the number of servers grows, manually creating and managing user accounts becomes inefficient and error-prone.

In this project, we use **Ansible** to automate user creation on a Linux server. Using Ansible playbooks ensures consistent, repeatable, and scalable user management across one or multiple servers.

---

# Objectives

By the end of this project, you will be able to:

- Understand how Ansible automates Linux administration.
- Install and configure Ansible.
- Configure passwordless SSH authentication.
- Create an Ansible inventory.
- Write an Ansible playbook for automated user creation.
- Configure user groups, home directories, and SSH public keys.
- Verify user creation and SSH login.

---

# Prerequisites

Before starting, ensure you have the following:

- Ubuntu control machine (AWS EC2 instance used in this project)
- Target Linux server
- AWS Account
- SSH key pair
- Internet connectivity
- Basic Linux knowledge

---

# Project Overview

This project covers the following tasks:

1. Install Ansible
2. Configure SSH authentication
3. Create an inventory file
4. Create an Ansible playbook
5. Configure user groups and SSH keys
6. Execute the playbook
7. Verify users and test SSH login

---

# Task 1 — Install and Configure Ansible

For this project, I used an Ubuntu EC2 instance on AWS as the Ansible Control Node.

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
config file = None
python version = 3.x.x
```

**Output**

*(Insert Screenshot: VerifyAnsible)*

---

# Configure Passwordless SSH

Generate an SSH key pair on the control node.

```bash
cd ~/.ssh

ls

ssh-keygen
```

Display the public key:

```bash
cat id_ed25519.pub
```

Copy the public key to the target server.

Test passwordless SSH:

```bash
ssh ubuntu@98.93.9.134
```

If successful, you'll log in without entering a password.

**Screenshots**

- keygen
- addkeytoNode
- logintoNodeServer

---

# Task 2 — Create the Inventory File

Create an Ansible working directory.

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
[linux_servers]
target1 ansible_host=98.93.9.134 ansible_user=ubuntu
```

Save the file.

**Screenshot**

*(Insert Screenshot: inventoryfile)*

---

# Verify Connectivity

Run the ping module to verify communication.

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

---

# Task 3 — Create the User Creation Playbook

Create a new playbook.

```bash
vim create_users.yml
```

**Screenshot**

*(Insert Screenshot: UserCreation)*

---

## Playbook

```yaml
---
- name: Create Linux Users
  hosts: linux_servers
  become: yes

  vars:
    users:
      - user1
      - user2

  tasks:

    - name: Create users
      user:
        name: "{{ item }}"
        shell: /bin/bash
        create_home: yes
        state: present
      loop: "{{ users }}"
```

Save the file.

**Screenshots**

- Playbook
- Userplaybook
- Playbookadded

---

# Task 4 — Configure Additional User Settings

Extend the playbook to create groups and configure SSH public keys.

```yaml
---
- name: Configure Linux Users
  hosts: linux_servers
  become: yes

  vars:
    users:
      - username: user1
        groups: sudo
        key: "{{ lookup('file', '/path/to/user1.pub') }}"

      - username: user2
        groups: developers
        key: "{{ lookup('file', '/path/to/user2.pub') }}"

  tasks:

    - name: Create users
      user:
        name: "{{ item.username }}"
        shell: /bin/bash
        groups: "{{ item.groups }}"
        append: yes
        create_home: yes
        state: present
      loop: "{{ users }}"

    - name: Add authorized SSH keys
      authorized_key:
        user: "{{ item.username }}"
        state: present
        key: "{{ item.key }}"
      loop: "{{ users }}"
```

> **Note:** Replace the file paths with the locations of your actual public SSH keys.

**Screenshots**

- Playbook
- AnsiblePlaybook
- AnsiblePlaybookUpdated

---

# Task 5 — Execute the Playbook

Run the playbook.

```bash
ansible-playbook -i inventory.ini create_users.yml
```

Example output:

```text
PLAY RECAP

target1
ok=5
changed=3
failed=0
```

**Screenshot**

*(Insert Screenshot: Usercreated)*

---

# Verify User Creation

Display all users.

```bash
cat /etc/passwd
```

**Screenshot**

*(Insert Screenshot: etc)*

---

Check the home directories.

```bash
ls /home
```

Expected output:

```text
ubuntu
user1
user2
```

**Screenshot**

*(Insert Screenshot: lshome)*

---

# Test SSH Login

Log in as **user1**.

```bash
ssh user1@98.93.9.134
```

**Screenshot**

*(Insert Screenshot: sshuserone)*

---

Log in as **user2**.

```bash
ssh user2@98.93.9.134
```

**Screenshot**

*(Insert Screenshot: sshusertwo)*

---

# Project Structure

```text
ansible/
├── inventory.ini
└── create_users.yml
```

---

# Validation Checklist

- ✅ Ansible installed successfully
- ✅ SSH key authentication configured
- ✅ Inventory created
- ✅ Playbook created
- ✅ Users automatically created
- ✅ Groups assigned
- ✅ SSH public keys configured
- ✅ Home directories created
- ✅ Users verified
- ✅ SSH login tested

---

# Common Troubleshooting

## SSH Permission Denied

Verify that the SSH public key has been copied to the target server.

---

## Host Unreachable

Check:

- Public IP address
- Security Group rules
- SSH service
- Internet connectivity

---

## Playbook Fails

Validate the playbook syntax.

```bash
ansible-playbook --syntax-check create_users.yml
```

---

## Verify Inventory

```bash
ansible-inventory -i inventory.ini --list
```

---

# Clean Up (Optional)

To remove the created users:

```bash
sudo userdel -r user1

sudo userdel -r user2
```

---

# Key Learnings

Throughout this project, I learned how to:

- Install and configure Ansible.
- Configure passwordless SSH authentication.
- Manage Linux servers using an inventory file.
- Automate user creation with Ansible playbooks.
- Assign users to groups.
- Configure SSH authorized keys.
- Verify successful automation across remote servers.

---

# Conclusion

In this project, we automated Linux user account creation using **Ansible**. Instead of manually creating users on each server, we used a reusable playbook to provision user accounts, configure home directories, assign groups, and install SSH public keys.

This approach significantly improves consistency, scalability, and efficiency in Linux server administration. The same playbook can easily be extended to manage hundreds of servers, enforce password policies, remove users, or integrate with enterprise identity management systems.

---

**End of Project**
