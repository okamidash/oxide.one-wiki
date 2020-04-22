---
title: RHEL8 SAS2008 controllers
description: Running unsupported HBA cards in RHEL8
published: true
date: 2020-04-22T17:40:33.247Z
tags: 
---

# Reasoning

RedHat officially dropped support for the sas2008 chipset of controller, however these controllers are still widely deployed and are a still a popular choice when building storage systems running on tradditional spinning hard drives.

See the official release notes [here.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/considerations_in_adopting_rhel_8/index#removed-adapters_hardware-enablement)

Below is how to side-load the driver in and allow for the installer to pick up your controller. I've tested this, and as long as you set it up following the below steps, you will be able to do minor point release upgrades with no issues.

# Download the driver needed
Download the corresponding `dd-mpt3sas` driver ISO for your version of RHEL that you’re installing from the [elrepo repository.](https://elrepo.org/linux/dud/el8/x86_64/) Notice the change behind the `el.xx` in the file name! If I was doing RHEL/CentOS8.0 I’d select:

`dd-mpt3sas-27.101.00.00-1.el8_0.elrepo.iso` 

If I am installing 8.1 I’d use:

`dd-mpt3sas-28.100.00.00-2.el8_1.elrepo.iso` 

Burn LSI driver to a USB key. 

# Installing RHEL8 with a unsupported HBA

Boot from RHEL/CentOS install media. On the install screen, select CentOS, press tab,  then append `inst.dd` to the kernel parameters for boot.

You’ll see a prompt asking for which drive to select from, if you don’t see your USB key, give it a moment, then press r to refresh, you should see it pop up. Type the number for your USB key. 

Type 1 to select the driver. (You should see an X inside of the check box, then press `c` to continue. Then press `c` to continue again.  Do the install like normal, then reboot.

Upon reboot the very first you need to do is ensure you have an updated version of dracut, there is a bug in earlier version that causes it to not bring in 3rd party modules resulting in a system that does not startup. DO NOT RUN A KERNEL UPDATE YET!!! First get dracut patched, reboot the system if there is an update for the package that is installed. Once rebooted, then will need to get elrepo.orgs repository configured. This will provide us updates to the mpt3sas driver. Once the driver is installed from their repos, you may then proceed with doing a full system upgrade including the kernel. You will find a file under /lib/modules/<currently-running-kernel>/extra/mpt3sas/mpt3sas.ko  This is the kernel module that is required to make the system boot with the sas 2008 based controller, do NOT delete it, it is symlinked in new kernels via the `soft-updates' directory. Care should be taken when doing point release upgrades. (IE RHEL 8.1 to 8.2) Ensure that the driver is updated first, then install the point release upgrade.

```
sudo yum update dracut
sudo reboot
sudo  rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
sudo yum install https://www.elrepo.org/elrepo-release-8.el8.elrepo.noarch.rpm
sudo yum check-update
sudo yum install kmod-mpt3sas
sudo reboot
sudo yum upgrade -y
sudo depmod -a
sudo dracut -f
sudo reboot
```

You’re now good to go with the rest of your server configuration.