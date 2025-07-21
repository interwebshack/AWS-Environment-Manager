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

### **Step 1: Configure AWS CLI**
Install AWS CLI v2 and configure credentials:
```shell
aws configure
```
Provide:
* **AWS Access Key ID**
* **AWS Secret Access Key**
* **Default region name** (e.g., `us-east-1`)
* **Output format** (e.g., `json`)
Verify:
```shell
aws sts get-caller-identity
```