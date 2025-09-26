
##  *Automating User Provisioning with Terraform*

###  Project Overview

This project demonstrates how to use **Terraform** to automate **user provisioning in Okta**. Instead of manually creating users through the Okta Admin Console, Terraform lets you define users as **code**, making the process repeatable, scalable, and version-controlled.

###  Features

* Create Okta users with attributes (first name, last name, email, login).
* Assign users to groups (e.g., Engineering, HR, Contractors).
* Manage lifecycle (active, suspended, deactivated) via Terraform.
* Reproducible and auditable user management (IaC approach).

---

##  Repo Structure

```
okta-terraform-users/
│── main.tf          # Main Terraform configuration
│── variables.tf     # Variables (e.g., user details, API keys)
│── terraform.tfvars # Values for variables
│── providers.tf     # Okta provider setup
│── README.md        # Documentation
```

---

##  Example Code

### providers.tf

```hcl
terraform {
  required_providers {
    okta = {
      source  = "okta/okta"
      version = "3.42.0"
    }
  }
}

provider "okta" {
  org_name  = var.okta_org_name
  base_url  = var.okta_base_url
  api_token = var.okta_api_token
}
```

### variables.tf

```hcl
variable "okta_org_name" {}
variable "okta_base_url" {
  default = "okta.com"
}
variable "okta_api_token" {}

variable "users" {
  type = list(object({
    first_name = string
    last_name  = string
    login      = string
    email      = string
  }))
}
```

### main.tf

```hcl
resource "okta_user" "users" {
  for_each = { for u in var.users : u.login => u }

  first_name = each.value.first_name
  last_name  = each.value.last_name
  login      = each.value.login
  email      = each.value.email
  status     = "ACTIVE"
}
```

### terraform.tfvars

```hcl
okta_org_name  = "your-org-name"
okta_api_token = "your-api-token"

users = [
  {
    first_name = "Alice"
    last_name  = "Smith"
    login      = "alice.smith@yourcompany.com"
    email      = "alice.smith@yourcompany.com"
  },
  {
    first_name = "Bob"
    last_name  = "Johnson"
    login      = "bob.johnson@yourcompany.com"
    email      = "bob.johnson@yourcompany.com"
  }
]
```

