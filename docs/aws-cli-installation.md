# AWS CLI Installation Guide

The **AWS Command Line Interface (AWS CLI)** is a tool that allows you to manage your AWS services from the terminal. This guide covers how to **download, install, and configure** the AWS CLI on Windows, macOS, and Linux.

---

## ✅ 1. Check Your System

- **AWS CLI v2** includes its own Python installation (no need to install Python separately).
- For the latest version, visit the [AWS CLI official documentation](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).

---

## ✅ 2. Install AWS CLI

### **Windows**
1. Download the **64-bit MSI Installer**:
   [AWS CLI Windows Installer](https://awscli.amazonaws.com/AWSCLIV2.msi)
2. Run the installer and follow the on-screen instructions.
3. **Verify installation**:
```powershell
aws --version
```
Expected output:
```powershell
aws-cli/2.x.x Python/3.x Windows/10
```
---
### **macOS**
**Option 1: Using the PKG Installer**
1. Download the `.pkg` file:
   [AWS CLI macOS Installer](https://awscli.amazonaws.com/AWSCLIV2.pkg)
2. Open the file and follow the installation steps.
3. Verify:
```shell
aws --version
```
**Option 2: Using the PKG Installer**
```shell
brew install awscli
```
---
### **Linux**
1. Download the AWS CLI v2 bundle:
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```
2. Unzip the package:
```bash
unzip awscliv2.zip
```
3. Install::
```bash
sudo ./aws/install
```
4. Verify:
```bash
aws --version
```
**Update AWS CLI:**
```bash
sudo ./aws/install --update
```
---
## ✅ 3. Configure AWS CLI
After installation, configure your credentials:
```shell
aws configure
```
You will need:
* **AWS Access Key ID**
* **AWS Secret Access Key**
* **Default region name** (e.g., `us-east-1`)
* **Output format** (e.g., `json`)
---
## ✅ 4. Verify Configuration
Run:
```shell
aws sts get-caller-identity
```
If your credentials are correct, you should see:
```shell
{
    "UserId": "AIDAEXAMPLEID",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/YourUserName"
}
```
---
## 🔗 Resources
* [AWS CLI Official Docs](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html)  
* [AWS CLI GitHub Repository](https://github.com/aws/aws-cli)  
