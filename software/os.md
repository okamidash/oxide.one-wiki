---
title: OS Tweaks
description: Operating system level tweaks and setup
published: true
date: 2020-01-25T17:37:20.069Z
tags: OS, Fedora, Virtual Machine, Physical Machine
---

OS Tweaks
---
# Operating System
The base operating system shall be based on Fedora 31 unless there is a specific reason not to. This is to make system management easier across the estate.

Physical hosts use Fedora server.

Virtual hosts use Fedora cloud images.

# Tweaks
The following section covers various tweaks to the base Fedora 31 operating system.

## SELINUX
Selinux is Disabled on all hosts.

This can be done by editing the file `/etc/selinux/config` and ensuring the line `SELINUX=` is `SELINUX=disabled`

## Firewall

The firewall on physical hosts is disabled. This can be done with

```shell
systemctl stop firewalld && systemctl stop firewalld
```

## Packages

### DNF Automatic

To make package management easier, all hosts shall have dnf automatic configured and installed.

Physical hosts shall have dnf-automatic configured to download only. Installing the packages is left up to the operator to do; which is completed on a weekly basis.

This is done so that bringing down a physical host (and the VMs that live on it) is planned, rather than sporadic.

Virtual hosts should have dnf-automatic configured to download and install immediately. This is done because bringing down a VM won't mean bringing down an entire stack (usually).

**Setup**

`sudo dnf install -y dnf-automatic`

**For physical hosts**

`sudo systemctl enable dnf-automatic-download.timer && sudo systemctl start dnf-automatic-download.timer`

**Virtual Hosts**

`sudo systemctl enable dnf-automatic-install.timer && sudo systemctl start dnf-automatic-install.timer`

### Physical Hosts

Physical hosts require a few more packages to be installed.

When installing the system, make sure to follow the spec for system storage.

**Virtualisation**

For hosts intended to be used as virtualization hosts, they must install the virtualization group. This can be done with:

```shell
dnf group install --with-optional virtualization
```

**Motd**

For MOTD generation to work, the following packages need to be installed.

- figlet
- lolcat

This can be done with:

```shell
dnf install -y lolcat figlet
```

**Cron**

```shell
dnf install -y cronie cronie-anacron
```

**LDAP**

For LDAP to work, please install the following packages.

```shell
dnf install -y freeipa-client autoconfig
```
