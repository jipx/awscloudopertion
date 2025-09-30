# 🛠️ AWS CloudFormation Lab: IAM User with Managed & Inline Policies

## 📘 Overview
This lab demonstrates how to use **AWS CloudFormation** to provision IAM resources:
- An IAM **User** (`awsstudent` by default).
- A **Managed Policy** (`lab_policy`), reusable and attachable to multiple identities.
- An **Inline Policy** (`lab_inline_policy`), embedded directly into the IAM User.
- **Outputs** for verification after deployment.

The lab also shows the difference between **managed** and **inline** IAM policies.

---

## ⚙️ Parameters (Inputs)
When launching the stack, you can provide custom values or use defaults.

| Parameter                | Type   | Default             | Description                                  |
|--------------------------|--------|---------------------|----------------------------------------------|
| `UserNameParam`          | String | `awsstudent`        | Name of the IAM User to create               |
| `ManagedPolicyNameParam` | String | `lab_policy`        | Name of the managed policy                   |
| `InlinePolicyNameParam`  | String | `lab_inline_policy` | Name of the inline policy                    |
| `UserPathParam`          | String | `/lab/`             | Path for IAM User (for grouping/organization)|

---

## 🧾 CloudFormation Template
The lab template defines:
- **IAM User** with configurable path.
- **Managed IAM Policy** with permissions:
  - `iam:ListUsers`, `iam:GetUser`, `iam:ListPolicies`
  - `s3:ListAllMyBuckets`, `s3:GetBucketLocation`
- **Inline IAM Policy** with permissions:
  - `ec2:DescribeInstances`, `ec2:DescribeVolumes`

---

## 🚀 Deployment

### 1. Save the template
Save the YAML file as `lab-iam.yaml`.

### 2. Deploy with AWS CLI
```bash
aws cloudformation create-stack   --stack-name lab-iam-stack   --template-body file://lab-iam.yaml   --capabilities CAPABILITY_NAMED_IAM
```

### 3. Deploy with custom parameters
```bash
aws cloudformation create-stack   --stack-name lab-iam-stack   --template-body file://lab-iam.yaml   --capabilities CAPABILITY_NAMED_IAM   --parameters       ParameterKey=UserNameParam,ParameterValue=student01       ParameterKey=ManagedPolicyNameParam,ParameterValue=student01_policy       ParameterKey=InlinePolicyNameParam,ParameterValue=student01_inline       ParameterKey=UserPathParam,ParameterValue=/students/
```

---

## ✅ Verification
Check resources created:

```bash
aws iam list-users
aws iam list-policies --scope Local
aws iam list-user-policies --user-name <your-username>
```

---

## 📤 Outputs
The template exports the following values:

- `UserName` → Name of the IAM User created  
- `ManagedPolicyName` → Name of the Managed Policy  
- `InlinePolicyName` → Name of the Inline Policy  

---

## 🎯 Teaching Points
- **Managed Policy**: Reusable, independent resource, attachable to many users.  
- **Inline Policy**: Tightly coupled with a single user.  
- **CloudFormation**: Automates IAM provisioning with consistency and repeatability.  
- **Parameters**: Make templates dynamic and reusable in labs.

---

## 🖼️ Diagram
👉 Include the PNG diagram generated for this lab to visually show the relationship between the IAM user, Managed Policy, and Inline Policy.
