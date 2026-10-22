# 🚀 Ansible Web Server Automation on AWS EC2

Automate Nginx web server deployment and configuration on AWS EC2 instances using Ansible.

This project demonstrates Infrastructure as Code (IaC) and configuration management by using Ansible playbooks and roles to automate web server setup. It also explores important Ansible concepts such as variables, loops, conditionals, handlers, Jinja2 templates, and Ansible Vault.

## 📌 Project Overview

Manually installing and configuring web servers can be repetitive and time-consuming. This project simplifies the process by automating web server configuration with Ansible.

Using an Ansible control node, you can connect to remote AWS EC2 instances over SSH and execute automation tasks to configure your web server.

### 🎯 Objectives

* Automate Nginx web server deployment.
* Configure remote AWS EC2 instances using Ansible.
* Understand Ansible playbooks, roles, and inventories.
* Use variables, loops, and conditional statements to simplify automation.
* Apply handlers to manage service changes.
* Explore Jinja2 templates for dynamic configuration.
* Learn secure variable management with Ansible Vault.

## ✨ Features

* **Automated Deployment:** Simplifies Nginx web server installation and configuration.
* **AWS EC2 Integration:** Automates configuration of remote cloud instances.
* **Ansible Roles:** Organizes automation tasks into reusable components.
* **Variables:** Makes configuration easier to customize and maintain.
* **Loops and Conditions:** Reduces repetitive tasks and supports conditional execution.
* **Handlers:** Supports event-driven actions, such as restarting services after configuration changes.
* **Jinja2 Templates:** Enables dynamic configuration files using templates.
* **Ansible Vault:** Demonstrates encrypted storage for sensitive information.
* **SSH Connectivity:** Uses SSH to communicate with managed hosts without requiring an Ansible agent on each host.

## 🛠️ Technologies Used

| Technology    | Purpose                                           |
| ------------- | ------------------------------------------------- |
| AWS EC2       | Cloud virtual machines for hosting the web server |
| Ansible       | Configuration management and automation           |
| Nginx         | Web server                                        |
| Linux         | Operating system environment                      |
| YAML          | Playbook and configuration file format            |
| SSH           | Secure communication with remote servers          |
| Jinja2        | Dynamic configuration templating                  |
| Ansible Vault | Encryption of sensitive variables                 |

## 🏗️ Project Architecture

The project follows an agentless automation approach.

```text
                 ┌────────────────────────┐
                 │   Ansible Control Node │
                 │                        │
                 │  Ansible Playbook      │
                 │  Inventory             │
                 │  Web Server Role       │
                 └────────────┬───────────┘
                              │
                         SSH Connection
                              │
                              ▼
                 ┌────────────────────────┐
                 │     AWS EC2 Instance   │
                 │                        │
                 │     Linux Server       │
                 │     Nginx Web Server   │
                 └────────────┬───────────┘
                              │
                              ▼
                     HTTP Traffic (80)
                              │
                              ▼
                       Web Application
```

## 📂 Project Structure

The repository contains the main playbook and a dedicated web server role.

```text
Ansible-webserver-automation/
│
├── roles/
│   └── webserver/
│       ├── tasks/
│       ├── handlers/
│       ├── templates/
│       ├── vars/
│       ├── defaults/
│       └── files/
│
├── webserver.yaml
└── README.md
```

**Note:** The role subdirectories shown above represent a conventional Ansible role layout. Adjust the structure to match the files actually present in your repository.

### Key Components

* `webserver.yaml` — The main playbook that targets managed hosts, enables privilege escalation, and invokes the `webserver` role.
* `roles/webserver/` — Contains the tasks and supporting files used to configure the web server.
* `tasks/` — Defines the automation steps.
* `handlers/` — Defines actions triggered by task notifications.
* `templates/` — Stores Jinja2 templates, if used.
* `vars/` and `defaults/` — Store role variables and default values, if present.
* `files/` — Stores static files that the role may deploy, if present.

## ⚙️ Prerequisites

Before running this project, make sure you have:

* An AWS account.
* An AWS EC2 instance running a supported Linux distribution.
* Ansible installed on your control node.
* SSH access to the target EC2 instance.
* An appropriate SSH private key.
* A security group configured to allow the required SSH and HTTP traffic.
* A user with the necessary permissions to install and configure packages.

## 🚀 Installation and Setup

### Step 1: Clone the Repository

```bash
git clone https://github.com/dtechnocloud/Ansible-webserver-automation.git
```

Navigate to the project directory:

```bash
cd Ansible-webserver-automation
```

### Step 2: Install Ansible

On a Debian or Ubuntu-based control node, you can install Ansible using:

```bash
sudo apt update
sudo apt install ansible -y
```

Verify the installation:

```bash
ansible --version
```

### Step 3: Configure Your AWS EC2 Instance

Launch an EC2 instance with a Linux distribution supported by your playbooks.

Configure the instance's security group to allow:

| Port | Protocol | Purpose                       |
| ---- | -------- | ----------------------------- |
| 22   | TCP      | SSH access for Ansible        |
| 80   | TCP      | HTTP access to the web server |
| 443  | TCP      | HTTPS access, if configured   |

Restrict SSH access to trusted IP addresses rather than allowing access from everywhere.

### Step 4: Configure SSH Access

Set the appropriate permissions on your private key:

```bash
chmod 400 /path/to/your-key.pem
```

Test connectivity to your EC2 instance:

```bash
ssh -i /path/to/your-key.pem ec2-user@YOUR_EC2_PUBLIC_IP
```

Replace the username with the appropriate account for your AMI. For example, Ubuntu instances commonly use `ubuntu`, while Amazon Linux commonly uses `ec2-user`.

### Step 5: Configure the Ansible Inventory

Create an inventory file named `inventory.ini` in the project root.

Example:

```ini
[webservers]
webserver1 ansible_host=YOUR_EC2_PUBLIC_IP ansible_user=ec2-user ansible_ssh_private_key_file=/path/to/your-key.pem
```

Replace the example values with your actual EC2 public IP address, SSH username, and private key path.

**Important:** Keep your private key outside the repository. Never commit private keys or other credentials to GitHub.

### Step 6: Test Connectivity

Run the Ansible ping module:

```bash
ansible -i inventory.ini webservers -m ping
```

A successful connection should return a response containing:

```json
{
    "ping": "pong"
}
```

This confirms that Ansible can communicate with the managed host.

## ▶️ Running the Playbook

The main playbook, `webserver.yaml`, invokes the `webserver` role and uses privilege escalation.

Run it with your configured inventory:

```bash
ansible-playbook -i inventory.ini webserver.yaml
```

If your remote user requires a sudo password, use:

```bash
ansible-playbook -i inventory.ini webserver.yaml --ask-become-pass
```

The playbook's exact behavior depends on the tasks implemented in `roles/webserver/`.

## 🌐 Verify the Deployment

After the playbook completes successfully, check whether Nginx is running on the EC2 instance.

For a system using systemd:

```bash
sudo systemctl status nginx
```

Check the HTTP response from the control node:

```bash
curl -I http://YOUR_EC2_PUBLIC_IP
```

You can also open the following address in your browser:

```text
http://YOUR_EC2_PUBLIC_IP
```

Replace `YOUR_EC2_PUBLIC_IP` with your instance's public IP address.

Ensure that the EC2 security group, operating system firewall, and Nginx configuration allow the requested traffic.

## 🔐 Security Best Practices

* Use Ansible Vault to encrypt sensitive variables.
* Never commit AWS credentials, SSH private keys, passwords, or unencrypted secrets.
* Restrict inbound SSH access to trusted IP addresses.
* Follow the principle of least privilege when configuring AWS IAM permissions.
* Keep Ansible, Linux packages, and Nginx updated.
* Store sensitive values in encrypted files rather than plain-text variables.
* Verify firewall rules before exposing services publicly.

Example command to create an encrypted Ansible Vault file:

```bash
ansible-vault create secrets.yml
```

Edit an existing encrypted file:

```bash
ansible-vault edit secrets.yml
```

Run a playbook that uses an encrypted variables file:

```bash
ansible-playbook -i inventory.ini webserver.yaml --ask-vault-pass
```

The last command assumes the playbook or role loads `secrets.yml` and uses its variables.

## 🧪 Troubleshooting

| Problem                      | Possible Solution                                                            |
| ---------------------------- | ---------------------------------------------------------------------------- |
| SSH connection failure       | Verify the public IP, SSH username, key path, and security group.            |
| Permission denied            | Check SSH key permissions and the remote user's access.                      |
| Ansible cannot find the host | Verify the inventory file path and host group name.                          |
| Nginx is not running         | Check the playbook output and Nginx service logs.                            |
| HTTP connection fails        | Verify the security group, firewall rules, Nginx status, and listening port. |
| Privilege escalation fails   | Confirm that the remote user has appropriate sudo permissions.               |
| Vault password error         | Verify that you are using the correct Vault password.                        |

## 📚 What I Learned

This project provides practical experience with:

* Infrastructure automation using Ansible.
* Deploying and configuring web servers on AWS EC2.
* Managing remote Linux servers over SSH.
* Structuring automation using Ansible roles.
* Writing and executing YAML playbooks.
* Working with variables, loops, conditionals, and handlers.
* Using Jinja2 templates for configuration management.
* Protecting sensitive data with Ansible Vault.
* Troubleshooting cloud infrastructure and service connectivity.

## 🔮 Future Improvements

Possible enhancements include:

* Provisioning EC2 instances automatically using Terraform.
* Configuring HTTPS using SSL/TLS certificates.
* Adding an automated CI/CD pipeline.
* Integrating Ansible with AWS dynamic inventory.
* Deploying a custom website through the playbook.
* Adding automated deployment verification.
* Implementing monitoring and logging.
* Improving security through automated compliance checks.

## 🤝 Contributing

Contributions, suggestions, and improvements are welcome.

1. Fork this repository.
2. Create a feature branch.
3. Commit your changes.
4. Push your branch.
5. Open a pull request.

## 👨‍💻 Author

**dtechnocloud**

GitHub: [@dtechnocloud](https://github.com/dtechnocloud)

Project Repository: [Ansible-webserver-automation](https://github.com/dtechnocloud/Ansible-webserver-automation)

---

⭐ If you find this project useful, consider giving the repository a star!

**Built to explore DevOps, cloud infrastructure, and automation with Ansible.**
