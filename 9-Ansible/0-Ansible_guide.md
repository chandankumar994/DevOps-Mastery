# 🚀 Ansible Guide for DevOps Engineers

> A practical, easy-to-understand guide with real-life examples, code samples, and interview preparation

---

## 📋 Table of Contents

1. [What is Ansible?](#what-is-ansible)
2. [Basic Concepts](#basic-concepts)
3. [How to Get Started](#how-to-get-started)
4. [Sample Code with Explanations](#sample-code-with-explanations)
5. [Ansible Workflow](#ansible-workflow)
6. [Real-Life Examples](#real-life-examples)
7. [Interview Questions & Answers](#interview-questions--answers)

---

## 🤔 What is Ansible?

Imagine you are the **IT manager of a company** with 500 servers. Every time there's a software update, you'd have to log into each server and run commands manually. That would take **days** and you'd definitely make mistakes.

**Ansible is your army of robots that does this work for you — simultaneously, on all 500 servers.**

- You write simple instructions (called **Playbooks**) in plain English-like YAML
- Ansible **connects** to all your servers via SSH
- It **executes** your instructions on every server automatically
- No special software needed on the servers — just Python and SSH!

> 🏫 **Real-Life Analogy:** Think of Ansible like a **school teacher** sending homework instructions to all students at once. The teacher (Ansible Control Node) sends the same task to all students (managed nodes), and every student completes the task on their own machine. The teacher doesn't need to sit next to each student!

### Ansible vs Other Tools

| Feature | Ansible | Chef | Puppet |
|---------|---------|------|--------|
| Language | YAML (easy) | Ruby (complex) | DSL (complex) |
| Agent Required | ❌ No (agentless) | ✅ Yes | ✅ Yes |
| Learning Curve | Low | High | High |
| Communication | SSH / WinRM | Pull-based | Pull-based |
| Setup Effort | Minimal | Heavy | Heavy |

---

## 📚 Basic Concepts

### 1. 🖥️ Control Node vs Managed Nodes

```
┌─────────────────────────────────────────────────────┐
│                                                     │
│   CONTROL NODE              MANAGED NODES           │
│   (Your Laptop /            (Target Servers)        │
│    Jenkins Server)                                  │
│                                                     │
│   ┌───────────┐    SSH     ┌──────────────────┐     │
│   │           │ ─────────▶ │  web-server-1    │     │
│   │  Ansible  │    SSH     ├──────────────────┤     │
│   │  Installed│ ─────────▶ │  web-server-2    │     │
│   │           │    SSH     ├──────────────────┤     │
│   └───────────┘ ─────────▶ │  db-server-1     │     │
│                             └──────────────────┘    │
└─────────────────────────────────────────────────────┘
```

- **Control Node** — The machine where Ansible is installed and commands are run FROM
- **Managed Nodes** — The target servers that Ansible configures (NO Ansible installation needed here!)

> 🎮 **Real-Life Analogy:** Control Node is like the **game controller** and Managed Nodes are the **characters on screen**. You press buttons on the controller (control node) and characters (servers) respond!

---

### 2. 📋 Inventory

The **inventory** is a file that lists all the servers Ansible will manage — like a **contact book** for your servers.

```ini
# Simple inventory file (hosts.ini)

[webservers]
web1.example.com
web2.example.com
192.168.1.10

[databases]
db1.example.com
db2.example.com

[all:vars]
ansible_user=ec2-user
```

> 📒 **Real-Life Analogy:** Inventory is like your **phone's contact list**. You group contacts (family, work, friends) just like you group servers (webservers, databases, cache).

---

### 3. 📜 Playbook

A **Playbook** is a YAML file containing a set of instructions (called **plays**) that Ansible will run on your servers.

```yaml
# Simple playbook structure
---
- name: Install and start Apache       # Play name
  hosts: webservers                    # Which servers to run on
  become: true                         # Run as sudo/root

  tasks:
    - name: Install Apache             # Task 1
      yum:
        name: httpd
        state: present

    - name: Start Apache               # Task 2
      service:
        name: httpd
        state: started
```

> 📖 **Real-Life Analogy:** A Playbook is like a **recipe book**. Each recipe (play) has a list of steps (tasks) to follow, and you can choose which recipe to cook (which play to run).

---

### 4. 🔧 Modules

Modules are **pre-built tools** in Ansible that perform specific actions — like installing packages, copying files, managing services, etc.

| Module | What it Does | Real-Life Equivalent |
|--------|-------------|---------------------|
| `yum` / `apt` | Install/remove packages | App Store installer |
| `copy` | Copy files to servers | USB file transfer |
| `template` | Copy files with dynamic content | Mail merge in Word |
| `service` | Start/stop/restart services | Windows Services manager |
| `user` | Manage user accounts | HR managing employees |
| `file` | Manage files and directories | File Explorer |
| `command` | Run shell commands | Terminal |
| `git` | Clone/pull Git repos | `git clone` manually |
| `docker_container` | Manage Docker containers | Docker CLI |
| `aws_ec2` | Manage AWS EC2 instances | AWS Console |

---

### 5. 🎭 Roles

Roles are a way to **organize playbooks** into reusable, structured packages — like modules in programming.

```
roles/
└── webserver/
    ├── tasks/          # What to do
    │   └── main.yml
    ├── handlers/       # What to do when notified (e.g., restart service)
    │   └── main.yml
    ├── templates/      # Jinja2 template files
    │   └── nginx.conf.j2
    ├── files/          # Static files to copy
    │   └── index.html
    ├── vars/           # Variables (high priority)
    │   └── main.yml
    ├── defaults/       # Default variables (low priority)
    │   └── main.yml
    └── meta/           # Role metadata and dependencies
        └── main.yml
```

> 🏗️ **Real-Life Analogy:** Roles are like **IKEA furniture kits**. Everything you need (screws, panels, instructions) is neatly packaged together. You can reuse the same kit to assemble furniture in different rooms!

---

### 6. 🔔 Handlers

Handlers are **special tasks** that only run when **notified** by another task. They run at the END of a play, and only once — even if notified multiple times.

```yaml
tasks:
  - name: Update nginx config
    template:
      src: nginx.conf.j2
      dest: /etc/nginx/nginx.conf
    notify: Restart Nginx   # 👈 Notifies the handler

handlers:
  - name: Restart Nginx     # 👈 Only runs if notified
    service:
      name: nginx
      state: restarted
```

> 🔔 **Real-Life Analogy:** Like a **notification bell** in a restaurant kitchen. The waiter (task) places an order and rings the bell. The chef (handler) comes out ONLY when the bell rings — not before, not without reason!

---

### 7. 📦 Variables

Variables make your playbooks **flexible and reusable**.

```yaml
# Define variables
vars:
  app_port: 8080
  app_name: "mywebapp"
  db_host: "db.example.com"

# Use variables with {{ }}
tasks:
  - name: Start application
    command: "./start-app.sh --port {{ app_port }} --name {{ app_name }}"
```

---

### 8. 🏷️ Tags

Tags let you **run only specific parts** of a playbook — like running only the "install" tasks and skipping "configure" tasks.

```yaml
tasks:
  - name: Install Nginx
    yum:
      name: nginx
    tags: install       # 👈 Tag

  - name: Configure Nginx
    template:
      src: nginx.conf.j2
      dest: /etc/nginx/nginx.conf
    tags: configure     # 👈 Different tag
```

```bash
# Run only tasks tagged "install"
ansible-playbook site.yml --tags install

# Skip tasks tagged "configure"
ansible-playbook site.yml --skip-tags configure
```

---

### 9. 🔐 Ansible Vault

Ansible Vault **encrypts sensitive data** like passwords and API keys so you can safely store them in Git.

```bash
# Encrypt a file
ansible-vault encrypt secrets.yml

# Decrypt a file
ansible-vault decrypt secrets.yml

# Edit encrypted file
ansible-vault edit secrets.yml

# Run playbook with vault password
ansible-playbook site.yml --ask-vault-pass
```

---

### 10. ✅ Idempotency

One of Ansible's superpowers — running the same playbook **multiple times** produces the **same result**. It only makes changes if needed.

> 🔁 **Real-Life Analogy:** Like a **thermostat**. You set it to 22°C. If it's already 22°C, it does nothing. If it's 18°C, it turns on the heater. Running it again doesn't "double heat" the room!

---

## 🚦 How to Get Started

### Step 1: Install Ansible

```bash
# Ubuntu / Debian
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible

# RHEL / CentOS / Amazon Linux
sudo yum install -y epel-release
sudo yum install -y ansible

# macOS
brew install ansible

# Using pip (any OS)
pip3 install ansible

# Verify installation
ansible --version
```

---

### Step 2: Set Up SSH Key Authentication

```bash
# Generate SSH key pair on Control Node (if not already done)
ssh-keygen -t rsa -b 4096 -C "ansible@controlnode"

# Copy public key to all managed nodes
ssh-copy-id ec2-user@web1.example.com
ssh-copy-id ec2-user@web2.example.com
ssh-copy-id ec2-user@db1.example.com

# Test SSH connection
ssh ec2-user@web1.example.com

# For AWS EC2 — specify the key file
ssh-copy-id -i ~/.ssh/my-aws-key.pem ec2-user@ec2-1-2-3-4.compute.amazonaws.com
```

---

### Step 3: Create Your Project Structure

```bash
mkdir my-ansible-project
cd my-ansible-project

# Create the basic files
touch inventory.ini
touch ansible.cfg
touch site.yml

# Your structure
my-ansible-project/
├── ansible.cfg         # Ansible configuration
├── inventory.ini       # List of servers
├── site.yml            # Main playbook
├── group_vars/         # Variables per group
│   ├── all.yml
│   ├── webservers.yml
│   └── databases.yml
├── host_vars/          # Variables per host
│   └── web1.example.com.yml
└── roles/              # Reusable roles
    ├── common/
    ├── webserver/
    └── database/
```

---

### Step 4: Configure `ansible.cfg`

```ini
# ansible.cfg — Ansible configuration file
[defaults]
inventory       = ./inventory.ini      # Default inventory file
remote_user     = ec2-user             # Default SSH user
private_key_file = ~/.ssh/my-key.pem  # SSH private key
host_key_checking = False             # Don't check host fingerprints (use carefully)
retry_files_enabled = False           # Don't create .retry files
forks           = 10                  # Run on 10 servers simultaneously

[privilege_escalation]
become          = True                # Use sudo by default
become_method   = sudo
become_user     = root
```

---

### Step 5: Test Connectivity (Ad-hoc Commands)

```bash
# Ping all servers to test connectivity
ansible all -m ping

# Ping only webservers group
ansible webservers -m ping

# Run a command on all servers
ansible all -m command -a "uptime"

# Check disk space on all db servers
ansible databases -m command -a "df -h"

# Install a package on webservers (ad-hoc)
ansible webservers -m yum -a "name=git state=present" --become
```

---

## 💻 Sample Code with Explanations

### Example 1: Basic Playbook — Install and Configure Nginx

#### `inventory.ini`
```ini
# Inventory file — list all your servers here

[webservers]
web1.example.com ansible_host=192.168.1.10
web2.example.com ansible_host=192.168.1.11

[databases]
db1.example.com  ansible_host=192.168.1.20
db2.example.com  ansible_host=192.168.1.21

[monitoring]
monitor.example.com ansible_host=192.168.1.30

# Group of groups — all app servers together
[app:children]
webservers
databases

# Variables that apply to ALL servers
[all:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=~/.ssh/mykey.pem
ansible_python_interpreter=/usr/bin/python3
```

---

#### `group_vars/webservers.yml`
```yaml
# Variables specific to the webservers group
---
nginx_port: 80
nginx_worker_processes: auto
nginx_keepalive_timeout: 65
app_root: /var/www/html
server_admin_email: admin@example.com
```

---

#### `group_vars/all.yml`
```yaml
# Variables that apply to all servers
---
ntp_server: pool.ntp.org
timezone: "Asia/Kolkata"
company_name: "MyStartup"
environment: production
```

---

#### `install_nginx.yml` — The Main Playbook
```yaml
---
# Play 1: Configure all servers with common settings
- name: Apply common configuration to all servers
  hosts: all                  # Run on ALL servers
  become: true                # Use sudo privileges

  tasks:
    # Check if Python is installed (required for Ansible modules)
    - name: Ensure Python3 is installed
      raw: apt-get install -y python3 || yum install -y python3
      changed_when: false

    # Update system packages to latest
    - name: Update all packages
      package:                # 'package' works on both yum and apt
        name: "*"
        state: latest
      tags: update

    # Set the correct timezone
    - name: Set timezone
      timezone:
        name: "{{ timezone }}"   # Uses variable from group_vars/all.yml

    # Install common tools on every server
    - name: Install common packages
      package:
        name:
          - vim
          - curl
          - wget
          - git
          - htop
          - net-tools
        state: present
      tags: packages

# Play 2: Install and configure Nginx on web servers only
- name: Install and configure Nginx web server
  hosts: webservers           # Only run on servers in [webservers] group
  become: true

  vars:                       # Play-level variables (override group_vars)
    nginx_version: "latest"

  handlers:
    # Handler: Only restarts Nginx when notified
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted

    # Handler: Only reloads Nginx config when notified
    - name: Reload Nginx
      service:
        name: nginx
        state: reloaded

  tasks:
    # Install Nginx package
    - name: Install Nginx
      package:
        name: nginx
        state: "{{ nginx_version }}"
      tags: install

    # Create the web root directory
    - name: Create web root directory
      file:
        path: "{{ app_root }}"    # /var/www/html (from group_vars)
        state: directory          # Ensure it's a directory
        owner: nginx              # Owned by nginx user
        group: nginx
        mode: '0755'              # Permissions: rwxr-xr-x

    # Deploy Nginx configuration from template
    - name: Deploy Nginx configuration
      template:                   # 'template' processes Jinja2 variables
        src: nginx.conf.j2        # Source template file
        dest: /etc/nginx/nginx.conf
        owner: root
        group: root
        mode: '0644'
        backup: yes               # Keep backup of old config
      notify: Reload Nginx        # Tell handler to reload after config change
      tags: configure

    # Copy the index.html file to the server
    - name: Deploy index.html
      copy:
        src: files/index.html     # File in your Ansible project
        dest: "{{ app_root }}/index.html"
        owner: nginx
        group: nginx
        mode: '0644'
      tags: deploy

    # Make sure Nginx is running and starts on boot
    - name: Ensure Nginx is started and enabled
      service:
        name: nginx
        state: started            # Make sure it's running
        enabled: true             # Start automatically on boot

    # Verify Nginx is listening on the right port
    - name: Verify Nginx is responding
      uri:
        url: "http://{{ ansible_host }}:{{ nginx_port }}"
        status_code: 200          # Expect HTTP 200 OK
      register: nginx_response    # Save the response

    # Print the result
    - name: Print Nginx status
      debug:
        msg: "Nginx is up! Status: {{ nginx_response.status }}"
```

---

#### `templates/nginx.conf.j2` — Jinja2 Template
```nginx
# This is a Jinja2 template — {{ variables }} get replaced with real values

user nginx;
worker_processes {{ nginx_worker_processes }};   {# From group_vars/webservers.yml #}
error_log /var/log/nginx/error.log warn;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    # Custom log format
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent"';

    access_log /var/log/nginx/access.log main;
    sendfile on;
    keepalive_timeout {{ nginx_keepalive_timeout }};  {# From group_vars #}

    server {
        listen       {{ nginx_port }};            {# Port from group_vars #}
        server_name  {{ ansible_hostname }};      {# Automatically detected hostname #}
        root         {{ app_root }};              {# Web root from group_vars #}
        index        index.html;

        # Conditional block using Jinja2
        {% if environment == 'production' %}
        # Production-only settings
        gzip on;
        gzip_types text/plain application/json;
        {% endif %}

        location / {
            try_files $uri $uri/ =404;
        }

        error_page 404 /404.html;
        error_page 500 502 503 504 /50x.html;
    }

    # Include additional server configs
    include /etc/nginx/conf.d/*.conf;
}
```

---

### Example 2: Complete Role Structure

```bash
# Create a role using ansible-galaxy command
ansible-galaxy init roles/webserver
```

#### `roles/webserver/tasks/main.yml`
```yaml
---
# Main tasks file for webserver role
# All tasks in a role are automatically included

- name: Include OS-specific variables
  include_vars: "{{ ansible_os_family }}.yml"
  # Loads RedHat.yml or Debian.yml based on OS

- name: Install Nginx
  package:
    name: "{{ nginx_package_name }}"   # Variable from vars file
    state: present
  tags: install

- name: Configure Nginx
  template:
    src: nginx.conf.j2
    dest: "{{ nginx_config_path }}"
  notify: Restart Nginx
  tags: configure

- name: Deploy application files
  copy:
    src: "{{ item.src }}"         # Loop variable
    dest: "{{ item.dest }}"
    mode: '0644'
  loop:                           # Loop through multiple files
    - { src: 'index.html', dest: '/var/www/html/index.html' }
    - { src: 'style.css',  dest: '/var/www/html/style.css' }
    - { src: '404.html',   dest: '/var/www/html/404.html' }
  tags: deploy

- name: Start and enable Nginx
  service:
    name: "{{ nginx_service_name }}"
    state: started
    enabled: true
```

---

#### `roles/webserver/handlers/main.yml`
```yaml
---
# Handlers — triggered by 'notify' in tasks

- name: Restart Nginx
  service:
    name: "{{ nginx_service_name }}"
    state: restarted

- name: Reload Nginx
  service:
    name: "{{ nginx_service_name }}"
    state: reloaded

- name: Test Nginx config
  command: nginx -t         # Test config before reloading
  changed_when: false
```

---

#### `roles/webserver/defaults/main.yml`
```yaml
---
# Default variables — lowest priority, easily overridden

nginx_port: 80
nginx_worker_processes: auto
nginx_keepalive_timeout: 65
nginx_package_name: nginx
nginx_service_name: nginx
nginx_config_path: /etc/nginx/nginx.conf
app_root: /var/www/html
```

---

#### `roles/webserver/vars/RedHat.yml`
```yaml
---
# Variables specific to RedHat/CentOS/Amazon Linux
nginx_package_name: nginx
nginx_config_path: /etc/nginx/nginx.conf
nginx_service_name: nginx
```

#### `roles/webserver/vars/Debian.yml`
```yaml
---
# Variables specific to Ubuntu/Debian
nginx_package_name: nginx
nginx_config_path: /etc/nginx/nginx.conf
nginx_service_name: nginx
```

---

#### `roles/webserver/meta/main.yml`
```yaml
---
galaxy_info:
  role_name: webserver
  author: your_name
  description: Installs and configures Nginx web server
  license: MIT
  min_ansible_version: "2.9"

  platforms:
    - name: EL          # Enterprise Linux (RHEL, CentOS)
      versions:
        - 7
        - 8
    - name: Ubuntu
      versions:
        - focal
        - jammy

# This role depends on 'common' role being applied first
dependencies:
  - role: common
```

---

#### `site.yml` — Master Playbook Using Roles
```yaml
---
# Apply common role to ALL servers
- name: Common configuration
  hosts: all
  become: true
  roles:
    - common             # roles/common/ is applied

# Apply webserver role to webservers
- name: Configure web servers
  hosts: webservers
  become: true
  roles:
    - role: webserver    # roles/webserver/ is applied
      vars:              # Override role defaults
        nginx_port: 8080
        app_root: /var/www/myapp

# Apply database role to db servers
- name: Configure database servers
  hosts: databases
  become: true
  roles:
    - role: database
      vars:
        db_port: 5432
        db_name: production_db
```

---

### Example 3: Variables, Loops, and Conditionals

```yaml
---
- name: Advanced Ansible concepts demo
  hosts: webservers
  become: true

  vars:
    users:              # List variable
      - name: alice
        role: developer
        shell: /bin/bash
      - name: bob
        role: devops
        shell: /bin/bash
      - name: charlie
        role: readonly
        shell: /bin/sh

    packages_by_env:    # Dictionary variable
      dev:
        - vim
        - strace
        - tcpdump
      prod:
        - vim
        - logrotate

  tasks:
    # Loop through users and create them
    - name: Create user accounts
      user:
        name: "{{ item.name }}"       # item refers to current loop element
        shell: "{{ item.shell }}"
        state: present
        create_home: true
      loop: "{{ users }}"             # Loop through the 'users' list
      tags: users

    # Add SSH key for each user
    - name: Add authorized SSH keys
      authorized_key:
        user: "{{ item.name }}"
        key: "{{ lookup('file', 'files/ssh_keys/{{ item.name }}.pub') }}"
        state: present
      loop: "{{ users }}"
      tags: ssh

    # Install packages based on environment (conditional)
    - name: Install environment-specific packages
      package:
        name: "{{ item }}"
        state: present
      loop: "{{ packages_by_env[environment] }}"  # environment from group_vars
      tags: packages

    # Conditional task — only run on RedHat-based systems
    - name: Configure SELinux (RedHat only)
      selinux:
        policy: targeted
        state: enforcing
      when: ansible_os_family == "RedHat"   # 👈 Condition
      tags: security

    # Conditional task — only run on Ubuntu
    - name: Configure UFW Firewall (Ubuntu only)
      ufw:
        rule: allow
        port: "{{ nginx_port }}"
        proto: tcp
      when: ansible_distribution == "Ubuntu"   # 👈 Another condition
      tags: firewall

    # Register output and use it in the next task
    - name: Check if application config exists
      stat:                               # Check file/directory status
        path: /etc/myapp/config.yml
      register: app_config               # 👈 Save result to variable

    - name: Deploy application config if missing
      template:
        src: app_config.yml.j2
        dest: /etc/myapp/config.yml
      when: not app_config.stat.exists   # 👈 Only if file doesn't exist

    # Loop with index
    - name: Create numbered directories
      file:
        path: "/data/volume{{ item }}"
        state: directory
        mode: '0755'
      loop: "{{ range(1, 6) | list }}"  # Creates volume1 to volume5
```

---

### Example 4: Ansible Vault for Secrets

```bash
# Create encrypted variables file
ansible-vault create group_vars/all/secrets.yml

# It opens your editor — type your secrets:
```

```yaml
# group_vars/all/secrets.yml (encrypted with vault)
---
db_root_password: "SuperSecret123!"
api_key: "sk-abc123def456ghi789"
ssl_certificate_key: |
  -----BEGIN PRIVATE KEY-----
  MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQC7...
  -----END PRIVATE KEY-----
```

```yaml
# Use vault secrets in your playbook just like normal variables
- name: Configure database
  hosts: databases
  become: true

  tasks:
    - name: Set MySQL root password
      mysql_user:
        name: root
        password: "{{ db_root_password }}"  # Comes from encrypted vault
        state: present

    - name: Configure API integration
      template:
        src: api_config.j2
        dest: /etc/myapp/api.conf
      vars:
        key: "{{ api_key }}"               # Also from vault
```

```bash
# Run playbook with vault password
ansible-playbook site.yml --ask-vault-pass

# Or use a vault password file (for CI/CD)
echo "myVaultPassword" > .vault_pass
chmod 600 .vault_pass
ansible-playbook site.yml --vault-password-file .vault_pass
```

---

### Example 5: Error Handling

```yaml
---
- name: Error handling examples
  hosts: webservers
  become: true

  tasks:
    # Ignore errors and continue (don't fail the whole playbook)
    - name: Try to stop old service (may not exist)
      service:
        name: old-service
        state: stopped
      ignore_errors: true     # 👈 Continue even if this fails

    # Try a command and handle failure gracefully
    - name: Check if application is running
      command: pgrep myapp
      register: app_status
      failed_when: false      # 👈 Never mark this task as failed

    - name: Start application if not running
      command: /opt/myapp/start.sh
      when: app_status.rc != 0  # rc = return code, 0 = success

    # Block / Rescue / Always — like try/catch/finally
    - name: Deploy application with error handling
      block:                    # 👈 TRY block
        - name: Pull latest code
          git:
            repo: https://github.com/mycompany/myapp.git
            dest: /opt/myapp
            version: main

        - name: Restart application
          service:
            name: myapp
            state: restarted

      rescue:                   # 👈 CATCH block (runs if block fails)
        - name: Rollback to previous version
          command: /opt/myapp/rollback.sh

        - name: Alert team about failure
          mail:
            to: team@example.com
            subject: "Deployment Failed on {{ inventory_hostname }}"
            body: "Deployment failed! Rolled back to previous version."

      always:                   # 👈 FINALLY block (always runs)
        - name: Update deployment log
          lineinfile:
            path: /var/log/deployments.log
            line: "{{ ansible_date_time.iso8601 }} - Deployment attempted on {{ inventory_hostname }}"
            create: true
```

---

## 🔄 Ansible Workflow

```
Write Inventory → Write Playbook → Run Ad-hoc test → Run Playbook → Verify
  (hosts.ini)       (site.yml)    (ansible all -m    (ansible-     (ansible all
                                   ping)              playbook       -m command
                                                      site.yml)      -a "uptime")
```

### Complete Command Reference

```bash
# ─────────────────────────────────────────────
#  INVENTORY COMMANDS
# ─────────────────────────────────────────────

# List all hosts in inventory
ansible all --list-hosts

# List hosts in a specific group
ansible webservers --list-hosts

# Show inventory in JSON format
ansible-inventory --list

# Show inventory as a graph
ansible-inventory --graph

# ─────────────────────────────────────────────
#  AD-HOC COMMANDS (Quick one-off tasks)
# ─────────────────────────────────────────────

# Ping all servers
ansible all -m ping

# Run command on all servers
ansible all -m command -a "uptime"

# Run shell command (supports pipes and redirects)
ansible webservers -m shell -a "df -h | grep /dev/xvda"

# Copy file to servers
ansible all -m copy -a "src=/local/file dest=/remote/file"

# Install package
ansible webservers -m yum -a "name=nginx state=present" -b

# Restart service
ansible webservers -m service -a "name=nginx state=restarted" -b

# Gather facts about servers
ansible web1.example.com -m setup

# Gather specific facts
ansible all -m setup -a "filter=ansible_distribution*"

# ─────────────────────────────────────────────
#  PLAYBOOK COMMANDS
# ─────────────────────────────────────────────

# Run a playbook
ansible-playbook site.yml

# Dry run — shows what WOULD happen (no actual changes)
ansible-playbook site.yml --check

# Dry run with diff (shows file changes)
ansible-playbook site.yml --check --diff

# Run only specific tags
ansible-playbook site.yml --tags "install,configure"

# Skip specific tags
ansible-playbook site.yml --skip-tags "update"

# Run on specific hosts only
ansible-playbook site.yml --limit web1.example.com

# Run on specific group
ansible-playbook site.yml --limit webservers

# Increase verbosity (for debugging)
ansible-playbook site.yml -v       # Verbose
ansible-playbook site.yml -vv      # More verbose
ansible-playbook site.yml -vvv     # Very verbose (shows SSH details)
ansible-playbook site.yml -vvvv    # Connection debugging

# Run with extra variables
ansible-playbook site.yml -e "environment=staging nginx_port=8080"

# Start from a specific task
ansible-playbook site.yml --start-at-task "Deploy Nginx configuration"

# Step through tasks interactively (confirm each task)
ansible-playbook site.yml --step

# Run with vault password
ansible-playbook site.yml --ask-vault-pass

# Syntax check only
ansible-playbook site.yml --syntax-check

# ─────────────────────────────────────────────
#  GALAXY COMMANDS (Community roles)
# ─────────────────────────────────────────────

# Install a role from Ansible Galaxy
ansible-galaxy install geerlingguy.nginx

# Create a new role scaffold
ansible-galaxy init roles/my_new_role

# List installed roles
ansible-galaxy list

# Install roles from requirements file
ansible-galaxy install -r requirements.yml

# ─────────────────────────────────────────────
#  VAULT COMMANDS
# ─────────────────────────────────────────────

# Create new encrypted file
ansible-vault create secrets.yml

# Encrypt existing file
ansible-vault encrypt secrets.yml

# Decrypt file
ansible-vault decrypt secrets.yml

# View encrypted file without decrypting
ansible-vault view secrets.yml

# Edit encrypted file
ansible-vault edit secrets.yml

# Change vault password
ansible-vault rekey secrets.yml

# Encrypt a single string value
ansible-vault encrypt_string 'MySecretPassword' --name 'db_password'
```

---

## 🌍 Real-Life Examples

### Scenario 1: Zero-Downtime Application Deployment

```yaml
---
# Rolling update — update servers one at a time (zero downtime)
- name: Zero-downtime application deployment
  hosts: webservers
  become: true
  serial: 1           # 👈 Update ONE server at a time (or use "30%" for 30%)

  pre_tasks:
    # Remove server from load balancer BEFORE updating
    - name: Remove server from load balancer
      uri:
        url: "http://loadbalancer.example.com/api/disable"
        method: POST
        body_format: json
        body:
          server: "{{ inventory_hostname }}"
      delegate_to: localhost    # 👈 Run this on control node, not the target server

    # Wait a moment for existing connections to drain
    - name: Wait for connections to drain
      pause:
        seconds: 30

  tasks:
    - name: Pull latest application code
      git:
        repo: "https://github.com/mycompany/webapp.git"
        dest: /opt/webapp
        version: "{{ app_version | default('main') }}"
        force: yes

    - name: Install Python dependencies
      pip:
        requirements: /opt/webapp/requirements.txt
        virtualenv: /opt/webapp/venv

    - name: Run database migrations
      command: /opt/webapp/venv/bin/python manage.py migrate
      args:
        chdir: /opt/webapp
      run_once: true    # 👈 Only run on the FIRST server (one migration is enough)

    - name: Collect static files
      command: /opt/webapp/venv/bin/python manage.py collectstatic --noinput
      args:
        chdir: /opt/webapp

    - name: Restart application service
      service:
        name: webapp
        state: restarted

    - name: Wait for application to be healthy
      uri:
        url: "http://{{ ansible_host }}:8000/health"
        status_code: 200
      retries: 10         # 👈 Try 10 times
      delay: 5            # Wait 5 seconds between retries
      register: health_check
      until: health_check.status == 200

  post_tasks:
    # Add server back to load balancer AFTER successful update
    - name: Add server back to load balancer
      uri:
        url: "http://loadbalancer.example.com/api/enable"
        method: POST
        body_format: json
        body:
          server: "{{ inventory_hostname }}"
      delegate_to: localhost
```

---

### Scenario 2: Complete LAMP Stack Setup

```yaml
---
# Install Linux + Apache + MySQL + PHP (LAMP stack)
- name: Setup LAMP Stack
  hosts: webservers
  become: true

  vars:
    mysql_root_password: "{{ vault_mysql_root_password }}"  # From vault
    php_version: "8.1"
    app_db_name: "myappdb"
    app_db_user: "appuser"
    app_db_password: "{{ vault_app_db_password }}"          # From vault

  tasks:
    # ── APACHE ──────────────────────────────────────────────
    - name: Install Apache
      package:
        name: httpd
        state: present

    - name: Start and enable Apache
      service:
        name: httpd
        state: started
        enabled: true

    # ── PHP ─────────────────────────────────────────────────
    - name: Add PHP repository
      yum_repository:
        name: remi-php81
        description: Remi PHP 8.1 repo
        baseurl: https://rpms.remirepo.net/enterprise/8/php81/x86_64/
        gpgcheck: no
        enabled: yes

    - name: Install PHP and extensions
      package:
        name:
          - "php{{ php_version }}"
          - "php{{ php_version }}-mysqlnd"
          - "php{{ php_version }}-json"
          - "php{{ php_version }}-mbstring"
          - "php{{ php_version }}-curl"
          - "php{{ php_version }}-xml"
        state: present

    # ── MYSQL ────────────────────────────────────────────────
    - name: Install MySQL server
      package:
        name: mysql-server
        state: present

    - name: Start and enable MySQL
      service:
        name: mysqld
        state: started
        enabled: true

    - name: Set MySQL root password
      mysql_user:
        name: root
        password: "{{ mysql_root_password }}"
        login_unix_socket: /var/lib/mysql/mysql.sock
        state: present

    - name: Create application database
      mysql_db:
        name: "{{ app_db_name }}"
        state: present
        login_user: root
        login_password: "{{ mysql_root_password }}"

    - name: Create application database user
      mysql_user:
        name: "{{ app_db_user }}"
        password: "{{ app_db_password }}"
        priv: "{{ app_db_name }}.*:ALL"
        login_user: root
        login_password: "{{ mysql_root_password }}"
        state: present

    # ── FIREWALL ─────────────────────────────────────────────
    - name: Open HTTP port in firewall
      firewalld:
        service: http
        permanent: true
        state: enabled

    - name: Open HTTPS port in firewall
      firewalld:
        service: https
        permanent: true
        state: enabled

    - name: Reload firewall
      service:
        name: firewalld
        state: reloaded
```

---

### Scenario 3: Dynamic Inventory with AWS EC2

```bash
# Install AWS collection
ansible-galaxy collection install amazon.aws

# Install boto3 (Python AWS SDK)
pip3 install boto3
```

```yaml
# aws_inventory.yml — Dynamic inventory config
plugin: amazon.aws.aws_ec2
regions:
  - us-east-1
  - us-west-2

# Group servers by their tags
keyed_groups:
  - prefix: tag
    key: tags

# Group by instance type
  - prefix: instance_type
    key: instance_type

# Group by environment tag
  - key: tags['Environment']
    prefix: env

filters:
  # Only show running instances
  instance-state-name: running
  # Only instances with the Project tag
  "tag:Project": MyApp

hostnames:
  - private-ip-address    # Use private IP for SSH

compose:
  # Create ansible_host variable from private IP
  ansible_host: private_ip_address
```

```bash
# Use dynamic inventory
ansible-playbook site.yml -i aws_inventory.yml

# List all EC2 hosts discovered
ansible-inventory -i aws_inventory.yml --list

# Run on servers with specific tag
ansible-playbook site.yml -i aws_inventory.yml --limit "tag_Environment_production"
```

---

### Scenario 4: CI/CD Pipeline Integration (Jenkins)

```groovy
// Jenkinsfile — Jenkins Pipeline using Ansible
pipeline {
    agent any

    environment {
        ANSIBLE_HOST_KEY_CHECKING = 'False'
        VAULT_PASSWORD = credentials('ansible-vault-password')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/mycompany/ansible-playbooks.git'
            }
        }

        stage('Syntax Check') {
            steps {
                sh 'ansible-playbook site.yml --syntax-check'
            }
        }

        stage('Lint') {
            steps {
                sh 'ansible-lint site.yml'
            }
        }

        stage('Deploy to Staging') {
            steps {
                sh """
                    ansible-playbook site.yml \
                        -i inventories/staging/hosts.ini \
                        --vault-password-file ${VAULT_PASSWORD} \
                        -e "app_version=${BUILD_NUMBER}" \
                        --tags deploy
                """
            }
        }

        stage('Integration Tests') {
            steps {
                sh 'ansible-playbook tests/integration.yml -i inventories/staging/hosts.ini'
            }
        }

        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            input {
                message "Deploy to Production?"
                ok "Yes, Deploy!"
            }
            steps {
                sh """
                    ansible-playbook site.yml \
                        -i inventories/production/hosts.ini \
                        --vault-password-file ${VAULT_PASSWORD} \
                        -e "app_version=${BUILD_NUMBER}" \
                        --tags deploy
                """
            }
        }
    }

    post {
        failure {
            slackSend channel: '#devops-alerts',
                      message: "Ansible deployment FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        }
        success {
            slackSend channel: '#deployments',
                      message: "Deployment SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        }
    }
}
```

---

### Scenario 5: Docker and Container Management

```yaml
---
- name: Docker infrastructure setup
  hosts: docker_hosts
  become: true

  tasks:
    - name: Install Docker dependencies
      package:
        name:
          - yum-utils
          - device-mapper-persistent-data
          - lvm2
        state: present

    - name: Add Docker repository
      yum_repository:
        name: docker-ce
        description: Docker CE Stable
        baseurl: https://download.docker.com/linux/centos/docker-ce.repo
        enabled: yes
        gpgcheck: yes

    - name: Install Docker CE
      package:
        name:
          - docker-ce
          - docker-ce-cli
          - containerd.io
          - docker-compose-plugin
        state: present

    - name: Start and enable Docker
      service:
        name: docker
        state: started
        enabled: true

    - name: Add users to docker group
      user:
        name: "{{ item }}"
        groups: docker
        append: true
      loop:
        - ec2-user
        - jenkins

    - name: Pull application Docker image
      community.docker.docker_image:
        name: "mycompany/webapp:{{ app_version | default('latest') }}"
        source: pull

    - name: Run application container
      community.docker.docker_container:
        name: webapp
        image: "mycompany/webapp:{{ app_version | default('latest') }}"
        state: started
        restart_policy: unless-stopped
        ports:
          - "80:8000"           # host_port:container_port
        env:
          DATABASE_URL: "postgresql://{{ db_user }}:{{ db_pass }}@{{ db_host }}/{{ db_name }}"
          SECRET_KEY: "{{ vault_secret_key }}"
          DEBUG: "false"
        volumes:
          - "/opt/webapp/media:/app/media"
          - "/opt/webapp/logs:/app/logs"
        healthcheck:
          test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
          interval: 30s
          timeout: 10s
          retries: 3
```

---

## 🎯 Interview Questions & Answers

### 🟢 Basic Level

---

**Q1: What is Ansible and what are its key features?**

> **Answer:**
> Ansible is an open-source **automation tool** used for configuration management, application deployment, and task automation. It's developed by Red Hat.
>
> **Key Features:**
> - **Agentless** — No software installation needed on managed nodes (only SSH + Python)
> - **Idempotent** — Running the same playbook multiple times = same result
> - **Simple YAML syntax** — Human-readable, no programming expertise needed
> - **Push-based** — Control node pushes configs to managed nodes
> - **Large module library** — 3000+ built-in modules
> - **Multi-platform** — Linux, Windows, network devices, cloud
>
> **Real-Life Example:** A team of 5 DevOps engineers needs to configure 200 servers. Without Ansible, each engineer manually SSHs into servers. With Ansible, they write one playbook and run it — all 200 servers configured in minutes!

---

**Q2: What is the difference between Ad-hoc commands and Playbooks?**

> **Answer:**
>
> | Ad-hoc Commands | Playbooks |
> |----------------|-----------|
> | One-time, quick tasks | Complex, repeatable tasks |
> | Single module, one line | Multiple tasks in YAML file |
> | Not reusable | Reusable and version-controlled |
> | Like a quick phone call | Like a detailed project plan |
> | `ansible all -m ping` | `ansible-playbook site.yml` |
>
> **When to use Ad-hoc:**
> ```bash
> # Quick check — is the server up?
> ansible all -m ping
>
> # Quick fix — restart nginx on all web servers NOW
> ansible webservers -m service -a "name=nginx state=restarted" -b
> ```
>
> **When to use Playbook:**
> ```bash
> # Full application deployment with multiple steps
> ansible-playbook deploy_webapp.yml
> ```

---

**Q3: What is Idempotency in Ansible? Why is it important?**

> **Answer:**
> Idempotency means **running the same operation multiple times produces the same result** — Ansible only makes changes when something actually needs to change.
>
> **Why important:**
> - Safe to run playbooks **repeatedly** (in cron, CI/CD)
> - No accidental **duplicate actions** (like creating the same user twice)
> - Playbooks serve as **documentation of desired state**
>
> ```yaml
> # This task is idempotent:
> - name: Install Nginx
>   package:
>     name: nginx
>     state: present    # "present" = "make sure it exists"
>
> # Run 1: Nginx not installed → Ansible installs it → CHANGED
> # Run 2: Nginx already installed → Ansible skips → OK (no change)
> # Run 3: Same → OK (no change)
> ```
>
> **Not Idempotent (avoid this):**
> ```yaml
> # BAD — running twice creates duplicate entries!
> - name: Add config line
>   command: echo "MaxConnections=100" >> /etc/myapp.conf
>   # Run 1: adds the line ✅
>   # Run 2: adds DUPLICATE line ❌
>
> # GOOD — Idempotent version
> - name: Add config line
>   lineinfile:
>     path: /etc/myapp.conf
>     line: "MaxConnections=100"
>     state: present
>   # Checks if line exists first, only adds if missing ✅
> ```

---

**Q4: What are Ansible Facts? How do you use them?**

> **Answer:**
> Ansible Facts are **automatically collected information** about managed nodes — like OS type, IP address, CPU, memory, hostname, etc.
>
> Ansible gathers them automatically at the start of each play using the `setup` module.
>
> ```bash
> # See all facts for a server
> ansible web1.example.com -m setup
>
> # Filter specific facts
> ansible web1.example.com -m setup -a "filter=ansible_distribution*"
> ```
>
> ```yaml
> # Use facts in playbooks with {{ ansible_* }}
> - name: Show system info
>   debug:
>     msg: |
>       Hostname: {{ ansible_hostname }}
>       OS: {{ ansible_distribution }} {{ ansible_distribution_version }}
>       IP: {{ ansible_default_ipv4.address }}
>       CPU Cores: {{ ansible_processor_vcpus }}
>       RAM: {{ ansible_memtotal_mb }} MB
>
> # Use facts in conditions
> - name: Install package for Ubuntu
>   apt:
>     name: nginx
>   when: ansible_distribution == "Ubuntu"
>
> - name: Install package for CentOS
>   yum:
>     name: nginx
>   when: ansible_distribution == "CentOS"
> ```
>
> **Disable fact gathering** (speeds up playbook when facts not needed):
> ```yaml
> - name: Quick playbook
>   hosts: all
>   gather_facts: false    # Skip fact collection
>   tasks:
>     - name: Just restart a service
>       service:
>         name: nginx
>         state: restarted
> ```

---

**Q5: What is the difference between `copy` and `template` modules?**

> **Answer:**
>
> | `copy` | `template` |
> |--------|-----------|
> | Copies file **as-is** (static) | Processes Jinja2 variables (dynamic) |
> | No variable substitution | Variables like `{{ var }}` are replaced |
> | Use for static files | Use for config files with variables |
> | Source: any file | Source: `.j2` file |
>
> ```yaml
> # copy — copies file exactly as it is
> - name: Copy static HTML file
>   copy:
>     src: files/index.html      # Static file
>     dest: /var/www/html/index.html
>
> # template — fills in variables before copying
> - name: Deploy Nginx config with variables
>   template:
>     src: templates/nginx.conf.j2   # Has {{ variables }}
>     dest: /etc/nginx/nginx.conf
> ```
>
> **Template file example (`nginx.conf.j2`):**
> ```nginx
> server {
>     listen {{ nginx_port }};           # Replaced with actual value
>     server_name {{ ansible_hostname }}; # Replaced with real hostname
> }
> ```

---

### 🟡 Intermediate Level

---

**Q6: What is Ansible Vault and when should you use it?**

> **Answer:**
> Ansible Vault is a built-in encryption tool that lets you **encrypt sensitive data** (passwords, API keys, certificates) so it can safely be stored in Git.
>
> **When to use it:**
> - Database passwords
> - API keys and tokens
> - SSL private keys
> - Any secret that shouldn't be in plain text
>
> ```bash
> # Encrypt an entire file
> ansible-vault encrypt group_vars/all/secrets.yml
>
> # Encrypt just a single string (useful for inline secrets)
> ansible-vault encrypt_string 'MyPassword123' --name 'db_password'
> # Output:
> # db_password: !vault |
> #   $ANSIBLE_VAULT;1.1;AES256
> #   66386439...
> ```
>
> ```yaml
> # Use in playbook normally — Ansible decrypts automatically at runtime
> - name: Configure database
>   mysql_user:
>     password: "{{ db_password }}"   # Vault-encrypted variable
> ```
>
> **Best Practices:**
> ```bash
> # Use separate vault files, not mixed with regular vars
> group_vars/
>   all/
>     vars.yml      # Regular variables (commit to Git)
>     secrets.yml   # Vault-encrypted (commit to Git — encrypted!)
>
> # Use vault password file in CI/CD (never hardcode password)
> ansible-playbook site.yml --vault-password-file ~/.vault_pass
> ```

---

**Q7: Explain `delegate_to` and `run_once` in Ansible.**

> **Answer:**
>
> **`delegate_to`** — Run a task on a **different host** than the current target.
>
> ```yaml
> - name: Deploy application
>   hosts: webservers      # Running on web servers
>   tasks:
>     # But THIS task runs on the load balancer (different host!)
>     - name: Disable server in load balancer
>       uri:
>         url: "http://lb.example.com/disable/{{ inventory_hostname }}"
>         method: POST
>       delegate_to: lb.example.com   # 👈 Run on load balancer
>
>     # This runs on localhost (control node)
>     - name: Log deployment to local file
>       copy:
>         content: "Deployed to {{ inventory_hostname }}"
>         dest: /tmp/deployment.log
>       delegate_to: localhost   # 👈 Run on control node
> ```
>
> **`run_once`** — Run a task only on the **first server** in the group, once.
>
> ```yaml
> - name: Database migration
>   hosts: webservers   # All web servers, but migration only needs to run once
>   tasks:
>     - name: Run DB migration
>       command: python manage.py migrate
>       run_once: true    # 👈 Only runs on web-server-1, not all servers
>
>     - name: Deploy app code
>       git:
>         repo: https://github.com/myapp.git
>         dest: /opt/app
>       # No run_once — deploys to ALL web servers
> ```

---

**Q8: What is the difference between `when` and `tags` for controlling task execution?**

> **Answer:**
>
> | `when` | `tags` |
> |--------|--------|
> | **Conditional** — based on variables/facts | **Selective** — based on labels |
> | Evaluated at runtime | Specified at runtime via CLI |
> | Task still runs, checks condition | Task is skipped entirely if tag not specified |
> | `when: ansible_os_family == "RedHat"` | `--tags install` |
>
> ```yaml
> tasks:
>   # when — runs conditionally based on OS
>   - name: Install on RedHat
>     yum:
>       name: nginx
>     when: ansible_os_family == "RedHat"   # Condition checked at runtime
>
>   - name: Install on Debian
>     apt:
>       name: nginx
>     when: ansible_distribution == "Ubuntu"
>
>   # tags — run selectively based on label
>   - name: Install packages
>     package:
>       name: nginx
>     tags:
>       - install
>       - packages
>
>   - name: Configure Nginx
>     template:
>       src: nginx.conf.j2
>       dest: /etc/nginx/nginx.conf
>     tags:
>       - configure
>       - never    # 'never' tag — only runs if explicitly called!
> ```
>
> ```bash
> # Run only tagged tasks
> ansible-playbook site.yml --tags install
>
> # Combined — conditional + tag filtering
> ansible-playbook site.yml --tags configure --limit webservers
> ```

---

**Q9: How does Ansible handle parallelism? What is `forks` and `serial`?**

> **Answer:**
>
> **`forks`** — How many servers Ansible manages **simultaneously** (default: 5).
>
> ```ini
> # ansible.cfg
> [defaults]
> forks = 20   # Manage 20 servers at the same time
> ```
>
> ```bash
> # Override at runtime
> ansible-playbook site.yml -f 20
> ```
>
> **`serial`** — How many servers to process per **batch** in a play (for rolling updates).
>
> ```yaml
> - name: Rolling update
>   hosts: webservers  # 10 web servers
>   serial: 2          # Update 2 servers at a time (rolling update)
>
>   # Sequence: servers 1-2 → complete → servers 3-4 → complete → ...
>
>   tasks:
>     - name: Update application
>       # ...
> ```
>
> ```yaml
> # serial with percentage
> serial: "30%"    # Update 30% of servers at a time
>
> # serial with progressive batches — start small, grow bigger
> serial:
>   - 1      # First: update 1 server (canary)
>   - 5      # Then: update 5 servers
>   - "50%"  # Then: update 50% of remaining
> ```
>
> > 🏭 **Real-Life Analogy:** `forks` is like how many **checkout counters** are open in a supermarket simultaneously. `serial` is like updating stores in a chain — first update 1 store (test), then 5 stores, then all the rest.

---

**Q10: What is Ansible Galaxy and how do you use it?**

> **Answer:**
> Ansible Galaxy is the **community hub** for sharing and downloading Ansible roles and collections — like npm for Node.js or pip for Python.
>
> ```bash
> # Search for roles
> ansible-galaxy search nginx
>
> # Install a popular community role
> ansible-galaxy install geerlingguy.nginx
> ansible-galaxy install geerlingguy.mysql
> ansible-galaxy install geerlingguy.docker
>
> # Install a specific version
> ansible-galaxy install geerlingguy.nginx,2.8.0
>
> # Install from requirements file (best practice for teams)
> ansible-galaxy install -r requirements.yml
> ```
>
> ```yaml
> # requirements.yml — list all required roles
> ---
> roles:
>   - name: geerlingguy.nginx
>     version: "3.1.0"
>
>   - name: geerlingguy.mysql
>     version: "4.3.2"
>
>   - name: geerlingguy.docker
>     version: "6.1.0"
>
> collections:
>   - name: amazon.aws
>     version: "5.0.0"
>
>   - name: community.docker
>     version: "3.0.0"
> ```
>
> ```yaml
> # Use installed Galaxy role in playbook
> - name: Setup web server using Galaxy role
>   hosts: webservers
>   become: true
>   roles:
>     - role: geerlingguy.nginx    # Community role
>       vars:
>         nginx_vhosts:
>           - listen: "80"
>             server_name: "example.com"
>             root: "/var/www/html"
> ```

---

### 🔴 Advanced Level

---

**Q11: How do you implement Ansible in a multi-environment setup?**

> **Answer:**
> Use **separate inventory directories** per environment with shared roles.
>
> ```
> ansible-project/
> ├── inventories/
> │   ├── dev/
> │   │   ├── hosts.ini          # Dev servers
> │   │   └── group_vars/
> │   │       ├── all.yml        # Dev-specific variables
> │   │       └── webservers.yml
> │   ├── staging/
> │   │   ├── hosts.ini
> │   │   └── group_vars/
> │   │       └── all.yml
> │   └── production/
> │       ├── hosts.ini
> │       └── group_vars/
> │           └── all.yml
> ├── roles/
> │   ├── common/
> │   ├── webserver/
> │   └── database/
> └── site.yml
> ```
>
> ```ini
> # inventories/dev/hosts.ini
> [webservers]
> dev-web1.example.com
>
> # inventories/production/hosts.ini
> [webservers]
> prod-web1.example.com
> prod-web2.example.com
> prod-web3.example.com
> ```
>
> ```yaml
> # inventories/dev/group_vars/all.yml
> environment: dev
> instance_type: t2.micro
> replicas: 1
> debug_mode: true
>
> # inventories/production/group_vars/all.yml
> environment: production
> instance_type: t3.large
> replicas: 3
> debug_mode: false
> ```
>
> ```bash
> # Deploy to dev
> ansible-playbook site.yml -i inventories/dev/
>
> # Deploy to production
> ansible-playbook site.yml -i inventories/production/
> ```

---

**Q12: What is `lookup` plugin in Ansible? Give real examples.**

> **Answer:**
> Lookup plugins allow Ansible to **fetch data from external sources** — files, environment variables, databases, AWS SSM, etc.
>
> ```yaml
> tasks:
>   # Read content of a local file
>   - name: Add SSH public key
>     authorized_key:
>       user: deploy
>       key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
>
>   # Read environment variable from control node
>   - name: Use env variable
>     debug:
>       msg: "AWS Region: {{ lookup('env', 'AWS_DEFAULT_REGION') }}"
>
>   # Read from AWS Systems Manager Parameter Store
>   - name: Get secret from AWS SSM
>     debug:
>       msg: "DB Password: {{ lookup('aws_ssm', '/myapp/prod/db_password', region='us-east-1') }}"
>
>   # Read a CSV file
>   - name: Process CSV data
>     debug:
>       msg: "{{ item }}"
>     loop: "{{ lookup('csvfile', 'alice file=users.csv col=1', wantlist=True) }}"
>
>   # Get password from HashiCorp Vault
>   - name: Get secret from HashiCorp Vault
>     debug:
>       msg: "{{ lookup('hashi_vault', 'secret=secret/myapp/db_password:value') }}"
>
>   # Generate or retrieve a random password (saves to file for reuse)
>   - name: Generate random password
>     debug:
>       msg: "{{ lookup('password', '/tmp/myapp_pass length=16 chars=ascii_letters,digits') }}"
> ```

---

**Q13: How do you optimize Ansible playbook performance?**

> **Answer:**
>
> ```ini
> # 1. ansible.cfg optimizations
> [defaults]
> forks = 50               # Increase parallel connections
> gathering = smart        # Cache facts (don't regather if cached)
> fact_caching = jsonfile
> fact_caching_connection = /tmp/ansible_facts
> fact_caching_timeout = 86400  # Cache facts for 24 hours
>
> [ssh_connection]
> pipelining = True        # Reduce SSH operations (big speed boost!)
> ssh_args = -o ControlMaster=auto -o ControlPersist=60s -o ServerAliveInterval=30
> # ControlMaster reuses SSH connections — huge performance boost!
> ```
>
> ```yaml
> # 2. Disable fact gathering when not needed
> - name: Quick service restart
>   hosts: webservers
>   gather_facts: false    # Skip fact gathering = faster!
>   tasks:
>     - service:
>         name: nginx
>         state: restarted
>
> # 3. Use package module with list (one call vs multiple)
> # SLOW — 3 separate package manager calls
> - package:
>     name: vim
>     state: present
> - package:
>     name: git
>     state: present
> - package:
>     name: curl
>     state: present
>
> # FAST — single package manager call
> - package:
>     name:
>       - vim
>       - git
>       - curl
>     state: present
>
> # 4. Use async for long-running tasks
> - name: Long-running backup task
>   command: /opt/scripts/backup.sh
>   async: 600       # Run for max 600 seconds (don't wait)
>   poll: 0          # Fire and forget
>   register: backup_job
>
> # Check back on the job later
> - name: Check backup status
>   async_status:
>     jid: "{{ backup_job.ansible_job_id }}"
>   register: job_result
>   until: job_result.finished
>   retries: 30
>   delay: 20
> ```

---

**Q14: What is the difference between `include_tasks` and `import_tasks`?**

> **Answer:**
>
> | `import_tasks` | `include_tasks` |
> |---------------|----------------|
> | **Static** — loaded at parse time | **Dynamic** — loaded at runtime |
> | Processed before playbook runs | Processed when reached during play |
> | Tags applied to all tasks inside | Tags only on the include statement |
> | Cannot use variables in filename | Can use variables in filename |
> | Better for performance | Needed for conditional/loop includes |
>
> ```yaml
> # import_tasks — static, filename cannot use variables
> - import_tasks: tasks/install.yml     # ✅ Works
> - import_tasks: "tasks/{{ env }}.yml" # ❌ Cannot use variables
>
> # include_tasks — dynamic, can use variables
> - include_tasks: "tasks/{{ env }}.yml"  # ✅ Works!
> - include_tasks: tasks/install.yml       # ✅ Also works
>
> # Practical difference with when:
>
> # import_tasks with when — condition applied to EACH task inside
> - import_tasks: tasks/install.yml
>   when: ansible_os_family == "RedHat"
>   # Every task in install.yml gets this 'when' condition
>
> # include_tasks with when — condition on the INCLUDE itself
> - include_tasks: tasks/install.yml
>   when: ansible_os_family == "RedHat"
>   # If condition is False, NONE of the tasks are even loaded
>
> # include_tasks with loop — import_tasks doesn't support this!
> - include_tasks: "tasks/configure_{{ item }}.yml"
>   loop:
>     - nginx
>     - mysql
>     - redis
> ```

---

**Q15: How do you test Ansible playbooks? What tools do you use?**

> **Answer:**
>
> **Testing Strategy (Layered approach):**
>
> ```bash
> # Layer 1: Syntax Check
> ansible-playbook site.yml --syntax-check
>
> # Layer 2: Lint (style and best practices)
> pip install ansible-lint
> ansible-lint site.yml
>
> # Layer 3: Dry Run (no actual changes)
> ansible-playbook site.yml --check --diff
>
> # Layer 4: Test in staging environment first
> ansible-playbook site.yml -i inventories/staging/
> ```
>
> **Molecule — The standard tool for role testing:**
> ```bash
> # Install Molecule with Docker driver
> pip install molecule molecule-docker
>
> # Initialize Molecule in a role
> cd roles/webserver
> molecule init scenario
>
> # Project structure after molecule init
> roles/webserver/
> └── molecule/
>     └── default/
>         ├── molecule.yml     # Molecule config
>         ├── converge.yml     # Playbook to test
>         └── verify.yml      # Tests to verify
> ```
>
> ```yaml
> # molecule/default/molecule.yml
> driver:
>   name: docker          # Use Docker containers as test instances
>
> platforms:
>   - name: ubuntu-test
>     image: ubuntu:22.04
>   - name: centos-test
>     image: centos:8
>
> provisioner:
>   name: ansible
>
> verifier:
>   name: ansible         # Use Ansible for verification
> ```
>
> ```yaml
> # molecule/default/verify.yml — Your tests!
> ---
> - name: Verify webserver role
>   hosts: all
>   gather_facts: false
>
>   tasks:
>     - name: Check Nginx is installed
>       package:
>         name: nginx
>         state: present
>       check_mode: true
>       register: result
>       failed_when: result.changed   # Fail if nginx wasn't installed
>
>     - name: Verify Nginx service is running
>       service:
>         name: nginx
>         state: started
>       check_mode: true
>       register: svc_result
>       failed_when: svc_result.changed
>
>     - name: Verify Nginx responds on port 80
>       uri:
>         url: "http://localhost:80"
>         status_code: 200
> ```
>
> ```bash
> # Run full Molecule test cycle
> molecule test
>
> # Individual stages
> molecule create    # Create Docker containers
> molecule converge  # Apply the role
> molecule verify    # Run tests
> molecule destroy   # Clean up containers
>
> # Keep containers running for debugging
> molecule converge
> molecule login     # SSH into test container
> ```

---

## 🎁 Bonus Tips

### 📌 Ansible Best Practices Cheat Sheet

```
✅ DO's                                    ❌ DON'Ts
──────────────────────────────────────     ──────────────────────────────────────
Use roles for reusable code                Don't put everything in one big playbook
Use group_vars and host_vars               Don't hardcode values in tasks
Use Ansible Vault for secrets              Don't store plain text secrets in Git
Use meaningful names for tasks             Don't use cryptic task names
Test with --check --diff first             Don't run apply directly in production
Use tags for selective execution           Don't run entire playbooks when unnecessary
Use handlers for service restarts          Don't restart services in tasks directly
Keep playbooks idempotent                  Don't use shell/command when module exists
Use ansible-lint in CI/CD pipeline         Don't ignore linting warnings
Version pin your Galaxy roles              Don't use unpinned community roles
Use FQCN for modules (e.g., ansible.builtin.copy)  Don't use short module names (deprecated)
```

---

### 🔑 Quick Reference Card

```bash
# Inventory
ansible all --list-hosts              # List all hosts
ansible-inventory --graph             # Show inventory graph

# Ad-hoc
ansible all -m ping                   # Test connectivity
ansible all -m setup                  # Gather facts
ansible webservers -m command -a "uptime"  # Run command

# Playbook
ansible-playbook site.yml             # Run playbook
ansible-playbook site.yml --check     # Dry run
ansible-playbook site.yml --diff      # Show changes
ansible-playbook site.yml -v          # Verbose output
ansible-playbook site.yml --tags X    # Run tagged tasks
ansible-playbook site.yml --limit X   # Run on specific host

# Vault
ansible-vault create secrets.yml      # Create encrypted file
ansible-vault encrypt secrets.yml     # Encrypt file
ansible-vault view secrets.yml        # View encrypted file
ansible-vault edit secrets.yml        # Edit encrypted file

# Galaxy
ansible-galaxy install role_name      # Install role
ansible-galaxy init roles/my_role     # Create role scaffold
ansible-galaxy install -r requirements.yml  # Install from file
```

---

## 📚 Learning Resources

| Resource | Description |
|----------|-------------|
| [Official Ansible Docs](https://docs.ansible.com) | Complete reference documentation |
| [Ansible Galaxy](https://galaxy.ansible.com) | Community roles and collections |
| [Ansible GitHub](https://github.com/ansible/ansible) | Source code and issues |
| [Jeff Geerling's Blog](https://www.jeffgeerling.com) | Best Ansible tutorials and books |
| [Molecule Docs](https://molecule.readthedocs.io) | Role testing framework |
| [Ansible Lint](https://ansible-lint.readthedocs.io) | Best practices linter |
| [KodeKloud](https://kodekloud.com) | Hands-on Ansible labs |
| [Ansible for DevOps Book](https://www.ansiblefordevops.com) | Best book for beginners |

---

> 💡 **Pro Tip:** The fastest way to learn Ansible is to **automate something you do manually every day**. Start by automating your local dev environment setup, then move to automating cloud server configuration. Break things in a test environment — that's where the real learning happens!

---

*Happy Automating! 🤖*
