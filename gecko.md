---
title: Gecko
description: 
published: true
date: 2021-09-12T16:38:45.465Z
tags: 
editor: markdown
dateCreated: 2021-07-04T23:15:05.880Z
---

# Gecko, the next generation kubernetes cluster

Network Name 	Network address 	Cidr 	Vlan ID 	Usable hosts 	DHCP range 	Gateway
core 	10.0.1.0 	25 	10 	10.0.1.1 - 10.0.1.126 	10.0.1.20-10.0.1.126 	10.0.1.1
management 	10.0.1.128 	26 	11 	10.0.1.129 - 10.0.1.190 	10.0.1.140-10.0.1.190 	10.0.1.129
hyper 	10.0.2.0 	24 	14 	10.0.2.1-10.0.2.255 	NONE 	10.0.2.1
out 	10.0.0.0 	24 	12 	10.0.0.1-10.0.0.250 	10.0.0.50-10.0.0.250 	10.0.0.1

# Networks (under gb.oxide.one)
## Networks
|BNetwork Name 		| Network address | Cidr 	| Vlan ID |  Usable hosts 						| DHCP range 						| Gateway | 
| -								| -								|	-			|	-				|	-													| -		 			 						| -				| 
| core						| 10.0.1.0				| 25		| 10			| 10.0.1.1 - 10.0.1.126 		| 10.0.1.20-10.0.1.126  | 10.0.1.1		|
| management 			| 10.0.1.128			| 26 		| 11 			| 10.0.1.129 - 10.0.1.190 	| 10.0.1.140-10.0.1.190 | 10.0.1.129 	|
| hyper						| 10.0.2.0				| 24		| 14			| 10.0.2.1-10.0.2.255 			|	NONE									| 10.0.2.1 		|
| out							| 10.0.0.0				| 24		| 12			| 10.0.0.1-10.0.0.250				| 10.0.0.50-10.0.0.250	| 10.0.0.1	|
# First-Boot Setup
| Name						| Network 	 | Start 			| End	 				| Cidr 		| Domain 						| VLAN | Total 	|
| ---- 						| ---- 			 |-						|						 -| -				|	-			 						| -	   | - 		 	|			
| overcloud		 		| 10.0.1.0 	 | 10.0.1.0		| 10.0.1.63 	| /26			| overcloud.infra 	| 2		 | 64		 	|
| gecko						| 10.0.1.64  | 10.0.1.64 	| 10.0.1.127 	| /26			|	core.infra  			| 3		 | 64			|
| spectre					| 10.0.1.128 | 10.0.1.128	| 10.0.1.192	| /26			|	virtual.infra			| 4		 | 64			|		
| secure					| 10.0.2.0	 | 10.0.2.0		| 10.0.2.63   | /26			| secure.infra			| 5 	 | 64			|
| vpn							| 10.0.2.64	 | 10.0.2.64	| 10.0.2.127	| /26			| vpn.infra					| 6		 | 64			|
| overcloud-kube  | 10.1.0.0	 | 10.1.0.0		| 10.1.3.255	| /22			| overcloud.kube		| -		 | 2048		|
| gecko-kube      | 10.1.4.0   | 10.1.2.0		| 10.1.7.255  | /22			| gecko.kube				| - 	 | 2048		|
| spectre-kube		| 10.1.6.0	 | 10.1.8.0		| 10.1.9.255	| /22			| spectre.kube			| - 	 | 2048   |
| overcloud-svc   | 10.1.10.0  | 10.1.10.0	| 10.1.11.255 | /22			| overcloud.svc			| - 	 | 2048 	|
| gecko-svc				| 10.1.12.0	 | 10.1.12.0 	| 10.1.13.255	| /22			| gecko.svc					| -		 | 2048   |
| spectre-kube		| 10.1.14.0	 | 10.1.14.0	| 10.1.15.255	| /22			| spectre.svc				| -		 | 2048   |

# Clusters
Gecko is technically a group of 3 clusters, although these will be treated as one.

## Overlord - Primary Cluster
Overlord is, as the name implies, an overlord.

Overlord consis