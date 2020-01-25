---
title: Filesystem Layout
description: Design Brief for filesystem layout
published: true
date: 2020-01-25T12:15:38.888Z
tags: Virtual Machine, Physical Machine, fs
---

# Filesystem Layout

For all machines, physical and virtual; storage specific directories are kept in the `/storage` directory. The purpose of this is to make it clear the type of storage, where it is and to root FS uncluttered.

The following directories are to be made when a system is provisioned.

`/storage/loc`

`/storage/net`

## Network Storage

Network based storage is kept under `/storage/net`

The pattern followed for network storage is as follows:

`/storage/net/{type}/{host}/{volume}`

Where `type` is the method of storage, abbreviated to 3 letters. It is good practice to keep the first character unique, so that tab completion is easier.

- Network FIle System (NFS): `/storage/net/nfs`

- Gluster FS: `/storage/net/gfs`

- Ceph Storage: `/storage/net/cep`

`host` relates to where the storage is kept. For a NFS based storage exported on the host 'blackbird' the directory would be `/storage/net/nfs/blackbird/`

`volume` relates to the name of the share/volume exported.  Continuing the previous example, for a share called `cloudinit` the full storage path is `/storage/net/nfs/blackbird/cloudinit`

*Special case*

For local net storage; i.e a gluster volume mounted; use `local` as the host.

Example:  `/storage/net/gfs/local/vol1`

## Local Storage

Local storage is to be mounted and kept under `/storage/loc` 

The pattern for local storage is as follows:

`/storage/loc/{type}/{volume}`

Where `type` is the type of storage. The naming convention for the type of storage is as follows.

- ZFS based Raid array: `ember`

- SSD based Storage: `flame`

- NVME based Storage: `blaze`
  
  This is to allow for clear identification of the type of storage, and it's relative speed. (it also looks cool)

`volume` relates to the name of the volume. For a ZFS filesystem with a volume exported as `glusterstore` the path would be as follows: `/storage/loc/zfs/ember/glusterstore`

## Root filesystem

Where possible, the root filesystem externally shall be only 3 partitions. The first being a FAT32 EFI boot partition, mounted at `/boot/efi`. The size shall not exceed 500MB.

This filesystem shall be labelled "EFI"

The second filesystem shall be an EXT4 filesystem mounted at `/boot` not exceeding 10GB. This filesystem shall be named "BOOT"

The third filesystem shall be an LVM based filesystem, taking up the rest of the device. This filesystem shall be named "ROOT"

The LVM partition shall be allocated up to 50GB to the root filesystem, and the rest kept for alternate directories.

The Root filesystem should be formatted as an EXT4 Partition.

## NVME Storage

For virtualization hosts; NVME Storage is available on the local system. 

The NVME storage filesystem should exist on a Logical Volume called 'virt'; formatted as XFS.

The mount point for this filesystem is `/storage/loc/blaze/virt`

Inside the filesystem, there shall be a directory called `images`.

Where NVME storage does not currently exist, there shall be SSD based storage ; with the same rules as above (except mounted at `/storage/loc/flame/virt`)
