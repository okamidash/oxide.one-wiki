---
title: Virtual Machine Classes
description: Design on Virtual Machine Classes
published: true
date: 2020-01-25T17:49:12.685Z
tags: class, Virtual Machine
---

Virtual Machine Classes
---

Virtual machines within oxide.one have specific classes.

| Class | CPUS | RAM (GB) |
| ----- | ---- | --------	|
| VA1		| 2		 | 2				|
| VA2 	| 4		 | 2				|
| VB1		| 4 	 | 4 				|
| VB2		| 4		 | 6				|
| VH1		| 8		 | 8				|

## VA1
VA1 is for Low power virtual machines that do not require much ram or processing power.

## VA2
VA2 Is for Virtual Machines that are light on RAM usage but require more processing power than VA1 type machines.

## VB1
VB1 Is for midrange VMs that require a decent amount of resources, without consuming much ram.

## VB2 
VB2 Is like VB1, but with more RAM.

## VH1
VH1 Is the Virtual Heavy VM. This type of VM will serve as a major workhorse, consuming significant processing power.

# VM Allocation
Kubernetes Nodes are always type `VH1`.
Ceph Nodes are always type `VA2`.
Torrenting servers are `VB2`.
FreeIPA Servers are `VB2`.