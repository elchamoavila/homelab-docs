# FreeBSD Installation and DHCP Reservation

## Introduction

I already installed FreeBSD as a VM on my laptop that is running NixOS, and I didn't document the process while doing it. But I will document some of what I did, and how I created a DHCP reservation in the VM application.

## NixOS VM Application Declaration and Configuration

In my NixOS configuration files, specifically in `~/nixos/modules/dev.nix`, where I decided to declare the virtualization applications, I declared qemu, libvirt, and virt-manager:
```
environment.systemPackages = with pkgs; [
      # virtualization
      qemu
      libvirt
      virt-manager
    ];
```

I also needed to enable some options in the NixOS configuration file, ``~/nixos/hosts/sanaa/configuration.nix`` (I linked ``/etc/nixos/`` to ``~/nixos/`` for easier NixOS configuration; I don't have to use elevated permissions to make edits to files. It's not the way for enterprise, I'm sure, but fine for my laptop):
```
  #virtualization
  virtualisation.libvirtd.enable = true;
  programs.virt-manager.enable = true;
```

I also needed to add my user to the "libvirtd" group, and I did that by declaring it in the same ``configuration.nix`` file:
```
  users.users.alejandro = {
    isNormalUser = true;
    description = "alejandro";
    extraGroups = [ "input" "video" "seat" "networkmanager" "wheel" "lp" "scanner" "libvirtd" ];
  };
```

After rebuilding the system with new configurations, I downloaded ``FreeBSD-15.1-RELEASE-amd64-disc1.iso``, and used the ``virt-manager`` GUI application to create the VM and install the installation.


## DHCP Reservation

I set the hostname of the FreeBSD VM to ``apollo``.
To set the DHCP Reservation, I first needed to find the MAC address of ``apollo``. I ran ``virsh -c qemu:///system net-dhcp-leases default`` in the NixOS host terminal after finishing installation and while ``apollo`` was still running and got:
```
alejandro@sanaa in  ~ took 3m19s 
❯ virsh -c qemu:///system net-dhcp-leases default                                   06:36 PM
 Expiry Time           MAC address         Protocol   IP address           Hostname   Client ID or DUID
------------------------------------------------------------------------------------------------------------
 2026-09-04 19:35:28   52:54:00:61:14:84   ipv4       192.168.122.168/24   apollo     01:52:54:00:61:14:84
```

``apollo`` has a MAC address of ``52:54:00:61:14:84``. I wanted to set a DHCP reservation for ``apollo`` to 192.168.122.101, and narrow the range the DHCP server gives IP addresses to .2 through .100. I ran ``virsh -c qemu:///system net-edit default`` and edited the file to this end result:
```
<network>
  <name>default</name>
  <uuid>e6e8452d-7dd6-4857-bdc9-c2eed95b9a94</uuid>
  <forward mode='nat'/>
  <bridge name='virbr0' stp='on' delay='0'/>
  <mac address='52:54:00:40:71:ba'/>
  <ip address='192.168.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.122.2' end='192.168.122.100'/>
      <host mac='52:54:00:61:14:84' name='apollo' ip='192.168.122.101'/>
    </dhcp>
  </ip>
</network>
```

I ran
```
virsh net-destroy default
virsh net-start default
```
to restart the network and confirmed the DHCP reservation worked.