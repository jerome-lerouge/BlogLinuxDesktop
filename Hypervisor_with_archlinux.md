# Install a multiboot with QEMU on Archlinux #

## Motiviations ##

I need to test multiboot with Archlinux and NixOS for tweeking the the Systemd-boot without bricked my current Archlinux (the hots in this article)
A good reason to install QEMU with the KVM extension, creating virtualized sysystem, installing the operating system and running virtualized Systemd-boot

## Installation ##

Start with the version with only the x86 emulation

`$ paru -S qemu-desktop`

## Creating new virtualized system ##

`$ qemu-img create -f qcow2 ~/Virtualization/arch_and_nix.cow 64G`

qemu-img create -f qcow2 ~/Virtualization/arch_and_nix.cow 64G

## Installating the guest operating system ##

Use a qcow2 image from Archlinux dowblaoded [here](https://gitlab.archlinux.org/archlinux/arch-boxes/-/packages/1422)
Need to change the root user password with the virt-customize tool :

`$ paru -S guestfs-tools
$ virt-customize -a Arch-Linux-x86_64-cloudimg.qcow2 --root-password password:[your password]`

Start the Guest :
`$ qemu-system-x86_64 -enable-kvm -machine q35 -m 4096M ~/Virtualization/Arch-Linux-x86_64-basic-20240515.236270.qcow2`

### Start Archlinux guest with UEFI ###

UEFI requires writable memory to work properly, so you need to emulate PC System Flash instead. OVMF is a TianoCore project to enable UEFI support for Virtual Machines. It can be installed with the edk2-ovmf package.

`$ paru -S edk2-ovmf
$ cp /usr/share/edk2/x64/MICROVM.4m.fd ~/Virtualization/`

Start the VM with UEFI and boot on the ISO install Archlinux :

`$ qemu-system-x86_64 -cdrom /home/jerome/Virtualization/archlinux-2024.06.01-x86_64.iso \
-boot menu=on -enable-kvm  -machine q35 -m 4096M \
-drive if=pflash,format=raw,file=/home/jerome/Virtualization/OVMF_CODE.4m.fd \
~/Virtualization/arch_and_nix.qcow2`

After configuring the guest system, we can reboot without the ISO:

`$ qemu-system-x86_64 -enable-kvm  -machine q35 -m 4096M -cpu host \
-drive if=pflash,format=raw,file=/home/jerome/Virtualization/OVMF_CODE.4m.fd \
~/Virtualization/arch_and _nix.qcow2`

## Create a Snapshot of the new guest ##

Before altering the guest image we can take a Snapshot to revert to a stable state in case of misconfiguration.

`$ qemu-img snapshot -c Archlinux_fresh_install arch_and_nix.qcow2`

We can list all the snapshot for a image:

`$ qemu-img snapshot -l`

## Source ##

Very good QEMU [documentation form Archlinux](https://wiki.archlinux.org/title/QEMU)
