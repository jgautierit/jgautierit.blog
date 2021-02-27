---
title: Update Manager 6.7 fails to remediate - Check your ports !
tags: VMware Upgrade vCenter UpdateManager
date: 2018-09-20 00:00:00 +0200
---
This quick thread will relate issues regarding the remediation of my upgrade to 6.7.
Working at a customer site and upgrading the hosts to 6.7, Update Manager was failing to remediate my ESXi upgrade with the following error :
{: .text-justify}
![Remediate Error](/assets/images/vumerror.png)

What a nice error message ...Very Helpful!
 
No more indication was provided by the vCenter tasks & events so I had to look at the logs.
{: .text-justify}
In vCenter Server Appliance, the Update manager logs are located here :
{: .text-justify}
```bash
/var/log/vmware/vmware-updatemgr/vum-server
```

Looking at the last "vmware-vum-server.log", I quickly matched an error with the last try :
{: .text-justify}
```
2018-09-20T11:31:14.436Z warning vmware-vum-server[06197] [Originator@6876 sub=Default] Failed to connect socket; <io_obj p:0x00007fb0840161f0, h:20, <TCP 'X.X.X.X : 42026'>, <TCP 'Y.Y.Y.Y : 80'>>, e: 111(Connection refused)
```

This error indicates that the IP X.X.X.X tries to communicate through the port 80 on the IP address Y.Y.Y.Y. X being my vCenter and Y my ESXi host.
{: .text-justify}
In order to prove that the error was the consequence of a blocked port, I used this the following command to test the ports :
{: .text-justify}
```bash
# curl -v telnet://Y.Y.Y.Y:80
```

Guess the result ?

```
* Rebuilt URL to: telnet://Y.Y.Y.Y:80/
*   Trying Y.Y.Y.Y...
* TCP_NODELAY set
* connect to Y.Y.Y.Y port 80 failed: Connection refused
* Failed to connect to Y.Y.Y.Y port 80: Connection refused
* Closing connection 0
curl: (7) Failed to connect to Y.Y.Y.Y port 80: Connection refused
```

We then tested a known working port such as 443 :

```
# curl -v telnet://Y.Y.Y.Y:443
* Rebuilt URL to: telnet://Y.Y.Y.Y:443
*   Trying Y.Y.Y.Y...
* TCP_NODELAY set
* Connected to Y.Y.Y.Y (Y.Y.Y.Y) port 443 (#0)
```

It become then obvious that the port was blocked by some firewall.
{: .text-justify}
We asked kindly the colleagues to open the port. Required ports for update manager are listed [here](https://kb.vmware.com/s/article/1004543 "KB1004543"): 
{: .text-justify}
Problem Solved!
{: .notice--success}