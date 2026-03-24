# Proxmox Post-Installation Configuration

## Overview
Objective of this section is to successfully configure this proxmox machine post-installation to get it set up for hosting VMs.
I would like to learn all steps by using the GUI on the browser and through the shell on the browser.

I'm not going to set a DHCP reservation just yet. I might do that in the future to gain hands-on experience doing that.

## Steps

### 1. Run the Proxmox VE Post Install Script
- On the server view side bar, clicked on pve, then in node view, clicked on Shell. Found the Post PVE Install script online (see second reference).
- Pasted and entered this script into the shell `bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh)"`
- Start the Proxmox VE Post Install Script (y/n)? Y
- Clicked Ok on Deb822 sources detected prompt.
- Disabled the 'pve-enterprise' repository.
- Disabled the 'ceph enterprise' repository.
- Added 'pve-no-subscription' repository. 
- Clicked no to not add 'pvetest' repository.
- Disabled subscription nag.
- Disabled high availability.
- Disabled Corosync for a Proxmox VE Cluster.
- Clicked yes to update Proxmox VE now.
- Was extremely patient.
- Rebooted Proxmox VE. Went from Verion 9.1.1 to 9.1.6.

### 2. Set up Backup Schedule
I'm not going to do this right now, because I have no NAS currently and I want to let the 250gb SSD in this machine rest once in a while, i.e. not age the SSD by getting closer to tthe stated TBW of the drive. But I will go over the steps so I can reference them later when I do add a NAS to my local network.
- Datacenter > Backup > Add > Set Schedule > Set Selection Mode: All > Set Storage: NAS location, or dedicated drive
- Retention Tab > Set Keep Last > Click Create

## Gotchas / Lessons Learned
Having a NAS would be super helpful. I plan on investing in a reburshied Dell Tower machine and install OpenMediaVault and set it up with RAID 1. I can have proxmox send all the back ups there, and I can store the isos there as well. I'll also have backups of my laptop there, and perhaps also a database for gitea to have another storage place for my github repositories.

## Next Steps
Now that proxmox is set up, [I will spin up an Ubuntu Server VM](./03-ubuntu-server-vm-setup).

## References
- https://www.youtube.com/watch?v=lFzWDJcRsqo
- https://community-scripts.org/categories?category=proxmox-and-virtualization&preview=post-pve-install

