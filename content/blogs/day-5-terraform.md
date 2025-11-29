---
title: "Day 5 — Understanding Terraform Variables (Input, Output, Locals & Precedence)"
date: 2025-11-28
draft: false
cover:
    image: "https://assets.community.aws/a/2vweWfdIr9epnbH2khiZpMtofDZ/Capture-JPG.webp?imgSize=560x300"
    alt: ""
    caption: ""
---

<div style="width: 100%;">
    {{< youtube QMsJholPkDY >}}
</div>


<br>

Today’s focus was on one of the most important concepts in Terraform: **Variables**.

Variables help make Terraform configurations reusable, flexible, and clean — especially when working with AWS infrastructure.

This blog covers why variables are needed, the different types, how to declare them, and how Terraform decides which value takes priority when multiple sources exist.

---

# **Why Do We Need Variables in Terraform?**

As your AWS infrastructure grows, hard-coding values becomes messy and repetitive.

Variables help solve this by allowing you to:

- Reuse configs across environments
- Change values without editing code
- Keep your Terraform files clean
- Avoid duplication
- Separate configuration from infrastructure logic

Terraform supports three major kinds of variables:

1. **Input variables**
2. **Output variables**
3. **Local values (locals)**

Let’s explore each.

---

---

# **3. Local Values (locals)**

Locals are internal variables — they don’t take input and aren’t printed as outputs.

They are just shortcuts to reduce repetition or combine values.

### Example:

```hcl
locals {
  environment = "dev"
  name        = "${local.environment}-app"
}

```

Use it like:

```hcl
tags = {
  Name = local.name
}

```

Locals = cleaner code & less duplication.

---

# **Example: Full Variable Workflow**

Files:

`variables.tf`

```hcl
variable "region" {
  type    = string
  default = "us-east-1"
}

```

`terraform.tfvars`

```
region = "us-west-2"

```

CLI:

```
terraform apply -var="region=ap-south-1"

```

Result:

Terraform uses **ap-south-1**, because CLI > tfvars > default.
