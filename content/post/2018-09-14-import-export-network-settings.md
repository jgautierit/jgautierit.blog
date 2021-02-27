---
title: Import/Export Network Settings on Windows
date: 2018-09-13 15:00:00 +0200
tags: network E1000 VMXNET3 VMware Windows
---

It is often required to change the network type of a Virtual NIC within a vSphere environnement. One of the most annoying things is to take note of the configuration in order to do not mess up the network settings.
{: .text-justify}
One particular scenario would be when you replace a e1000 by a vmxnet3.
{: .text-justify}

### netsh

Windows has a handy utility called netsh. This utility allows to import/export the network configuration of a NIC easily. 
{: .text-justify}

To export :
```bash
# netsh interface dump > c:\network.txt
```
To import : 
```bash
# netsh -c interface -f c:\network
```

