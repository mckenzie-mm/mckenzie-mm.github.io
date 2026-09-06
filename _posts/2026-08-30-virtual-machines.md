---
layout: post
author: ted
title: "Virtual Machines (VM)"
summary: Portability & Consistency. Software-based computers running inside another physical computer.
tags: [devops]
---
## What are they?

A virtual machine (VM), is a software-based version of a physical computer. Instead of running directly on hardware, a VM operates inside a program called a hypervisor that emulates a complete computer system, including a processor, memory, storage, and network connections. This allows multiple VMs to run on a single physical machine (CPU), each with its own operating system and applications, as if they were independent computers. The Operating System being emulated is referred to as the Guest Operating System and the machine that it runs on is called the Host Operating System. 

![Alternative text for accessibility]({% link assets/images/fig-1.drawio.png %})
**Figure 1. Virtual Machine**

## Hypervisor
The hypervisor acts as an intermediary between the VM and the actual computer hardware. Every time a VM needs to perform an action—such as running software, accessing storage, or using the processor—the hypervisor intercepts these requests and decides how to allocate resources like CPU power, memory, and disk space. You can think of a hypervisor as an operating system for VMs, managing multiple virtual machines on a single physical computer. Popular hypervisors like VirtualBox and VMware enable users to run multiple operating systems simultaneously while providing strong isolation.

Modern hypervisors optimize performance by giving VMs direct access to certain hardware components when possible, reducing the need for constant intervention. However, some level of overhead remains because the hypervisor still needs to manage and coordinate resources efficiently. This means that while VMs can leverage most of the system’s hardware, they can’t use 100% of it, as some processing power is always reserved for managing virtualization itself. This small trade-off is often worth it, as hypervisors keep each VM isolated and secure, preventing one VM from interfering with another.

## Virtual Machine Images & Snapshots
A Virtual Machine Image is a single file or a template that contains a snapshot of an operating system, system settings, programs, and data. It acts as a master blueprint used to create and launch identical virtual machines. Virtual Machines are referred to as Virtual Instances, to emphasize that they are created from Images. A virtual machine (VM) snapshot is a saved copy of a VM's disk data, settings, and optional running memory at a specific point in time. It lets you roll back changes after a test or failed update, but it is not a standalone backup. 

## Application of Virtual Machines
When I first started working for defence in 2007, the standard method of delivering software was as an App running on a Virtual Machine installed on desktop computers with a Windows Operating system. The rationality behind this was that the desktop computers that the software was installed on were usually different than the computers on which the software was developed. By using a Virtual Machine it was possible to ensure consistency between the developed and the installed software, since the software running on the VM was completely independent of the underlying hardware.

VM Images also greatly simplified deployment and upgrades. VM Images act as a pre-packaged, read-only template containing an operating system, application files, settings, and libraries. Instead of manually installing and configuring software on each new desktop computer, you clone the master image to spin up identical, ready-to-use environments in minutes., which are very easy to deploy. Furthermore, in the event of a failure with an upgrade, images can be rolled back to the previous state.

![Alternative text for accessibility]({% link assets/images/fig-2.drawio.png %})
**Figure 2. Virtual Machine with App running on Host OS**


## Available Hypervisor Software

The following table shows available hy­per­vi­sor software for Windows, Linux, and macOS, as well as the range of possible guest systems that are able to be hosted by the hypervisor.

**Table 1. available hy­per­vi­sor software**

| Hypervisor Software | Host OS | Guest OS |
| :--- | :--- | :--- |
| Oracle VM Vir­tu­al­Box | • Windows<br> • Linux<br> • Mac OS X<br> • macOS<br> • Solaris<br> | • Windows<br> • Linux<br> • Solaris<br> • FreeBSD<br> |
| VMware Work­sta­tion Player | • Windows<br> • Linux<br> | • Windows<br> • Linux<br> • NetWare<br> • Solaris<br> • FreeBSD<br> |
| VMware Fusion | • Mac OS X<br> • macOS<br> | • Windows<br> • Linux<br> • NetWare<br> • Solaris<br> • FreeBSD<br> • macOS<br> • Mac OS X<br> |
| Parallels Desktop for Mac | • Mac OS<br> • X macOS<br> | • Windows<br> • Linux<br> • macOS<br> • Mac OS X<br> • Solaris<br> • FreeBSD<br> • Android OS<br> • Chrome OS<br> |

<br>

## Suggested Resources

This post was compiled and written using the following on-line resources.

[ https://gcore.com/learning/what-are-vms ](https://gcore.com/learning/what-are-vms) 

[ https://www.ionos.ca/digitalguide/server/know-how/virtual-machines ](https://www.ionos.ca/digitalguide/server/know-how/virtual-machines/)

