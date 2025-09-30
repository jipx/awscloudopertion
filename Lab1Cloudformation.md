# 🏗️ CloudFormation Lab – Using AWS Systems Manager

# 🏗️ CloudFormation Template – Lab 1: Using AWS Systems Manager

## 📘 Purpose
This CloudFormation template provisions the environment for **Module 2: Lab 1 – Using AWS Systems Manager**.  
It creates a managed EC2 instance with all the networking, security, and IAM resources needed so students can complete the lab tasks without manual setup.

---
![alt text](<Images/unnamed (3).png>)


## 🚀 What This Template Builds

### 1. Networking
- **VPC** (`10.0.0.0/16`) with DNS support
- **Internet Gateway** and public route
- **Public Subnet** (`10.0.0.0/24`) with automatic public IPs
- **Route Table Association** for internet access

### 2. IAM
- **AppRole** – EC2 role with:
  - `AmazonSSMManagedInstanceCore` → SSM agent access
  - `AmazonEC2ReadOnlyAccess` → read-only EC2 operations
- **AppInstanceProfile** – attaches the role to the EC2 instance

### 3. Security
- **AppSecurityGroup** – allows inbound HTTP (`tcp/80`) from anywhere
  - ⚠️ No SSH access → use Session Manager for secure connections

### 4. Compute
- **SSMInstance** – Amazon Linux 2 EC2 instance
  - Instance type: `t2.micro`
  - IAM instance profile for SSM
  - Security group with HTTP access
  - UserData ensures SSM agent runs
  - Tagged as `WidgetLab-Managed-Instance`

### 5. Automation
- **SSM Document – `InstallDashboardApp`**
  - Installs Apache and PHP
  - Downloads AWS SDK for PHP
  - Deploys the **Widget Dashboard App**
  - Used in the lab with **Run Command**

### 6. Outputs
- **ServerIP** → Public IP of the EC2 instance  
  Students use this in a browser to verify the Widget Dashboard app.

---

## 🏗️ How It Fits the Lab
- **Task 1 (Inventory):** Students enable inventory on the managed instance.  
- **Task 2 (Run Command):** Students run the `InstallDashboardApp` document to install the Widget Dashboard.  
- **Task 3 (Parameter Store):** Students create a parameter `/dashboard/show-beta-features` to toggle app features.  
- **Task 4 (Session Manager):** Students can open a secure shell session without SSH.  

---

## 🔒 Security Notes
- Uses **modern IAM policy** `AmazonSSMManagedInstanceCore` (replaces deprecated `AmazonEC2RoleforSSM`)  
- No SSH ports opened (management is via Session Manager)  
- Security group limited to **HTTP (port 80)**  

---

## 🧹 Cleanup
To remove all resources:
1. Delete any Parameter Store entries created during the lab  
2. Delete the CloudFormation stack  

This will remove the VPC, EC2 instance, IAM roles, security group, and SSM Document.

---

## ✅ Key Takeaways
- CloudFormation automates lab setup, ensuring consistency across students  
- The EC2 instance is pre-integrated with Systems Manager  
- The SSM Document simplifies the Run Command exercise  
- Students focus on **learning Systems Manager**, not building infrastructure


## 📘 Overview
This CloudFormation stack sets up the lab environment for **Module 2: Lab 1 – Using AWS Systems Manager**.  
The business scenario: **Widget Manufacturing** wants to manage their application servers securely without relying on SSH. As a system operator, you will use **AWS Systems Manager (SSM)** to:

- Collect inventory data about installed software
- Install a custom web application remotely
- Manage application feature toggles using Parameter Store
- Optionally access instances using Session Manager

---

## 🚀 Deployment Instructions

1. **Launch the Stack**
   - Open the **AWS Management Console → CloudFormation**
   - Click **Create stack → With new resources (standard)**
   - Upload the `lab1-ssm.yaml` template
   - Use default parameters (KeyName is pre-provisioned in Qwiklabs)

2. **Wait for Completion**
   - Deployment takes ~2–3 minutes
   - Check the **Outputs** tab for the value `ServerIP`

3. **Verify**
   - You should see one EC2 instance named **WidgetLab-Managed-Instance**
   - Instance is SSM-enabled and ready for lab tasks

---

## 🏗️ Architecture

```
+----------------------------+
| AWS Systems Manager        |
| - Inventory                |
| - Run Command              |
| - Parameter Store          |
| - Session Manager (opt.)   |
+-------------+--------------+
              |
              v
+-----------------------------------+
| EC2 Managed Instance              |
| - Amazon Linux 2                  |
| - Apache + PHP                    |
| - Widget Dashboard App (via SSM)  |
| - SSM Agent + IAM Role            |
+-----------------------------------+
              |
         HTTP (port 80)
              |
              v
+---------------------+
| Student Browser     |
| Widget Dashboard UI |
+---------------------+
```

---

## 🎯 Lab Tasks

### Task 1 – Inventory
- Navigate: **Systems Manager → Fleet Manager → Managed Instance**
- Create an inventory association (`Inventory-Association`)
- Review software data in the **Inventory tab**

**CLI equivalent:**
```bash
aws ssm create-association   --targets "Key=instanceIds,Values=<InstanceID>"   --name "Inventory-Association"
```

---

### Task 2 – Run Command
- Navigate: **Systems Manager → Run Command**
- Select the **InstallDashboardApp** document (created by the stack)
- Target the managed instance and run
- Open `http://<ServerIP>` → The **Widget Dashboard** should load

**CLI equivalent:**
```bash
aws ssm send-command   --targets "Key=instanceIds,Values=<InstanceID>"   --document-name "InstallDashboardApp"
```

---

### Task 3 – Parameter Store
- Navigate: **Systems Manager → Parameter Store → Create parameter**
  - Name: `/dashboard/show-beta-features`
  - Value: `True`
- Refresh the app → Now shows **3 charts**
- Delete the parameter → App reverts to **2 charts**

**CLI equivalent:**
```bash
aws ssm put-parameter   --name "/dashboard/show-beta-features"   --value "True"   --type String
```

---

### Task 4 – (Optional) Session Manager
- Navigate: **Systems Manager → Session Manager**
- Start a session with the managed instance
- Run commands in the browser shell without SSH

**CLI equivalent:**
```bash
aws ssm start-session --target <InstanceID>
```

---

## 🔒 Security Notes
- Instance role uses `AmazonSSMManagedInstanceCore` (modern, least privilege)  
- Security group only allows inbound HTTP (port 80)  
- No SSH access is required — use Session Manager instead  

---

## 🧹 Cleanup
- Delete `/dashboard/show-beta-features` parameter from Parameter Store
- Delete the CloudFormation stack to remove all resources

---

## ✅ Key Takeaways
- Systems Manager centralizes **operations at scale**
- **No SSH needed** → secure, auditable access
- **Run Command + Inventory** simplify fleet-wide management
- **Parameter Store** enables feature toggles and configuration control
