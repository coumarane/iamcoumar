+++
date = '2025-05-29T12:13:29+02:00'
draft = false
title = 'Linux Kernel'
+++

🌟 Demystifying Linux Kernel Development with Multipass 🌟

As I explore the fascinating world of Linux Kernel Development, I’ve been experimenting with writing and building custom kernel modules. Along the way, I discovered a streamlined way to set up and experiment using Multipass, a lightweight VM manager for Linux, Windows and macOS.

https://github.com/canonical/multipass

💡 Here’s an Example and Steps to Get Started with Kernel Modules Using Multipass:

This example is based on MacBook Pro (chip: Apple M1 Pro).

# Setting Up Multipass on macOS

## Prerequisites
Ensure that Homebrew is installed on your macOS before proceeding. For instructions, visit [Homebrew](https://brew.sh/).

## Installation

**1. Install Multipass**
Run the following command to install Multipass using Homebrew:
```bash
brew install --cask multipass
```

**2. Verify Installation**
Check if Multipass was installed successfully:
```bash
multipass version
```

**3. Explore Available Commands**
View the list of available commands:
```bash
multipass help
```

## Creating an Instance for the Demo
**1. Create a Demo Instance**
Launch an instance named `primary`:
```bash
multipass launch 24.04 --name primary
```

**2. Connect to the Instance**
Access the shell of the `primary` instance:
```bash
multipass shell primary
```


## Configuring SSH Access
**1. Modify SSH Configuration**
Open the SSH configuration file:
```bash
sudo vim /etc/ssh/sshd_config
```

Update the `KbdInteractiveAuthentication` property to `yes`:
```plaintext
KbdInteractiveAuthentication yes
```

**2. Reload Daemon and Restart SSH Service**
Reload the system daemon:
```bash
sudo systemctl daemon-reload
```

Restart the SSH service:
```bash
sudo service ssh restart
sudo systemctl restart ssh
```

**3. Set User Password**
Set a password for the default `ubuntu` user. For this example, the password will be `demo123`:
```bash
sudo passwd ubuntu
```


## Linux Device Drivers
This guide outlines the steps to develop, build, and manage a simple Linux kernel module using an example "Hello World" module.

### Setup Environment 
1. Connect to Multipass Instance
Connect to the intance if not done
```bash
multipass shell primary
```

2. Create a Directory for Module Development
Create a directory to store the module files:
```bash
mkdir ldd
```

3. Exit Current Instance
To disconnect from the current instance:
```bash
exit
```

4. Connect to Multipass Ubuntu Using VSCode Remote SSH
Use the following SSH parameters:

* Host: ubuntu@192.168.64.3
* Method: VSCode Remote SSH


5. Install Required Utilities
Update the system and install the necessary build tools and kernel headers:
```bash
sudo apt update
sudo apt upgrade
sudo apt install -y build-essential linux-headers-$(uname - r) kmod
```

## Create a "Hello World" Kernel Module
1. Write the Source Code
Create a file named `helloworld.c` with the following content:
```c
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/init.h>

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Coumarane COUPPANE");
MODULE_DESCRIPTION("Simple Hello World From HelloWorld Module");
MODULE_VERSION("1.0.0");

static int __init helios_init(void)
{
    printk(KERN_ALERT "Hello world from HelloWorld module!! \n");
    return 0;
}

static void __exit helios_exit(void)
{
    printk(KERN_ALERT "Destructing HelloWorld Module \n");
}

module_init(helios_init);
module_exit(helios_exit);
```

2. Create a Makefile
Create a `Makefile` in the same directory with the following content:
```makefile
obj-m += helios.o

KDIR := /lib/modules/$(shell uname -r)/build
PWD := $(shell pwd)

all:
	$(MAKE) -C $(KDIR) M=$(PWD) modules

clean:
	$(MAKE) -C $(KDIR) M=$(PWD) clean
```

## Build and Manage the Module
1. Build the Module
Run the following command in the module directory:

```bash
make
```

2. Load the Module
Insert the module into the kernel:
```bash
sudo insmod helios.ko
```

3. Check Logs
View kernel messages related to the module:
```bash
sudo dmesg | tail
```

1. Remove the Module
Unload the module from the kernel:
```bash
sudo rmmod helios.ko
```
