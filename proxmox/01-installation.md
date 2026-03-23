# Proxmox Installation

## Overview
I'm going to install proxmox on my ultra slim form factor dell desktop.
I already did before, but i'm going to do it again so I document every step.

## Prerequisites
- find my trusty usb

## Steps
### 1. Create Proxmox USB flash installation medium
Downloaded proxmox-ve_9.1-1.iso from https://www.proxmox.com/en/downloads.

Plugged in usb into laptop to whats on it. I saw that it has opernmediavault on it. I wanted to replace it with proxmox.

Ran `lsblk`. I saw the USB is designated as sda.

Ran `sudo dd bs=4M if=proxmox-ve_9.1-1.iso of=/dev/sda conv=fsync oflag=direct status=progress`. Verified with Thunar File Manager that it is now a Proxmox installation media.

### 2. Set-up Installation on Machine
Plugged USB into machine, booted, press f12 continously until boot selection menu showed, and selected `UEFI: General USB Flash Disk 1100`

Selected `Install Proxmox VE (graphical)`. Agreed with EULA.

Selected Target Harddisk: /dev/sda (232.89Gib, Samsung SSD 860). Its the only hard drive in the machine currenlty. In options, selected Filesystem ext4. Clicked Next.

Location and Time Zone selection. Country: United States. Time zone:America/Los_Angeles. Keyboard Layout:U.S. English. Clicked Next.

Provided Administration Password and my Email Address. Clicked Next

Management Network Configuration.
Management Interface:nic0 - This is the correct one as I have the machine wired to my router via ethernet.
Hostname (FQDN):pve.homelab.local IP Address (CIDR):192.168.1.1/24 Gateway:192.168.1.254 DNS Server:192.168.1.254. Clicked Next. Confirmed settings.

My reasoning is that I want this host at 192.168.1.1 and VMs at .2, .3, etc.

### 3. Install
Clicked Install. Rebooted Successfully. Now going on my laptop that is on the local network to see if I can access proxmox from my browser. Success!

Now onwards to [configuration](./02-post-install-config.md)!


## Gotchas / Lessons Learned
Learned advatages of setting the router as the DNS, in case I want the router to resolve IPs from hostnames in the future. Seems like I can easily change this in the future as well.


## References
https://wiki.archlinux.org/title/USB_flash_installation_medium
https://www.youtube.com/watch?v=lFzWDJcRsqo

