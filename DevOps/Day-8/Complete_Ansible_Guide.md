
# 🚀 Complete Ansible Guide for AWS - All-in-One

## 📋 Table of Contents
1. [Prerequisites & Lab Objectives](#prerequisites--lab-objectives)
2. [AWS EC2 Setup](#aws-ec2-setup)
3. [Ansible Installation on Amazon Linux](#ansible-installation-on-amazon-linux)
4. [Passwordless SSH Key Generation & Setup](#passwordless-ssh-key-generation--setup)
5. [Ansible Configuration](#ansible-configuration)
6. [Complete Ad-hoc Commands Reference](#complete-ad-hoc-commands-reference)
7. [Simple Ansible Project - Web Server](#simple-ansible-project---web-server)
8. [Configuration Files & Templates](#configuration-files--templates)
9. [Quick Setup Script](#quick-setup-script)
10. [Best Practices](#best-practices)
11. [Troubleshooting Guide](#troubleshooting-guide)
12. [Assessment & Lab Exercises](#assessment--lab-exercises)

---

## Prerequisites & Lab Objectives

### 🎯 Learning Objectives
By the end of this lab, students will be able to:
- Set up Ansible infrastructure on AWS EC2 instances
- Configure passwordless SSH authentication
- Execute comprehensive Ansible ad-hoc commands
- Create and run Ansible playbooks with roles
- Deploy a complete web server using Ansible automation
- Implement best practices for configuration management

### 📋 Prerequisites
- [ ] AWS Account with EC2 permissions
- [ ] Basic Linux command line knowledge
- [ ] SSH client installed (PuTTY, Terminal, or VS Code)
- [ ] Text editor familiarity (nano, vim, or VS Code)
- [ ] Understanding of YAML syntax (basic)

---

## AWS EC2 Setup

### Step 1: Launch EC2 Instances

#### 1.1 Control Node (Ansible Master)
```
AMI: Amazon Linux 2023 AMI
Instance Type: t2.micro (free tier eligible)
Key Pair: Create new or use existing
Storage: 8 GB gp3 (default)
```

#### 1.2 Managed Nodes (Target Servers)
```
Launch 2-3 instances with same configuration
AMI: Amazon Linux 2023 AMI
Instance Type: t2.micro
Key Pair: Use SAME key pair as control node
Storage: 8 GB gp3 (default)
```

### Step 2: Security Group Configuration

Create security group with following inbound rules:
```
Type        Protocol    Port Range    Source          Description
SSH         TCP         22           0.0.0.0/0       SSH access
HTTP        TCP         80           0.0.0.0/0       Web server
HTTPS       TCP         443          0.0.0.0/0       Secure web server
Custom TCP  TCP         8080         0.0.0.0/0       Alternative web port
```

### Step 3: Instance Network Details
```
VPC: Default VPC
Subnet: Any public subnet
Auto-assign Public IP: Enable
```

---

## Ansible Installation on Amazon Linux

### Method 1: Using DNF Package Manager (Recommended for AL2023)

```bash
# Connect to your control node
ssh -i your-key.pem ec2-user@your-control-node-ip

# Update system packages
sudo dnf update -y

# Install Ansible and dependencies
sudo dnf install -y ansible python3-pip git tree wget curl vim

# Verify installation
ansible --version
python3 --version

# Expected output should show Ansible version 2.14+ or newer
```

### Method 2: Using Python PIP (Alternative)

```bash
# Update system
sudo dnf update -y

# Install Python3 and pip
sudo dnf install -y python3 python3-pip python3-devel

# Install Ansible using pip
pip3 install ansible --user

# Add to PATH permanently
echo 'export PATH=$PATH:~/.local/bin' >> ~/.bashrc
source ~/.bashrc

# Verify installation
ansible --version
```

### Method 3: Using Amazon Linux Extras (For Amazon Linux 2)

```bash
# Only for Amazon Linux 2 (not AL2023)
sudo amazon-linux-extras install ansible2 -y

# Install additional packages
sudo yum install -y python3-pip git tree
```

### Verification Commands

```bash
# Check Ansible installation
ansible --version
ansible-config dump --only-changed

# Check Python version
python3 --version

# Check pip packages
pip3 list | grep ansible
```

---

## Passwordless SSH Key Generation & Setup

### Step 1: Generate SSH Key Pair on Control Node

```bash
# Generate RSA key pair (4096 bits for better security)
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -C "ansible-lab-$(date +%Y%m%d)"

# Press Enter for all prompts to use defaults (no passphrase for lab)

# Verify key generation
ls -la ~/.ssh/
# You should see: id_rsa (private key) and id_rsa.pub (public key)

# Set correct permissions
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub

# Display public key (copy this for distribution)
cat ~/.ssh/id_rsa.pub
```

### Step 2: Distribute Public Key to Managed Nodes

#### Method 1: Using ssh-copy-id (Recommended)

```bash
# Copy public key to each managed node
ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@MANAGED-NODE-IP

# Example for multiple nodes:
ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@172.31.45.67
ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@172.31.45.68
ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@172.31.45.69

# You'll be prompted for the original key file password
```

#### Method 2: Manual Copy (When ssh-copy-id fails)

```bash
# First, copy your public key content
cat ~/.ssh/id_rsa.pub

# SSH to each managed node using original key
ssh -i your-original-key.pem ec2-user@MANAGED-NODE-IP

# On each managed node, create .ssh directory and add public key
mkdir -p ~/.ssh
echo "PASTE_YOUR_PUBLIC_KEY_HERE" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh

# Exit and repeat for all managed nodes
exit
```

#### Method 3: Using Ansible Ad-hoc Command (Advanced)

```bash
# Create temporary inventory with original key authentication
cat > temp_inventory << EOF
[managed_nodes]
172.31.45.67 ansible_ssh_private_key_file=~/your-original-key.pem ansible_user=ec2-user
172.31.45.68 ansible_ssh_private_key_file=~/your-original-key.pem ansible_user=ec2-user
172.31.45.69 ansible_ssh_private_key_file=~/your-original-key.pem ansible_user=ec2-user
EOF

# Copy SSH key using Ansible (one command for all nodes)
ansible managed_nodes -i temp_inventory -m authorized_key -a "user=ec2-user key='{{ lookup('file', '~/.ssh/id_rsa.pub') }}'"

# Clean up temporary file
rm temp_inventory
```

### Step 3: Test Passwordless Connection

```bash
# Test connection to each managed node
ssh ec2-user@MANAGED-NODE-IP-1
ssh ec2-user@MANAGED-NODE-IP-2
ssh ec2-user@MANAGED-NODE-IP-3

# Should connect without prompting for password
# Type 'exit' to disconnect from each
```

---

## Ansible Configuration

### Step 1: Create Project Directory Structure

```bash
# Create main project directory
mkdir -p ~/ansible-lab
cd ~/ansible-lab

# Create organized directory structure
mkdir -p {playbooks,roles,group_vars,host_vars,files,templates}
mkdir -p roles/webserver/{tasks,handlers,templates,files,vars,defaults,meta}
mkdir -p roles/database/{tasks,handlers,templates,files,vars,defaults,meta}

# Verify structure
tree . || ls -la
```

### Step 2: Create Ansible Configuration File (ansible.cfg)

```bash
cat > ansible.cfg << 'EOF'
[defaults]
# Basic Configuration
inventory = inventory
remote_user = ec2-user
private_key_file = ~/.ssh/id_rsa
host_key_checking = False
timeout = 30
gather_facts = True
forks = 5
retry_files_enabled = False

# Output Configuration
stdout_callback = yaml
bin_ansible_callbacks = True
display_skipped_hosts = False
display_ok_hosts = True

# Logging
log_path = ./ansible.log

# SSH Configuration
[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s -o UserKnownHostsFile=/dev/null -o IdentitiesOnly=yes
pipelining = True

# Privilege Escalation
[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False

# Inventory Configuration
[inventory]
enable_plugins = ini, yaml, auto

# Colors (optional but helpful)
[colors]
highlight = white
verbose = blue
warn = bright purple
error = red
debug = dark gray
deprecation = purple
skip = cyan
unreachable = red
ok = green
changed = yellow
diff_add = green
diff_remove = red
diff_lines = cyan
EOF
```

### Step 3: Create Comprehensive Inventory File

```bash
cat > inventory << 'EOF'
# Ansible Inventory File - Replace IPs with your actual EC2 instance IPs

# Web Servers Group
[webservers]
web1 ansible_host=172.31.45.67 server_id=1 environment=production
web2 ansible_host=172.31.45.68 server_id=2 environment=staging
web3 ansible_host=172.31.45.69 server_id=3 environment=development

# Database Servers Group
[databases]
db1 ansible_host=172.31.45.70 server_id=10 environment=production
db2 ansible_host=172.31.45.71 server_id=11 environment=staging

# Load Balancer Group
[loadbalancers]
lb1 ansible_host=172.31.45.72 server_id=20 environment=production

# Application Servers Group
[appservers]
app1 ansible_host=172.31.45.73 server_id=30 environment=production
app2 ansible_host=172.31.45.74 server_id=31 environment=staging

# Environment Groups
[production:children]
webservers
databases
loadbalancers

[staging:children]
webservers
databases

[development:children]
webservers

# All servers common variables
[all:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python3

# Web servers specific variables
[webservers:vars]
http_port=80
https_port=443
server_name=example.com
document_root=/var/www/html

# Database servers specific variables
[databases:vars]
mysql_port=3306
mysql_max_connections=100

# Load balancer specific variables
[loadbalancers:vars]
lb_method=roundrobin
health_check_interval=30
EOF
```

### Step 4: Test Ansible Configuration

```bash
# Test basic connectivity
ansible all -m ping

# List all hosts
ansible-inventory --list

# Show inventory graph
ansible-inventory --graph

# Test specific groups
ansible webservers -m ping
ansible databases -m ping

# Check configuration
ansible-config dump --only-changed
```

---

## Complete Ad-hoc Commands Reference

### Basic Syntax & Patterns

```bash
# Basic syntax
ansible [pattern] -m [module] -a "[module_options]"

# Common patterns
ansible all                    # All hosts
ansible webservers            # All hosts in webservers group
ansible web1                  # Specific host
ansible web*                  # Pattern matching
ansible webservers:databases  # Multiple groups
ansible webservers:!web1      # Exclude specific host
```

### 1. Connectivity & Basic Testing

```bash
# Ping all hosts
ansible all -m ping

# Ping specific group
ansible webservers -m ping

# Ping with specific user
ansible all -m ping -u ec2-user

# Test with verbose output
ansible all -m ping -v

# Test connectivity and gather basic facts
ansible all -m setup -a "filter=ansible_hostname"
```

### 2. System Information Commands

```bash
# Check uptime
ansible all -m command -a "uptime"

# Check disk usage
ansible all -m command -a "df -h"

# Check memory usage
ansible all -m command -a "free -h"

# Check CPU information
ansible all -m command -a "lscpu"

# Check running processes
ansible all -m command -a "ps aux" | head -20

# Check system load
ansible all -m command -a "w"

# Check who is logged in
ansible all -m command -a "who"

# Check last logins
ansible all -m command -a "last -n 5"

# Get complete system information
ansible all -m setup

# Get specific system facts
ansible all -m setup -a "filter=ansible_distribution*"
ansible all -m setup -a "filter=ansible_memory*"
ansible all -m setup -a "filter=ansible_processor*"
ansible all -m setup -a "filter=ansible_network*"
```

### 3. File and Directory Operations

```bash
# Create a directory
ansible all -m file -a "path=/tmp/ansible-test state=directory mode=0755"

# Create multiple directories
ansible all -m file -a "path=/tmp/test/{dir1,dir2,dir3} state=directory mode=0755"

# Create a file
ansible all -m file -a "path=/tmp/test.txt state=touch mode=0644"

# Copy file from control node to managed nodes
ansible all -m copy -a "src=/tmp/localfile.txt dest=/tmp/remotefile.txt mode=0644 backup=yes"

# Create file with specific content
ansible all -m copy -a "content='Hello from Ansible\nLine 2\nLine 3' dest=/tmp/hello.txt mode=0644"

# Copy with ownership
ansible all -m copy -a "src=/tmp/test.txt dest=/tmp/owned.txt owner=ec2-user group=ec2-user mode=0644"

# Download file from URL
ansible all -m get_url -a "url=https://releases.ansible.com/ansible/ansible-2.9.tar.gz dest=/tmp/ansible.tar.gz mode=0644"

# Synchronize directories (like rsync)
ansible all -m synchronize -a "src=/tmp/source/ dest=/tmp/destination/"

# Find files
ansible all -m find -a "paths=/tmp patterns='*.txt'"

# Change file permissions
ansible all -m file -a "path=/tmp/test.txt mode=0600"

# Change ownership
ansible all -m file -a "path=/tmp/test.txt owner=root group=root" --become

# Create symbolic link
ansible all -m file -a "src=/tmp/test.txt dest=/tmp/link.txt state=link"

# Remove file
ansible all -m file -a "path=/tmp/test.txt state=absent"

# Remove directory recursively
ansible all -m file -a "path=/tmp/ansible-test state=absent"
```

### 4. Package Management

```bash
# Update package cache
ansible all -m dnf -a "update_cache=yes" --become

# Install single package
ansible all -m dnf -a "name=htop state=present" --become

# Install multiple packages
ansible all -m dnf -a "name=htop,tree,wget,curl,vim state=present" --become

# Install specific version
ansible all -m dnf -a "name=httpd-2.4.* state=present" --become

# Upgrade all packages
ansible all -m dnf -a "name=* state=latest" --become

# Remove package
ansible all -m dnf -a "name=htop state=absent" --become

# Install from URL
ansible all -m dnf -a "name=https://download.example.com/package.rpm state=present" --become

# Check if package is installed
ansible all -m package_facts

# List installed packages
ansible all -m shell -a "dnf list installed | head -20" --become
```

### 5. Service Management

```bash
# Start service
ansible all -m service -a "name=httpd state=started" --become

# Stop service
ansible all -m service -a "name=httpd state=stopped" --become

# Restart service
ansible all -m service -a "name=httpd state=restarted" --become

# Reload service
ansible all -m service -a "name=httpd state=reloaded" --become

# Enable service (start at boot)
ansible all -m service -a "name=httpd enabled=yes" --become

# Disable service
ansible all -m service -a "name=httpd enabled=no" --become

# Start and enable service
ansible all -m service -a "name=httpd state=started enabled=yes" --become

# Check service status
ansible all -m service_facts

# Check specific service status
ansible all -m shell -a "systemctl status httpd" --become
```

### 6. User and Group Management

```bash
# Create user
ansible all -m user -a "name=testuser state=present shell=/bin/bash" --become

# Create user with home directory
ansible all -m user -a "name=testuser state=present createhome=yes home=/home/testuser" --become

# Create user with specific UID
ansible all -m user -a "name=testuser uid=1500 state=present" --become

# Add user to groups
ansible all -m user -a "name=testuser groups=wheel,docker append=yes" --become

# Set user password (hashed)
ansible all -m user -a "name=testuser password='$6$salt$hash' state=present" --become

# Create system user
ansible all -m user -a "name=serviceuser system=yes shell=/sbin/nologin" --become

# Delete user
ansible all -m user -a "name=testuser state=absent" --become

# Delete user and home directory
ansible all -m user -a "name=testuser state=absent remove=yes force=yes" --become

# Create group
ansible all -m group -a "name=developers state=present" --become

# Delete group
ansible all -m group -a "name=developers state=absent" --become
```

### 7. Archive and Compression

```bash
# Create tar.gz archive
ansible all -m archive -a "path=/tmp/test dest=/tmp/test.tar.gz format=gz"

# Create zip archive
ansible all -m archive -a "path=/tmp/test dest=/tmp/test.zip format=zip"

# Extract tar.gz archive
ansible all -m unarchive -a "src=/tmp/test.tar.gz dest=/tmp/ remote_src=yes"

# Extract archive from control node
ansible all -m unarchive -a "src=/local/path/archive.tar.gz dest=/tmp/"

# Extract with specific owner
ansible all -m unarchive -a "src=/tmp/test.tar.gz dest=/tmp/ remote_src=yes owner=ec2-user group=ec2-user" --become
```

### 8. Network and Security

```bash
# Configure firewall (firewalld)
ansible all -m firewalld -a "service=http permanent=yes state=enabled immediate=yes" --become

# Open specific port
ansible all -m firewalld -a "port=8080/tcp permanent=yes state=enabled immediate=yes" --become

# Check open ports
ansible all -m shell -a "ss -tlnp"

# Test connectivity to port
ansible all -m wait_for -a "host=google.com port=80 timeout=5"

# Download and verify checksum
ansible all -m get_url -a "url=https://example.com/file.txt dest=/tmp/file.txt checksum=sha256:abc123..."
```

### 9. Cron Job Management

```bash
# Add cron job
ansible all -m cron -a "name='disk cleanup' minute=0 hour=2 job='find /tmp -type f -mtime +7 -delete'"

# Add cron job for specific user
ansible all -m cron -a "name='backup' minute=30 hour=1 user=ec2-user job='/home/ec2-user/backup.sh'"

# List cron jobs
ansible all -m shell -a "crontab -l"

# Remove cron job
ansible all -m cron -a "name='disk cleanup' state=absent"
```

### 10. Advanced Commands

```bash
# Run command as different user
ansible all -m command -a "whoami" --become --become-user=apache

# Run shell command with pipes (use shell module carefully)
ansible all -m shell -a "ps aux | grep httpd | grep -v grep"

# Set environment variables
ansible all -m shell -a "echo $PATH" -e "PATH=/custom/path:$PATH"

# Run command with timeout
ansible all -m command -a "sleep 10" -T 15

# Reboot servers (use with extreme caution!)
ansible all -m reboot --become

# Wait for servers to come back online
ansible all -m wait_for_connection -a "delay=30 timeout=300"

# Gather subset of facts
ansible all -m setup -a "gather_subset=network,hardware"

# Get specific fact value
ansible all -m setup -a "filter=ansible_default_ipv4" | grep address

# Check disk space and alert if low
ansible all -m shell -a "df -h | awk '$5 > 80 {print $0}'"

# Run multiple commands
ansible all -m shell -a "uptime && free -h && df -h"
```

### 11. Debugging and Troubleshooting Commands

```bash
# Verbose output levels
ansible all -m ping -v      # verbose
ansible all -m ping -vv     # more verbose
ansible all -m ping -vvv    # debug level
ansible all -m ping -vvvv   # connection debug

# Check connectivity issues
ansible all -m setup -a "gather_subset=min" --connection=local

# Test with different connection methods
ansible all -m ping --connection=ssh
ansible all -m ping --connection=paramiko

# Check SSH configuration
ansible all -m shell -a "ssh -V"

# Validate inventory
ansible-inventory --list --yaml

# Test specific host with all details
ansible web1 -m setup -vvv
```

---

## Simple Ansible Project - Web Server

### Project Structure

```bash
# Create complete project structure
mkdir -p ~/ansible-webserver-project
cd ~/ansible-webserver-project

# Create directories
mkdir -p {playbooks,roles,group_vars,host_vars,files,templates,vars}
mkdir -p roles/webserver/{tasks,handlers,templates,files,vars,defaults,meta}
mkdir -p roles/database/{tasks,handlers,templates,files,vars,defaults,meta}
mkdir -p roles/common/{tasks,handlers,templates,files,vars,defaults,meta}

# Create inventory and config
touch {inventory,ansible.cfg}
```

### Main Site Playbook

```bash
cat > playbooks/site.yml << 'EOF'
---
- name: Complete Web Server Infrastructure Setup
  hosts: all
  become: yes
  gather_facts: yes
  
  pre_tasks:
    - name: Update system packages
      dnf:
        name: "*"
        state: latest
        update_cache: yes
      tags: update

- name: Deploy Web Servers
  hosts: webservers
  become: yes
  roles:
    - common
    - webserver
  tags: webserver

- name: Deploy Database Servers
  hosts: databases
  become: yes
  roles:
    - common
    - database
  tags: database

- name: Final Verification
  hosts: all
  become: yes
  tasks:
    - name: Ensure all services are running
      service:
        name: "{{ item }}"
        state: started
      loop:
        - httpd
      when: inventory_hostname in groups['webservers']
      ignore_errors: yes

    - name: Display access information
      debug:
        msg: |
          ========================================
          🎉 Deployment Complete!
          
          Web Server URLs:
          {% for host in groups['webservers'] %}
          - http://{{ hostvars[host]['ansible_default_ipv4']['address'] }}
          {% endfor %}
          
          Server Details:
          - Hostname: {{ ansible_hostname }}
          - IP: {{ ansible_default_ipv4.address }}
          - OS: {{ ansible_distribution }} {{ ansible_distribution_version }}
          ========================================
      when: inventory_hostname in groups['webservers']
EOF
```

### Common Role (Base Configuration)

```bash
# Create common role tasks
cat > roles/common/tasks/main.yml << 'EOF'
---
- name: Install common packages
  dnf:
    name:
      - wget
      - curl
      - vim
      - git
      - htop
      - tree
      - unzip
      - tar
    state: present
  tags: packages

- name: Create common directories
  file:
    path: "{{ item }}"
    state: directory
    mode: '0755'
  loop:
    - /opt/scripts
    - /var/log/custom
  tags: directories

- name: Set timezone
  timezone:
    name: "{{ server_timezone | default('UTC') }}"
  tags: timezone

- name: Configure system limits
  copy:
    content: |
      * soft nofile 65536
      * hard nofile 65536
      * soft nproc 32768
      * hard nproc 32768
    dest: /etc/security/limits.d/99-custom.conf
    mode: '0644'
  tags: limits
EOF
```

### Web Server Role

```bash
# Create webserver role tasks
cat > roles/webserver/tasks/main.yml << 'EOF'
---
- name: Install Apache web server
  dnf:
    name: httpd
    state: present
  notify: restart apache
  tags: install

- name: Install additional web packages
  dnf:
    name:
      - httpd-tools
      - mod_ssl
    state: present
  tags: install

- name: Start and enable Apache
  service:
    name: httpd
    state: started
    enabled: yes
  tags: service

- name: Create web directories
  file:
    path: "{{ item }}"
    state: directory
    owner: apache
    group: apache
    mode: '0755'
  loop:
    - "{{ document_root }}"
    - "{{ document_root }}/images"
    - "{{ document_root }}/css"
    - "{{ document_root }}/js"
    - /var/log/httpd
  tags: directories

- name: Deploy main website template
  template:
    src: index.html.j2
    dest: "{{ document_root }}/index.html"
    owner: apache
    group: apache
    mode: '0644'
  notify: restart apache
  tags: content

- name: Deploy server info page
  template:
    src: info.html.j2
    dest: "{{ document_root }}/info.html"
    owner: apache
    group: apache
    mode: '0644'
  tags: content

- name: Deploy health check page
  copy:
    content: |
      <!DOCTYPE html>
      <html>
      <head><title>Health Check</title></head>
      <body>
        <h1>Server Health: OK</h1>
        <p>Status: Running</p>
        <p>Timestamp: {{ ansible_date_time.iso8601 }}</p>
      </body>
      </html>
    dest: "{{ document_root }}/health.html"
    owner: apache
    group: apache
    mode: '0644'
  tags: content

- name: Configure Apache main config
  template:
    src: httpd.conf.j2
    dest: /etc/httpd/conf/httpd.conf
    backup: yes
  notify: restart apache
  tags: config

- name: Configure firewall for HTTP
  firewalld:
    service: http
    permanent: yes
    state: enabled
    immediate: yes
  ignore_errors: yes
  tags: firewall

- name: Configure firewall for HTTPS
  firewalld:
    service: https
    permanent: yes
    state: enabled
    immediate: yes
  ignore_errors: yes
  tags: firewall

- name: Verify Apache is running
  uri:
    url: "http://{{ ansible_default_ipv4.address }}"
    method: GET
    status_code: 200
  retries: 3
  delay: 5
  tags: verify
EOF

# Create webserver handlers
cat > roles/webserver/handlers/main.yml << 'EOF'
---
- name: restart apache
  service:
    name: httpd
    state: restarted

- name: reload apache
  service:
    name: httpd
    state: reloaded
EOF

# Create webserver defaults
cat > roles/webserver/defaults/main.yml << 'EOF'
---
# Default variables for webserver role
document_root: /var/www/html
server_admin: admin@example.com
server_name: "{{ ansible_fqdn }}"
listen_port: 80
max_clients: 256
EOF
```

### Web Server Templates

```bash
# Create main website template
cat > roles/webserver/templates/index.html.j2 << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ website_title | default('Welcome to ' + ansible_hostname) }}</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            line-height: 1.6;
            color: #333;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        
        .header {
            background: rgba(255, 255, 255, 0.95);
            padding: 30px;
            border-radius: 15px;
            text-align: center;
            margin-bottom: 30px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
        }
        
        .header h1 {
            color: #2c3e50;
            font-size: 3em;
            margin-bottom: 10px;
        }
        
        .header p {
            color: #7f8c8d;
            font-size: 1.2em;
        }
        
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
            margin: 20px 0;
        }
        
        .card {
            background: rgba(255, 255, 255, 0.95);
            padding: 25px;
            border-radius: 15px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
            transition: transform 0.3s ease;
        }
        
        .card:hover {
            transform: translateY(-5px);
        }
        
        .card h3 {
            color: #2c3e50;
            margin-bottom: 15px;
            font-size: 1.5em;
        }
        
        .card ul {
            list-style: none;
        }
        
        .card li {
            padding: 8px 0;
            border-bottom: 1px solid #ecf0f1;
        }
        
        .card li:last-child {
            border-bottom: none;
        }
        
        .status {
            display: inline-block;
            padding: 5px 15px;
            border-radius: 20px;
            font-weight: bold;
            text-transform: uppercase;
            font-size: 0.8em;
        }
        
        .status.success {
            background: #2ecc71;
            color: white;
        }
        
        .links {
            text-align: center;
            margin: 30px 0;
        }
        
        .btn {
            display: inline-block;
            padding: 12px 25px;
            margin: 10px;
            background: #3498db;
            color: white;
            text-decoration: none;
            border-radius: 25px;
            transition: background 0.3s ease;
        }
        
        .btn:hover {
            background: #2980b9;
        }
        
        .footer {
            text-align: center;
            color: rgba(255, 255, 255, 0.8);
            margin-top: 40px;
            padding: 20px;
        }
        
        @media (max-width: 768px) {
            .header h1 {
                font-size: 2em;
            }
            
            .grid {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🚀 {{ website_title | default('Welcome to ' + ansible_hostname) }}</h1>
            <p>Successfully deployed using Ansible automation!</p>
            <span class="status success">✅ Online</span>
        </div>
        
        <div class="grid">
            <div class="card">
                <h3>📊 Server Information</h3>
                <ul>
                    <li><strong>Hostname:</strong> {{ ansible_hostname }}</li>
                    <li><strong>FQDN:</strong> {{ ansible_fqdn }}</li>
                    <li><strong>IP Address:</strong> {{ ansible_default_ipv4.address }}</li>
                    <li><strong>Operating System:</strong> {{ ansible_distribution }} {{ ansible_distribution_version }}</li>
                    <li><strong>Architecture:</strong> {{ ansible_architecture }}</li>
                    <li><strong>Kernel:</strong> {{ ansible_kernel }}</li>
                </ul>
            </div>
            
            <div class="card">
                <h3>💻 Hardware Details</h3>
                <ul>
                    <li><strong>CPU:</strong> {{ ansible_processor_cores }} cores</li>
                    <li><strong>Memory:</strong> {{ (ansible_memtotal_mb/1024)|round(2) }} GB</li>
                    <li><strong>Available Memory:</strong> {{ (ansible_memfree_mb/1024)|round(2) }} GB</li>
                    <li><strong>Disk Space:</strong> {{ ansible_mounts[0].size_total | filesizeformat }}</li>
                    <li><strong>Virtualization:</strong> {{ ansible_virtualization_type | default('Physical') }}</li>
                </ul>
            </div>
            
            <div class="card">
                <h3>🌐 Network Configuration</h3>
                <ul>
                    <li><strong>Interface:</strong> {{ ansible_default_ipv4.interface }}</li>
                    <li><strong>Gateway:</strong> {{ ansible_default_ipv4.gateway }}</li>
                    <li><strong>Netmask:</strong> {{ ansible_default_ipv4.netmask }}</li>
                    <li><strong>DNS:</strong> {{ ansible_dns.nameservers[0] | default('N/A') }}</li>
                    <li><strong>MAC Address:</strong> {{ ansible_default_ipv4.macaddress }}</li>
                </ul>
            </div>
            
            <div class="card">
                <h3>⚡ Deployment Info</h3>
                <ul>
                    <li><strong>Deployed:</strong> {{ ansible_date_time.date }}</li>
                    <li><strong>Time:</strong> {{ ansible_date_time.time }}</li>
                    <li><strong>Timezone:</strong> {{ ansible_date_time.tz }}</li>
                    <li><strong>Uptime:</strong> {{ ansible_uptime_seconds | int // 3600 }}h {{ (ansible_uptime_seconds | int % 3600) // 60 }}m</li>
                    <li><strong>Environment:</strong> {{ environment | default('Production') }}</li>
                </ul>
            </div>
        </div>
        
        <div class="links">
            <a href="/info.html" class="btn">📋 Detailed Server Info</a>
            <a href="/health.html" class="btn">🏥 Health Check</a>
        </div>
        
        <div class="footer">
            <p>🔧 Powered by Ansible Automation Platform</p>
            <p>Server ID: {{ server_id | default('Unknown') }} | Environment: {{ environment | default('Production') }}</p>
        </div>
    </div>
</body>
</html>
EOF

# Create detailed info template
cat > roles/webserver/templates/info.html.j2 << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Detailed Server Information - {{ ansible_hostname }}</title>
    <style>
        body {
            font-family: 'Courier New', monospace;
            background: #1a1a1a;
            color: #00ff00;
            margin: 0;
            padding: 20px;
            line-height: 1.6;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
        }
        
        .header {
            text-align: center;
            border: 2px solid #00ff00;
            padding: 20px;
            margin-bottom: 30px;
            background: #2a2a2a;
        }
        
        .section {
            margin: 20px 0;
            border: 1px solid #333;
            background: #222;
            padding: 20px;
            border-radius: 5px;
        }
        
        .section h2 {
            color: #00ffff;
            border-bottom: 1px solid #00ffff;
            padding-bottom: 10px;
        }
        
        .info-grid {
            display: grid;
            grid-template-columns: 1fr 2fr;
            gap: 10px;
            margin: 10px 0;
        }
        
        .label {
            color: #ffff00;
            font-weight: bold;
        }
        
        .value {
            color: #ffffff;
        }
        
        pre {
            background: #000;
            padding: 15px;
            border-left: 3px solid #00ff00;
            overflow-x: auto;
            margin: 10px 0;
        }
        
        .back-link {
            display: inline-block;
            color: #00ffff;
            text-decoration: none;
            padding: 10px 20px;
            border: 1px solid #00ffff;
            margin: 20px 0;
        }
        
        .back-link:hover {
            background: #00ffff;
            color: #000;
        }
        
        @media (max-width: 768px) {
            .info-grid {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🖥️ DETAILED SERVER INFORMATION</h1>
            <p>System Analysis Report for {{ ansible_hostname }}</p>
        </div>
        
        <div class="section">
            <h2>🔍 System Overview</h2>
            <div class="info-grid">
                <span class="label">Hostname:</span>
                <span class="value">{{ ansible_hostname }}</span>
                
                <span class="label">FQDN:</span>
                <span class="value">{{ ansible_fqdn }}</span>
                
                <span class="label">Domain:</span>
                <span class="value">{{ ansible_domain | default('Not set') }}</span>
                
                <span class="label">Machine ID:</span>
                <span class="value">{{ ansible_machine_id }}</span>
                
                <span class="label">Boot ID:</span>
                <span class="value">{{ ansible_boot_time }}</span>
            </div>
        </div>
        
        <div class="section">
            <h2>🐧 Operating System</h2>
            <div class="info-grid">
                <span class="label">Distribution:</span>
                <span class="value">{{ ansible_distribution }} {{ ansible_distribution_version }}</span>
                
                <span class="label">OS Family:</span>
                <span class="value">{{ ansible_os_family }}</span>
                
                <span class="label">Kernel:</span>
                <span class="value">{{ ansible_kernel }}</span>
                
                <span class="label">Architecture:</span>
                <span class="value">{{ ansible_architecture }}</span>
                
                <span class="label">Python Version:</span>
                <span class="value">{{ ansible_python_version }}</span>
            </div>
        </div>
        
        <div class="section">
            <h2>⚙️ Hardware Information</h2>
            <div class="info-grid">
                <span class="label">CPU Model:</span>
                <span class="value">{{ ansible_processor[2] | default('Unknown') }}</span>
                
                <span class="label">CPU Cores:</span>
                <span class="value">{{ ansible_processor_cores }}</span>
                
                <span class="label">CPU Count:</span>
                <span class="value">{{ ansible_processor_count }}</span>
                
                <span class="label">Total Memory:</span>
                <span class="value">{{ (ansible_memtotal_mb/1024)|round(2) }} GB</span>
                
                <span class="label">Available Memory:</span>
                <span class="value">{{ (ansible_memfree_mb/1024)|round(2) }} GB</span>
                
                <span class="label">Swap Total:</span>
                <span class="value">{{ (ansible_swaptotal_mb/1024)|round(2) }} GB</span>
            </div>
        </div>
        
        <div class="section">
            <h2>🌐 Network Configuration</h2>
            <div class="info-grid">
                <span class="label">Primary Interface:</span>
                <span class="value">{{ ansible_default_ipv4.interface }}</span>
                
                <span class="label">IP Address:</span>
                <span class="value">{{ ansible_default_ipv4.address }}</span>
                
                <span class="label">Netmask:</span>
                <span class="value">{{ ansible_default_ipv4.netmask }}</span>
                
                <span class="label">Gateway:</span>
                <span class="value">{{ ansible_default_ipv4.gateway }}</span>
                
                <span class="label">MAC Address:</span>
                <span class="value">{{ ansible_default_ipv4.macaddress }}</span>
                
                <span class="label">DNS Servers:</span>
                <span class="value">{{ ansible_dns.nameservers | join(', ') }}</span>
            </div>
        </div>
        
        <div class="section">
            <h2>💾 Storage Information</h2>
            <pre>
{% for mount in ansible_mounts %}
{{ "%-20s" | format(mount.mount) }} {{ "%-10s" | format(mount.fstype) }} {{ "%-15s" | format(mount.size_human) }} {{ "%-15s" | format(mount.size_available_human) }}
{% endfor %}
            </pre>
        </div>
        
        <div class="section">
            <h2>🔧 Virtualization</h2>
            <div class="info-grid">
                <span class="label">Type:</span>
                <span class="value">{{ ansible_virtualization_type | default('Physical') }}</span>
                
                <span class="label">Role:</span>
                <span class="value">{{ ansible_virtualization_role | default('N/A') }}</span>
            </div>
        </div>
        
        <div class="section">
            <h2>⏰ Time Information</h2>
            <div class="info-grid">
                <span class="label">Current Date:</span>
                <span class="value">{{ ansible_date_time.date }}</span>
                
                <span class="label">Current Time:</span>
                <span class="value">{{ ansible_date_time.time }}</span>
                
                <span class="label">Timezone:</span>
                <span class="value">{{ ansible_date_time.tz }}</span>
                
                <span class="label">Uptime:</span>
                <span class="value">{{ ansible_uptime_seconds | int // 86400 }}d {{ (ansible_uptime_seconds | int % 86400) // 3600 }}h {{ ((ansible_uptime_seconds | int % 86400) % 3600) // 60 }}m</span>
            </div>
        </div>
        
        <a href="/" class="back-link">← Back to Home</a>
    </div>
</body>
</html>
EOF

# Create Apache configuration template
cat > roles/webserver/templates/httpd.conf.j2 << 'EOF'
# Apache Configuration Template - Generated by Ansible

ServerRoot "/etc/httpd"
Listen {{ listen_port | default(80) }}

User apache
Group apache

ServerAdmin {{ server_admin | default('admin@localhost') }}
ServerName {{ server_name | default(ansible_fqdn) }}

<Directory />
    AllowOverride none
    Require all denied
</Directory>

DocumentRoot "{{ document_root | default('/var/www/html') }}"

<Directory "{{ document_root | default('/var/www/html') }}">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

<IfModule dir_module>
    DirectoryIndex index.html index.php
</IfModule>

<Files ".ht*">
    Require all denied
</Files>

ErrorLog "logs/error_log"
LogLevel warn

<IfModule log_config_module>
    LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
    LogFormat "%h %l %u %t \"%r\" %>s %b" common
    CustomLog "logs/access_log" combined
</IfModule>

<IfModule mime_module>
    TypesConfig /etc/mime.types
    AddType application/x-compress .Z
    AddType application/x-gzip .gz .tgz
    AddType text/html .shtml
    AddOutputFilter INCLUDES .shtml
</IfModule>

AddDefaultCharset UTF-8
EnableSendfile on

# Security Headers
Header always set X-Content-Type-Options nosniff
Header always set X-Frame-Options DENY
Header always set X-XSS-Protection "1; mode=block"

# Performance settings
MaxRequestWorkers {{ max_clients | default(256) }}
ThreadsPerChild 25
KeepAlive On
MaxKeepAliveRequests 100
KeepAliveTimeout 5
EOF
```

### Database Role

```bash
# Create database role tasks
cat > roles/database/tasks/main.yml << 'EOF'
---
- name: Install MariaDB server
  dnf:
    name:
      - mariadb-server
      - mariadb
      - python3-PyMySQL
    state: present
  tags: install

- name: Start and enable MariaDB
  service:
    name: mariadb
    state: started
    enabled: yes
  tags: service

- name: Configure MariaDB
  template:
    src: my.cnf.j2
    dest: /etc/my.cnf
    backup: yes
  notify: restart mariadb
  tags: config

- name: Secure MariaDB installation
  mysql_user:
    name: root
    password: "{{ mysql_root_password | default('secure_password') }}"
    login_unix_socket: /var/lib/mysql/mysql.sock
    state: present
  tags: security

- name: Create application database
  mysql_db:
    name: "{{ app_database | default('webapp') }}"
    state: present
    login_user: root
    login_password: "{{ mysql_root_password | default('secure_password') }}"
  tags: database

- name: Create application user
  mysql_user:
    name: "{{ app_db_user | default('appuser') }}"
    password: "{{ app_db_password | default('app_password') }}"
    priv: "{{ app_database | default('webapp') }}.*:ALL"
    state: present
    login_user: root
    login_password: "{{ mysql_root_password | default('secure_password') }}"
  tags: database
EOF

# Create database handlers  
cat > roles/database/handlers/main.yml << 'EOF'
---
- name: restart mariadb
  service:
    name: mariadb
    state: restarted
EOF

# Create MariaDB configuration template
cat > roles/database/templates/my.cnf.j2 << 'EOF'
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
symbolic-links=0

# Performance settings
max_connections={{ mysql_max_connections | default(100) }}
innodb_buffer_pool_size=128M
query_cache_size=32M
query_cache_type=1

# Security settings
bind-address=127.0.0.1

[mysqld_safe]
log-error=/var/log/mariadb/mariadb.log
pid-file=/var/run/mariadb/mariadb.pid

!includedir /etc/my.cnf.d
EOF
```

### Group Variables

```bash
# Create web servers group variables
cat > group_vars/webservers.yml << 'EOF'
---
# Web server configuration
website_title: "Ansible Lab Web Server"
document_root: /var/www/html
server_admin: admin@example.com
listen_port: 80
max_clients: 256
environment: production

# Common settings
server_timezone: UTC
log_level: warn

# Security settings
enable_ssl: false
ssl_certificate: /etc/ssl/certs/server.crt
ssl_private_key: /etc/ssl/private/server.key
EOF

# Create database servers group variables
cat > group_vars/databases.yml << 'EOF'
---
# Database configuration
mysql_root_password: SecureRootPassword123!
mysql_max_connections: 150
app_database: webapp_db
app_db_user: webapp_user
app_db_password: WebAppPassword123!

# Performance settings
innodb_buffer_pool_size: 256M
query_cache_size: 64M

# Backup settings
backup_retention_days: 7
backup_schedule: "0 2 * * *"
EOF

# Create all servers variables
cat > group_vars/all.yml << 'EOF'
---
# Global variables for all servers
server_timezone: UTC
ntp_servers:
  - 0.amazon.pool.ntp.org
  - 1.amazon.pool.ntp.org

# Security settings
disable_root_login: true
password_authentication: false

# Monitoring
enable_monitoring: true
log_retention_days: 30

# Common packages
common_packages:
  - wget
  - curl
  - vim
  - git
  - htop
  - tree
EOF
```

### Running the Project

```bash
# Create run script
cat > run-playbook.sh << 'EOF'
#!/bin/bash

echo "🚀 Starting Ansible Web Server Deployment"
echo "========================================="

# Colors
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
RED='\033[0;31m'
NC='\033[0m'

# Functions
print_status() {
    echo -e "${GREEN}[INFO]${NC} $1"
}

print_warning() {
    echo -e "${YELLOW}[WARNING]${NC} $1"
}

print_error() {
    echo -e "${RED}[ERROR]${NC} $1"
}

# Check if inventory exists
if [ ! -f inventory ]; then
    print_error "Inventory file not found!"
    print_warning "Please create inventory file with your server IPs"
    exit 1
fi

# Syntax check
print_status "Checking playbook syntax..."
if ansible-playbook playbooks/site.yml --syntax-check; then
    print_status "Syntax check passed!"
else
    print_error "Syntax check failed!"
    exit 1
fi

# Check connectivity
print_status "Testing connectivity to all hosts..."
if ansible all -m ping; then
    print_status "All hosts reachable!"
else
    print_error "Some hosts are unreachable!"
    exit 1
fi

# Run playbook
print_status "Deploying web servers..."
echo "Choose deployment mode:"
echo "1. Full deployment (default)"
echo "2. Check mode (dry run)"
echo "3. Web servers only"
echo "4. Database servers only"

read -p "Enter choice (1-4): " choice

case $choice in
    2)
        print_status "Running in check mode..."
        ansible-playbook playbooks/site.yml --check -v
        ;;
    3)
        print_status "Deploying web servers only..."
        ansible-playbook playbooks/site.yml --tags webserver -v
        ;;
    4)
        print_status "Deploying database servers only..."
        ansible-playbook playbooks/site.yml --tags database -v
        ;;
    *)
        print_status "Running full deployment..."
        ansible-playbook playbooks/site.yml -v
        ;;
esac

if [ $? -eq 0 ]; then
    print_status "Deployment completed successfully!"
    echo ""
    echo "🎉 Web servers are now accessible at:"
    ansible webservers -m debug -a "msg='http://{{ ansible_default_ipv4.address }}'" | grep -A1 "msg"
else
    print_error "Deployment failed!"
    exit 1
fi
EOF

chmod +x run-playbook.sh
```

---

## Configuration Files & Templates

### Complete ansible.cfg

```bash
cat > complete-ansible.cfg << 'EOF'
[defaults]
# Basic Configuration
inventory = inventory
remote_user = ec2-user
private_key_file = ~/.ssh/id_rsa
host_key_checking = False
timeout = 30
gather_facts = True
forks = 10
retry_files_enabled = False
system_warnings = False

# Output Configuration
stdout_callback = yaml
bin_ansible_callbacks = True
display_skipped_hosts = False
display_ok_hosts = True
force_color = True

# Logging
log_path = ./ansible.log
debug = False

# Role and Collection paths
roles_path = ./roles:~/.ansible/roles:/etc/ansible/roles
collections_paths = ./collections:~/.ansible/collections:/usr/share/ansible/collections

# SSH Configuration
[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s -o UserKnownHostsFile=/dev/null -o IdentitiesOnly=yes
pipelining = True
control_path_dir = ~/.ansible/cp
control_path = %(directory)s/%%h-%%p-%%r

# Privilege Escalation
[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False

# Inventory Configuration
[inventory]
enable_plugins = ini, yaml, auto, script, advanced_host_list

# Performance
[connection]
pipelining = True

# Colors
[colors]
highlight = white
verbose = blue
warn = bright purple
error = red
debug = dark gray
deprecation = purple
skip = cyan
unreachable = red
ok = green
changed = yellow
diff_add = green
diff_remove = red
diff_lines = cyan

# Diff output
[diff]
always = True
context = 3
EOF
```

### Production Inventory Template

```bash
cat > production-inventory.ini << 'EOF'
# Production Inventory Template
# Replace IP addresses with your actual server IPs

# Web Tier
[webservers]
web-prod-01 ansible_host=10.0.1.10 server_id=web01 environment=production
web-prod-02 ansible_host=10.0.1.11 server_id=web02 environment=production
web-prod-03 ansible_host=10.0.1.12 server_id=web03 environment=production

# Application Tier
[appservers]
app-prod-01 ansible_host=10.0.2.10 server_id=app01 environment=production
app-prod-02 ansible_host=10.0.2.11 server_id=app02 environment=production

# Database Tier
[databases]
db-master ansible_host=10.0.3.10 server_id=db01 environment=production mysql_role=master
db-slave ansible_host=10.0.3.11 server_id=db02 environment=production mysql_role=slave

# Load Balancers
[loadbalancers]
lb-prod-01 ansible_host=10.0.4.10 server_id=lb01 environment=production

# Cache Servers
[cache]
redis-prod-01 ansible_host=10.0.5.10 server_id=cache01 environment=production

# Monitoring
[monitoring]
monitor-prod-01 ansible_host=10.0.6.10 server_id=mon01 environment=production

# Environment Groups
[production:children]
webservers
appservers
databases
loadbalancers
cache
monitoring

# Geographic Groups
[us-east-1:children]
webservers
appservers

[us-west-2:children]
databases
cache

# Functional Groups
[frontend:children]
webservers
loadbalancers

[backend:children]
appservers
databases

[infrastructure:children]
cache
monitoring

# All Production Variables
[production:vars]
deployment_environment=production
backup_enabled=true
monitoring_enabled=true
ssl_enabled=true
firewall_strict=true

# Web Servers Variables
[webservers:vars]
http_port=80
https_port=443
max_clients=512
keepalive_timeout=5
server_tokens=off

# Database Variables
[databases:vars]
mysql_port=3306
mysql_max_connections=200
mysql_innodb_buffer_pool_size=1G
backup_schedule="0 2 * * *"

# Application Variables
[appservers:vars]
app_port=8080
jvm_heap_size=2G
connection_pool_size=50

# Load Balancer Variables
[loadbalancers:vars]
lb_algorithm=roundrobin
health_check_interval=5
ssl_termination=true

# Global Variables
[all:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python3

# Time and Locale
timezone=UTC
locale=en_US.UTF-8

# Security
ssh_port=22
fail2ban_enabled=true
automatic_security_updates=true

# Monitoring
log_aggregation=enabled
metrics_collection=enabled
alerting=enabled
EOF
```

---

## Quick Setup Script

```bash
cat > complete-setup.sh << 'EOF'
#!/bin/bash
# Complete Ansible Lab Setup Script
# This script automates the entire setup process

set -e

# Configuration
SCRIPT_VERSION="2.0"
PROJECT_NAME="ansible-complete-lab"
LOG_FILE="setup.log"

# Colors
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
PURPLE='\033[0;35m'
CYAN='\033[0;36m'
NC='\033[0m'

# Logging function
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" >> "$LOG_FILE"
    echo -e "$1"
}

# Print functions
print_header() {
    echo -e "\n${BLUE}===============================================${NC}"
    echo -e "${BLUE}$1${NC}"
    echo -e "${BLUE}===============================================${NC}\n"
    log "HEADER: $1"
}

print_status() {
    echo -e "${GREEN}[✓]${NC} $1"
    log "SUCCESS: $1"
}

print_warning() {
    echo -e "${YELLOW}[!]${NC} $1"
    log "WARNING: $1"
}

print_error() {
    echo -e "${RED}[✗]${NC} $1"
    log "ERROR: $1"
}

print_info() {
    echo -e "${CYAN}[i]${NC} $1"
    log "INFO: $1"
}

# Banner
show_banner() {
    clear
    echo -e "${PURPLE}"
    cat << 'EOF'
    ╔═══════════════════════════════════════════════════════════╗
    ║                                                           ║
    ║     🚀 ANSIBLE COMPLETE LAB SETUP SCRIPT 🚀              ║
    ║                                                           ║
    ║     Automated Setup for AWS EC2 with Amazon Linux        ║
    ║     Version: v2.0                                         ║
    ║                                                           ║
    ╚═══════════════════════════════════════════════════════════╝
EOF
    echo -e "${NC}\n"
}

# Check prerequisites
check_prerequisites() {
    print_header "Checking Prerequisites"
    
    local missing_deps=()
    
    # Check OS
    if [ -f /etc/os-release ]; then
        . /etc/os-release
        if [[ "$ID" == "amzn" ]]; then
            print_status "Operating System: Amazon Linux detected"
        else
            print_warning "This script is optimized for Amazon Linux"
            print_info "Current OS: $PRETTY_NAME"
        fi
    else
        print_error "Cannot determine operating system"
        missing_deps+=("os-release")
    fi
    
    # Check internet connectivity
    if ping -c 1 google.com &> /dev/null; then
        print_status "Internet connectivity: Available"
    else
        print_error "No internet connectivity detected"
        missing_deps+=("internet")
    fi
    
    # Check if running as root
    if [ "$EUID" -eq 0 ]; then
        print_error "Please do not run this script as root"
        print_info "Run as: ./setup.sh"
        exit 1
    fi
    
    # Check available disk space (minimum 2GB)
    local available_space=$(df / | awk 'NR==2 {print $4}')
    if [ "$available_space" -gt 2097152 ]; then
        print_status "Disk space: Sufficient ($(( available_space / 1024 / 1024 ))GB available)"
    else
        print_warning "Low disk space: $(( available_space / 1024 / 1024 ))GB available"
    fi
    
    if [ ${#missing_deps[@]} -gt 0 ]; then
        print_error "Missing prerequisites: ${missing_deps[*]}"
        exit 1
    fi
    
    print_status "All prerequisites met!"
}

# Update system
update_system() {
    print_header "Updating System Packages"
    
    print_info "This may take several minutes..."
    
    if command -v dnf &> /dev/null; then
        print_info "Using DNF package manager"
        if sudo dnf update -y >> "$LOG_FILE" 2>&1; then
            print_status "System packages updated successfully"
        else
            print_error "Failed to update system packages"
            exit 1
        fi
    elif command -v yum &> /dev/null; then
        print_info "Using YUM package manager"
        if sudo yum update -y >> "$LOG_FILE" 2>&1; then
            print_status "System packages updated successfully"
        else
            print_error "Failed to update system packages"
            exit 1
        fi
    else
        print_error "No supported package manager found"
        exit 1
    fi
}

# Install Ansible and dependencies
install_ansible() {
    print_header "Installing Ansible and Dependencies"
    
    if command -v ansible &> /dev/null; then
        local current_version=$(ansible --version | head -n1 | cut -d' ' -f2)
        print_warning "Ansible is already installed (version: $current_version)"
        
        read -p "Do you want to reinstall? (y/N): " -n 1 -r
        echo
        if [[ ! $REPLY =~ ^[Yy]$ ]]; then
            print_info "Skipping Ansible installation"
            return
        fi
    fi
    
    print_info "Installing Ansible and essential packages..."
    
    local packages=(
        "ansible"
        "python3"
        "python3-pip"
        "git"
        "tree"
        "wget"
        "curl"
        "vim"
        "htop"
        "unzip"
        "tar"
    )
    
    if command -v dnf &> /dev/null; then
        if sudo dnf install -y "${packages[@]}" >> "$LOG_FILE" 2>&1; then
            print_status "Ansible and dependencies installed successfully"
        else
            print_error "Failed to install Ansible"
            exit 1
        fi
    else
        if sudo yum install -y "${packages[@]}" >> "$LOG_FILE" 2>&1; then
            print_status "Ansible and dependencies installed successfully"
        else
            print_error "Failed to install Ansible"
            exit 1
        fi
    fi
    
    # Verify installation
    if ansible --version >> "$LOG_FILE" 2>&1; then
        local version=$(ansible --version | head -n1)
        print_status "Ansible installation verified: $version"
    else
        print_error "Ansible installation verification failed"
        exit 1
    fi
    
    # Install additional Python packages
    print_info "Installing additional Python packages..."
    pip3 install --user requests boto3 >> "$LOG_FILE" 2>&1 || print_warning "Some Python packages may not have installed"
}

# Generate SSH keys
setup_ssh_keys() {
    print_header "Setting up SSH Keys"
    
    local ssh_key_path="$HOME/.ssh/id_rsa"
    
    if [ -f "$ssh_key_path" ]; then
        print_warning "SSH key already exists at $ssh_key_path"
        
        # Show key fingerprint
        local fingerprint=$(ssh-keygen -lf "$ssh_key_path" 2>/dev/null | awk '{print $2}')
        print_info "Current key fingerprint: $fingerprint"
        
        read -p "Do you want to generate a new key? (y/N): " -n 1 -r
        echo
        if [[ ! $REPLY =~ ^[Yy]$ ]]; then
            print_info "Using existing SSH key"
            return
        fi
        
        print_info "Backing up existing key..."
        local backup_name="id_rsa.backup.$(date +%Y%m%d_%H%M%S)"
        mv "$ssh_key_path" "$HOME/.ssh/$backup_name"
        mv "$ssh_key_path.pub" "$HOME/.ssh/$backup_name.pub"
        print_status "Existing key backed up as $backup_name"
    fi
    
    print_info "Generating new 4096-bit RSA key pair..."
    
    # Create .ssh directory if it doesn't exist
    mkdir -p "$HOME/.ssh"
    chmod 700 "$HOME/.ssh"
    
    # Generate key
    if ssh-keygen -t rsa -b 4096 -f "$ssh_key_path" -N "" -C "ansible-lab-$(date +%Y%m%d)" >> "$LOG_FILE" 2>&1; then
        print_status "SSH key pair generated successfully"
    else
        print_error "Failed to generate SSH key pair"
        exit 1
    fi
    
    # Set correct permissions
    chmod 600 "$ssh_key_path"
    chmod 644 "$ssh_key_path.pub"
    
    print_status "SSH key permissions set correctly"
    
    # Display public key
    print_info "Your SSH public key:"
    echo -e "${YELLOW}$(cat "$ssh_key_path.pub")${NC}"
    echo
    print_warning "IMPORTANT: You need to copy this public key to your managed nodes!"
    print_info "Use: ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@MANAGED_NODE_IP"
    echo
    read -p "Press Enter to continue..."
}

# Create project structure
create_project_structure() {
    print_header "Creating Project Structure"
    
    local project_dir="$HOME/$PROJECT_NAME"
    
    if [ -d "$project_dir" ]; then
        print_warning "Project directory already exists: $project_dir"
        
        read -p "Do you want to recreate it? (y/N): " -n 1 -r
        echo
        if [[ $REPLY =~ ^[Yy]$ ]]; then
            print_info "Backing up existing directory..."
            mv "$project_dir" "${project_dir}.backup.$(date +%Y%m%d_%H%M%S)"
        else
            print_info "Using existing project directory"
            cd "$project_dir"
            return
        fi
    fi
    
    print_info "Creating project directory structure..."
    
    # Create main directory
    mkdir -p "$project_dir"
    cd "$project_dir"
    
    # Create subdirectories
    local directories=(
        "playbooks"
        "roles/common/{tasks,handlers,templates,files,vars,defaults,meta}"
        "roles/webserver/{tasks,handlers,templates,files,vars,defaults,meta}"
        "roles/database/{tasks,handlers,templates,files,vars,defaults,meta}"
        "group_vars"
        "host_vars"
        "files"
        "templates"
        "vars"
        "inventories/{production,staging,development}"
        "scripts"
        "docs"
        "logs"
    )
    
    for dir in "${directories[@]}"; do
        mkdir -p "$dir"
    done
    
    print_status "Project structure created successfully"
    
    # Display structure
    if command -v tree &> /dev/null; then
        print_info "Project directory structure:"
        tree -L 3 .
    else
        print_info "Project created at: $project_dir"
        ls -la
    fi
}

# Create configuration files
create_configuration_files() {
    print_header "Creating Configuration Files"
    
    print_info "Creating ansible.cfg..."
    
    # Create ansible.cfg
    cat > ansible.cfg << 'ANSIBLE_CFG'
[defaults]
inventory = inventory
remote_user = ec2-user
private_key_file = ~/.ssh/id_rsa
host_key_checking = False
timeout = 30
gather_facts = True
forks = 10
retry_files_enabled = False
stdout_callback = yaml
log_path = ./logs/ansible.log

[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s
pipelining = True

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
ANSIBLE_CFG
    
    print_info "Creating sample inventory..."
    
    # Create sample inventory
    cat > inventory << 'INVENTORY'
# Sample Inventory - Replace with your actual server IPs

[webservers]
web1 ansible_host=172.31.45.67
web2 ansible_host=172.31.45.68

[databases]
db1 ansible_host=172.31.45.69

[all:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=~/.ssh/id_rsa
INVENTORY
    
    print_info "Creating sample playbook..."
    
    # Create test playbook
    cat > playbooks/test-connectivity.yml << 'PLAYBOOK'
---
- name: Test Connectivity
  hosts: all
  gather_facts: yes
  tasks:
    - name: Ping all servers
      ping:
    
    - name: Check uptime
      command: uptime
      register: uptime_result
    
    - name: Display uptime
      debug:
        msg: "{{ inventory_hostname }}: {{ uptime_result.stdout }}"
    
    - name: Gather system information
      setup:
        filter: ansible_distribution*
    
    - name: Display OS information
      debug:
        msg: "{{ inventory_hostname }} is running {{ ansible_distribution }} {{ ansible_distribution_version }}"
PLAYBOOK
    
    print_info "Creating group variables..."
    
    # Create group variables
    cat > group_vars/all.yml << 'ALL_VARS'
---
# Global variables for all servers
timezone: UTC
common_packages:
  - wget
  - curl
  - vim
  - git
  - htop
  - tree

# Security settings
disable_root_login: true
password_authentication: false

# Monitoring
enable_monitoring: true
log_retention_days: 30
ALL_VARS
    
    cat > group_vars/webservers.yml << 'WEB_VARS'
---
# Web server specific variables
http_port: 80
https_port: 443
document_root: /var/www/html
server_admin: admin@example.com
website_title: "Ansible Lab Web Server"
WEB_VARS
    
    print_info "Creating utility scripts..."
    
    # Create run script
    cat > scripts/run-playbook.sh << 'RUN_SCRIPT'
#!/bin/bash
echo "🚀 Ansible Playbook Runner"
echo "========================="

if [ ! -f inventory ]; then
    echo "❌ Error: inventory file not found!"
    exit 1
fi

echo "Available playbooks:"
ls -1 playbooks/*.yml 2>/dev/null | nl

read -p "Enter playbook number or path: " choice

if [[ "$choice" =~ ^[0-9]+$ ]]; then
    playbook=$(ls -1 playbooks/*.yml 2>/dev/null | sed -n "${choice}p")
else
    playbook="$choice"
fi

if [ ! -f "$playbook" ]; then
    echo "❌ Error: Playbook not found!"
    exit 1
fi

echo "🔍 Checking syntax..."
ansible-playbook "$playbook" --syntax-check

echo "🔗 Testing connectivity..."
ansible all -m ping

echo "▶️  Running playbook: $playbook"
ansible-playbook "$playbook" -v
RUN_SCRIPT
    
    chmod +x scripts/run-playbook.sh
    
    # Create inventory checker
    cat > scripts/check-inventory.sh << 'INVENTORY_SCRIPT'
#!/bin/bash
echo "📋 Inventory Checker"
echo "==================="

if [ ! -f inventory ]; then
    echo "❌ Error: inventory file not found!"
    exit 1
fi

echo "📝 Inventory contents:"
ansible-inventory --list --yaml

echo ""
echo "🌐 Host connectivity:"
ansible all -m ping --one-line
INVENTORY_SCRIPT
    
    chmod +x scripts/check-inventory.sh
    
    print_status "Configuration files created successfully"
}

# Install additional tools
install_additional_tools() {
    print_header "Installing Additional Tools"
    
    print_info "Installing development and monitoring tools..."
    
    local additional_packages=(
        "nc"
        "telnet"
        "tcpdump"
        "strace"
        "lsof"
        "iotop"
        "nethogs"
    )
    
    if command -v dnf &> /dev/null; then
        sudo dnf install -y "${additional_packages[@]}" >> "$LOG_FILE" 2>&1 || print_warning "Some additional packages may not have installed"
    else
        sudo yum install -y "${additional_packages[@]}" >> "$LOG_FILE" 2>&1 || print_warning "Some additional packages may not have installed"
    fi
    
    print_status "Additional tools installed"
}

# Create documentation
create_documentation() {
    print_header "Creating Documentation"
    
    print_info "Generating README and documentation..."
    
    cat > README.md << 'README'
# Ansible Lab Environment

## Quick Start

1. **Update inventory file** with your server IPs
2. **Distribute SSH keys** to managed nodes:
   ```bash
   ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@YOUR_SERVER_IP
   ```
3. **Test connectivity**:
   ```bash
   ansible all -m ping
   ```
4. **Run test playbook**:
   ```bash
   ansible-playbook playbooks/test-connectivity.yml
   ```

## Directory Structure

```
├── ansible.cfg          # Ansible configuration
├── inventory            # Host inventory
├── playbooks/           # Ansible playbooks
├── roles/               # Ansible roles
├── group_vars/          # Group variables
├── host_vars/           # Host-specific variables
├── scripts/             # Utility scripts
└── logs/               # Log files
```

## Useful Commands

```bash
# Test connectivity
ansible all -m ping

# Run ad-hoc commands
ansible all -a "uptime"

# Check inventory
./scripts/check-inventory.sh

# Run playbooks
./scripts/run-playbook.sh
```

## Next Steps

1. Customize inventory with your server IPs
2. Modify group_vars for your environment
3. Create custom playbooks for your needs
4. Explore the roles directory for reusable components

Happy Automating! 🚀
README
    
    cat > docs/cheat-sheet.md << 'CHEATSHEET'
# Ansible Cheat Sheet

## Basic Commands
```bash
# Ping all hosts
ansible all -m ping

# Run command on all hosts
ansible all -a "command"

# Run with sudo
ansible all -a "command" --become

# Install package
ansible all -m dnf -a "name=package state=present" --become
```

## Playbook Commands
```bash
# Run playbook
ansible-playbook playbook.yml

# Check syntax
ansible-playbook playbook.yml --syntax-check

# Dry run
ansible-playbook playbook.yml --check

# Verbose output
ansible-playbook playbook.yml -v
```

## Inventory Commands
```bash
# List inventory
ansible-inventory --list

# Show inventory graph
ansible-inventory --graph
```
CHEATSHEET
    
    print_status "Documentation created"
}

# Final verification and summary
final_verification() {
    print_header "Final Verification"
    
    print_info "Verifying installation..."
    
    # Check Ansible
    if command -v ansible &> /dev/null; then
        local ansible_version=$(ansible --version | head -n1)
        print_status "Ansible: $ansible_version"
    else
        print_error "Ansible not found in PATH"
    fi
    
    # Check SSH key
    if [ -f ~/.ssh/id_rsa ]; then
        print_status "SSH key: Present"
    else
        print_error "SSH key not found"
    fi
    
    # Check project structure
    if [ -f ansible.cfg ] && [ -f inventory ]; then
        print_status "Project files: Created"
    else
        print_error "Project files missing"
    fi
    
    print_info "Setup completed successfully!"
}

# Display completion message
show_completion() {
    print_header "🎉 Setup Complete!"
    
    cat << EOF

${GREEN}✅ Your Ansible lab environment is ready!${NC}

${CYAN}📁 Project Location:${NC} $(pwd)
${CYAN}🔑 SSH Key:${NC} ~/.ssh/id_rsa.pub

${YELLOW}⚡ Next Steps:${NC}
1. Edit the inventory file with your EC2 instance IPs
2. Copy your SSH public key to managed nodes:
   ${BLUE}ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@YOUR_SERVER_IP${NC}
3. Test connectivity:
   ${BLUE}ansible all -m ping${NC}
4. Run your first playbook:
   ${BLUE}ansible-playbook playbooks/test-connectivity.yml${NC}

${YELLOW}🛠️ Useful Scripts:${NC}
- ${BLUE}./scripts/run-playbook.sh${NC} - Interactive playbook runner
- ${BLUE}./scripts/check-inventory.sh${NC} - Verify inventory and connectivity

${YELLOW}📖 Documentation:${NC}
- ${BLUE}README.md${NC} - Getting started guide
- ${BLUE}docs/cheat-sheet.md${NC} - Command reference

${GREEN}Happy Learning and Automating! 🚀${NC}

EOF
}

# Error handling
handle_error() {
    print_error "Setup script encountered an error on line $1"
    print_info "Check the log file: $LOG_FILE"
    print_info "You can re-run the script to continue from where it left off"
    exit 1
}

# Set error trap
trap 'handle_error $LINENO' ERR

# Main execution
main() {
    show_banner
    
    # Initialize log
    echo "Ansible Lab Setup Script - $(date)" > "$LOG_FILE"
    
    print_info "Log file: $LOG_FILE"
    
    read -p "🚀 Ready to set up your Ansible lab? (Y/n): " -n 1 -r
    echo
    if [[ $REPLY =~ ^[Nn]$ ]]; then
        print_info "Setup cancelled by user"
        exit 0
    fi
    
    # Run setup steps
    check_prerequisites
    update_system
    install_ansible
    setup_ssh_keys
    create_project_structure
    create_configuration_files
    install_additional_tools
    create_documentation
    final_verification
    show_completion
    
    print_status "Setup completed in $(pwd)"
    print_info "Total setup time: $(date)"
}

# Run main function
main "$@"
EOF

chmod +x complete-setup.sh
```

---

## Best Practices

### 1. Project Organization Best Practices

```yaml
# Standard directory structure
ansible-project/
├── ansible.cfg                 # Ansible configuration
├── inventory/                  # Multiple inventory files
│   ├── production
│   ├── staging
│   └── development
├── playbooks/                  # Main playbooks
│   ├── site.yml               # Master playbook
│   ├── webservers.yml         # Web server playbook
│   └── databases.yml          # Database playbook
├── roles/                     # Reusable roles
│   ├── common/
│   ├── webserver/
│   └── database/
├── group_vars/                # Group variables
│   ├── all.yml
│   ├── webservers.yml
│   └── databases.yml
├── host_vars/                 # Host-specific variables
├── files/                     # Static files
├── templates/                 # Jinja2 templates
├── vault/                     # Encrypted files
└── scripts/                   # Utility scripts
```

### 2. Security Best Practices

```bash
# Use Ansible Vault for sensitive data
ansible-vault create secrets.yml
ansible-vault edit secrets.yml
ansible-vault encrypt existing-file.yml

# Run playbook with vault
ansible-playbook site.yml --ask-vault-pass

# Use vault password file
echo "your_vault_password" > .vault_pass
chmod 600 .vault_pass
ansible-playbook site.yml --vault-password-file .vault_pass

# Encrypt specific variables
ansible-vault encrypt_string 'secret_password' --name 'mysql_root_password'
```

### 3. Playbook Best Practices

```yaml
---
# Always name your plays and tasks
- name: Deploy Web Servers
  hosts: webservers
  become: yes
  
  # Use variables for reusability
  vars:
    http_port: 80
    
  # Pre-tasks for setup
  pre_tasks:
    - name: Update package cache
      dnf:
        update_cache: yes
        
  # Main tasks or roles
  tasks:
    - name: Ensure Apache is installed
      dnf:
        name: httpd
        state: present
      notify: restart apache
      tags: packages
      
  # Handlers for service restarts
  handlers:
    - name: restart apache
      service:
        name: httpd
        state: restarted
        
  # Post-tasks for verification
  post_tasks:
    - name: Verify web service is running
      uri:
        url: "http://{{ ansible_default_ipv4.address }}"
        status_code: 200
```

### 4. Performance Best Practices

```ini
# ansible.cfg performance settings
[defaults]
forks = 10                    # Parallel execution
host_key_checking = False     # Skip SSH key verification
pipelining = True            # SSH pipelining
gather_facts = False         # Skip facts if not needed

[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s
pipelining = True
```

### 5. Testing Best Practices

```bash
# Always test syntax first
ansible-playbook site.yml --syntax-check

# Use check mode for dry runs
ansible-playbook site.yml --check

# Test on a subset of hosts
ansible-playbook site.yml --limit test_servers

# Use tags for selective execution
ansible-playbook site.yml --tags "packages,config"

# Verbose output for debugging
ansible-playbook site.yml -vvv
```

---

## Troubleshooting Guide

### Common SSH Issues

```bash
# Problem: Permission denied (publickey)
# Solution 1: Check SSH key permissions
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub

# Solution 2: Verify key is in authorized_keys
ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@server-ip

# Solution 3: Debug SSH connection
ssh -vvv ec2-user@server-ip
ansible all -m ping -vvv
```

### Inventory Issues

```bash
# Problem: Host unreachable
# Solution: Verify inventory syntax
ansible-inventory --list
ansible-inventory --graph

# Test specific host
ansible host_name -m ping -vvv

# Check DNS resolution
nslookup server-hostname
```

### Playbook Execution Issues

```bash
# Problem: Task failures
# Solution 1: Use ignore_errors
- name: Task that might fail
  command: /path/to/command
  ignore_errors: yes

# Solution 2: Use failed_when
- name: Check service status
  command: systemctl status httpd
  register: service_status
  failed_when: service_status.rc not in [0, 3]

# Solution 3: Use changed_when
- name: Run idempotent command
  command: /path/to/idempotent-script
  changed_when: false
```

### Performance Issues

```bash
# Problem: Slow execution
# Solution 1: Increase forks
ansible-playbook site.yml --forks 20

# Solution 2: Disable fact gathering
- hosts: all
  gather_facts: no

# Solution 3: Use strategy plugins
- hosts: all
  strategy: free  # Don't wait for all hosts
```

### Module Issues

```bash
# Problem: Module not found
# Solution: Check available modules
ansible-doc -l | grep module_name
ansible-doc module_name

# Problem: Python interpreter issues
# Solution: Set Python interpreter
[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

---

## Assessment & Lab Exercises

### Exercise 1: Basic Setup (30 minutes)

**Objectives:**
- Launch EC2 instances
- Install Ansible
- Configure SSH keys
- Test connectivity

**Tasks:**
1. Launch 1 control node + 2 managed nodes
2. Install Ansible on control node
3. Generate and distribute SSH keys
4. Create inventory file
5. Test `ansible all -m ping`

**Verification:**
```bash
ansible all -m ping
ansible all -a "uptime"
ansible all -m setup -a "filter=ansible_hostname"
```

### Exercise 2: Ad-hoc Commands (45 minutes)

**Objectives:**
- Master essential ad-hoc commands
- Understand module usage
- Practice system administration tasks

**Tasks:**
1. System information gathering (10 commands)
2. Package management (5 commands)
3. Service management (5 commands)
4. File operations (5 commands)
5. User management (3 commands)

**Sample Commands:**
```bash
# System info
ansible all -a "uptime"
ansible all -a "free -h"
ansible all -a "df -h"

# Package management
ansible all -m dnf -a "name=htop state=present" --become
ansible all -m dnf -a "name=* state=latest" --become

# Service management
ansible all -m service -a "name=httpd state=started" --become
```

### Exercise 3: First Playbook (60 minutes)

**Objectives:**
- Create structured playbook
- Use variables and templates
- Implement handlers
- Deploy web server

**Requirements:**
- Install Apache web server
- Configure custom index page
- Start and enable service
- Configure firewall
- Verify deployment

**Evaluation Criteria:**
- Correct YAML syntax
- Proper task naming
- Use of variables
- Handler implementation
- Idempotency

### Exercise 4: Advanced Project (90 minutes)

**Objectives:**
- Role-based architecture
- Multi-tier deployment
- Template usage
- Error handling

**Components:**
- Web server role
- Database role
- Load balancer configuration
- Monitoring setup

**Deliverables:**
- Complete project structure
- Working playbooks
- Documentation
- Verification tests

### Assessment Rubric

| Criteria | Excellent (4) | Good (3) | Satisfactory (2) | Needs Improvement (1) |
|----------|---------------|----------|------------------|----------------------|
| **Setup** | Flawless automation, all components working | Minor issues, mostly automated | Manual intervention needed | Significant problems |
| **Ad-hoc Commands** | Masters 20+ commands, creative usage | Comfortable with 15+ commands | Basic usage of 10+ commands | Struggles with basic commands |
| **Playbooks** | Complex, well-structured, reusable | Good structure, working solution | Basic functionality achieved | Syntax errors, limited functionality |
| **Best Practices** | Follows all conventions, excellent documentation | Good practices, adequate docs | Some best practices followed | Poor organization, minimal docs |
| **Troubleshooting** | Independently resolves all issues | Resolves most issues with minimal help | Needs guidance for complex issues | Requires significant assistance |

### Final Project Requirements

**Scenario:** Deploy a complete web application infrastructure

**Components Required:**
1. **Web Tier:** 2 Apache servers with load balancing
2. **Database Tier:** MariaDB with master-slave replication
3. **Monitoring:** Basic monitoring setup
4. **Security:** Firewall configuration, SSL certificates
5. **Backup:** Automated backup scripts

**Deliverables:**
1. Complete Ansible project structure
2. All playbooks and roles
3. Comprehensive documentation
4. Demo presentation (10 minutes)
5. Troubleshooting log

**Timeline:** 2 weeks for completion


