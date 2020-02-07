---
title: Network Design
description: The Network Design within Oxide.one
published: true
date: 2020-02-07T17:42:00.527Z
tags: Network
---

![untitled_diagram.jpg](/untitled_diagram.jpg)

Network Design
---
Within the oxide.one system, there are several networks. They are outlined as follows.

| Name | Subnet | CIDR | DNS | Domain | VLAN
| ---- | ------ | ---- | --- | ------ | ---- |
| Outer 		|	10.0.0.0	| 24	| 1.1.1.1 |	dhcp. 	|	0 |
| Server	 	| 10.0.2.0	| 24	| DNS1 		|	srv.		| 0 |
| Virtual		| 10.0.4.0	| 24	| DNS1 		|	vm.			| 4 |
| Utility		| 10.0.5.0	| 24	| DNS1 		|	nix. 		|	5 |
| Zoned			| 10.0.6.0	| 24	| DNS1 		|	zone. 	|	6 |
| Kube	 		| 10.0.7.0	| 24	| DNS1 		|	kube.		| 7 |
| Wireguard	| 10.0.8.0	| 24	| DNS1 		| wg.	 		| 8 |
| Management|	10.0.10.0	| 24	| DNS1 		|	mgmt.		| 10 |

The domain is a subdomain within .oxide.one, so the outer network would be dhcp.oxide.one. The gateway for each network exists on 10.0.x.1. 

DNS1 is currently **10.0.5.8**

# Networks
## Outer
This network is the wider network. This proivdes internet access to all other networks.
## Server
The Server network is for physical servers only.
## Virtual
The virtual network is designed for general purpose virtual machines that do not have any dependants. 
##  Utility
The Utility network is designed for virtual machines that provide utlity or function and have dependants. This includes services such as NFS and DNS. 
##  Zoned
This network is for untrusted virtual machines, such as [Metasploitable](https://information.rapid7.com/download-metasploitable-2017.html). They do not have access to any other networks, and have limited access to the internet.
##  Kube
The kube network is for [Kubernetes](https://kubernetes.io/) related virtual machines. This isn't strictly Kubernetes nodes. This can also be resources that provide resources only to a kuberentes cluster (such as [ceph](https://ceph.io/)).
## Wireguard
The Wireguard network is for virtual machines that need access to a VPN. Any machine on this network is connected to a VPN over [wireguard](https://www.wireguard.com/) to [Mullvad](https://mullvad.net/en/).
##  Management
The management network is for IPMI Devices and Access point pages. 

# DNS
For each network; there is a subdomain associated with it. These subdomains exist within the oxide.one domain. 

In addition to all the networks and subdomains above, there exists a subdomain for services. svc.oxide.one is to reach services within other networks. All records within svc.oxide.one is CNAME'd to another host.

One such example of this, is for NFS. The NFS server exists as midnight.nix.oxide.one, but it is exposed as a service through nfs.svc.oxide.one. This allows for abstraction away from hosts.

DNS is provided by [FreeIPA](https://www.freeipa.org/) (BIND9). DHCP updates are sent to FreeIPA; so that machines that used DHCP always have a correct A record behind their hostname.

# DHCP
For the outer network, DHCP is provided by [AsusWRT](https://www.asus.com/ASUSWRT/). For all other networks, DHCP is provided by [VyOS](https://vyos.readthedocs.io/en/latest/). The range of IPs is from .50 through to .220. 