+++
title       = "Storing Secrets in Local HashiCorp Vault and Using Them in Terraform"
description = "Step-by-step guide to set up a local Vault server, store secrets, and consume them in Terraform."
date        = 2025-08-28T10:00:00Z
lastmod     = 2025-08-28T10:30:00Z
draft       = false
categories  = ["IaC", "Terraform", "Vault"]
tags        = ["iac", "terraform", "vault"]
toc         = true
aliases     = ["/post/plateform/iac/vault-terraform-guide/"]
+++

# Storing Secrets in Local HashiCorp Vault and Using Them in Terraform

This guide shows how to:
- Install and run a **local HashiCorp Vault** server in development mode.
- Store secrets securely inside Vault.
- Retrieve and use those secrets in **Terraform**.

---

## Prerequisites
Before starting, make sure you have:
- [Terraform](https://developer.hashicorp.com/terraform/downloads) ≥ 1.12 
- [Vault](https://developer.hashicorp.com/vault/install) ≥ 1.16 
- A terminal with **bash/zsh** (Linux/macOS/WSL) or **PowerShell** (Windows).  
- Basic understanding of Terraform providers and variables.


## Install Vault
Follow the official installation guide:[ Vault Installation Guide](https://developer.hashicorp.com/vault/install)
**Quick steps:**
1. Download and unzip Vault into a folder of your choice.
2. Add the Vault binary path to your `$PATH`.
3. Verify Installation:
```bash
vault version
```

## Start Vault Server (Development Mode)
Run Vault in development mode:
```bash
vault server -dev
```

This will output the following information:
```
API Address: http://127.0.0.1:8200
Unseal Key: buQ1uDuYD/0aldCuPHsLzeoydPpUtCogmmhH84SLTCw=
Root Token: hvs.9FJLoAeUX7EnmldcGyyRfSQM
Development mode is not secure and should only be used for testing purposes.
```

> **⚠️ Security Warning**:
-dev mode exposes the root token and stores all data in-memory only.
Never use this setup for production. For hardening, see [Vault Production Hardening](https://developer.hashicorp.com/vault/tutorials/operations/production-hardening?utm_source=chatgpt.com).

---

## Enable the KV Secrets Engine
```bash
vault secrets enable -path=secret kv
```
If the path already exists, you may see the following error:

```
Error enabling: Error making API request.
URL: POST http://127.0.0.1:8200/v1/sys/mounts/secret
Code: 400. 
Errors:* path is already in use at secret/
```

This means the secret/ path is already active.

---

## Store and Retrieve a Secret
1. Store:
```bash
vault kv put secret/demo/user user_login="admin" user_password="admin123"
```

2. Retrieve
```bash
vault kv get secret/demo/user
```

---

## Use Vault Secrets in Terraform 
1. Provider Configuration
```hcl
provider "vault" {  
    address = "http://127.0.0.1:8200"  
    token  = var.vault_token
}
```

2. Access the Secret 

The `vault_generic_secret` data source allows Terraform to read values from Vault.  
This example fetches the `user_login` and `user_password` we stored earlier:
```hcl
data "vault_generic_secret" "user_credentials" {  
    path = "secret/demo/user"
}
```

3. Define Variables

Create `variables.tf` and add this code:
```hcl
variable "vault_token" {  
    description = "The token for accessing Vault"  
    type        = string  
    sensitive   = true
}
```

4. Use Secret in a Resource

Create `main.tf` and put this code:
```hcl
resource "local_file" "credentials_file" {  
    filename = "${path.module}/user_credentials_file.txt"  content  = <<EOT
User Login = ${data.vault_generic_secret.user_credentials.data["user_login"]}
User Password = ${data.vault_generic_secret.user_credentials.data["user_password"]}
EOT
}
```

---

## Verify Integration
Open a new terminal and test the code

1. Export environment variables:
```bash
export VAULT_ADDR=http://127.0.0.1:8200
export TF_VAR_vault_token="hvs.9FJLoAeUX7EnmldcGyyRfSQM"
```

2. Run Terraform:
```bash
terraform init
terraform plan
terraform apply -auto-approve
```
3. Check the generated file:
```bash
cat user_credentials_file.txt
```

Expected content:
```
User Login    = admin
User Password = admin123
```

---

## Cleanup
When finished:
```bash
terraform destroy -auto-approve
```

Stop the Vault dev server:
```bash
CTRL + C
```