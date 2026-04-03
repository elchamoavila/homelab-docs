# SSH Setup for Proxmox

## Overview
The objective is to set up SSH on the Proxmox machine so I can ssh into it from my laptop inside my LAN.

## Steps

### 1. Generate an SSH Key
I actually already have one, since I am using Github, but I created it in the past by running this script on my laptop: 
``ssh-keygen -t ed25519 -C "user@machine"``

### 2. Check if ssh is enabled in Proxmox
On the Proxmox Web GUI, in the pve Shell, i ran:
```
root@pve:~# systemctl status sshd
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-04-03 15:30:53 PDT; 19min ago
 Invocation: 4c937f350c854d1396fb008eea03b67c
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 849 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
   Main PID: 866 (sshd)
      Tasks: 1 (limit: 18893)
     Memory: 4M (peak: 7.6M)
        CPU: 51ms
     CGroup: /system.slice/ssh.service
             └─866 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups"

Apr 03 15:30:53 pve systemd[1]: Starting ssh.service - OpenBSD Secure Shell server...
Apr 03 15:30:53 pve sshd[866]: Server listening on 0.0.0.0 port 22.
Apr 03 15:30:53 pve sshd[866]: Server listening on :: port 22.
Apr 03 15:30:53 pve systemd[1]: Started ssh.service - OpenBSD Secure Shell server.
Apr 03 15:33:27 pve sshd-session[1510]: Connection closed by 192.168.1.91 port 45208 [preauth]
```
Seems to be enabled and running. I checked to see if I could ssh into proxmox with the password set during installation by running:
```
ssh root@192.168.1.1
```
and I was successful! SSH is working.

###
