+++
title       = "Kubernetes Cluster on OVH with Ansible"
description = "Setup a Kubernetes cluster on two dedicated OVH VMs (1 master, 1 worker) using Ansible from macOS."
date        = 2025-08-26T12:00:00Z
lastmod     = 2025-08-26T12:00:00Z
draft       = false
categories  = ["Ansible","Kubernetes","Platform"]
tags        = ["ovh","ansible","k8s","cluster","vm","dedicated-ip"]
toc         = true
aliases     = ["/post/plateform/ansible/ansible-playbook/"]
+++


# Kubernetes Cluster on OVH with Ansible

This document describes how I provisioned a Kubernetes cluster on OVH dedicated VMs using Ansible.

The cluster consists of:
- 1 Master node
- 1 Worker node

An additional dedicated public IP assigned for cluster services (e.g. ingress).

## Prerequisites
For this lab I use macos and I have installed ansible cli 
```bash
brew install ansible

# to test the version
ansible --version
```
Buy 2 VMs example  KS-B | Intel Xeon E5-1620v2 at OVH
the spec :
CPU: Intel Xeon E5-1620v2 - 4c/8t - 3.7 GHz/3.9 GHz
RAM: 32 Go ECC 1333 MHz
Disk: 1×120 Go SSD SATA


## Environment Overview
| Component  | Details                                    |
| ---------- | ------------------------------------------ |
| Provider   | OVH Dedicated Server                       |
| Master VM  | Ubuntu 22.04 LTS, 4 vCPU, 8GB RAM          |
| Worker VM  | Ubuntu 22.04 LTS, 4 vCPU, 8GB RAM          |
| Extra IP   | Used for Ingress / LoadBalancer setup      |
| Controller | macOS with Ansible CLI                     |
| Tools      | Ansible, SSH, kubeadm, containerd, kubectl |

## Prepare VMs
Once you have bought the two VM then connect to your account and then go to the manager dedicated server and choose one by one to setup the os. For my case i have installed `Ubuntu Server 24.04 "Noble Numbat" LTS`.



**Optional :** if want you can partion disk:

- Recommended Partition Scheme (1 x 2TB disk or 2 x 480 GB in RAID)
If you're using 1 disk per node, use something like this:

|   Mount Point	    |         Recommended Size	            |   Purpose                             
| ----------------- | ------------------------------------- | --------------------------------------------- |
| /                 |   30 GB	(30720 MiB)                 |   OS + MicroK8s binaries + Snap               | 
|  /var	            |   20 GB	(20480 MiB)                 |   Logs, apt cache, snapd, containerd data     |
|  /home	        |   5 GB (optional) (5120 MiB)	        |   SSH keys, user files                        |
|  /data	        |   Rest of disk (91244 MiB)	        |   Application data (PostgreSQL, MinIO, etc.)  |
|  swap	            |   4 GB (if no large RAM) (4000 MiB)	|   Swap space                                  |


