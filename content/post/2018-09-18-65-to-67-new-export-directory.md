+++
author = "Jonathan Gautier"
title = "New Export Directory - Upgrade from 6.5 to 6.7"
date = "2018-09-18"
description = "How to create an export directory when upgrading vCenter."
tags = [
    "vCenter",
    "VMware",
    "Upgrade",
]
+++

This post is dedicated to the "Stage 2" of the upgrade process from vCenter 6.5 to vCenter 6.7. I recently came across a scenario where the configuration exceeded the size of the root partition within the source vCenter appliance.
{: .text-justify}
In this case, a new export has to be provided to the upgrade utility in order to go forward.

![Export](/assets/images/vcconfig.jpg)


This process is also applicable to previous versions. The [KB](https://kb.vmware.com/s/article/2113947 "KB2113947") defines the process that I will describe below, however it has been created for vCenter 6.0.
{: .text-justify}
Usually, these steps are not required. Especially when the data (configuration and historical data) are not big enough (2-3GB), but this time I had 21GB to migrate.
{: .text-justify}
Of course, I could have applied a workaround and cleaned up a bit the events/tasks table but I wanted to export everything.
{: .text-justify}
The target of this blog article is not to copy/paste the [Knowledge Base article](https://kb.vmware.com/s/article/2113947 "KB2113947") but to demonstrate that it is still valid for 6.5. Also, I hope it will bring clarity to the administrators. 
{: .text-justify}
Make sure that you have a valid backup ready to restore and take a snapshot before proceeding!
{: .notice--warning}

## How to create the export?

If you don't read carefully (like I did the first time..) you will probably waste minutes of your precious time.
{: .text-justify}
The message on the screenshot indicates that the root partition on the **SOURCE** vCenter does not have enough space and requires an export directory, do not be confused with the **DESTINATION**.
{: .text-justify}

I believe that I could have increased the size of the root partition. However, if you take a screenshot, you won't be able to resize the primary disk. Please also note that by the [VMware article](https://kb.vmware.com/s/article/2145603 "KB2145603"), it is not supported.Don't worry too much though,it would only be temporary as the source appliance is meant to be shutdown.
{: .text-justify}
{:.notice--info}

## Create a new disk.

The first step is to create the new disk on the source VM (original vCenter). If you don't want to spend too much time on the recognition of that disk afterwards, I recommend to use a size that is not currently being used by any of the disks of the vCenter Appliance.Also, use enough capacity to store the data needed as provided by the screenshot.
{: .text-justify}

Once the disk has been created, the VMware KB states that you need to rescan the storage by running :
{: .text-justify}

```bash
# echo "- - -" > /sys/class/scsi_host/host0/scan
```

Unfortunately, this did not work as expected for me, I had to restart the whole appliance.
{: .text-justify}

After the reboot, list the disks by using :

```bash
# fdisk -l
```

## Identify

At this point, you will be able to identify the disk previously created (in my situation, I created a disk of 120GB) :
{: .text-justify}

```bash
Disk /dev/sdm: 120 GiB, 128849018880 bytes, 251658240 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

Once that identification has been done, proceed by creating a partition and its file system :
{: .text-justify}

```

# fdisk -u /dev/sdm

Welcome to fdisk (util-linux 2.27.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.
Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xeba5fea2.
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-251658239, default 2048): “Enter”
Last sector, +sectors or +size{K,M,G,T,P} (2048-251658239, default 251658239): “Enter”
Created a new partition 1 of type 'Linux' and of size 120 GiB.
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

# mkfs -t ext3 -j /dev/sdm
mke2fs 1.42.13 (17-May-2015)
Found a dos partition table in /dev/sdm
Proceed anyway? (y,n) y
Creating filesystem with 31457280 4k blocks and 7864320 inodes
Filesystem UUID: 68d869a7-f1c2-45e3-a3d8-355681905610
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done

# mkdir /storage/tmp2
# mount -t ext3 /dev/sdm /storage/tmp2
```

Don't forget to replace the device name (sdm) by the one you identified with fdisk previously.
{: .text-justify}

## Provide the new export to the upgrade utility

The last step is the easiest, just provide the newly created export to the utility as per the screenshot below :
{: .text-justify}

![Export](/assets/images/export.jpg)

Job Done!
{: .notice--success}















