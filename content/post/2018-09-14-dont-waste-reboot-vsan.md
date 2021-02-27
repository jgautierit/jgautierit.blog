---
title: Don't waste reboots in vSAN!
tags: vSAN VMware
date: 2018-09-14 17:00:00 +0200
---
This topic will focus on vSAN and more particularly to situations where a host in a vSAN cluster becomes "not responding".
{: .text-justify}
Many reasons can lead to a host becoming not responding and often a call to GSS becomes essential.
If the host is not responding, a reboot can become required as the last step and most of the time a root cause analysis will be expected.
{: .text-justify}
Usually, the regular ESXi logs are not enough to diagnose the issue.

## Why a NMI?

The NMI or Non Maskable Interrupt allows to generate the famous "Purple Screen Of Death" on the ESXi. As a consequence, a dump will be generated and will allow the analysis by VMware of the dump taken previously in order to establish a root cause.
{: .text-justify}
![NMI PSOD](/assets/images/PSOD.jpeg)

Warning : A NMI should be the last step. Do not send the NMI if no troubleshooting steps was tried.
{: .notice--warning}

## How to generate a NMI?

Every vendor should have the option in their management interface like ILO or IDRAC. Please refer to their documentation.
{: .text-justify}

## How to grab it in logs ?

When you gather the logs through ESXi via vm-support, an automatic script is going to grab the dump and put it in the vm-support tgz. As the dump can be quite heavy, please make sure that you have enough space in the directory where you will generate the support bundle.
{: .text-justify}
Last comment but not the least, the host must have a diagnostic partition configured otherwise no dump will be collecting leading to a wasted reboot.
{: .text-justify}
