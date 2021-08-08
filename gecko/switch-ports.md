---
title: Switch ports diagram
description: The switching ports and their mappings
published: true
date: 2021-08-08T13:11:10.191Z
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

### Port mappings (luna)

| Port Group 	| Ports 			| Network 			| vlan 	|
| -						| -						|	-							| -		 	|
| core				| 1-8 				| 10.0.1.0/25 	| 10		|
| management	| 17-24				| 10.0.1.128/26 |	11		|
| out					| 9-12				| 10.0.0.0/24		| 12		|
| untagged 		| 13-16				| none					| none	|

### Port mappings (celestia)

| Port Group 	| Ports 			| Network 			| vlan 	|
| -						| -						|	-							| -		 	|
| core				| 3-24 				| 10.0.1.0/25 	| 10		|
| management	| 17-24				| 10.0.1.128/26 |	11		|
| out					| 9-12				| 10.0.0.0/24		| 12		|
| hyper				| N/A					| 10.0.2.0/24 	| 14		|
| untagged 		| 1-2				| none					| none	|

## Networks
|BNetwork Name 		| Network address | Cidr 	| Vlan ID |  Usable hosts 						| DHCP range 						| Gateway | 
| -								| -								|	-			|	-				|	-													| -		 			 						| -				| 
| core						| 10.0.1.0				| 25		| 10			| 10.0.1.1 - 10.0.1.126 		| 10.0.1.20-10.0.1.126  | 10.0.1.1		|
| management 			| 10.0.1.128			| 26 		| 11 			| 10.0.1.129 - 10.0.1.190 	| 10.0.1.140-10.0.1.190 | 10.0.1.129 	|
| hyper						| 10.0.2.0				| 24		| 14			| 10.0.2.1-10.0.2.255 			|	NONE									| 10.0.2.1 		|
| out							| 10.0.0.0				| 24		| 12			| 10.0.0.1-10.0.0.250				| 10.0.0.50-10.0.0.250	| 10.0.0.1	|

# First-Boot Setup
This section describes the first time setup for both Luna and Celestia, for their respective switches.
## 1. Hostname setup
Set the identities of both switches
**Luna**
```
/system identity set name=core-switch-luna
```
**Celestia**
```
/system identity set name=core-switch-celestia
```
## 2. Crosslink bridge
Create a link between both switches so that they can effectively talk to each other.

**Luna**
```
/interface bonding add slaves=sfp-sfpplus1,sfp-sfpplus2 name=crosslink comment="Crosslink between Luna and Celestia" mode=802.3ad
/ip address add address=172.16.0.1/24 interface=crosslink
```
**Celestia**
```
/interface bonding add slaves=sfp-sfpplus1,sfp-sfpplus2 name=crosslink comment="Crosslink between Luna and Celestia" mode=802.3ad 
/ip address add address=172.16.0.2/24 interface=crosslink
```

## 3. MTU
Set the MTU to 9000 for all the interfaces, because speed.
**Both**
```
/interface set [/interface find interface=all] mtu=9000 l2mtu=9100
```

## 4. Interface Lists
Create the Interface lists
**Both**
```
/interface list add name=core comment="Core Network. 10.0.1.0/25 VLAN 10"
/interface list add name=management comment="Management Network. 10.0.1.128/26 VLAN 11"
/interface list add name=out comment="Outer Network. 10.0.0.0/24 VLAN 12"
/interface list add name=untag comment="Untagged Network. No vlan"
```
Then, add the interfaces to the lists.
**Luna**
```
:for i from=1 to=8 do={:local iname "ether$i"; /interface list member add list=core interface=$iname}
:for i from=17 to=24 do={:local iname "ether$i"; /interface list member add list=management interface=$iname}
:for i from=9 to=12 do={:local iname "ether$i"; /interface list member add list=out interface=$iname}
:for i from=13 to=16 do={:local iname "ether$i"; /interface list member add list=untag interface=$iname}
```

**Celestia**
```
/interface list add name=qsfp-1 comment="qsfp one"
/interface list add name=qsfp-2 comment="qsfp two"
:for i from=3 to=24 do={:local iname "sfp-sfpplus$i"; /interface list member add list=core interface=$iname}
:for i from=1 to=4 do={:local iname "qsfpplus1-$i"; /interface list member add list=qsfp-1 interface=$iname}
:for i from=1 to=4 do={:local iname "qsfpplus2-$i"; /interface list member add list=qsfp-2 interface=$iname}
```

## 5. Mainline Bridge
**Both**
```
/interface bridge add name=mainline vlan-filtering=no
```
**Luna**
```
/interface bridge port add bridge=mainline frame-types=admit-only-vlan-tagged ingress-filtering=yes interface=crosslink
```
**Celestia**
```
/interface bridge port add bridge=mainline interface=crosslink frame-types=admit-only-vlan-tagged
```
Then add the interface lists to the bridge.
**Both**
```
/interface bridge port add bridge=mainline interface=core pvid=10 ingress-filtering=yes
/interface bridge port add bridge=mainline interface=management pvid=11 ingress-filtering=yes
/interface bridge port add bridge=mainline interface=out pvid=12 ingress-filtering=yes
/interface bridge port add bridge=mainline interface=untag ingress-filtering=yes
```
**Celestia**
```
/interface bridge port add bridge=mainline interface=qsfp-1 ingress-filtering=yes
/interface bridge port add bridge=mainline interface=qsfp-2 ingress-filtering=yes
```

Then add a vlan bridge interface to the bridge.
```
/interface vlan add interface=mainline mtu=9000 name=sw-core vlan-id=10
/interface vlan add interface=mainline mtu=9000 name=sw-management vlan-id=11
/interface vlan add interface=mainline mtu=9000 name=sw-out vlan-id=12

```
Finally, add a DHCP IP for the ports
```
/ip dhcp-client add interface=sw-management
```

## 6. Vlan Tagging
It's necessary to set the bridge to to VLAN tagging.
**Luna**
```
/interface bridge vlan add bridge=mainline tagged=mainline,crosslink vlan-ids=10
/interface bridge vlan add bridge=mainline tagged=mainline,crosslink vlan-ids=11
/interface bridge vlan add bridge=mainline tagged=mainline,crosslink vlan-ids=12
```
**Celestia**
```
/interface bridge vlan add bridge=mainline tagged=qsfpplus1-1,crosslink,mainline vlan-ids=10
/interface bridge vlan add bridge=mainline tagged=qsfpplus1-1,crosslink,mainline vlan-ids=11
/interface bridge vlan add bridge=mainline tagged=qsfpplus1-1,crosslink,mainline vlan-ids=12
```
## 7. Enabling Filtering
**Celestia**
```
/interface bridge set mainline vlan-filtering=yes ingress-filtering=yes
```
**Luna**
```
/interface bridge set mainline vlan-filtering=yes ingress-filtering=yes
```

