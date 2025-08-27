+++
title       = "{{ replace .Name "-" " " | title }}"
description = "Ansible playbook, role, or best practice."
date        = {{ .Date }}
lastmod     = {{ .Date }}
draft       = true
categories  = ["Platform","Ansible"]
tags        = []
series      = []
toc         = true
+++


## Goal

## Inventory Example
```ini
[web]
host1 ansible_host=192.168.1.10
```

## Playbook Example
```
- hosts: web
  tasks:
    - name: Install nginx
      apt: { name: nginx, state: present }
```