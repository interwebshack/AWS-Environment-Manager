# AWS Environment Manager with Ansible

## Overview
This project provides a secure and automated way to **manage isolated AWS environments** by enforcing strict IAM governance and resource isolation policies.  
The solution is designed for controlled multi-user environments where resource sprawl and privilege escalation must be prevented.

Key principles implemented in this design:
- **IAM Permission Boundaries**: Prevent users from escalating their privileges or attaching broader policies.
- **Scoped IAM Managed Policies**: Limit access to AWS services such as EC2, VPC, and ELB, with resource-level conditions enforced by tags.
- **Tag-Based Governance Model**: All resources must be created with a specific tag (e.g., `Environment=Isolated`) to ensure isolation and enable selective cleanup.
- **Lifecycle Management**: Includes `create`, `check`, and `destroy` operations, with a **nuke mode** that removes all tagged resources.
- **Security by Design**: Integrated with Checkov for IaC compliance scanning and GitHub Security Dashboard for visibility.

This approach ensures:
- Users can only manage their own tagged resources.
- Policies prevent privilege escalation or cross-environment interference.
- Full cleanup capability for rapid teardown of isolated environments.

---

## ✅ Features
- **IAM Permission Boundaries** for controlled privilege delegation and to prevent privilege escalation.
- **Tag-based governance** to isolate resources by key/value.
- **Lifecycle support**: create, check, destroy environments.
- **Nuke mode** for full cleanup of all tagged resources.
- **CI/CD pipeline with:**
  - **Ansible linting & syntax validation**
  - **YAML linting**
  - **Secret detection**
  - **Molecule tests using Docker**
  - **Checkov security scan with GitHub Security Dashboard integration**

---

## ✅ Prerequisites
- **AWS account** with:
  - Admin privileges (or equivalent) for IAM and networking.
  - Ability to create IAM users, policies, and permission boundaries.
- **IAM user** for running Ansible with:
  - `iam:*`, `ec2:*`, `vpc:*`, `elasticloadbalancing:*` permissions.
- **Local environment**:
  - `Linux` or `Windows with WSL2`
    - Ansible does not work on Windows without WSL2.  
  - git (Optional)
  - Python 3.10+
    - Ansible 10.x requires Python 3.10+  
    - Ansible 11.x requires Python 3.11+  
  - AWS CLI v2
  - `Docker` or `Podman` (for Molecule tests)

---

## ✅ Setup

> These instructions are centered around a user on Windows with WSL2

### **AWS Account Requirements**
- An AWS **management/admin account** (or account with permissions to manage IAM and networking).
- This account must have:
  - `iam:*` permissions to create IAM policies, permission boundaries, and users.
  - `ec2:*`, `vpc:*`, and related services permissions for resource creation/deletion.

> **Tip:** Use a **dedicated AWS account** for these environments to ensure isolation.

---

### **Create an IAM User for Ansible Execution**
- Create an **IAM user** in the management account with **AdministratorAccess** or equivalent permissions.
- Generate:
  - **Access Key ID**
  - **Secret Access Key**

Store these credentials in a secure location (we'll configure them in the next step).

---

### **Clone the repository**
```powershell
git clone https://github.com/interwebshack/AWS-Environment-Manager.git
cd AWS-Environment-Manager
```

---

### **Start up your WSL2 terminal**

* Open a powershell terminal and type:
```powershell
wsl
```

---

### **✅ Setup Development Environment**  

>WSL2 instructions - Adjust for your linux distro

```shell
sudo apt update && sudo apt upgrade -y                 # Update your current distro
sudo apt install python3 python3-pip python3-venv -y   # Install Python3 and Package Manager          
```

---

### **Verify Python3 installation**
```shell
python3 -c "import sys; import platform; print(sys.version); print(platform.platform());"

```
You should see a response like the following:

>Make sure it  indicates that you are running on WSL2

```shell
3.10.12 (main, May 27 2025, 17:12:29) [GCC 11.4.0]
Linux-5.15.167.4-microsoft-standard-WSL2-x86_64-with-glibc2.35
```

```shell
pip3 --version
```

You should see a response like the following:

```shell
pip 22.x.x from /usr/lib/python3/dist-packages/pip (python 3.x)
```

---

### **✅ Create .venv and activate**  

```shell
python3 -m venv .venv
source .venv/bin/activate

python3 -m pip install --upgrade pip
pip install .              # Without development dependencies
pip install .[dev]         # With development dependencies
```

---

### Verify installation of AWS CLI:
```shel
aws --version
```
---

### **Configure AWS CLI**
Configure **AWS CLI v1**:  

>AWS CLI v1 is not a stand alone package and requires Python. (AWS CLI v2, is a standalone application with Python built in.)

After you have installed the `AWS CLI` run the following to configure your credentials:

```shell
aws configure
```
You will be prompted to provide:

* **AWS Access Key ID** – Get this from the [IAM Console → Users → Security Credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html).
* **AWS Secret Access Key** - Generated when you create an Access Key in IAM.  
* **Default region name** (e.g., `us-east-1`) – Choose a region where your resources are hosted.
* **Output format** (e.g., `json`) – Recommended default.
Verify:
```shell
aws sts get-caller-identity
```
This should return your AWS account ID and user details.  

---

### **✅ Verify installation**  
```shell
ansible --version
ansible-galaxy --version
```

---

### **✅ Install the `amazon.aws` Ansible collection from [Ansible Galaxy](https://galaxy.ansible.com/amazon/aws)**  
What is `amazon.aws`?
* It is the **official Ansible collection for managing AWS resources**.
* It contains **modules, plugins, and roles** that let you automate tasks in AWS, such as:
  * Creating EC2 instances
  * Managing VPCs, subnets, and security groups
  * Handling IAM users, roles, and policies
  * Working with S3 buckets, Lambda, RDS, etc.

```shell
ansible-galaxy collection install amazon.aws
```

---

### Configure Environment Settings
In this step, you define the **governance model** for your AWS environment. These settings determine:
* **Which tag key/value must exist on all resources** (for isolation and selective cleanup).
* **IAM user name** (created for the isolated environment).
* **Policy names** (IAM policy and permission boundary).

Edit the file:
```shell
config/env_config.yml
```
**Configuration Parameters**
| Parameter                      | Description                                                                                       | Example Value                  |
| ------------------------------ | ------------------------------------------------------------------------------------------------- | ------------------------------ |
| `resource_tag_key`             | Tag key applied to all resources in this environment for isolation and lifecycle management.      | `Project`                      |
| `resource_tag_value`           | Tag value identifying resources for this specific project or team.                                | `FinanceApp`                   |
| `iam_user_name`                | IAM user name created for managing this environment.                                              | `financeapp-deployer`          |
| `iam_policy_name`              | IAM Managed Policy name granting scoped access to resources with the specified tag.               | `FinanceAppResourcePolicy`     |
| `iam_permission_boundary_name` | IAM Permission Boundary name that enforces tag-based isolation and prevents privilege escalation. | `FinanceAppPermissionBoundary` |

---

**Sample** `env_config.yml`
```yaml
resource_tag_key: "Project"                                  # Tag key for environment isolation
resource_tag_value: "FinanceApp"                             # Tag value applied to all resources in this environment
iam_user_name: "financeapp-deployer"                         # IAM user for deploying resources for FinanceApp
iam_policy_name: "FinanceAppResourcePolicy"                  # IAM policy granting access only to FinanceApp resources
iam_permission_boundary_name: "FinanceAppPermissionBoundary" # Permission boundary restricting privilege escalation

```
---
**Why is this important?**
* **Tag Enforcement**:
  * All resources (VPCs, EC2 instances, security groups) created by this IAM user must have the tag defined here.
  * Without the tag, actions are **denied by policy**.
* **IAM Isolation**:
  * The IAM user cannot modify or delete resources outside its tagged scope.
* **Nuke Mode Integration**:
  * When you run `nuke=true`, only resources matching this tag are deleted, preventing accidental cross-environment cleanup.

## Usage

### Create IAM User and Policy

```shell
ansible-playbook playbooks/manage_environment.yml -e "mode=create"
```

---

### Check Tagged Resources  

```shell
ansible-playbook playbooks/manage_environment.yml -e "mode=check"
```

---

### Destroy IAM and Resources

* IAM only:
```shell
ansible-playbook playbooks/manage_environment.yml -e "mode=destroy nuke=false"
```

---

* IAM + Delete tagged resources:
```shell
ansible-playbook playbooks/manage_environment.yml -e "mode=destroy nuke=true"
```

---



## ✅ Secrets Required
In GitHub repository settings:
* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`

## ✅ CI/CD Pipeline
* Runs linting, syntax checks, Molecule tests, and secret detection.  
* Workflow file: `.github/workflows/ansible-ci.yml`  

## ✅ Guidance for Interpreting Checkov Results in GitHub Security Dashboard
Once this workflow runs:
1. Go to your GitHub repository.
2. Navigate to **Security → Code scanning alerts**.
3. You will see **Checkov findings** (similar to how GitHub Advanced Security shows alerts).
4. Each alert includes:
    * **File and line number** in your Ansible playbook or YAML.
    * **Rule ID and Description** (e.g., `CKV_AWS_1: Ensure IAM policies do not allow` * `actions`).
    * **Severity** (High, Medium, Low).
    * **Remediation steps**.

## ✅ Key Checks for This Project:
* **IAM policies**:
    * Checkov flags overly permissive Action: "*" or Resource: "*" in policies.
* **Tag compliance**:
    * Flags if tags are missing on resources.
* **Encryption**:
    * Ensures resources like EBS volumes and S3 buckets (if added later) enforce encryption.
* **Credential exposure**:
    * Detects hardcoded AWS keys or passwords.


## **Clean Up Python Virtual Environment** 
```powershell
Remove-Item -Recurse -Force .venv
```
