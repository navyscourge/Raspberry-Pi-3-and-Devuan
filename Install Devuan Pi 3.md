# Installing Devuan on a Pi 3

Devuan is a Debian based Linux OS without systemd. It can be downloaded as an image for several configurations from [the Devuan website](https://devuan.org/). These instructions are for the ARM 64-bit system for the Raspberry Pi model 3; the downloaded file will not run on other models. The installation is done using a Linux system, such as the Raspbian system on a Raspberry Pi.

The current Devuan image is Jessie 1.0.0, released on 2017-05-25, as described on that website.

## Requirements

The following are required to carry out these instructions:

* A working Raspberry Pi 3 to run the Devuan system
* A blank microSD card on which to put the Devuan system
* A microSD card adaptor (usually with a USB connection)
* A WIRED network connection to the Devuan system Pi (the WiFi connection will need to be configured)
* A working Linux system to copy the Devuan system to the microSD card (this can be the same Raspberry Pi 3 as above)

## Download and check

Download the image you want, preferably using one of the mirrors provided. This is 175MB. Also download the shasums and the signature files.

```
pi@hostname ~ $ curl -O https://mirror.leaseweb.com/devuan/devuan_jessie/embedded/devuan_jessie_1.0.0_arm64_raspi3.img.xz
pi@hostname ~ $ curl -O https://mirror.leaseweb.com/devuan/devuan_jessie/embedded/SHA256SUMS
pi@hostname ~ $ curl -O https://mirror.leaseweb.com/devuan/devuan_jessie/embedded/SHA256SUMS.asc
```

Now verify the files that you have downloaded.

```
pi@hostname ~ $ gpg --verify SHA256SUMS.asc && sha256sum -c SHA256SUMS
```

EXPLAIN !!

## Install (preparation)

You will need a microSD card, size at least 2GB although this may not allow very much modification due to the limited space. You will need to connect this to the machine that has the downloaded files using a USB adaptor. There are several options:

* Run the following command, before and after inserting the card in a terminal window. The difference will be the new device.

```
pi@hostname ~ $ df -h
(details)
```

* If using a GUI system, open the file manager before inserting the card. When inserted, a message should be given about the new device.

* There are also several disk image writers that can be used.

The Raspberry Pi Foundation has instructions that may also be followed [on their website](https://www.raspberrypi.org/documentation/installation/installing-images/linux.md)

## Install (write image)

It is not correct to copy (or cut and paste) the downloaded file to the SD card, nor should the downloaded file be used without being decompressed. The method chosen may include a decompession step. Writing to the SD card will erase all existing data on the card, so make sure to use the correct device.

* Run the following command, combining the decompress and write in one line:

```
pi@hostname ~ $ xzcat devuan_jessie_1.0.0_arm64__raspi3.img.xz | sudo dd of=/dev/sda bs=2M
```

* Run the following commands, keeping the decompress and write on separate lines:

```
pi@hostname ~ $ tar -xf devuan_jessie_1.0.0_arm64__raspi3.img.xz
pi@hostname ~ $ sudo dd if=devuan_jessie_1.0.0_arm64__raspi3.img of=/dev/sda bs=2M
```

* There are also several disk image writers that can be used.

Once complete, eject or unmount the SD card using the GUI file manager or the linux command umount, and physically remove the card from the adapter.

## Verification

If not turned off, shutdown the Raspberry Pi 3 that is to run the Devuan system. Insert the SD card into the PI and turn on the power. When the login prompt appears, login with user root and password toor. The system does not have a desktop system installed, so the following commands are done using the command line.

If the Pi does not boot to the login prompt, check that you have copied the image correctly.

EXPLAIN HOW

Run the following command at the prompt to check the card space:

```
pi@hostname ~ $ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root       1.8G  572M  1.1G  34% /
devtmpfs        422M     0  422M   0% /dev
tmpfs            86M  212K   85M   1% /run
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           171M     0  171K   0% /run/shm
/dev/mmcblk0p1   61M   30M   32M  49% /boot
```

The image supplied is small, with only 1.1 GB free. This may be too small to be able to install the additional software that you want.

## First steps

The Pi should now be running the Devuan system if the Verification step was successful. What you do next will depend on what you want to use the Pi for. These are the suggested steps you might take.

### Expand the system

Shutdown the Pi and remove the SDcard; the filesystem cannot be resized if it is mounted. The other way is to create a script that runs at start up then reboot, which runs that script (as done by the Raspberry Pi Foundation).

Put the SDcard in another system, such as the original system that you used to create the Devuan card. Run the 'df -h' command as in the Install (preparation) step above to identify the correct device name for that card.

Use a terminal window, and enter the following commands:

```
pi@hostname ~ $ sudo fdisk /dev/sda

Welcome to fdisk (util-linux 2.25.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Command (m for help): p
Disk /dev/mmcblk0: 29.7 GiB, 31914983424 bytes, 62333952 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x12345678

Device         Boot  Start     End Sectors  Size Id Type
/dev/mmcblk0p1           1  125000  125000   61M  c W95 FAT32 (LBA)
/dev/mmcblk0p2      125001 3870815 3745815  1.8G 83 Linux

Command (m for help): d
Partition number (1,2, default 2): 2

Partition 2 has been deleted.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2): 2
First sector (125001-62333951, default 126976): 125000
Last sector, +sectors or +size{K,M,G,T,P} (125001-62333951, default 62333951):

Created a new partition 2 of type 'Linux' and of size 29.7 GiB.

Command (m for help): p
Disk /dev/mmcblk0: 29.7 GiB, 31914983424 bytes, 62333952 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x12345678

Device         Boot  Start      End  Sectors  Size Id Type
/dev/mmcblk0p1           1   125000   125000   61M  c W95 FAT32 (LBA)
/dev/mmcblk0p2      125001 62333951 62333951 29.7G 83 Linux

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Re-reading the partition table failed.: Device or resource busy

The kernel still uses the old table. The new table will be used at the next rebo
ot or after you run partprobe(8) or kpartx(8).

pi@hostname ~ $ sudo resize2fs /dev/sdc2
resize2fs 1.43.3 (04-Sep-2016)
Filesystem at /dev/sdc2 is mounted on /media/pi/abc123...; on-line resizing
required
old_desc_blocks = 1, new_desc_blocks = 4
The filesystem on /dev/sdc2 is now 7776118 (4k) blocks long.

pi@hostname ~ $ df -h
(details)

```

