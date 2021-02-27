---
title: Omnistack "Virtual machine memory usage" alarm, a workaround.
tags: VMware HPE HCI Simplivity
date: 2019-10-25 00:00:00 +0200
---
If you have been running SimpliVity in your data centre, you probably came across these alarms. SimpliVity presents many advantages but being a non-native solution can have some little annoyance. One of them being the Virtual machine memory usage being triggered continuously on the Omnistack VMs. These alarms are “normal”. This is simply because these OVC uses memory reservations. 
{: .text-justify}

![Memory](/assets/images/memory.jpg)

By default, in vCenter, a critical alarm is being triggered when the VM memory usage is above 95% for 10 minutes. One of the workarounds I found [here](https://virtualknightz.com/2018/04/05/how-can-i-disable-high-memory-alarm-for-a-specific-vms/ "OVC Alarms") is to disable the alarm at the vCenter level, and then enable it for every VM folder but (unless I am wrong) it looks like not possible any more in the latest vCenter version as you cannot enable the alarm at the vCenter level and disable it for some folders either.  
{: .text-justify}

![Alarm](/assets/images/Alarm.jpg)

The other workaround I found is the following.

1 - Disable the Alarm at the vCenter level.

![Disable](/assets/images/disable.jpg)

Disabling the alarm will disable it for every VMs attached to your vCenter, do not forget to enable it correctly following the next steps.
{: .notice--warning}

2 - Create a VM folder for your Omnistack VMs.

3 - Move your Omnistack VMs to the folder.

4 - Create one or many VMs folders for your production VMs (these VMs will be monitored).

5 - Re-create the alarm with the following settings.

![New Alarm](/assets/images/newalarm.jpg)

![New Alarm 1](/assets/images/newalarm1.jpg)

![New Alarm 2](/assets/images/newalarm2.jpg)

![New Alarm 3](/assets/images/newalarm3.jpg)

6 - Move your production VMs to the folders you just created.

Be careful to do not forget to recreate the alarm for every folder containing the VMs you want to monitor.
Do not forget to include the VM in a folder when you create a new one either.
{: .notice--warning}

Otherwise you can just create a global VM folder and put different folders inside.
{: .notice--info}

![Hierarchy](/assets/images/hierarchy.jpg)