---
title: Virtual Machines
description: Virtual Machines in use within Oxide.one
published: true
date: 2020-01-25T17:09:48.919Z
tags: Virtual Machine
---

Virtual Machines
---

| Name | Network | IP | OS | Application |
| ---- | ------- | -- | -- | ----------- |
| Zinc								|	ALL 			| 10.0.x.1 	| 	VyOS 				| Router |
| Proton							| Utility		| 10.0.5.8 	|	Fedora 31 		|	FreeIPA (DNS) |
| Zenith							| Wireguard	|	DHCP 			|	Fedora 31			| Torrenting |
| Midnight						| Utility		| DHCP 			|	FreeNAS				| NAS/NFS | 
| ceph-node-(01-03) 	|	Kube			|	DHCP 			|	Ubuntu 18.04	| Ceph Storage |
| apollo-node-(01-09)	| Kube			| DHCP			| Fedora 31			| Kubernetes Node |

Zinc and Proton are currently hosted by Hydrogen.

Zenith changes hosts frequently.

Midnight exists on Phoenix, as it has the Storage controller passed through to the VM.

There exists one Ceph node on each class B1 machine.

The Apollo Kubernetes cluster has 3 nodes on each class B1 machine.

 