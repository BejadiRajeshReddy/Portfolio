---
title: "Day 6 "
date: 2025-11-28
draft: false
cover:
    image: "https://cdn.codersociety.com/uploads/terafformaws.png"
    alt: ""
    caption: ""
---

## **Terraform Structure Overview**

A Terraform project usually contains four important blocks:

| Block | Purpose |
| --- | --- |
| **terraform** | Declares providers and versions |
| **provider** | Connects Terraform to AWS / Cloud |
| **resource** | Creates infrastructure |
| **data** | Reads existing infrastructure |

Now let’s see each one with examples.

# 🌍 1. **Terraform Block**

The **terraform block** is used to configure settings that affect **Terraform itself**, not the cloud provider or the resources. The **terraform block** tells Terraform **how to behave**.

It does *not* create infrastructure — it configures Terraform’s settings.

You usually place it at the top of your file.

### **What it does:**

- Defines required providers
- Pins provider versions
- Sets Terraform CLI version
- Configures backend (remote state)

Example:

```hcl
terraform {
  required_version = ">= 1.5.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }

  backend "s3" {
    bucket = "my-tf-state-bucket"
    key    = "terraform/state.tfstate"
    region = "ap-south-1"
  }
}
```

### **Components Explained:**

- `required_version`  Ensures Terraform is using the correct version.
- `required_providers`  Tells Terraform which providers to download and their versions.
- `backend`    Where the state file is stored (local by default, S3/DynamoDB for teams).

## 🔍 **So what happens when you don’t add the block?**

Terraform will:

- Automatically install the latest AWS provider version
- Use whatever version it finds suitable

This is okay for **learning** or **small tests**, but **dangerous for production**.

## ✅ **Why Terraform Works Without the `terraform` Block**

### **1️⃣ Terraform Automatically Downloads the Required Provider**

If you write this:

```hcl
provider "aws" {
  region = "eu-east-1"
}
```

and Terraform sees that you used:

```hcl
resource "aws_instance" "web" {}
```

Terraform **automatically understands** that you need the AWS provider and downloads the latest compatible version.

So the `terraform` block is **not mandatory**.

## ✅ **Then Why Do We Use the `terraform { required_providers {} }` Block?**

👉 This block is used to **control versions and source of providers**, especially in production.

Example:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.23.0"
    }
  }
}
```

This ensures:

- Same provider version for everyone (Team members get consistent results)
- Avoids breaking changes (A future Terraform update won't suddenly break your code)
- Explicit control (You know which version is being used)

---

# ☁️ **2. Provider Block (Mandatory)**

This tells Terraform which cloud we are using.

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}
```

### ✔ Explanation

- `required_providers`: Ask Terraform to download AWS provider plugin.
- `provider "aws"`: Sets region and credentials (default uses `aws configure`).

---

# 🚀 **3. Resource Block (Creates Infrastructure)**

### **A resource = something Terraform will CREATE, UPDATE, DELETE.**

Examples:

- EC2 instance
- VPC
- S3 bucket
- IAM user
- RDS database

Syntax:

```hcl
resource "<PROVIDER>_<SERVICE>" "<NAME>" {
  # arguments
}
```

Example:

```hcl
resource "aws_s3_bucket" "mybucket" {
  bucket = "my-tf-bucket-123"
}
```

---