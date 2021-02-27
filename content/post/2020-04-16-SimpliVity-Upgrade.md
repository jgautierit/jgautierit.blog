---
title: SimpliVity Upgrade - Walkthrough 
tags: VMware HPE HCI Simplivity
date: 2020-04-16 00:00:00 +0200
---

This new post is dedicated to the update / upgrade of SimpliVity Omnistack and its underlying stack (VMware, Firmwares).
The purpose of it is to show how the different update stages go along as well as the reflections to be carried out before taking action. 
{: .text-justify}

A complete update is divided into different stages:
1. Updating the arbiter.
2. Updating the SimpliVity "Web Client" plugin.
3. The Omnistack update.
4. The vSphere update (ESXi).
5. The server firmware update (SVTSP).

In this list, several steps are optional. Why? Because SimpliVity allows flexibility in terms of updates as long as the interoperability matrix is checked.
{: .text-justify}

Therefore, we could consider that we want to update the vSphere version (vCenter + ESXi) without updating Omnistack and vice-versa.
{: .text-justify}

One condition though, if Omnistack is updated, it must be done with the arbiter and the plugin for vSphere.
{: .text-justify}

If you do update/upgrade ESXi, do not forget to do the same on vCenter before performing them on ESXi (check the HPE interoperability to confirm the supported version of vCenter with Omnistack and the VMware interoperability matrix between ESXi/vCenter.)
{: .text-justify}

Disclaimer: This guide does not exempt anyone from consulting official documentation. This article is only there to potentially clarify different steps.
{: .notice--warning}

# Identify all versions

## Omnistack

First, we need to know what version we are running currently in order to understand :
- What version exists today ? Is there a new version ?
- What is going to be the upgrade path ? Am I allowed to update from the current version to the target version ?
- What documentation I am going to use for the upgrade ?
{: .text-justify}
There are several ways to find this version. The easiest is to click on your vCenter Menu > HPE SimpliVity Federation -> Connected Clusters -> Cluster version.
{: .text-justify}
![Omnistack version](/assets/images/clusterversion.jpg)

If you want to double check, you can SSH one of your OVC and run :
```bash
$ svt-version-show
```
![Omnistack Version](/assets/images/svtversionshow.jpg)

## vSphere

### ESXi

Once again, multiple ways to know the build it's currently running. The easiest is to go to your vCenter Inventory, click on one of your ESXi and in Summary the build will appear.
{: .text-justify}
Not sure what build belongs to what update ? Check [here](https://kb.vmware.com/s/article/2143832 "ESXi builds version")
{: .text-justify}
### vCenter

In your vCenter inventory, click on your vCenter and in Summary, the version and build will appear.
{: .text-justify}
## Firmware

If you're familiar with HPE servers updates, you probably know that you can patch your firmware with the HPE SPP.
For SimpliVity servers, it's almost the same thing. This is called SVTSP.
{: .text-justify}
As far as I know, there is no way to clearly identify the exact version that was previously applied on the server.
{: .text-justify}
It is therefore necessary to be logical in order to "match" the current version of your firmware as well as a possible SVTSP, by consulting for example:
* The ILO version
* The System ROM version
{: .text-justify}

![Firmware version](/assets/images/fwversion.jpg)

Once you collected this version, go to the interoperability document and do a small research with one of the version above.
{: .text-justify}
This should give you the SVTSP version that was applied previously and which version can be applied today.
{: .text-justify}
![Interoperability](/assets/images/interoperability.jpg)

## Arbiter & Web Client Plugin

Before 4.0.0, the arbiter and plugin version where totally different than the Omnistack version. It was very confusing for administrators.
{: .text-justify}
From 4.0.0, these are now matching the Omnistack version which means that if your Omnistack is in this version, the Web client and Arbiter version will be 4.0.0
{: .text-justify}
If you want to confirm the current version of your plugin click on your vCenter Menu > HPE SimpliVity Federation -> About.
{: .text-justify}
![Plugin Version](/assets/images/plugin.jpg)

If you want to confirm the current version of the arbiter, go to your control panel and check the installed software.
 {: .text-justify}
![Arbiter Version](/assets/images/arbiter2.jpg)

# Get the right documentation

The HPE document library is useful to find the documentation we need in this case. 
{: .text-justify}
Click [here](https://techlibrary.hpe.com/us/en/enterprise/integrated-systems/info-library/index.aspx?cat=hyper_converged_systems&subcat=svt&type=3 "HPE Document Library") to be redirected.

For me, these two PDF files are essentials :
1. HPE SimpliVity OmniStack X.X.X Interoperability Guide
2. HPE OmniStack X.X.X for vSphere Upgrade Guide
{: .text-justify}
The first document will be useful to cross check every version involved in the updates and verify the interoperability.
The second will guide you through every stages of the update. I recommend to check it for every new version.
{: .text-justify}
# Gather all updates 

To download the SVTSP necessary for the update, open the interoperability document and click on the SVTSP required. It will automatically redirect you to the download page.
{: .text-justify}
![SVTSP](/assets/images/svtspversion.jpg)

On the screenshot, you can notice that different versions of the SVTSP are available and compatible in this example with 4.0.0.
{: .text-justify}
To download the ESXi version, open the interoperability document and click on the HPE link within the Source section. 
{: .text-justify}
![SVTSP](/assets/images/esxiversion.jpg)

To download all the others (Upgrade Manager, Omnistack Update, Arbiter and Web Client plugin) follow the procedure located within the "Obtain the HPE OmniStack upgrade software" within the "HPE OmniStack for vSphere Upgrade Guide", they will be all in the same package.
{: .text-justify}
![Omnistack Download](/assets/images/downloadomnistack.jpg)

Before updating, please read carefully the interoperability guide to make sure that your upgrade path will be supported.
{: .text-justify}
Once everything is downloaded, open the zip file "Omnistack Software" and extract the :

- HPE-SimpliVity-Plug-in-for-vSphere-Client-XXXXXXXXXXX.run
- Simplivity-arbiter-XXXXX.msi
- Simplivity-upgrade-manager-XXXXXX.exe
- SimpliVity-OmniStack-Software-revA-x.x.x.tar
{: .text-justify}
At this point, you should have all the listed above as well as the SVTSP .iso and the ESXi Offline bundle.
{: .text-justify}
# Update

You have now confirmed that all targets versions are compatible with each other and also the update path. We need a last check.
Make sure that all your VMs are HA Compliant! There is different ways to do this depending on the version of Omnistack you run. 
{: .text-justify}

This way is available on all versions :

```bash
$  svt-vm-show
```

![HA Compliant](/assets/images/HAcompliant.jpg)

## Arbiter

For the arbiter, it's very simple, upload the .msi to your windows machine, execute it and next, next, next... It will upgrade the version.
Do not uninstall the current arbiter. This is advised in the documentation.
{: .text-justify}
## Webclient Plugin

For this procedure you need to uninstall, install the plugin as there is no update. 

1. Upload the bundle to the /tmp of your vCenter Appliance with WinSCP for instance (if you are getting an error at this stage, follow this [link](https://kb.vmware.com/s/article/2107727 "KB2107727").
2. SSH the vCenter and execute the following commands :

```bash
Command> shell
# cd /opt/Hewlett \Packard \Enterprise/HPESimpliVityPluginForVSphereClient-XXXX  (replace XXX by the current version or just press tab)
# ./uninstall
```
Confirm by pressing the letter Y and then Enter.

The uninstall is complete, we need to install the new plugin.
```bash
# cd /tmp
# chmod +x HPE-SimpliVity-Plug-in-for-vSphere-Client-linux-XXXX.run (replace XXX by the current version or just press tab)
# ./HPE-SimpliVity-Plug-in-for-vSphere-Client-linux-XXXX.run
```
Press enter until it goes through the License Agreement, accept it by pressing the letter y and confirm the default installation directory by pressing enter.
{: .text-justify}
Then enter your SSO username (administrator@vsphere.local) and password as well as an unnecessary email address and finally press Y to continue and install.
{: .text-justify}
At the end, it will ask you to restart the ui service, press Y.

![Plugin Install](/assets/images/plugininstall.jpg)

## Upgrade manager for all the others.

All the manual updates are now completed. We can now use the software provided by HPE to install the Firmware, Omnistack and ESXi updates.
{: .text-justify}
Before executing the upgrade manager, make sure that the update files (.iso and .zip) are in the same directory as the upgrade manager. HPE was probably too lazy to implement a Browse button.
{: .text-justify}
Once everything is in the same folder, launch the update manager. It will require the FQDN of your vCenter as well as the credentials.
{: .text-justify}
Select in the drop down menu each updates you want to perform, if you select the firmware upgrade it will also ask you for the ILO details.
{: .text-justify}

![Select](/assets/images/select.jpg)

The update manager will first run a series of pre-checks and then start the upgrade node after node. If you do have vSphere DRS, SimpliVity will use it to move your workloads off one host to another.
{: .text-justify}

![Prechecks](/assets/images/precheck.jpg)

At the end, you will need to commit the Omnistack update in order to apply the new version.
{: .text-justify}
![Commit](/assets/images/commit.jpg)


You can find a video describing the steps [here](https://hpesimplivityvideos.brightcovegallery.com/detail/videos/install-upgrade/video/6117232112001/hpe-simplivity-lifecycle-management-using-simplivity-upgrade-manager?autoStart=true "Update manager")

## Troubleshooting

### SVTSP

If for any reasons the firmware update is failing through the Update Manager, just boot the server on the SVTSP iso through ILO.
{: .text-justify}
### Omnistack

For Omnistack, please consult the svt-upgrade.log inside the OVC and try to search for the error message.
{: .text-justify}
# Conclusion

I hope this article has been useful to clarify the update process for SimpliVity. 

HPE has now released a series of videos recording the upgrade process. These videos are available [here](https://hpesimplivityvideos.brightcovegallery.com/ "Videos") and cover most of the topics described above.
{: .text-justify}