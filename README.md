# AWS Environment Manager with Ansible

## Overview
This project provides a secure and automated way to **manage isolated AWS environments** using **IAM permission boundaries** and **tag-based governance**. It includes lifecycle operations (**create**, **check**, **destroy**) and includes a GitHub Actions pipeline with **security scanning and compliance checks**.

---

## ✅ Features
- **IAM Permission Boundaries** to prevent privilege escalation.
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
  - Python 3.8+
  - AWS CLI v2
  - Docker (for Molecule tests)

---

## ✅ Setup

### **Step 1: Install and Configure AWS CLI**
Install **AWS CLI v2** and configure credentials:
For a full installation guide, see [AWS CLI Installation Guide](docs/aws-cli-installation.md).

>AWS CLI v2 comes with its own Python, so you don’t need to install Python separately. (If you’re installing v1, Python is required.)

Run the following to configure your credentials:

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