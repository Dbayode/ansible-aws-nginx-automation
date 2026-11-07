
# AWS EC2 Infrastructure Automation Using Ansible

## Project Overview

This project demonstrates how Ansible can automate the configuration and deployment of an Nginx web server hosted on Amazon EC2.

The implementation follows DevOps best practices, including SSH key-based authentication, configuration management, infrastructure security, and idempotency.

## Technologies Used

- Amazon Web Services (AWS)
- Amazon EC2
- Amazon Linux 2023
- Ansible
- Nginx
- SSH
- YAML
- Git and GitHub

## Project Architecture

The project uses two Amazon EC2 instances:

**Ansible Controller:** Executes Ansible playbooks and manages remote server configurations.

**Managed Node:** Hosts the Nginx web server and custom HTML landing page.

Ansible communicates with the managed node over SSH using public-key authentication.

The website is accessible through HTTP on port 80.

## Implementation

### 1. Infrastructure Provisioning

An Amazon EC2 instance was configured to host the Nginx web server.

AWS Security Groups were configured to permit SSH access on port 22 from authorised sources and HTTP access on port 80.

### 2. SSH Authentication

SSH key-based authentication was configured between the Ansible controller and the managed EC2 instance.

Connectivity was verified using the Ansible ping module.

### 3. Ansible Configuration Management

An Ansible inventory was created to define the managed server.

A YAML playbook was developed to automate:

- Updating installed packages.
- Installing Nginx.
- Starting the Nginx service.
- Enabling Nginx at system startup.
- Deploying a custom HTML landing page.
- Verifying that the web server responds successfully.

### 4. Deployment Validation

The Ansible playbook completed successfully.

The final execution reported:

```text
ok=6
changed=0
unreachable=0
failed=0
```

### 5. Idempotency

The playbook demonstrated idempotent behaviour.

Ansible evaluated the existing server configuration and reported that no changes were necessary.

This helps prevent unnecessary configuration changes and supports consistent infrastructure management.

## Challenges and Troubleshooting

### SSH Connection Timeout

**Problem:** The Ansible controller initially could not establish an SSH connection to the managed EC2 instance.

**Resolution:** AWS network connectivity and SSH access settings were investigated.

### SSH Authentication Failure

**Problem:** Ansible could reach the managed instance but authentication failed because the configured private key was unavailable.

**Resolution:** SSH key-based authentication was configured, and the Ansible inventory was updated to reference the correct private key.

### Successful Connectivity

The connection was verified using:

```bash
ansible -i inventory.ini webservers -m ping
```

The managed node returned:

```text
SUCCESS
ping: pong
```

## Key Learning Outcomes

This project provided practical experience in:

1. AWS EC2 provisioning and networking.
2. SSH authentication and remote server administration.
3. Ansible inventory management.
4. YAML playbook development.
5. Automated software installation and configuration.
6. Idempotency and configuration consistency.
7. Troubleshooting cloud infrastructure connectivity.

## Conclusion

The project demonstrates how Ansible can simplify infrastructure configuration and software deployment.

By automating repetitive administrative tasks, DevOps engineers can improve deployment consistency, reduce manual intervention, and maintain reliable server configurations.

Further improvements could include provisioning AWS infrastructure using Terraform, integrating Ansible with CI/CD pipelines, and configuring HTTPS.
