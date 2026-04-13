# Ubuntu VM DHCP Reservation Config

## Overview
The objective is to change the static IP from manually being set up by the Ubuntu VM to having it be handled by the DHCP server in my home router

## Steps

### Step 1: Reserve the IP on DHCP/Router

I have an AT&T router. In my browser, I went to 192.168.1.254, and went to 'Home Network' > 'IP Allocation'.
With a terminal window next to my browser window, I sshed into the ubuntu server vm and ran ``> ip link show ens18`` to get MAC address of the VM. In the browser, I used that MAC address to know which device to allocate 192.168.1.2.

### Step 2: Update Netplan Config

I will leave this file: ``/etc/cloud/cloud.cfg.d/99-disable-network-config.cfg`` with this written:
```
network:
  config: disabled
```

...to continue diabling cloud-init.

I originally created this file ``/etc/netplan/01-static.yaml`` to manually set the IP of this device by writing this in it:
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

But since we are now having the DHCP server reserve 192.168.1.2 for this device, we are writing this in it:
```
network:
  version: 2
  ethernets:
    ens18:
      dhcp4: true

```

### Step 3: Apply

I ran:
```
sudo netplan apply
```

Still successfully sshed in, didn't get kicked off.

I ran ``ip addr show ens18`` and got this:
```
    inet 192.168.1.2/24 metric 100 brd 192.168.1.255 scope global dynamic ens18
```
Success!
