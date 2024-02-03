# NUC Media Server

Docker compose file to quickly set up my media server after a fresh install

## Media Server Specs

- Intel NUC i3 10th gen, model number [NUC10FNH](https://www.intel.com/content/dam/support/us/en/documents/intel-nuc/NUC10i357FN_TechProdSpec.pdf).
- 16Gb DDR4 Ram
- 256Gb M.2 SSD (system drive)
- 4Tb 2.5" SATA SSD (media drive)
- [Arch Linux](https://www.archlinux.org) Server install with Docker & SSH

## Getting Ready to Install

- Disable WIFI and Bluetooth in the NUC's BIOS. Not needed for a media server
- Download the latest [Arch Linux](https://www.archlinux.org/download/) ISO file
- Write the Arch ISO to a USB flash drive as GPT so it will boot into a UEFI system
- Boot the NUC using the USB drive ready to install Arch

## Installation

### Internet
The boot process should have detected the ethernet connection and assigned an IP address from your router.
To check:

```
ip a
```
You should see an ip address (e.g. 192.168.1.217). Ping a website to check if your internet connection is working ok.

```
ping -c 3 archlinux.org
```

### Partition the Hard Drives

Use 'lsblk' to see what hard drives are named. The M.2 drive will likely be 'nvme0n1', the SATA SSD drive will be 'sda'.

Use cfdisk to set up your M.2 drive as the system drive.

```
cfdisk /dev/nvme0n1
```

Create a boot and root partitions. The Arch install will create a swap file later in the installation.

```
nvme0n1p1    /boot    512M    EFI partition
nvme0n1p2    /root    Rest of space left for the operating system (Linux file system)
```
Write you changes and exit cfdisk

Use cfdisk again to set up your SATA drive ready to be a media drive.

```
cfdisk /dev/sda
```

Create one Linux file system.

```
sda2    /home    Use all of the space available on the drive
```
Write you changes and exit cfdisk

### Format the partitions

```
mkfs.fat -F32 /dev/nvme0n1p1

mkfs.ext4 /dev/nvme0n1p2

mkfs.ext4 /dev/sda1
```

### Mount the file systems

Mount your root partition first:

```
mount /dev/nvme0n1p2 /mnt
mkdir /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot
mkdir /mnt/home
mount /dev/sda1 /mnt/home
```

Check that all of the partitions are mounted correctly

```
lsblk
```
Make sure /mnt /mnt/boot & /mnt/home are there and assigned to the correct partition.



