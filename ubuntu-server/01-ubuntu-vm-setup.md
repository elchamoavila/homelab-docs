# Post-Install Ubuntu VM Setup and Config

## Overview
The objective is to do all the necessary setup and configuration changes after creating the Ubuntu Server VM. This will include:
- Set-up qemu-guest-agent
- Set Static IP address
- Set-up SSH and harden

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
Seems good.
In the Proxmox Web GUI, I went to 102(vm-ubuntu-server) > console, logged in, and ran:``sudo apt-get install qemu-guest-agent``. Success.

In the Proxmox Web GUI, I went to 102(vm-ubuntu-server) > Options, I selected 'QEMU Guest Agent', and edited it to enable it by checking yes on 'Use QEMU Guest Agent' and pressing Okay. I then rebooted the Ubuntu Server VM.

After reboot, I checked to see if the qemu-guest-agent was running by running the following in the Ubuntu VM console:
```
systemctl status qemu-guest-agent
```
The terminal returned with ``Active: active (running)``. Don't gotta tell me twice!

### Step 2: Set Static IP Address

First, I ran ``ip addr show`` in the Ubuntu console to see what IP address it was using. It returned ``192.186.1.138``. As mention in the installation documentation of this VM, I want to set that to ``192.186.1.2``.

I checked to see what is in ``/etc/netplan/`` and saw that the only file was ``50-cloud-init.yaml``. Google searches told me that even if I edit that file, cloud=init

According to the Network configuration page of the cloud-init wiki (check references below), cloud-init, which is shipped with Ubuntu Server, has network management that is not compatible with setting up a static IP address on a VM in a homelab on a LAN. Cloud init will override changes made to the ``50-cloud-init.yaml`` file, so we have to disable that behavior. That behavior is meant for cloud instances. 

Cloud-init searches for network configuration from various places, but where we are wanting edit is in the System config: a ``network:`` entry in ``/etc/cloud/cloud.cfg.d/*`` configuration files. So we just need to make a file there and place the appropriate things in that file.

I created file ``/etc/cloud/cloud.cfg.d/99-disable-network-config.cfg`` and wrote this in it:
```
network:
  config: disabled
```

I then created a new netplan file at ``/etc/netplan/01-static.yaml`` and wrote this in it:
```
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: false
      addresses:
        - 192.168.1.2/24
      routes:
        - to: default
          via: 192.168.1.254
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
```
I changed file permissions so that only root can read and write it by running:
```
sudo chmod 600 /etc/netplan/01-static.yaml
```
To apply the new settings, I ran ``sudo netplan apply``.

To see if it apply, I ran ``ip addr show ens18`` and it returned:
```
inet 192.168.1.2/24
```
Cool! I will restart the machine and see if it sticks.

``ip addr show`` shows that ``192.168.1.2`` is the new static IP. Success! 

### Step 3: Setup SSH

#### Step 2.1: Check if SSH is installed and running on Ubuntu VM
In the Proxmox Web Gui, in the Ubuntu VM console, I checked to see if ssh was already installed and running:
```
systemctl status ssh
```
It returned: ``Active: active (running)``. Awesome.

I checked to see if I could ssh into the Ubuntu VM from my laptop by running the following from my laptop:
```
❯ ssh alejandro@192.168.1.2
```
Success!

#### Step 2.2: Copy keys over to Ubuntu VM from Laptop
From my laptop:
```
❯ ssh-copy-id alejandro@192.168.1.2
```
Afterwards, I checked to see if I could ssh from my laptop without a password:
```
❯ ssh alejandro@192.168.1.2
```
Success!

#### Step 2.3: SSH Harden Ubuntu VM
I edited ``/etc/ssh/sshd_config`` and set the following settings:
```
PasswordAuthentication no
PubkeyAuthentication yes
```
I restarted SSH on Ubuntu:
```
sudo systemctl restart ssh
```
I then installed fail2ban:
```
sudo apt install fail2ban
```
and checked to see if it was auto-enabled:
```
systemctl status fail2ban
```
Success!

#### Step 2.4: Edit ssh config on Laptop
On my laptop, I added the following to ``~/.ssh/config``:
```
Host ubuntu-vm
  HostName 192.168.1.2
  user alejandro
  IdentityFile ~/.ssh/id_ed25519
```
Now all I need to do to ssh into the Ubuntu Server VM is:
```
❯ ssh ubuntu-vm
```
Nice.

## Gotchas / Lessons Learned
I definately want to learn more about cloud-init and netplan and how that all works. I wonder how cloud-init is meant to work in a cloud deployment.

## References
- https://www.youtube.com/watch?v=lFzWDJcRsqo 
- https://pve.proxmox.com/wiki/Qemu-guest-agent
- https://ubuntu.com/server/docs/explanation/networking/configuring-networks/
- https://docs.cloud-init.io/en/latest/reference/network-config.html
