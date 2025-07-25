# Task 1: Terraform Server Launch

---

## Launch a Terraform Server

### Step 1: Create an EC2 Instance

Follow these steps to manually launch a `t2.micro` instance:

* **Instance Type:** `t2.micro`
* **Operating System:** `Ubuntu 22.04 LTS`
* **Region:** `ap-south-1`

### Step 2: Configure Network and Security

* **Security Groups:** Enable inbound traffic for the following protocols:
    * `SSH`
    * `HTTP`
    * `HTTPS`
* **Key Pair:** Create a new key pair for secure SSH access.

### Step 3: Configure Storage

* **Storage Size:** Set the root volume to `10 GiB`.

### Step 4: Connect to the Instance

Once the instance is launched, connect to it using one of the following methods:

* **MobaXterm**
* **PuTTY**
* **EC2 Instance Connect**

Use the username **"ubuntu"** for your connection.

### Step 5: Set Hostname

After connecting, run the following commands to set the hostname to **"terraform"**:

```bash
sudo hostnamectl set-hostname terraform
bash
```
## Step 6: Install Terraform on the Instance

This section details the steps to install Terraform on your designated Terraform workstation instance.

1.  **Update Package List and Install Prerequisites:**
    First, update your package list and install necessary tools like `wget` and `unzip`.

    ```bash
    sudo apt update -y
    sudo apt install -y wget unzip
    ```

2.  **Download Terraform:**
    Download the Terraform zip file for Linux AMD64 directly from HashiCorp's releases. We'll use version `1.8.2` for this example, but you can adjust `TERRAFORM_VERSION` as needed for future updates.

    ```bash
    TERRAFORM_VERSION="1.8.2"
    wget [https://releases.hashicorp.com/terraform/$](https://releases.hashicorp.com/terraform/$){TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip
    ```

3.  **Unzip and Clean Up:**
    Extract the downloaded zip file and then remove the redundant zip file.

    ```bash
    unzip terraform_${TERRAFORM_VERSION}_linux_amd64.zip
    rm terraform_${TERRAFORM_VERSION}_linux_amd64.zip
    ```

4.  **Move Terraform to Local Binaries:**
    Move the unzipped `terraform` executable to `/usr/local/bin/` so it's accessible system-wide.

    ```bash
    sudo mv terraform /usr/local/bin/
    ```

5.  **Verify Installation:**
    Confirm that Terraform has been installed correctly and is accessible in your PATH by checking its version.

    ```bash
    terraform -v
    ```
    You should see output similar to `Terraform v1.8.2` (or whatever version you installed).
