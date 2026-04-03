# SSH Setup for Proxmox

## Overview
The objective is to set up SSH on the Proxmox machine so I can ssh into it from my laptop inside my LAN.

## Steps

### 1. Generate an SSH Key
I actually already have one, since I am using Github, but I created it in the past by running this script on my laptop: 
``ssh-keygen -t ed25519 -C "user@machine"``

### 2. Check if ssh is enabled in Proxmox
In the Proxmox Web GUI, in the pve Shell, I ran:
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
Success! SSH is working.

### 2. Add public key to Proxmox

```
alejandro@sanaa in  ~ 
❯ ssh-copy-id root@192.168.1.1                                                      04:06 PM
/run/current-system/sw/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/alejandro/.ssh/id_ed25519.pub"
/run/current-system/sw/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/run/current-system/sw/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@192.168.1.1's password: 

Number of key(s) added: 1

Now try logging into the machine, with: "ssh 'root@192.168.1.1'"
and check to make sure that only the key(s) you wanted were added.

alejandro@sanaa in  ~ took 11s 
❯ ssh root@192.168.1.1                                                              04:06 PM
Linux pve 6.17.13-2-pve #1 SMP PREEMPT_DYNAMIC PMX 6.17.13-2 (2026-03-13T08:06Z) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Apr  3 15:59:05 2026 from 192.168.1.91
root@pve:~# 
```

### 3. Disable Password Authentication and Enable Addional SSH Hardening

Firstly, I installed vim on Proxmox so I dont have to stumble with nano.
```
root@pve:~# apt install vim
```
Then I proceeded to edit the SSH config:
```
root@pve:~# vim /etc/ssh/sshd_config
```
And I made sure the following lines existed in these setting and were uncommented:
```
PermitRootLogin prohibit-password
PasswordAuthentication no
PubkeyAuthentication yes
```
I also set the following settings for additional ssh hardening
```
ClientAliveInterval 300
ClientAliveCountMax 2
```
I saved the config file and restarted SSH:
```
root@pve:~# systemctl restart sshd
```
While leaving that session open, I opened another terminal to test ssh login again:
```
alejandro@sanaa in  ~ 
❯ ssh root@192.168.1.1                                                              04:21 PM
Linux pve 6.17.13-2-pve #1 SMP PREEMPT_DYNAMIC PMX 6.17.13-2 (2026-03-13T08:06Z) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Apr  3 16:07:15 2026 from 192.168.1.91
root@pve:~#
```
Success!

In proxmox, in installed fail2ban:
```
root@pve:~# apt install fail2ban
```
Then I checked to see if fail2ban is active:
```
root@pve:~# systemctl status fail2ban
WARNING: terminal is not fully functional
Press RETURN to continue 
● fail2ban.service - Fail2Ban Service
     Loaded: loaded (/usr/lib/systemd/system/fail2ban.service; enabled; preset: enabled)
     Active: active (running) since Fri 2026-04-03 16:24:20 PDT; 29s ago
 Invocation: e682fb9a050a450587fc4004cec40334
       Docs: man:fail2ban(1)
   Main PID: 10007 (fail2ban-server)
      Tasks: 5 (limit: 18893)
     Memory: 13.7M (peak: 14.3M)
        CPU: 137ms
     CGroup: /system.slice/fail2ban.service
             └─10007 /usr/bin/python3 /usr/bin/fail2ban-server -xf start

Apr 03 16:24:20 pve systemd[1]: Started fail2ban.service - Fail2Ban Service.
Apr 03 16:24:20 pve fail2ban-server[10007]: Server ready
```
Thats something new that I learned today! I didn't know that ``apt install`` automatically enables and starts services in Debian. So nothing else to do there!

### 4. Set up ssh config file on Laptop
In my laptop, I created the ssh config file at ``~/.ssh/config``, as I didn't have one already, and added this to that file:
```
Host proxmox
  HostName 192.168.1.1
  user root
  IdentityFile ~/.ssh/id_ed25519
```
I tested to see if I can have an easier ssh experience:
```
alejandro@sanaa in  ~ took 1m38s 
❯ ssh proxmox                                                                       04:30 PM
Linux pve 6.17.13-2-pve #1 SMP PREEMPT_DYNAMIC PMX 6.17.13-2 (2026-03-13T08:06Z) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Apr  3 16:22:12 2026 from 192.168.1.91
root@pve:~#
```
Success! This is actually the first time i've ever set up an ssh config file. Why haven't I done this in the past? Nice.

## Gotchas/ Lessons Learned

As just mentioned I didn't know that ``apt install`` automatically enables and starts services. Is that default on Debian and Ubuntu? I think when I was on Arch, you had to enable services manually after installing.

I'm also really happy about this ssh config file. I kind of feel dumb for not knowing about that!

I'll have to do this all again for the Debian VM, so it's nice to get some practice in this. I'll be sure to make that documentation more concise, unless something unexpected comes up.
