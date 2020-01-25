---
title: OS Tweaks
description: Operating system level tweaks and setup
published: true
date: 2020-01-25T17:39:15.517Z
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

# Users and Privileges

## Root Account

The root account is disabled.

## okami

okami is the default user setup for all systems. Every VM and Physical host should have this user existing by way of FreeIPA (LDAP).

They belong in the following groups (if they exist):

- docker

- wheel

- libvirt

## Sudo

Users in the wheel group shall have passwordless sudo enabled. This can be achieved with the following line.

`%wheel ALL=(ALL) NOPASSWD: ALL`

The following settings shall also be set in the sudoers file.

```
 Defaults env_keep += "LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET" Defaults env_keep += "HOME" Defaults env_keep += "XAPPLRESDIR XFILESEARCHPATH XUSERFILESEARCHPATH" Defaults env_keep += "QTDIR KDEDIR" Defaults env_keep += "XDG_SESSION_COOKIE"
```

## SSH

SSH is the primary method of accessing any system across the estate.

Password authentication is not allowed anywhere.

`PasswordAuthentication no`

## LDAP

LDAP Through FreeIPA is to be setup and configured for each client on the network. This is to allow for easy user management across systems. This is setup using the FreeIPA ansible roles; and the home dir shall be set on creation.

# Misc

## MOTD

Physical hosts should have a MOTD setup using a script. Currently they should use the script provided by [this repo](https://github.com/okamidash/motdshell)

For the script to run properly; place the script in `/usr/local/bin/dynmotd`

Then append the following line onto /etc/profile:

`/usr/local/bin/dynmotd`

## Cron

Cron is used to handle backing up of virtual machines.

Enable and start Cron with

```shell
systemctl enable --now crond.service
```

Then add the following line to `/etc/anacrontab`

```shell
@daily  25 backup-machines /usr/local/bin/backupscript.sh
```

Then grab the backupscript.sh file from [this repo](https://github.com/okamidash/motdshell) and place it in /usr/local/bin/

