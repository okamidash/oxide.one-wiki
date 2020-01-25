---
title: Physical Machines
description: Description of physical machines within the oxide.one realm.
published: true
date: 2020-01-25T17:03:15.327Z
tags: Physical Machine, Hypervisor
---

Physical Machines
---
# Class A1 Machines
Currently, there is only one Class A1 Machine, Hydrogen.

Hydrogen is the 'master node' of the estate. This server runs everything mission critical to operation of the estate. 

It's IP is `10.0.2.2`

Hydrogen currently provides the Routing, DNS and Master nodes for Kubernetes and Ceph.

# Class B1 Machines
The following machines are class B1.

- Blackbird (`10.0.2.4`)
- Inferno (`10.0.2.6`)
- Phoenix (`10.0.2.8`)

# Unclassified Machines
Halcyon is an Unclassed machine. It is designed to be the holdover machine while infrastructure is migrated. Once infrastructure is successfully migrated, the machine shall be Decomissioned.