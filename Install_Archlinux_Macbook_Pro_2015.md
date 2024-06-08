# Log to install Archlinux on a MacbookPro Early 2015

## Hardware information

Installation d'un nouveau disque Nvme pour passer de 128 Go à 2 To...Une note à faire sur le processus d'installation de ce nouveau disque avec notamment l'ouverture du MacbookPro.

### Préparation du support d'installation

Documentation sur l'installation Archlinux disponible sur le [wiki France](https://wiki.archlinux.org/title/Installation_guide_(Français))
Téléchargement d'une image ISO depuis un des mirroirs disponibles [ici](https://archlinux.org/download/) pour préparer un boot device sur USB
La préparation du media USB se fait en suivant la méthode recommandée dans [un environnement mac](https://wiki.archlinux.org/title/USB_flash_installation_medium_(Français))

```bash
$ diskutil list
$ diskutil unmountDisk /dev/disk3
# dd if=path/to/archlinux-2024.02.01-x86_64.iso of=/dev/rdisk3 bs=1m
$ sudo dd if=archlinux-2024.02.01-x86_64.iso of=/dev/disk3 bs=1m
```

Cette opération prend du temsp aussi on peut utiliser la commande ctrl + t pour avoir des informations sur l'avancement du processus.
A la fin du processus une pop-up apparait qui ce qui nous informe de la fin du transfert et on peut simplement cliquer sur ignorer.

### Test de démarrage sur le nouveau support

## Installation Archlinux on hardrive

### Set the console keyboard ###

`$ localectl list-keymaps
$ loadkeys mac-fr`

### Update the system clock ###

`$ timedatectl`

### Partition the disk ###

To identify the disks
`$ fdisk -l`

Use cfidik to partition the disk sda

`$ cfidik /dev/sda`

### Format the disk ###

`$ mkfs.ext4 /dev/sda3
$ mkswap /dev/sda2
$ mkfs.fat -F 32 /dev/sda1`

### Mount the file systems ###

`$ mount /dev/sda3 /mount
$ mount --mkdir /dev/sda1 /mnt/boot
$ swapon /dev/sda2`

### Install essential packages ###

`$ pacstrap -K /mnt base linux linux-firmware intel-ucode linux-firmware neovim`

### Configure the system

Generate an fstab file
`$ genfstab -U /mnt >> /mnt/etc/fstab`

Change root in the new system:
`$ arch-chroot /mnt`

Set the time zone:
`$ ln -sf /usr/share/zoneinfo/Europe/Paris /etc/localtime`

Run hwclock to generate /etc/adjtime
`$ hwclock --systohc`

Localization, edit /etc/locale.gent and uncomment
> en_US.UTF-8 UTF-8

`$ locale-gen
$ echo "LANG=en_US.UTF-8" >> /etc/locale.conf
$ echo "KEYMAP=mac-fr" >> /etc/vconsole.conf`

Create the hostname file:
`$ "virtarch" >> /etc/hostname`

Configure the guest network
We are using systemd-networkd to configure the network (not NetworkManager)
Use networkctl list to list the devices on the system.

Next create a file for wired adaptater using DHCP
`$ nvim /etc/systemd/network/20-wired.network`
> [Match]
> Name=enpxs0
> [Network]
> DHCP=yes

Finish with editing > /etc/systemd/resolved.conf and add a valide DNS
> [Resolve]
> DNS=8.8.8.8

Next restart the two services :
`$ systemctl restart systemd-networkd.service
$ systemctl restart systemd-resolved.service`

Creating a new initramfs is usualy not required:
`$ mkinitcpio -P`

Change root password:

`$ passwd`

Choose and install the systemd boot loader:

Install efivar abd configure bootctl
`$ pacman -S efivar
$ bootctl install`

Configure boot loader:
The loader configuration is stored in the file /boot/loader/loader.conf

# TODO: need to edit /boot/loader/entries/arch.conf

Reboot the newly install system !

### Configuration wifi network

- Connect to the network: Wi-Fi—authenticate to the wireless network using iwctl.

`$ station wlan0 scan
$ station wlan0 getnetwork
$ station wlan0 connect "SSID"`

- Configure the network connection via DHCP provided by systemd-networkd and systemd-resolved

> I only configure systemd-resolved, #todo try to enable and start systemd-networkd

#### Wired Network ####

Need to edit the resolved.conf file for setting a DNS server. Choose 8.8.8.8 (Google's DNS)

`$ vim /etc/systemd/resolved.conf
$ systemctl restart systemd-resolved.service`

### Configure driver network Brcmfmac

#### Install networkManager

Packet allready installed but doesn't have unit starting. Fix it :

`$ sudo systemctl enable systemd-networkd`

#### Loading package

Try limit warning and error message
`$ paru -S crda`
[Error with country code](https://bbs.archlinux.org/viewtopic.php?id=198051&p=6)

> [!NOTE]
> doesn't work, delete package and dependencies.
>
#### Try to customize the firmware's wireless adaptater

Get errors when loading module brcmfmac for the wireless adaptater. Try to load the good firmware to fix these problem. Supect problem with the card when suspend the laptop.

`$ journalctl -p3 -b0`

To get information about the network card

`$ lspci -vnn -d 14e4:
$ sudo lshw -class network
$ sudo dmesg | grep brcm`

Next generate a firmware configuration file for my BCM43602 wireless adaptater with a hardware ID of 14e4:43ba
[describe here](https://gist.github.com/MikeRatcliffe/9614c16a8ea09731a9d5e91685bd8c80).

Install the package brcm43xx-firmware.

`$ paru -S brcm43xx-firmware`

> [!NOTE]
> doesn't work

### Configuration time synchronization

systemd-timesyncd — A simple SNTP daemon that only implements a client side, focusing only on querying time from one remote server. It should be more than appropriate for most installations. More information  here [here](https://wiki.archlinux.org/title/Systemd-timesyncd)

```bash
systemctl enable systemd-timesyncd.service
systemctl start systemd-timesyncd.service
```

## Fine tuning

### Installation ags

### Power managemnt

Need to chose one package LMT or TLP for power management.

#### Documentation

[Laptop Mode Tool](https://wiki.archlinux.org/title/Laptop_Mode_Tools)
[TLP](https://wiki.archlinux.org/title/TLP)

# todo

## Configure hibernation and battery event to stop the system when low battery

## Bugs opens

### Supend doesn't work

When typping suspend, get a instant restart.
Wifi seems to be crashed after this operation.
Command to reproduce :
`$ systemctl suspend`

Find a solution : need to desactive ACPI for LID0 before going to suspend.

`$ sudo sh -c "echo LID0 > /proc/acpi/wakeup"`

We can check that the LID0 envent is not anymore used to exit suspend mode :

`$ cat /proc/acpi/wakeup`

#### Make modification after reboot

We use the systemd-files service whose main purpose is to manage temporary files.
Add the following file /etc/tmpfiles.d/disable-lid0-wake.conf
>
`#    Path                  Mode UID  GID  Age Argument
w    /proc/acpi/wakeup     -    -    -    -   LID0
`

Next we must add a script in systemd to change the flag LID0 before and and after the suspend command.

# TODO find in the documentation the best way to change the flag LID0

### Stop system when battery is to low

# TODO find documentation about udev and battery state

Need to configure hibernation, next find a solution to detect battery state withe udev rules.

Documentation to read [Latop Issues >> Macbook Pro 2015](https://bbs.archlinux.org/viewtopic.php?pid=1630727#p1630727)
