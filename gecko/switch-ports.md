---
title: Switch ports diagram
description: The switching ports and their mappings
published: true
date: 2021-07-28T08:25:10.753Z
tags: 
editor: markdown
dateCreated: 2021-07-27T12:59:36.075Z
---

# Switches

## Switch 1 - Top of rack (QSFP+) | celestia
DHCP name: cs-celestia

### Port mappings


## Switch 2 - Bottom of Top of rack (SFP+) | luna
DHCP name: cs-luna

### Port mappings

| Port Group 	| Ports 			| Network 			| vlan 	|
| -						| -						|	-							| -		 	|
| core				| 1-8 				| 10.0.1.0/25 	| 10		|
| management	| 17-24				| 10.0.1.128/26 |	11		|
| out					| 8-12				| 10.0.0.0/24		| 12		|
| untagged 		| 13-16				| none					| none	|

## Networks
|BNetwork Name 		| Network address | Cidr 	| Vlan ID |  Usable hosts 						| DHCP range 						| Gateway | 
| -								| -								|	-			|	-				|	-													| -		 			 						| -				| 
| core						| 10.0.1.0				| 25		| 10			| 10.0.1.1 - 10.0.1.126 		| 10.0.1.20-10.0.1.126  | 10.0.1.1		|
| management 			| 10.0.1.128			| 26 		| 11 			| 10.0.1.129 - 10.0.1.190 	| 10.0.1.140-10.0.1.190 | 10.0.1.129 	|	
| out							| 10.0.0.0				| 24		| 12			| 10.0.0.1-10.0.0.250				| 10.0.0.50-10.0.0.250	| 10.0.0.1	|

# Data
Ports

Your content here