# Nginx Configuration with Ansible

This directory contains Ansible playbooks and roles for automating the installation and configuration of Nginx web servers with static website deployment.

## 📁 Directory Structure

```
Nginx-configuration/
├── hosts                           # Ansible inventory file
├── nginx.yml                       # Main playbook
├── nginx/                          # Nginx installation role
│   ├── tasks/
│   │   └── main.yml               # Install and start Nginx
│   ├── handlers/
│   │   └── main.yml               # Service handlers
│   ├── defaults/
│   │   └── main.yml               # Default variables
│   └── vars/
│       └── main.yml               # Role variables
└── nginx_static_website/           # Static website deployment role
    ├── tasks/
    │   └── main.yml               # Deploy static website
    ├── handlers/
    │   └── main.yml               # Handlers for website changes
    ├── defaults/
    │   └── main.yml               # Default variables
    └── vars/
        └── main.yml               # Role variables
```

## 🎯 Purpose

This configuration automates:
- Installation of Nginx web server on Ubuntu systems
- Starting and enabling Nginx service
- Deploying a static website from a Git repository
- Managing website content in `/var/www/html/`

## 📋 Prerequisites

- Ansible installed on control node
- Target servers running Ubuntu
- SSH access configured with sudo privileges
- Python installed on target servers

## 🚀 Usage

### Running the Playbook

```bash
# Test the playbook (dry-run)
ansible-playbook --check -i hosts nginx.yml

# Deploy Nginx and static website
ansible-playbook -i hosts nginx.yml

# Run with verbose output
ansible-playbook -i hosts nginx.yml -v
```

### Inventory Configuration

Edit the `hosts` file with your server details:

```ini
[nginx_server]
server1 ansible_host=<Private-IP-1>
server2 ansible_host=<Private-IP-2>

[all:vars]
ansible_user=ubuntu
```

## 🔧 Roles

### 1. nginx

Installs and configures the Nginx web server.

**Tasks:**
- Installs Nginx using apt package manager
- Starts the Nginx service
- Enables Nginx to start on boot

**Default Port:** 80

### 2. nginx_static_website

Deploys a static website to the Nginx server.

**Tasks:**
- Clones a Git repository containing the static website
- Removes previous website content
- Deploys new website files to `/var/www/html/`

**Default Repository:** https://github.com/cloudacademy/static-website-example.git

## 🛠️ Customization

### Change the Website Repository

Edit `nginx_static_website/tasks/main.yml`:

```yaml
- name: Cloning the repo
  git:
    repo: https://github.com/YOUR-USERNAME/YOUR-REPO.git
    dest: "/tmp/static-website-example"
    update: yes
```

### Modify Nginx Configuration

Add custom Nginx configurations by creating additional tasks in the `nginx/tasks/main.yml` file or by using templates.

## 🧪 Testing

After deployment, verify the installation:

```bash
# Check Nginx status on target servers
ansible nginx_server -i hosts -m shell -a "systemctl status nginx"

# Test web access
curl http://<server-ip>
```

Or open your browser and navigate to: `http://<server-public-ip>`

## 📊 What Gets Deployed

The playbook performs the following on each target server:

1. ✅ Installs Nginx web server
2. ✅ Enables and starts Nginx service
3. ✅ Clones static website repository
4. ✅ Deploys website content to `/var/www/html/`
5. ✅ Website becomes accessible on port 80

## 🔒 Security Considerations

- Ensure target servers have proper security group rules
- Port 80 should be open for HTTP traffic
- SSH (port 22) should only be accessible from the Ansible control node
- Consider using HTTPS with SSL certificates for production

## 🐛 Troubleshooting

### Nginx fails to start
```bash
# Check Nginx configuration
ansible nginx_server -i hosts -m shell -a "nginx -t"

# View Nginx logs
ansible nginx_server -i hosts -m shell -a "tail -n 50 /var/log/nginx/error.log"
```

### Website not displaying
- Verify files are in `/var/www/html/`
- Check Nginx service is running
- Ensure port 80 is open in security groups
- Check file permissions in `/var/www/html/`

### SSH connection issues
- Verify the ansible_host IPs are correct
- Ensure SSH keys are properly configured
- Check security group allows SSH from control node

## 📝 Notes

- The playbook is idempotent and can be run multiple times safely
- Each run will refresh the website content from the Git repository
- Previous website content is cleared before deployment
- Nginx is configured with default settings

## 🔗 Integration

This playbook is integrated with the main `Installation.yml` playbook and can be executed:
- Standalone using this directory's `nginx.yml`
- As part of the complete infrastructure deployment
- Through Jenkins CI/CD pipeline

---

For complete infrastructure setup including MongoDB, refer to the main [README.md](../README.md)
