# Proxmox Installation

## Overview
I'm going to install proxmox on my ultra slim form factor dell desktop.
I already did before, but i'm going to do it again so I document every step.

## Prerequisites
- find my trusty usb

## Steps
### 1. Create Proxmox USB flash installation medium
Download proxmox-ve_9.1-1.iso from https://www.proxmox.com/en/downloads.
Plug in usb into laptop to whats on it. I see that it has opernmediavault on it. I want to replace it with proxmox.
Run `lsblk`. I see the USB is designated as sda
Run `sudo dd bs=4M if=proxmox-ve_9.1-1.iso of=/dev/sda conv=fsync oflag=direct status=progress`

## Gotchas / Lessons Learned

## References
https://wiki.archlinux.org/title/USB_flash_installation_medium
