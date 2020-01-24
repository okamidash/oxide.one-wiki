---
title: Architecture
description: 
published: true
date: 2020-01-24T19:44:08.842Z
tags: 
---

# Section 1 | Architecture

This section will cover the overall design, as well as describe the physical hosts and their attributes. The following image gives a rundown of the hosts I will be referencing in this section.

## Diagram

![host_diagram](files/host_architecture.png)

## Physical hosts

I have a collection of physical hosts in the oxide.one estate. I will discuss their physcial attributes now.

### Overview

| Name      | IP        | Model   | Size | CPU            | Ram (GB) |
| --------- | --------- | ------- | ---- | -------------- | -------- |
| Hydrogen  | 10.0.2.2  | CSE-815 | 1U   | 2x E5-2620 V1  | 68       |
| Blackbird | 10.0.2.4  | CSE-825 | 2U   | 2x E5-2650 V2  | 100      |
| Inferno   | 10.0.2.6  | CSE-825 | 2U   | 2x E5-2650 V2  | 100      |
| Phoenix   | 10.0.2.8  | CSE-826 | 2U   | 2x E5-2650 V2  | 16       |
| Cobra     | 10.0.2.10 | D20     | DSK  | 2x E5-2687W V1 | 64       |
| Helix     | 10.0.2.12 | P310    | DSK  | 1x I7-4790     | 32       |

### Hydrogen

Hydrogen is the 'master node' of the estate. This server runs everything mission critical to operation of the estate. If more than 1 other service depends on something, it will live on hydrogen.

Some VMs that currently live on hydrogen:

- cobalt

- nitrogen

- banshee

Hydrogen currently operates DHCP for the oxide.one realm as well.

### Blackbird

Blackbird is a 'slave node'. It one of the 3 main workhorses of the oxide.one estate.  It serves as a ceph node, and acts as a kubernetes host. Blackbird, along with Inferno and Phoenix provide the majority of the storage across the oxide.one estate; and their configuration is kept in parallel (physically and virtually). It is connected to the network with a 10G SFP+ Link.

### Inferno

Inferno is also a 'slave node'.  It is also connected with an SFP+ Link.

### Phoenix

Phoenix is a slave node, but it is linked to the network with a QSFP+ Link to provide additional bandwidth. This is because Phoenix has more storage capability than Inferno and Blackbird, and it serves as Network storage for lithium and Plex.

### Cobra

Cobra is a Lenovo D20. It currently runs development environments, as well as Lithium.

Plex also runs on Cobra in a docker container, which is often it's main resource hog.

### Helix

Helix is the dedicated 'docker box'. It provides several key docker containers, like my blog and jenkins build server.

## Virtual Hosts

My Virtual hosts is not an exhaustive list, and it does not contain my kubernetes virtual machines.

### Overview

| Name     | IP       | Network   | Host      | vCPUS | Ram (MB) |
| -------- | -------- | --------- | --------- | ----- | -------- |
| proton   | DHCP     | virtual   | Blackbird | 6     | 2048     |
| cobalt   | 10.0.5.2 | utility   | Hydrogen  | 4     | 2048     |
| nitrogen | 10.0.5.4 | utility   | Hydrogen  | 6     | 6144     |
| banshee  | 10.0.5.6 | utility   | Hydrogen  | 6     | 2048     |
| icereach | DHCP     | zoned     | Inferno   | 6     | 4096     |
| lithium  | DHCP     | wireguard | Cobra     | 6     | 6192     |

### Proton

Proton is the server that hosts okami.dev. It lives on the *virtual* network, but should really be moved to the *zoned* network. It runs Fedora 30

### Cobalt

Cobalt is my FreeIPA server. It serves DNS and LDAP to all the networks. This is core infrastructure. It runs Fedora 30. This runs on the *utility* network. 

### Nitrogen

Nitrogen is my Mailserver running Mailcow on docker. It runs on the *utility* network.

### Banshee

Banshee runs HaProxy and proxies requests to Blackbird and Inferno, which both run Nginx in a docker container. It runs Fedora 30.

### Icereach

Icereach is my Kali Linux box.

### Lithium

Lithium is my Torrenting server. I use it to power requests.doubledash.org; as well as download linux ISO images. It runs Fedora 30.

## Docker Containers

| Name      | Image      | Host      | Port     | Purpose              |
| --------- | ---------- | --------- | -------- | -------------------- |
| Ghost     | ghost      | Helix     | 2368     | Personal Blog        |
| Portainer | portainer  | Helix     | 9000     | Docker MGMT          |
| Registry  | registry:2 | Helix     | 5000     | Docker Registry      |
| Postgres  | postgres   | Helix     | 5432     | Database for blog    |
| Gitea     | gitea      | Helix     | 22, 3000 | Git Server           |
| Jenkins   | jenkins    | Helix     | N/A      | Build Server         |
| InfluxDB  | influxdb   | Helix     | N/A      | Database for Grafana |
| Grafana   | grafana    | Helix     | N/A      | Grafana (Graphs)     |
| Redis     | redis      | Hydrogen  | Host     | Database for cloud   |
| MariaDB   | mariadb    | Hydrogen  | Host     | Database for cloud   |
| Nextcloud | custom     | Blackbird | 475      | Cloud Storage        |
| Nginx-pxy | custom     | Blackbird | 443      | Load balancer        |
| Nextcloud | custom     | Inferno   | 475      | Cloud Storage        |
| Nginx-pxy | custom     | Inferno   | 443      | Load Balancer        |
| Plex      | pms-docker | Cobra     | Host     | Plex Media           |

---
