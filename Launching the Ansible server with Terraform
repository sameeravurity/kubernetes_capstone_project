### Step 1: Generate a Keypair using `ssh-keygen`

Run this command on your Terraform workstation instance to generate an SSH key pair. This key will be used to securely access the Ansible server.

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/terraform-key -N ""

This command will create two files in your ~/.ssh/ directory:

~/.ssh/terraform-key (private key)

~/.ssh/terraform-key.pub (public key)
Step 2: Create a variables.tf File
Create a file named variables.tf and add the following Terraform variables. These variables define configurable parameters for your infrastructure.
```bash
vi variables.tf

Add the following content to the variables.tf file:
```bash
variable "region" {
  default = "ap-south-1"
}

variable "instance_type" {
  default = "t2.micro"
}

variable "key_name" {
  default = "terraform-key"
}

variable "public_key_path" {
  default = "~/.ssh/terraform-key.pub"
}

variable "ami_id" {
  default = "ami-0f918f7e67a3323f0" # Note: This AMI ID is for Ubuntu 22.04 LTS in ap-south-1, verify it's the latest/correct one.
}
```
Step 3: Create a main.tf File
Create your main Terraform configuration file named main.tf. This file will define the AWS provider, key pair, security group, and the EC2 instance itself.
```bash
vi main.tf
```
Add the following Terraform configuration to the main.tf file:
```bash
provider "aws" {
  region = var.region
}

# Resource to manage the public key generated locally
# Note: The 'tls_private_key' resource is not strictly necessary for importing an existing public key.
# It's typically used to generate a *new* key pair within Terraform.
# However, if you're managing the local key generation outside of Terraform,
# 'public_key = file(var.public_key_path)' in aws_key_pair is sufficient.
# For simplicity and to directly use the locally generated key, we are relying on 'file(var.public_key_path)'.
resource "tls_private_key" "generated" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

resource "aws_key_pair" "deployer_key" {
  key_name   = var.key_name
  public_key = file(var.public_key_path) # Uses the public key generated in Step 1
}

resource "aws_security_group" "allow_ssh" {
  name        = "allow_ssh"
  description = "Allow SSH inbound traffic"

  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"] # WARNING: 0.0.0.0/0 allows access from anywhere. Restrict this in production.
  }
  ingress {
    description = "Allow HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"] # WARNING: 0.0.0.0/0 allows access from anywhere. Restrict this in production.
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1" # Allows all outbound traffic
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "ec2_instance" {
  ami             = var.ami_id
  instance_type   = var.instance_type
  key_name        = aws_key_pair.deployer_key.key_name
  security_groups = [aws_security_group.allow_ssh.name] # Associates the security group by name

  tags = {
    Name = "Ansible-EC2"
  }
}
```
Markdown

### Step 1: Generate a Keypair using `ssh-keygen`

Run this command on your Terraform workstation instance to generate an SSH key pair. This key will be used to securely access the Ansible server.

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/terraform-key -N ""
This command will create two files in your ~/.ssh/ directory:

~/.ssh/terraform-key (private key)

~/.ssh/terraform-key.pub (public key)

Step 2: Create a variables.tf File
Create a file named variables.tf and add the following Terraform variables. These variables define configurable parameters for your infrastructure.

Bash

vi variables.tf
Add the following content to the variables.tf file:

Terraform

variable "region" {
  default = "ap-south-1"
}

variable "instance_type" {
  default = "t2.micro"
}

variable "key_name" {
  default = "terraform-key"
}

variable "public_key_path" {
  default = "~/.ssh/terraform-key.pub"
}

variable "ami_id" {
  default = "ami-0f918f7e67a3323f0" # Note: This AMI ID is for Ubuntu 22.04 LTS in ap-south-1, verify it's the latest/correct one.
}
Step 3: Create a main.tf File
Create your main Terraform configuration file named main.tf. This file will define the AWS provider, key pair, security group, and the EC2 instance itself.

Bash

vi main.tf
Add the following Terraform configuration to the main.tf file:

Terraform

provider "aws" {
  region = var.region
}

# Resource to manage the public key generated locally
# Note: The 'tls_private_key' resource is not strictly necessary for importing an existing public key.
# It's typically used to generate a *new* key pair within Terraform.
# However, if you're managing the local key generation outside of Terraform,
# 'public_key = file(var.public_key_path)' in aws_key_pair is sufficient.
# For simplicity and to directly use the locally generated key, we are relying on 'file(var.public_key_path)'.
resource "tls_private_key" "generated" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

resource "aws_key_pair" "deployer_key" {
  key_name   = var.key_name
  public_key = file(var.public_key_path) # Uses the public key generated in Step 1
}

resource "aws_security_group" "allow_ssh" {
  name        = "allow_ssh"
  description = "Allow SSH inbound traffic"

  ingress {
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"] # WARNING: 0.0.0.0/0 allows access from anywhere. Restrict this in production.
  }
  ingress {
    description = "Allow HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"] # WARNING: 0.0.0.0/0 allows access from anywhere. Restrict this in production.
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1" # Allows all outbound traffic
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "ec2_instance" {
  ami             = var.ami_id
  instance_type   = var.instance_type
  key_name        = aws_key_pair.deployer_key.key_name
  security_groups = [aws_security_group.allow_ssh.name] # Associates the security group by name

  tags = {
    Name = "Ansible-EC2"
  }
}
Step 4: Create an outputs.tf File
Create an outputs.tf file to display useful information, such as the public IP address of the launched instance and a ready-to-use SSH command.
```bash
vi outputs.tf
```
Add the following content to the outputs.tf file:
```bash
output "instance_public_ip" {
  description = "The public IP address of the Ansible EC2 instance."
  value       = aws_instance.ec2_instance.public_ip
}

output "ssh_command" {
  description = "SSH command to connect to the Ansible EC2 instance."
  value       = "ssh -i ~/.ssh/terraform-key ubuntu@${aws_instance.ec2_instance.public_ip}"
}
```
Step 5: Run the Terraform Commands
Execute the following Terraform commands in sequence within the directory containing your .tf files.

Initialize Terraform: Downloads necessary providers and modules.
```bash
terraform init
```
Validate Configuration: Checks for syntax errors and configuration issues.
```bash
terraform validate
```
Plan Changes: Shows you what actions Terraform will take (create, modify, destroy) without actually performing them.
```bash
terraform plan
```
Apply Changes: Executes the planned actions to provision the resources in AWS. You will be prompted to confirm.
```bash
terraform apply
```
Step 6: SSH into the Instance
After Terraform successfully applies the changes, it will output the instance_public_ip and ssh_command. Use the provided SSH command to connect to your newly launched Ansible workstation.

Example (replace 35.154.119.39 with the actual IP output by terraform apply):
```bash
ssh -i ~/.ssh/terraform-key ubuntu@35.154.119.39
```
````markdown
## Ansible Tasks: Installing HTTPD

This section outlines the steps to install Ansible on your designated Ansible server and then deploy an HTTPD web server using an Ansible playbook.

### Step 1: SSH into the Ansible Server

Use the private key generated by Terraform to securely connect to your Ansible workstation instance. Replace `35.154.119.39` with the actual public IP address of your Ansible EC2 instance obtained from Terraform outputs.

```bash
ssh -i ~/.ssh/terraform-key ubuntu@35.154.119.39
````

### Step 2: Install Ansible on the Server

Once connected to the Ansible server, update the package list and install Ansible.

```bash
sudo apt update
sudo apt install ansible -y
```

### Step 3: Set Up Inventory `ini` File

Configure Ansible's inventory to manage `localhost`, treating the Ansible server itself as the managed node.

1.  **Create `/etc/ansible` directory:**

    ```bash
    sudo mkdir -p /etc/ansible
    ```

2.  **Create an `ini` file for hosts:**

    ```bash
    sudo nano /etc/ansible/hosts
    ```

3.  **Add the following line to the `hosts` file:**

    ```ini
    localhost ansible_connection=local
    ```

    Save and exit the `nano` editor (Ctrl+O, Enter, Ctrl+X).

### Step 4: Test Ansible Connection to Localhost

Verify that Ansible can connect to `localhost` by running a ping module.

```bash
ansible localhost -m ping
```

You should see a success message indicating `pong`.

### Step 5: Create a Playbook `install_httpd.yml`

Create an Ansible playbook named `install_httpd.yml` to define the tasks for installing Apache (httpd).

```bash
sudo vi install_httpd.yml
```

Add the following YAML content to the `install_httpd.yml` file:

```yaml
---
- name: Install HTTPD on localhost
  hosts: localhost
  become: true # Required to run tasks with sudo privileges
  tasks:
    - name: Install Apache
      ansible.builtin.apt:
        name: apache2
        state: present
        update_cache: yes
```

### Step 6: Run the Playbook

Execute the Ansible playbook to install Apache on the localhost.

```bash
ansible-playbook install_httpd.yml
```

### Step 7: Check if Apache is Installed

After the playbook completes, open a web browser and navigate to the public IP address of your Ansible server to verify that the Apache web server is running.

```
http://<YOUR_ANSIBLE_SERVER_PUBLIC_IP>/
```

For example, using the IP from earlier:

```
[http://35.154.119.39/](http://35.154.119.39/)
```

You should see the default Apache welcome page.

```
```
