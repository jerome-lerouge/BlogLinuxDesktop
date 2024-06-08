# Install NixOS on a MacBookPro 12.1

## Official Documentation
[Need to read it before going on !](https://nixos.org/manual/nixos/stable/) 
## Installation alternative

### Graphique Installation

Use the GUI Calamares installer, limited choice for managing hard drive partions. Doesn't the good EFI boot partition on Nvme Disk. Choose the EFI on /dev/sda of the Fedora installation. NixOS use Systemd for the boot loader while Fedora use Grub. After finishing the installation processus, Systemd loader doesn't show the Fedora entry for starting the system.
Loose the Fedora Distro on the disk !

TODO: read the NixOS Documentation about multiboot.

### Using the command line installation processus

For a better control of the configuration with other distro installed on the disks, I choose to use command line install processus wich allow me to chose the UEFI boot partition on my disk. My current setup is a MacBookPro 13 Inch early 2015 (verison 12.1), with MacOS and Archlinux installed. My objective is to install NixOs and manage multiboot with the 3 OS.
Good howto for installation with the [CLI](https://www.youtube.com/watch?v=fDxhkSnFSPw&t=0s) and a NixOS Installation [Cheat Sheet](https://www.ml4w.com/wp-content/uploads/2023/08/ml4w_nixosinstallation_cheatsheet.pdf)

### Let's go !

#### Main configuration file

Edit the file /etc/nixos/configuration.nix with sudoeddit:

`$ sudoeddit /etc/nixos/configuration.nix` 

With the next command we applied the changes we just made.

`sudo nixos-rbuild switch` 

#### Choose the channel

By default we are on the 23.11 channel, and if we want to live on the bleeding edge we need to change the channel :

`$ sudo nix-channel --add https://channels.nixos.org/nixos-unstable nixos` 

#### Upgrade system

After changing our channel to bleeding edge with unstable, we can upgrade to the latest version :

`$ sudo nixos-rebuild switch --upgrade` 

We can upgrade automaticaly the system by adding the following lines in the configuration file :

> [!NOTE]
> system.autoUpgrade.enable = true;
> system.autoUpgrade.allowReboot = true;
> system.autoUpgrade.channel = "https://channels.nixos.org/nixos-unstable;


The system will not update their version automaticaly, because the version is tight to a channel. Channel need to be update separetly.
To update a channel, we need to use Nix Flake. 
Todo: complete the information about Nix Flake.
Todo: understand why we have 2 differents mode ops to upgrade the system (channel vs Flake)

## Next : 

1. learn the NixOS langage use to configure the distro
2. Install NixOS on the mac !
3. Configure Hyprland in NixOS
4. Customize NixOS in a gaming laptop

## Source

1. [NixOS beginner guide](https://www.youtube.com/watch?v=bjTxiFLSNFA) 
