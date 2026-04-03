# Post-Install Ubuntu VM Setup and Config

## Overview
The objective is to do all the necessary setup and configuration changes after creating the Ubuntu Server VM. This will include:
- Set up qemu-guest-agent

## Steps

### Step 1: Install Qemu-guest-agent

The Proxmox wiki says this about Qemu-guest-agent:
```
== Introduction - What is qemu-guest-agent ==

The qemu-guest-agent is a helper daemon, which is installed in the guest. It is used
to exchange information between the host and guest, and to execute command in the guest.

In Proxmox VE, the qemu-guest-agent is used for mainly two things:
# To properly shutdown the guest, instead of relying on ACPI commands or windows policies
# To freeze the guest file system when making a backup/snapshot (on windows, use the volume shadow copy service VSS). If the guest agent is enabled and running, it calls ''guest-fsfreeze-freeze'' and ''guest-fsfreeze-thaw'' to improve consistency.
```

== Introduction - What is qemu-guest-agent ==

The qemu-guest-agent is a helper daemon, which is installed in the guest. It is used
to exchange information between the host and guest, and to execute command in the guest.

In Proxmox VE, the qemu-guest-agent is used for mainly two things:
# To properly shutdown the guest, instead of relying on ACPI commands or windows policies
# To freeze the guest file system when making a backup/snapshot (on windows, use the volume shadow copy service VSS). If the guest agent is enabled and running, it calls ''guest-fsfreeze-freeze'' and ''guest-fsfreeze-thaw'' to improve consistency.

## References
- https://www.youtube.com/watch?v=lFzWDJcRsqo 
- https://pve.proxmox.com/wiki/Qemu-guest-agent
