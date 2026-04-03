# Ubuntu Server VM Install

## Overview
The objective is to successfully set up the VM on Proxmox for an Ubuntu Server. This document will only cover setting up and starting the VM on Proxmox. Installation and configuration of the Ubuntu Server will be on another document, and linked to at the bottom of this document.

## Prerequisites
- Download ubuntu-24.04.4-live-server-amd64.iso from https://ubuntu.com/download/server

## Steps

### 1. Upload Installation Media to Proxmox
In the Proxmox web application, in Server View, I went to Datacenter > pve > local (pve), then in the side panel of the main window, went to ISO Images, and clicked on Download from URL. I for URL: I put `https://releases.ubuntu.com/24.04.4/ubuntu-24.04.4-live-server-amd64.iso`, then clicked on 'Query URL'. Proxmox correctly identified the file, and I downloaded the ISO to Proxmox. 

The goal in the future is have that ISO live in a NAS instead of on the same drive Proxmox is on.

### 2. Create VM

- On Proxmox, click on Create VM.

- In the General Tab, I set the following; Node: pve, VM ID: 102, Name: vm-ubuntu-serve

- I set the VM ID to 102 to match the IP address I want to give this VM; 192.168.1.2.

- In the OS tab, I set the following: Storate:Local, and ISO image: the ubuntu server iso I just downloaded.

- In the System tab, I changed nothing.

- In the Disks tab, I set Disk size to 32GiB.

- In the CPU tab, I set Cores to 2.

- In the Memory tab, I allocated 3072 MiB to this VM.

- In the Network tab, I set the bridge to vmbr0.

- In the Confirm tab, I reviewed the settings, and clicked on Finish.

### 3. Install Ubuntu Server

In Proxmox, in Server View, I went to Datacenter > pve > 102, then went to the Console tab in the sidebar of the main window, and installed Ubuntu. I enabled third-party drivers just in case. I did not enable Ubuntu Pro. I chose yes to Install OpenSSH server. I did not select any featured server snaps.

## Gotchas / Lessons Learned

I had to stop and think how much disk space and memory to give this machine. I want to set up this machine with Ubuntu Server, Windows Server, Windows 11 Client VM, and some space left over for something I might want to learn later. The machine only has 250 GBs of space. I'm thinking I might start off the allocations like this:
| OS | Space |
|----|-------|
| Ubuntu Server | 32 GB |
| Windows Server | 55 GB |
| Windows Client | 60 GB |
| something else | 20-32 GB |

The machine only has 16 GB of RAM, so for RAM allocation I'm considering the following:

| OS | RAM |
|----|-----|
| Ubuntu Server | 3 GB |
| Windows Server | 4 GB |
| Windows Client | 4 GB |
| something else | 2 GB |

Thankfully, I can adjust this after I set up the VMs.

## Next Steps
Post-Install Config

## References
- https://www.youtube.com/watch?v=lFzWDJcRsqo
- https://ubuntu.com/download/server
