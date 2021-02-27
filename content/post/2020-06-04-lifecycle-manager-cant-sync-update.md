---
title: vSphere 7 Lifecycle Manager - Can't Sync Updates 
tags: VMware vCenter
date: 2020-06-04 00:00:00 +0200
---

After upgrading vCenter 6.7 to 7.0, I had a strange issue where I could not Sync Updates anymore within the Lifecycle Manager in vCenter 7.0

![LCM sync](/assets/images/lcmsync.jpg)

The error message was the following : A general system occured: An error occured while downloading depot metadata from file:///storage/updatemgr/patch-store/hostupdate/vmw/vmw-ESXi-5.0.0-metadata.zip

![LCM error](/assets/images/lcmerror.jpg)

From the error, it looks like it's trying to download the metadata file from the 5.0 zip file which should not be really used anymore since vCenter 7.0 is not compatible anymore with any hosts < 6.5.

I went to look at the directory (/storage/updatemgr/patch-store/hostupdate/vmw/) within vCenter and indeed the file was missing. In order to see if I was really missing a file here, I went to look in a freshly deployed vCenter and it seems like there is no file named this way either.

I tried to compare the xml files and see if anything was missing or corrupted but everything seemed to be fine.

At some point, I decided to reset completely Update Manager but I was not sure how. 

I found this VMware Knowledge Base article [here](https://kb.vmware.com/s/article/2147284 "VMware KB2147284") which describe how to reset the database.

After executing the following steps my issue was fixed and update manager was not complaining about the file anymore (even though it's still does not exist...)

```bash
# service-control --start vmware-updatemgr
# /usr/lib/vmware-updatemgr/bin/updatemgr-utility.py reset-db
# rm -rf /storage/updatemgr/patch-store/*
# service-control --start vmware-updatemgr
```

I hope this has been useful for you and I'd like to thank you for reading!

