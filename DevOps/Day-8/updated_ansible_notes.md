
## 🎯 Understanding AWS EC2 User & IP Configuration

### **IMPORTANT: User and IP Guidelines**

#### **1. Which User to Use?**
- **Control Node (Ansible Master)**: Always use `ec2-user` 
- **Managed Nodes (Target Servers)**: Always use `ec2-user`
- **Never use `root` user** for Ansible operations
- All commands in this guide assume you're logged in as `ec2-user`

#### **2. Which IP Address to Use?**

| Connection Type | IP Type | When to Use | Example |
|----------------|---------|-------------|---------|
| **From Internet** | Public IP | Connect from your laptop/desktop | `54.123.45.67` |
| **Between EC2 Instances** | Private IP | Ansible control → managed nodes | `172.31.45.67` |

**Rule of Thumb:**
- Use **Public IP** to SSH from your computer to EC2
- Use **Private IP** in Ansible inventory files for EC2-to-EC2 communication

---

## 📋 Prerequisites

Before starting, ensure you have:
- [ ] AWS Account with EC2 permissions
- [ ] Key pair downloaded (.pem file)
- [ ] SSH client (Terminal on Mac/Linux, PuTTY on Windows)
- [ ] Basic Linux command knowledge

---

## 🔧 Step-by-Step Setup Guide

### Step 1: Launch EC2 Instances in AWS Console

#### 1.1 Launch Control Node (Ansible Master)
```
1. Go to AWS EC2 Console
2. Click "Launch Instance"
3. Choose "Amazon Linux 2023 AMI"
4. Select "t2.micro" (free tier)
5. Create new key pair or select existing one
6. Download the .pem file (save it securely!)
7. Configure Security Group:
   - SSH (22) from 0.0.0.0/0
   - HTTP (80) from 0.0.0.0/0  
   - HTTPS (443) from 0.0.0.0/0
8. Launch Instance
9. Note down: Public IP and Private IP
```

#### 1.2 Launch Managed Nodes (2-3 instances)
```
Repeat the same process for 2-3 more instances:
- IMPORTANT: Use the SAME key pair
- Same Security Group settings
- Note down Public and Private IPs for each
```

#### 1.3 Example Instance Details After Launch
```
Control Node:
- Public IP: 54.123.45.10
- Private IP: 172.31.32.10

Managed Node 1:
- Public IP: 54.123.45.11  
- Private IP: 172.31.32.11

Managed Node 2:
- Public IP: 54.123.45.12
- Private IP: 172.31.32.12
```

### Step 2: Connect to Control Node

#### 2.1 Set Key Permissions (Linux/Mac)
```bash
# Navigate to where you downloaded the .pem file
cd ~/Downloads

# Set correct permissions (CRITICAL STEP)
chmod 400 your-key-name.pem

# Example:
chmod 400 ansible-lab-key.pem
```

#### 2.2 Connect to Control Node
```bash
# Connect using PUBLIC IP and ec2-user
ssh -i ansible-lab-key.pem ec2-user@54.123.45.10

# Replace with your actual:
# - Key file name: ansible-lab-key.pem
# - Public IP: 54.123.45.10
```

#### 2.3 Windows Users (PuTTY)
```
1. Convert .pem to .ppk using PuTTYgen
2. Open PuTTY
3. Host Name: ec2-user@54.123.45.10
4. Connection > SSH > Auth > Browse for .ppk file
5. Click Open
```

### Step 3: Install Ansible on Control Node

```bash
# After connecting to control node as ec2-user
# You should see prompt like: [ec2-user@ip-172-31-32-10 ~]$

# Update system
sudo dnf update -y

# Install Ansible and essential tools
sudo dnf install -y ansible python3-pip git tree wget curl vim htop

# Verify installation
ansible --version
whoami  # Should show: ec2-user
```

### Step 4: Generate SSH Keys on Control Node

```bash
# Generate SSH key pair (as ec2-user)
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa

# Press Enter for all prompts (no passphrase for lab)

# Verify key creation
ls -la ~/.ssh/
# Should show: id_rsa (private) and id_rsa.pub (public)

# Display public key (copy this output)
cat ~/.ssh/id_rsa.pub
```

### Step 5: Distribute SSH Keys to Managed Nodes

#### Method 1: Using ssh-copy-id (Recommended)
```bash
# From control node (as ec2-user), copy key to each managed node
# Use PRIVATE IP addresses for managed nodes

ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@172.31.32.11
ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@172.31.32.12

# You'll be prompted for the original .pem key
# This might fail initially, use Method 2 if needed
```

#### Method 2: Manual Key Distribution (If Method 1 fails)
```bash
# Step 1: Copy your public key content
cat ~/.ssh/id_rsa.pub
# Copy the entire output

# Step 2: SSH to each managed node using original key
# (Open a new terminal window from your computer)
ssh -i ansible-lab-key.pem ec2-user@54.123.45.11

# Step 3: On the managed node, add the public key
mkdir -p ~/.ssh
echo "PASTE_YOUR_PUBLIC_KEY_HERE" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh
exit

# Repeat for all managed nodes
```

### Step 6: Test SSH Connectivity

```bash
# From control node, test passwordless connection
# Use PRIVATE IPs for internal communication
ssh ec2-user@172.31.32.11
ssh ec2-user@172.31.32.12

# Should connect without password prompt
# Type 'exit' to return to control node
```

### Step 7: Create Ansible Project Structure

```bash
# On control node (as ec2-user)
cd /home/ec2-user

# Create project directory
mkdir -p ansible-lab
cd ansible-lab

# Create directory structure
mkdir -p {playbooks,roles,group_vars,host_vars,files,templates}
mkdir -p roles/webserver/{tasks,handlers,templates,files,vars,defaults}
```

### Step 8: Create Configuration Files

#### 8.1 Create ansible.cfg
```bash
cat > ansible.cfg << 'EOF'
[defaults]
inventory = inventory
remote_user = ec2-user
private_key_file = ~/.ssh/id_rsa
host_key_checking = False
timeout = 30
gather_facts = True
stdout_callback = yaml

[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
EOF
```

#### 8.2 Create Inventory File
```bash
# CRITICAL: Use PRIVATE IP addresses in inventory
cat > inventory << 'EOF'
# Ansible Inventory - Use PRIVATE IPs for EC2-to-EC2 communication

[webservers]
web1 ansible_host=172.31.32.11
web2 ansible_host=172.31.32.12

[databases]
db1 ansible_host=172.31.32.13

# Common variables for all hosts
[all:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python3
EOF

# IMPORTANT: Replace the IP addresses above with your actual PRIVATE IPs
```

### Step 9: Test Ansible Connectivity

```bash
# Test ping to all hosts
ansible all -m ping

# Expected successful output:
# web1 | SUCCESS => {
#     "changed": false,
#     "ping": "pong"
# }
# web2 | SUCCESS => {
#     "changed": false, 
#     "ping": "pong"
# }

# If successful, you're ready to proceed!
```

---

## 🔍 IP Address Reference Guide

### **Public IP Usage**
```bash
# Connect from your computer to EC2 instances
ssh -i key.pem ec2-user@54.123.45.10    # Control node
ssh -i key.pem ec2-user@54.123.45.11    # Managed node 1
ssh -i key.pem ec2-user@54.123.45.12    # Managed node 2
```

### **Private IP Usage**  
```bash
# Used in Ansible inventory and for EC2-to-EC2 communication
[webservers]
web1 ansible_host=172.31.32.11    # Private IP
web2 ansible_host=172.31.32.12    # Private IP

# SSH between EC2 instances (from control node)
ssh ec2-user@172.31.32.11         # Uses private IP
ssh ec2-user@172.31.32.12         # Uses private IP
```

### **Finding Your IP Addresses**
```bash
# On any EC2 instance, check:

# Private IP
hostname -I
# or
curl -s http://169.254.169.254/latest/meta-data/local-ipv4

# Public IP  
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```

---

## 🧪 Essential Ad-hoc Commands

All commands should be run as `ec2-user` from the control node:

### Basic Connectivity
```bash
# Ping all hosts
ansible all -m ping

# Check uptime
ansible all -a "uptime"

# Check current user on all hosts
ansible all -a "whoami"

# Check IP addresses
ansible all -a "hostname -I"
```

### System Information
```bash
# Check OS version
ansible all -a "cat /etc/os-release"

# Check memory
ansible all -a "free -h"

# Check disk space
ansible all -a "df -h"

# System facts
ansible all -m setup -a "filter=ansible_distribution*"
```

### Package Management (with sudo)
```bash
# Update packages
ansible all -m dnf -a "name=* state=latest update_cache=yes" --become

# Install package
ansible all -m dnf -a "name=htop state=present" --become

# Install multiple packages
ansible all -m dnf -a "name=htop,tree,wget state=present" --become
```

### File Operations
```bash
# Create directory
ansible all -m file -a "path=/tmp/test state=directory mode=0755"

# Create file with content
ansible all -m copy -a "content='Hello from Ansible' dest=/tmp/hello.txt mode=0644"

# Check file contents
ansible all -a "cat /tmp/hello.txt"
```

---

## 🌐 Simple Web Server Deployment

### Create Simple Playbook
```bash
cat > playbooks/webserver.yml << 'EOF'
---
- name: Deploy Simple Web Server
  hosts: webservers
  become: yes
  
  tasks:
    - name: Install Apache
      dnf:
        name: httpd
        state: present
    
    - name: Start and enable Apache
      service:
        name: httpd
        state: started
        enabled: yes
    
    - name: Create custom index page
      copy:
        content: |
          <!DOCTYPE html>
          <html>
          <head>
              <title>Welcome to {{ ansible_hostname }}</title>
              <style>
                  body { 
                      font-family: Arial, sans-serif; 
                      text-align: center; 
                      margin: 50px;
                      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
                      color: white;
                      min-height: 80vh;
                      display: flex;
                      align-items: center;
                      justify-content: center;
                      flex-direction: column;
                  }
                  .container {
                      background: rgba(255,255,255,0.1);
                      padding: 40px;
                      border-radius: 15px;
                      backdrop-filter: blur(10px);
                  }
                  h1 { font-size: 3em; margin-bottom: 20px; }
                  p { font-size: 1.2em; }
              </style>
          </head>
          <body>
              <div class="container">
                  <h1>🚀 Success!</h1>
                  <p>Web server running on <strong>{{ ansible_hostname }}</strong></p>
                  <p>IP Address: <strong>{{ ansible_default_ipv4.address }}</strong></p>
                  <p>Deployed with Ansible by <strong>ec2-user</strong></p>
                  <p>OS: {{ ansible_distribution }} {{ ansible_distribution_version }}</p>
              </div>
          </body>
          </html>
        dest: /var/www/html/index.html
        mode: '0644'
    
    - name: Configure firewall
      firewalld:
        service: http
        permanent: yes
        state: enabled
        immediate: yes
      ignore_errors: yes
EOF
```

### Run the Playbook
```bash
# Check syntax
ansible-playbook playbooks/webserver.yml --syntax-check

# Dry run
ansible-playbook playbooks/webserver.yml --check

# Execute playbook
ansible-playbook playbooks/webserver.yml

# Verify deployment
ansible webservers -m service -a "name=httpd" --become
```

### Test Web Servers
```bash
# Test from control node using private IPs
curl http://172.31.32.11
curl http://172.31.32.12

# Or test from your browser using PUBLIC IPs
# http://54.123.45.11
# http://54.123.45.12
```

---

## 🛠️ Quick Setup Automation Script

Create this script to automate the entire setup:

```bash
cat > setup-ansible-lab.sh << 'EOF'
#!/bin/bash

echo "🚀 Ansible Lab Quick Setup"
echo "=========================="

# Colors
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
RED='\033[0;31m'
NC='\033[0m'

print_status() {
    echo -e "${GREEN}[✓]${NC} $1"
}

print_warning() {
    echo -e "${YELLOW}[!]${NC} $1"
}

print_error() {
    echo -e "${RED}[✗]${NC} $1"
}

# Check current user
if [ "$USER" != "ec2-user" ]; then
    print_error "This script must be run as 'ec2-user'"
    print_warning "Current user: $USER"
    exit 1
fi

print_status "Running as ec2-user ✓"

# Update system
print_status "Updating system packages..."
sudo dnf update -y > /dev/null 2>&1

# Install Ansible
print_status "Installing Ansible and tools..."
sudo dnf install -y ansible python3-pip git tree wget curl vim htop > /dev/null 2>&1

# Verify Ansible
if command -v ansible &> /dev/null; then
    print_status "Ansible installed: $(ansible --version | head -n1)"
else
    print_error "Ansible installation failed"
    exit 1
fi

# Generate SSH keys
if [ ! -f ~/.ssh/id_rsa ]; then
    print_status "Generating SSH key pair..."
    ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N "" > /dev/null 2>&1
    print_status "SSH key generated"
else
    print_warning "SSH key already exists"
fi

# Create project structure
PROJECT_DIR="$HOME/ansible-lab"
if [ ! -d "$PROJECT_DIR" ]; then
    print_status "Creating project structure..."
    mkdir -p "$PROJECT_DIR"/{playbooks,roles,group_vars,host_vars,files,templates}
    mkdir -p "$PROJECT_DIR"/roles/webserver/{tasks,handlers,templates,files,vars,defaults}
else
    print_warning "Project directory already exists"
fi

cd "$PROJECT_DIR"

# Create ansible.cfg
print_status "Creating ansible.cfg..."
cat > ansible.cfg << 'ANSIBLE_CFG'
[defaults]
inventory = inventory
remote_user = ec2-user
private_key_file = ~/.ssh/id_rsa
host_key_checking = False
timeout = 30
gather_facts = True
stdout_callback = yaml

[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
ANSIBLE_CFG

# Create sample inventory
print_status "Creating sample inventory..."
cat > inventory << 'INVENTORY'
# Replace these PRIVATE IP addresses with your actual EC2 private IPs

[webservers]
web1 ansible_host=172.31.32.11
web2 ansible_host=172.31.32.12

[databases]
db1 ansible_host=172.31.32.13

[all:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_python_interpreter=/usr/bin/python3
INVENTORY

# Create test playbook
print_status "Creating test playbook..."
cat > playbooks/test.yml << 'PLAYBOOK'
---
- name: Test Connection and Basic Info
  hosts: all
  gather_facts: yes
  
  tasks:
    - name: Ping all hosts
      ping:
    
    - name: Check uptime
      command: uptime
      register: uptime_output
    
    - name: Display system info
      debug:
        msg: |
          Host: {{ inventory_hostname }}
          OS: {{ ansible_distribution }} {{ ansible_distribution_version }}
          IP: {{ ansible_default_ipv4.address }}
          Uptime: {{ uptime_output.stdout }}
PLAYBOOK

print_status "Setup completed!"
echo ""
print_warning "IMPORTANT NEXT STEPS:"
echo "1. Update inventory file with your PRIVATE IP addresses"
echo "2. Distribute SSH keys to managed nodes:"
echo "   ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@PRIVATE_IP"
echo "3. Test connectivity: ansible all -m ping"
echo ""
print_status "Your SSH public key:"
cat ~/.ssh/id_rsa.pub
echo ""
print_status "Project directory: $PROJECT_DIR"
EOF

chmod +x setup-ansible-lab.sh
```

---

## 🚨 Common Issues and Solutions

### Issue 1: Permission Denied (publickey)
```bash
# Problem: Can't SSH to managed nodes
# Solution: Check SSH key permissions
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub

# Verify key distribution
ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@PRIVATE_IP
```

### Issue 2: Host Unreachable
```bash
# Problem: Ansible can't reach managed nodes
# Solution: Check security groups and IPs

# Verify inventory uses PRIVATE IPs
cat inventory

# Test SSH manually
ssh ec2-user@172.31.32.11

# Check security groups allow SSH (port 22)
```

### Issue 3: Wrong User
```bash
# Problem: Running as wrong user
# Solution: Always use ec2-user

# Check current user
whoami

# Switch to ec2-user if needed
sudo su - ec2-user
```

### Issue 4: Ansible Command Not Found
```bash
# Problem: Ansible not in PATH
# Solution: Reinstall or add to PATH

# Check if installed
which ansible

# Reinstall if needed
sudo dnf install -y ansible

# Add to PATH if needed
export PATH=$PATH:/usr/local/bin
```

---

## 📋 Complete Command Reference

### Connection Commands (Run as ec2-user)
```bash
# Connect to control node from your computer
ssh -i key.pem ec2-user@PUBLIC_IP

# Connect from control node to managed nodes
ssh ec2-user@PRIVATE_IP
```

### Ansible Commands (Run from control node as ec2-user)
```bash
# Test connectivity
ansible all -m ping

# System information
ansible all -a "uptime"
ansible all -a "free -h" 
ansible all -a "df -h"

# Package management (requires --become)
ansible all -m dnf -a "name=htop state=present" --become

# Service management (requires --become)
ansible all -m service -a "name=httpd state=started" --become

# File operations
ansible all -m copy -a "content='test' dest=/tmp/test.txt"

# Run playbooks
ansible-playbook playbooks/playbook.yml
```

---

## 🎯 Lab Exercise Checklist

### ✅ Phase 1: Setup (30 minutes)
- [ ] Launch EC2 instances (1 control + 2 managed)
- [ ] Connect to control node as `ec2-user`
- [ ] Install Ansible
- [ ] Generate SSH keys
- [ ] Distribute keys to managed nodes
- [ ] Test `ansible all -m ping`

### ✅ Phase 2: Configuration (20 minutes) 
- [ ] Create project directory
- [ ] Configure ansible.cfg
- [ ] Create inventory with PRIVATE IPs
- [ ] Test ad-hoc commands
- [ ] Verify user permissions

### ✅ Phase 3: Deployment (30 minutes)
- [ ] Create web server playbook
- [ ] Run playbook successfully
- [ ] Verify web servers accessible
- [ ] Test from browser using PUBLIC IPs

### ✅ Phase 4: Verification (10 minutes)
- [ ] All services running
- [ ] Web pages display correctly
- [ ] Ansible facts collection working
- [ ] Documentation completed

---

## 🔒 Security Best Practices

### SSH Security
```bash
# Always use key-based authentication
# Never share private keys
# Set proper permissions on keys
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
```

### AWS Security
```bash
# Use Security Groups properly
# Restrict SSH to your IP when possible
# Use Private IPs for internal communication
# Regularly rotate access keys
```

### Ansible Security
```bash
# Use Ansible Vault for sensitive data
ansible-vault create secrets.yml

# Don't hardcode passwords in playbooks
# Use become only when necessary
# Validate playbooks before running
ansible-playbook playbook.yml --syntax-check
```

---

## 🎉 Success Verification

When everything is working correctly, you should see:

### 1. Successful SSH Connections
```bash
ssh ec2-user@172.31.32.11  # Should connect without password
```

### 2. Successful Ansible Ping
```bash
ansible all -m ping
# All hosts return "pong"
```

### 3. Working Web Servers
```bash
# Test using PUBLIC IPs in browser:
http://54.123.45.11
http://54.123.45.12
```

### 4. Proper User Context
```bash
whoami          # Returns: ec2-user
ansible all -a "whoami"  # All return: ec2-user
```

---

## 📖 Quick Reference Card

| Task | Command | User | IP Type |
|------|---------|------|---------|
| Connect from laptop | `ssh -i key.pem ec2-user@IP` | ec2-user | Public |
| Connect between EC2 | `ssh ec2-user@IP` | ec2-user | Private |
| Ansible inventory | `ansible_host=IP` | ec2-user | Private |
| Run Ansible commands | `ansible all -m ping` | ec2-user | Private |
| Browse web servers | `http://IP` | N/A | Public |

---

## 🚀 **You're Now Ready to Master Ansible on AWS!**

**Remember the Golden Rules:**
1. **Always use `ec2-user`** for all operations
2. **Use PUBLIC IPs** to connect from internet
3. **Use PRIVATE IPs** in Ansible inventory  
4. **Test connectivity** before running playbooks
5. **Use `--become`** for system-level tasks
ible-guide.md.
