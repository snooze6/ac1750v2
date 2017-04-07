# AC1750 v2

## Introduction

So I bought this router from Amazon because is full-compatible with OpenWRT. You know that is v2 because of the sticker of the router, it says something like:
```
ver: 2.0
```
On the top-right corner of the sticker.

So the first thing I did was update the firmware.

__Please do not update the firmware__

![fuck.png](fuck.png)

It says in the official page that it's locked, it won't let you flash OpenWRT or any custom firmware, so what should we do now?

## Escaping the matrix I

The upgrading page of the router says error 18005 in every firmware that I tried to flash but there's another flashing methoud using tftp, so let's try it!.

![1.jpg](1.jpg)

First of all we must set our IP to 192.168.0.66, that's the IP in which the router will search for the firmware.

Then we can open a tftp server on our pc using [this link](http://tftpd32.jounin.net/tftpd32_download.html) for instance but you can use whatever you want.

We set the firmware name to:
```
ArcherC7v2_tp_recovery.bin
```

And we can try to flash it shutting off the router and starting it while pressing the WPS/reset button. It'll search for an tftp server and will found ours so it'll download the image and flash it.

![2.jpg](2.jpg)

## Escaping the matrix II

Now that we are in a previous version of the firmware which it's not locked we can flash OpenWRT as always, using the web interface.

## Upgrading the matrix

Now we'll run out of space pretty soon because of the limited memory of the router, the answer might be using a external usb as storage to gain more free space.

```bash
opkg install kmod-usb-storage kmod-scsi-core kmod-fs-msdos kmod-fs-vfat block-mount usbutils kmod-usb-core kmod-usb2 file kmod-nls-iso8859-1 kmod-nls-cp437 kmod-fs-ext4
```
With the previous packages we can see now /dev/sda1 and mount it

```bash
mkdir /mnt/sda1
mount /dev/sda1 /mnt/sda1

df
Filesystem           1K-blocks      Used Available Use% Mounted on
rootfs                   12544      4956      7588  40% /
/dev/root                 2560      2560         0 100% /rom
tmpfs                    63020       332     62688   1% /tmp
/dev/mtdblock3           12544      4956      7588  40% /overlay
overlayfs:/overlay       12544      4956      7588  40% /
tmpfs                      512         0       512   0% /dev
/dev/sda1             15318416     38144  14482424   0% /mnt/sda1
```

Checking the mount command

```bash
# Prepare external storage root
mount /dev/sda1 /mnt ; tar -C /overlay -cvf - . | tar -C /mnt -xf - ; umount /mnt

# Create fstab
block detect > /etc/config/fstab; \
   sed -i s/option$'\t'enabled$'\t'\'0\'/option$'\t'enabled$'\t'\'1\'/ /etc/config/fstab; \
   sed -i s#/mnt/sda1#/overlay# /etc/config/fstab; \
   cat /etc/config/fstab;

reboot
```

![3.PNG](3.PNG)

## Anexes:

* Non locked firmware
* OpenWRT

## Interesting links

* [Error 18005 discussion](http://www.dd-wrt.com/phpBB2/viewtopic.php?t=287073&postdays=0&postorder=asc&start=30)
* [Wiki OpenWRT 1750](https://wiki.openwrt.org/toh/tp-link/tl-wdr7500)
