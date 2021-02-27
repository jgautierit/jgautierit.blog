+++
author = "Jonathan Gautier"
title = "vSphere 7 Lifecycle Manager - Can't Sync the HCL"
date = "2020-06-23"
description = "XXX"
+++

After upgrading vCenter 6.7 to 7.0 for one of my customer, I had a strange issue where I could not Sync the HCL with the Lifecycle Manager in vCenter 7.0

![LCM sync HCL](/assets/images/synchcl.jpg)

Im ny situation, the task was stuck at 10% and failing afterwards with the error "A general system error occcured..."

![Error HCL](/assets/images/errorlcm.jpg)

Looking at the logs (/var/log/vmware/vmware-updatemgr/vum-server/vmware-vum-server.log), it was really difficult to find something relevant but it looks like the download was failing. 

```bash
20-06-23T10:09:55.200Z error vmware-vum-server[28017] [Originator@6876 sub=com.vmware.vcIntegrity.hcl.UpdateHclDatastoreTask] Task:com.vmware.vcIntegrity.hcl.UpdateHclDatastoreTask ID:52b92e6b-1b4c-1584-c1ad-da57f7571f88. Task Failed. Error: Error:
-->    com.vmware.vapi.std.errors.error
--> Messages:
-->    com.vmware.vcIntegrity.hcl.vvs.unreachable.summary
-->    com.vmware.vcIntegrity.hcl.vvs.unreachable.resolution
-->
```

Looking at the tasks, I found out that VMware use multile python scripts in the following directory   /usr/lib/vmware-updatemgr/config/

Thanks to my buddy Y.Ilmi for guiding me to the specific script called vvs-config.json.

This script provides the URL that VMware use to download the hcl : vvs.esp.vmware.com

![vvs-config](/assets/images/vvsconfigjson.jpg)

vCenter was unable to reach the URL (use curl to test it) I asked the security team to grant access.

Problem solved!

![HCL](/assets/images/HCL2.jpg)

![HCL](/assets/images/hcl3.jpg)