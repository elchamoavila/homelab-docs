# Windows Server VM Install

## Overview
The objective is to successfully set up the VM on Proxmox for Windows Server and install Windows Server. Considering the evaluation version of this ISO lasts only 180-days, then I would need to reinstall, documenting this process seems helpful.

## Prerequisites
- Download the Windows Server ISO from https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2025 onto my laptop
- Download the VirtiO ISO from https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/archive-virtio/?C=M;O=D

## Steps

### 1. Upload Installation Media to Proxmox
In the Proxmox web application, in Server View, I went to Datacenter > pve > local (pve), then in the side panel of the main window, went to ISO Images, and clicked on Upload. I selected SERVER_EVAL_x64FRE_en-us.iso, which I had downloaded on my laptop. It was 5 gigabytes over my home network, so it took a while. I then uploaded the VirtiO ISO, which was virtio-win-0.1.285.iso in this case 

The goal in the future is have that ISO live in a NAS instead of on the drive Proxmox is on.

### 2. Create VM

- On Proxmox, I clicked on Create VM.

- In the General Tab, I set the following; Node: pve, VM ID: 103, Name: vm-windows-server

- I set the VM ID to 103 to match the IP address I want to give this VM; 192.168.1.3.

- In the OS tab, I set the following: Storage:Local, and ISO image: the windows server iso I just uploaded. I selected Guest OS: Type: Microsoft Windows Version: 11

- I checked "Add additional drive for VirtIO drivers" and selected the virtio iso I uploaded.

- In the System tab, I selected local-lvm for EFI Storage and de-selected 'Add TPM'. I made sure "VirtIO SCSI Single" was selected for SCSI controller and Qemu Agent was checked.

- In the Disks tab, I set Bus/Device to 'VirtIO Block', Storage to 'Local-lvm', Disk size to 55 GiB, and Cache to 'Write back'

- In the CPU tab, I set Cores to 2, and Type to 'host'.

- In the Memory tab, I allocated 4096 MB to this VM.

- In the Network tab, I set the bridge to vmbr0.

- In the Confirm tab, I reviewed the settings, and clicked on Finish.

### 3. Install Windows Server

In Proxmox, in Server View, I went to Datacenter > pve > 103, and clicked on Hardware. I checked that there were two cd drives ready, one with the windows server iso and another one with the virtio iso. I started the VM. I went to console view to be able to interact with the installation process.

While booting I found out that you need to press a button in the few seconds you have when it asks you to "Boot from CD", otherwise it wont automatically boot iso and time out.

I selected "Windows Server 2022 Standard Evaluation (Desktop Experience)". I selected "Custom: Install " option since this is a new vm. I'm guessing I might choose the other one in 180 days.

I clicked on 'Load Driver', then on 'Browse', then I clicked on CD Drive (D:) virtio > amd64 > 2k22, and clicked Next. Now we can see the unallocated space, 55 G, and I clicked Next.

The installer proceeded to install Windows, then rebooted by itself. The youtube video said it would run for a bit then restart itself again.

### 4. Windows Server Setup

I set the administrator password, then logged in.

I went to File Explorer > CD Drive (D:) virtio. I right clicked on virtio-win-guest-tools and ran as administrator. I stepped through the installer accepting all the defaults.

After it installed, I shut down the vm from inside the os, selecting reason for shutdown "Application: maintanence (planned)"

In the Proxmox WebUI, I selected 102 > Hardware, I selected the cd drive with the virtio iso, clicked on the Remove button and confirmed.

I clicked on the remaining CD drive, clicked Edit, selected "Do not use any media", and clicked OK.

I restarted the VM and logged in to make sure it worked. Success!

## Gotchas / Lessons Learned

### VirtiO
I'm curious to know more about VirtIO. I will study it and add what I learned here.

### CPU Type
I'm curious to know why we changed the CPU type when setting up the VM to Host. I will read up and share here as well.

### Allocation Plan

Below is what I have planned for this machine.

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

## Next Steps

## References
- https://www.youtube.com/watch?v=5A6pHU7f9n0

