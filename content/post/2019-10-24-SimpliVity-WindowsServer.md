---
title: Windows Server 2019 optimized for SimpliVity
tags: VMware HPE Simplivity HCI
date: 2019-10-24 00:00:00 +0200
---

I’ve recently started to implement HPE SimpliVity clusters. 
As per the documentation, SimpliVity uses 8k blocks while Windows Server uses 4k by default. 
HPE recommend using this block size for VMs, especially if you use file servers. In order to install a brand new Windows server, a workaround has to be implemented if you wish to use this specific block size on the OS disk. 
At the beginning, I was following this useful blog [article](https://www.v-wave.ch/en/2016/05/18/create-simplivity-optimized-windows-server/ "8kdiskOS") where it is explained how to change the block size when installing Windows servers. 
Things changed a bit since the creation of this article so I will describe the procedure below.
{: .text-justify}
## Create a VM with a new disk.
Well, the first step is obvious : create the VM. For this demonstration, I will use Windows Server 2019 so I am going to create a VM with a brand new disk of 40GB and a VMware Paravirtual controller.
{: .text-justify}
## Start the VM installation
The next step is to boot the VM on the Windows ISO. 

Do not forget to include the Windows ISO of the VMware tools in order to detect your disk (using the paravirtual driver).
{: .notice--warning}
![Tools](/assets/images/tools.jpg)

When the driver is installed and the disk is detected, please proceed and select "New".

![New](/assets/images/newdisk.jpg)

Microsoft will create 4 partitions necessary for the Windows Server's installation.
{: .text-justify}
Identify the partition that we are going to use to install the system (the biggest) and press with your keyboard Shift + F10.

![Prompt](/assets/images/prompt.jpg)

## Format to NTFS with 8k block size
Enter the following commands :
```bash
X:\Sources>diskpart
DISKPART>list disk
DISKPART>select disk 0
DISKPART>select partition 4
DISKPART>select volume 3
DISKPART>format quick unit=8192
DISKPART>exit
DISKPART>exit
```
Click Refresh and Next.

![Refresh](/assets/images/refresh.jpg)

As soon as your Windows installation is completed, you can verify the NTFS block size with the following command :
```bash
DISKPART>filesystems
```

![NTFS](/assets/images/ntfs8k.jpg)