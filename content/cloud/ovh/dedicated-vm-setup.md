+++
title       = "OVH Dedicated Server Setup"
description = "Provisioning an OVH dedicated server (KS-B) with Ubuntu 24.04 LTS for Kubernetes lab environments."
date        = 2025-08-26T18:00:00Z
lastmod     = 2025-08-26T18:00:00Z
draft       = false
categories  = ["Cloud","OVH"]
tags        = ["ovh","dedicated-server","ubuntu","ssh","kubernetes"]
toc         = true
+++

# OVH Dedicated Server Setup

This document describes the process of buying and configuring a dedicated server at OVH to serve as a Kubernetes lab environment.
It covers the KS-B line of servers, partitioning, and SSH configuration.

**Note:** You need an OVH account

## 🖥️ Example Server Chosen

KS-B | Intel Xeon E5-1620v2 at OVH
CPU: Intel Xeon E5-1620v2 (4c/8t) 3.7 GHz / 3.9 GHz
RAM: 32 GB ECC 1333 MHz
Disk: 1×120 GB SSD SATA

## 📦 Installation

1. Buy a dedicated server [(in this guide: server kimsufi KS-B)](https://eco.ovhcloud.com/fr/kimsufi/ks-b/).
2. Once the server has been provision, log into the [OVH Manager](https://www.ovh.com/manager).
3. Goto the section `Dedicated servers`
   ![ovh_manager_dedicated_server.png](images//ovh/ovh_manager_dedicated_server.png)
4. Click on the server name, it will bring you on the VM detail page.
5. From the tab `General information` click on ... button at `Operating system (OS)` and click the sub menu to start the installation. It will open a pop
   ![ovh_install_vm_master_part1.png](images/ovh/ovh_install_vm_master_part1.png)
6. During installation, choose the OS:
   * Ubuntu Server 24.04 "Noble Numbat" LTS
7. Provide your SSH Public Key at setup.
   * Generate one if not already created (from macOS):
```bash
ssh-keygen -t rsa -b 4096 -C "k8s-master" -f ~/.ssh id_rsa_k8smaster
```
   * Upload the public key (~/.ssh/id_rsa_k8smaster.pub) in OVH panel.

## 📂 Disk Partitioning
Recommended partitioning during install:

| Mount Point | Size                       | Purpose                                    |
| ----------- | -------------------------- | ------------------------------------------ |
| `/`         | 30 GB (30720 MiB)          | OS, MicroK8s binaries, Snap                |
| `/var`      | 20 GB (20480 MiB)          | Logs, apt cache, snapd, containerd data    |
| `/home`     | 5 GB (optional) (5120 MiB) | SSH keys, user files                       |
| `/data`     | Rest of disk (\~91 GB)     | Application data (PostgreSQL, MinIO, etc.) |
| `swap`      | 4 GB (4000 MiB)            | Swap space                                 |

## 🔑 SSH Configuration (macOS)
Once the installation is complete:

1. Copy the private key to your ~/.ssh/ (should already be there from generation step).

2. Add entry in ~/.ssh/config:
```bash
# Master node
Host k8s-master
  HostName 193.70.35.121
  User ubuntu
  IdentityFile ~/.ssh/id_rsa_k8smaster
  IdentitiesOnly yes
```

3. Connect to the server:
```bash
ssh k8s-master
```

## ✅ Validation
If successful, you’ll land in your Ubuntu VM:
```bash
Welcome to Ubuntu 24.04 LTS (GNU/Linux 6.x-xx-generic x86_64)
```