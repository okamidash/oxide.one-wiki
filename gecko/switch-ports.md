---
title: Switch ports diagram
description: The switching ports and their mappings
published: true
date: 2021-07-28T11:39:31.976Z
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
| out					| 9-12				| 10.0.0.0/24		| 12		|
| untagged 		| 13-16				| none					| none	|

## Networks
|BNetwork Name 		| Network address | Cidr 	| Vlan ID |  Usable hosts 						| DHCP range 						| Gateway | 
| -								| -								|	-			|	-				|	-													| -		 			 						| -				| 
| core						| 10.0.1.0				| 25		| 10			| 10.0.1.1 - 10.0.1.126 		| 10.0.1.20-10.0.1.126  | 10.0.1.1		|
| management 			| 10.0.1.128			| 26 		| 11 			| 10.0.1.129 - 10.0.1.190 	| 10.0.1.140-10.0.1.190 | 10.0.1.129 	|	
| out							| 10.0.0.0				| 24		| 12			| 10.0.0.1-10.0.0.250				| 10.0.0.50-10.0.0.250	| 10.0.0.1	|

# Commands - core-switch-luna (10.0.1.131)
# Set the system identity
`/system identity set name=core-switch-luna`
## Set MTU
`/interface set [/interface find interface=all] mtu=9000 l2mtu=9100`
## Add LACP bridge
`/interface bonding add slaves=sfp-sfpplus1,sfp-sfpplus2 mode=802.3ad lacp-rate=30secs link-monitoring=mii name=uplink mtu=9000`
## Bridge settings
```
/interface bridge add name=mainline vlan-filtering=no
/interface bridge port add bridge=mainline interface=uplink

# Add the interface lists
/interface list add name=core comment="Core Network. 10.0.1.0/25 VLAN 10"
/interface list add name=management comment="Management Network. 10.0.1.128/26 VLAN 11"
/interface list add name=out comment="Outer Network. 10.0.0.0/24 VLAN 12"
/interface list add name=untag comment="Untagged Network. No vlan"

# Add the interfaces
:for i from=1 to=8 do={:local iname "ether$i"; /interface list member add list=core interface=$iname}
:for i from=17 to=24 do={:local iname "ether$i"; /interface list member add list=management interface=$iname}
:for i from=9 to=12 do={:local iname "ether$i"; /interface list member add list=out interface=$iname}
:for i from=13 to=16 do={:local iname "ether$i"; /interface list member add list=untag interface=$iname}

# Add the bridge links
/interface bridge port add bridge=mainline interface=core pvid=10
/interface bridge port add bridge=mainline interface=management pvid=11
/interface bridge port add bridge=mainline interface=out pvid=12
/interface bridge port add bridge=mainline interface=untag

# Add bridge vlan entries
/interface bridge vlan add bridge=mainline tagged=core untagged=uplink vlan-ids=10

# Grab the core network list
:global corenets ""; :foreach interface in=[/interface list member print as-value where list=core] do={:set $corenets ($corenets . "," . ($interface->"interface")) }; :set corenets [:pick $corenets 1 ([:len $corenets])];

# Grab the Management network list
:global mgmtnets ""; :foreach interface in=[/interface list member print as-value where list=management] do={:set $mgmtnets ($mgmtnets . "," . ($interface->"interface")) }; :set mgmtnets [:pick $mgmtnets 1 ([:len $mgmtnets])];

# Grab the Out network list
:global outnets ""; :foreach interface in=[/interface list member print as-value where list=out] do={:set $outnets ($outnets . "," . ($interface->"interface")) }; :set outnets [:pick $outnets 1 ([:len $outnets])];

# Set the Core network Vlan Tagging
/interface bridge vlan add bridge=mainline tagged=uplink untagged=$corenets vlan-ids=10

# Set the Management network Vlan tagging
/interface bridge vlan add bridge=mainline tagged=uplink untagged=$mgmtnets vlan-ids=11

# Set the Outward network Vlan tagging
/interface bridge vlan add bridge=mainline tagged=uplink untagged=$outnets vlan-ids=12

# Add a management interface port and IP
/interface vlan add interface=mainline name=MGMT vlan-id=11
/ip address add address=10.0.1.131/26 interface=MGMT

# Set the system identity
/system identity set name=core-switch-luna

```
# Commands - core-switch-celestia (10.0.1.130)
## Set MTU
`/interface set [/interface find interface=all] mtu=9000 l2mtu=9100`
## Set System identity
`/system identity set name=core-switch-celestia`