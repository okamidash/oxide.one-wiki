---
title: Virtualization
description: Virtual Machine Design
published: true
date: 2020-01-25T12:19:11.562Z
tags: Virtual Machine, Storage
---


## Virtual Networks

Networks are handled differently in the oxide.one realm. Instead of using NAT for networking, bridging is preferred. This requires a bit of setup though.

For each Network (out of the 9); there needs to have a bridge interface existing on the host machine assigned to the VLAN interface.

Then, you will need to define the networks in libvirt. 

For all of the networks, except for the *virtual* network, they are to be named consistent with the network name. For example, the *zoned* network is named *zoned*.

This is to allow for less confusion, as the bridge, libvirt network and physical network will all be named the same.

The _exception_ to all this is the *virtual* network. This shall be named 'default' so that libvirt does not complain that there are no default networks.

The code to create bridged networks is as follows.

```xml
<network>
  <name>NAME</name>
  <forward mode="bridge"/>
  <bridge name="BRIDGENAME"/>
</network>
```

Where `NAME` is the name of the network, and `BRIDGENAME` is the name of the bridge.

To import the network into libvirt; run the following command.

```shell
virsh net-define ./bridge.xml
```

Additionally, you will need to set the following sysctl value.

```
net.ipv4.ip_forward = 1
```

This can be set permanently with the following line:

```shell
echo "net.ipv4.ip_forward = 1" | sudo tee /etc/sysctl.d/99-ipforward.conf
```

## Storage

The 'default' storage for libvirt shall point to the 'virt' lvm partition. This is either

```
/storage/loc/blaze/virt/images
```

or

```
/storage/loc/inferno/virt/images
```
