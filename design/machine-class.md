---
title: Machine Class
description: The Design document for Machine Class within Oxide.one
published: true
date: 2020-01-25T16:51:02.805Z
tags: class, Physical Machine
---

Machine Class
---

The following is a table describing the classes of machines that are used within oxide.one.

| Class	| Size	| CPU (2x)	| RAM (GB)	| NIC	| Net Speed (Gb/S) |
| ----- | ----- | --------- | --------- | --- |----------------|
| A1 		|	1U 		|	E5-2620 v1 |	48 |	MLNX X353A |	40 |
| B1 		|	2U 		|	E5-2650 v2 |	64 |	INTEL X520 |	10 |


# Class A1
Class A1 is a 1U Server, typically installed in a Supermicro CSE-815 Chassis. The purpose of Class A1 is to provide a fair amount of compute and network power inside a small frame. A1 Class servers are used to host mission critical services, such as DNS, Routing or Kubernetes Master nodes.

A1 Servers are designed to have a high uptime, so the CPU model is kept to a relatively low spec to allow for overkill cooling, and low power draw. RAM is kept low for cost purposes.

Storage for class A1 servers are proivded by a single SSD on a Sata 3 Link. Typically the size will be 500GB. This serves for all storage.

Given it's purpose, Class A1 is provided with a Mellanox ConnectX-3, a 40Gb/s NIC. This it to allow for high performance routing. 

Class A1 servers are installed with Fedora 31 Server.


# Class B1
Class B1 is a 2U Server, typically installed in a Supermicro CSE-825 or CSE-827 Chassis. The purpose of Class A2 is to provide compute power and act as a general purpose hypervisor. 

B1 Class servers are designed for performance, so their CPU model is significantly more powerful along with RAM being higher. They are used to host general purpose virtual machines, such as Kubernetes nodes. Class B1 servers have a lower priority for uptime than A1. 

Storage for class B1 servers are provided by 2 SSDS. The first being a 250-500GB SSD on a Sata 3 Link. The second being a 500GB NVME SSD on a PCIE Link. 

The Sata SSD provides OS level storage; whereas the NVME provides Virtual Machine and Ceph Storage.

Class B1 is fitted with an Intel X520-SR1 10Gb/s Nic. This is so that there is a fast link to the network, but not fast enough to saturate the routing link (40Gb/s)

Class B1 servers are installed with Fedora 31 Server.