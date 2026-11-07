# AWS EC2 Nginx Automation Using Ansible Roles

## Project Overview

This project demonstrates the automated deployment and configuration of an Nginx web server on Amazon EC2 using Ansible.

The project initially used a single Ansible playbook but was subsequently refactored into a reusable **Ansible Role** to improve modularity, maintainability, security, and configuration management.

The implementation incorporates Ansible Roles, Jinja2 templates, variables, handlers, Ansible Vault, SSH key-based authentication, and idempotency.

## Project Objectives

- Provision and configure an Amazon EC2 instance.
- Establish secure SSH communication between an Ansible controller and managed node.
- Automate Nginx installation and configuration.
- Organise automation using reusable Ansible Roles.
- Replace hardcoded configuration values with variables.
- Use Jinja2 templates for dynamic configuration.
- Implement handlers for service configuration changes.
- Encrypt sensitive variables using Ansible Vault.
- Validate deployment and idempotency.
- Maintain version-controlled infrastructure configuration using Git and GitHub.

## Technologies Used

| Technology | Purpose |
|---|---|
| AWS EC2 | Cloud infrastructure |
| Amazon Linux 2023 | Linux operating system |
| Ansible | Configuration management |
| Ansible Roles | Modular automation |
| Ansible Vault | Secret encryption |
| Jinja2 | Dynamic configuration templates |
| Nginx | Web server |
| SSH | Secure remote connectivity |
| YAML | Automation configuration |
| Git and GitHub | Version control |

## Architecture

The deployment uses two EC2 instances:

1. **Ansible Controller:** Executes Ansible playbooks and manages the remote server.
2. **Managed Node:** Hosts the Nginx web server and custom HTML landing page.

The controller connects to the managed node using SSH on port 22. Nginx serves the website over HTTP on port 80.

AWS Security Groups restrict SSH access to authorised sources while allowing public HTTP traffic.

## Repository Structure

```text
ansible-aws-nginx/
├── ansible.cfg
├── inventory.ini
├── site.yml
├── group_vars/
│   └── webservers/
│       ├── main.yml
│       └── vault.yml
├── roles/
│   └── nginx/
│       ├── defaults/
│       │   └── main.yml
│       ├── tasks/
│       │   └── main.yml
│       ├── handlers/
│       │   └── main.yml
│       └── templates/
│           ├── index.html.j2
│           └── nginx.conf.j2
├── screenshots/
├── .gitignore
└── README.md
```

## Implementation

### 1. AWS Infrastructure Setup

An Amazon EC2 instance running Amazon Linux 2023 was provisioned to host Nginx.

Security Group rules were configured to permit:

- SSH (TCP 22) from authorised IP addresses.
- HTTP (TCP 80) for public website access.

### 2. SSH Authentication

SSH key-based authentication was configured between the controller and managed node.

During initial setup, connectivity failed because the expected private key was unavailable.

A new SSH key pair was configured and the public key authorised on the managed node.

Successful connectivity was verified using:

```bash
ansible -i inventory.ini webservers -m ping
```

### 3. Ansible Role Implementation

The original flat playbook was refactored into a reusable Nginx Role.

The role separates:

- **Tasks:** Package installation, configuration deployment, and verification.
- **Handlers:** Reloading Nginx when configuration changes.
- **Defaults:** Reusable configuration values.
- **Templates:** Dynamic HTML and Nginx configuration files.

The main playbook, `site.yml`, invokes the Nginx Role.

### 4. Variables and Jinja2 Templates

Configuration values such as the Nginx package, service name, document root, port, website title, and website message are defined as variables.

Jinja2 templates generate the website and Nginx configuration using these values.

This reduces hardcoded configuration and improves reusability.

### 5. Ansible Vault

Ansible Vault was introduced to demonstrate encrypted variable management.

The encrypted file is located at:

`group_vars/webservers/vault.yml`

Vault encryption was verified using the `$ANSIBLE_VAULT` file header.

The Vault password and SSH private keys are excluded from version control.

The Vault demonstration variable is not required by the public Nginx website.

### 6. Handlers

A handler was implemented to reload Nginx when its configuration changes.

The handler is notified by the configuration template task, preventing unnecessary reloads during subsequent executions.

Static HTML updates do not require a web server restart.

## Deployment Instructions

### Prerequisites

- Ansible installed on the controller.
- An accessible Linux EC2 managed node.
- SSH key-based authentication configured.
- Appropriate AWS Security Group rules.
- Ansible Vault password.

### Step 1: Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/ansible-aws-nginx-automation.git
cd ansible-aws-nginx-automation
```

### Step 2: Configure Inventory

Update `inventory.ini` with the current managed node IP address, SSH username, and private key path.

Never commit the private key itself.

### Step 3: Test Connectivity

```bash
ansible -i inventory.ini webservers -m ping
```

### Step 4: Validate Syntax

```bash
ansible-playbook -i inventory.ini site.yml \
  --syntax-check --ask-vault-pass
```

### Step 5: Preview Changes

```bash
ansible-playbook -i inventory.ini site.yml \
  --check --diff --ask-vault-pass
```

Check mode previews configuration changes but does not fully validate a real deployment.

### Step 6: Deploy Nginx

```bash
ansible-playbook -i inventory.ini site.yml \
  --ask-vault-pass
```

### Step 7: Verify Deployment

```bash
curl -I http://YOUR_EC2_PUBLIC_IP
```

A successful deployment should return HTTP status `200 OK`.

Open the instance's public IP address in a browser to verify the custom landing page.

## Idempotency Testing

The playbook is designed to be idempotent.

After the initial deployment, execute it again:

```bash
ansible-playbook -i inventory.ini site.yml \
  --ask-vault-pass
```

When the managed node is already in the desired state and no package updates are available, the expected result is:

```text
changed=0
unreachable=0
failed=0
```

The original flat playbook achieved `changed=0`. The refactored role should be independently tested to confirm the same behaviour.

## Troubleshooting

### SSH Connection Timeout

**Problem:** Ansible initially could not connect to the managed EC2 instance.

**Investigation:** Checked AWS Security Groups, instance connectivity, and SSH access.

### SSH Authentication Failure

**Problem:** The configured private key was missing, causing public-key authentication failure.

**Resolution:** Configured SSH key-based authentication and corrected the inventory's private key path.

### Ansible Vault YAML Error

**Problem:** The Vault variables file contained invalid YAML formatting.

**Resolution:** Corrected the YAML structure, encrypted the file, and successfully completed the playbook syntax check.

### Verification Skipped in Check Mode

**Observation:** The HTTP verification task was skipped during the dry-run execution.

**Explanation:** Check mode does not perform every operation and cannot replace real deployment verification.

## Security Best Practices

- Restrict SSH access to authorised IP addresses.
- Use SSH key-based authentication.
- Never commit private SSH keys.
- Encrypt sensitive variables with Ansible Vault.
- Keep Vault passwords outside version control.
- Use appropriate file permissions.
- Apply least-privilege access.
- Remove unused AWS resources after testing.

## Future Improvements

- Provision infrastructure using Terraform.
- Integrate Ansible with GitHub Actions.
- Add automated testing using Molecule.
- Configure HTTPS using TLS certificates.
- Implement Nginx configuration validation before reload.
- Use dynamic AWS inventory management.
- Introduce monitoring and logging.

## Conclusion

This project demonstrates the transition from basic Ansible automation to a modular configuration management approach.

By introducing Ansible Roles, variables, templates, handlers, and Vault, the project improves maintainability, security, and reusability while supporting consistent cloud server configuration.

The project also provided practical experience troubleshooting SSH authentication, managing AWS infrastructure, and validating automation through idempotency testing.
