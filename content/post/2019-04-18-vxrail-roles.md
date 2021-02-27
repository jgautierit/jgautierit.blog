---
title: VxRAIL Roles Script
tags: VMware DELL vSAN VxRAIL
date: 2019-04-18 00:00:00 +0200
---

In this article, I will provide an easy script to create the required roles when you use an external vCenter in VxRAIL.
{: .text-justify}
N.B : This script works for VxRail 4.7.x. This can change depending on the features/requirement that VxRAIL will implement in the future.
{: .text-justify}
{: .notice--warning}

N.B : This script is valid as of 11/01/2021. Updated by my colleague Steve Delassus.
{: .text-justify}
{: .notice--warning}

```bash
# This is a script to create the required roles for VxRAIL users when an external vCenter is required.
# Made by Jonathan Gautier on the 08/04/2019
# Review by Steve Delassus on the 11/01/2021 based on the latest solve procedure 

Write-Host "This is a PowerCLI Script to create the required roles for VxRAIL" -ForeGroundColor Cyan 
Connect-VIServer "Your vCenter FQDN or IP "

#VxRail Initial Gobal Role Name
$vxrailrole1 = "VxRail Initial Global"

#VxRail DC Gobal Role Name
$vxrailrole2 = "VxRail Datacenter Global"

$initglobal = @(
'System.Anonymous'
'System.View'
'System.Read'
'Global.ManageCustomFields'
'Global.SetCustomField'
'Global.LogEvent'
'Global.Licenses'
'Global.Diagnostics'
'Global.Settings'
'Folder.Create'
'Folder.Delete'
'Datastore.Browse'
'Datastore.DeleteFile'
'Datastore.FileManagement'
'Datastore.AllocateSpace'
'Datastore.UpdateVirtualMachineFiles'
'Network.Assign'
'DVSwitch.Modify'
'DVSwitch.HostOp'
'DVSwitch.ResourceManagement'
'DVPortgroup.Modify'
'Host.Inventory.AddHostToCluster'
'Host.Inventory.RemoveHostFromCluster'
'Host.Inventory.EditCluster'
'Host.Config.SystemManagement'
'Host.Config.Maintenance'
'Host.Config.Storage'
'Host.Config.NetService'
'Host.Config.Network'
'Host.Config.Settings'
'Host.Config.Power'
'VirtualMachine.Inventory.Delete'
'VirtualMachine.Inventory.Unregister'
'VirtualMachine.Interact.PowerOn'
'VirtualMachine.Interact.PowerOff'
'VirtualMachine.Interact.ConsoleInteract'
'VirtualMachine.Interact.DeviceConnection'
'VirtualMachine.Interact.SetCDMedia'
'VirtualMachine.Interact.GuestControl'
'VirtualMachine.GuestOperations.Query'
'VirtualMachine.GuestOperations.Modify'
'VirtualMachine.GuestOperations.Execute'
'VirtualMachine.GuestOperations.QueryAliases'
'VirtualMachine.GuestOperations.ModifyAliases'
'VirtualMachine.State.CreateSnapshot'
'VirtualMachine.State.RemoveSnapshot'
'Resource.HotMigrate'
'Resource.ColdMigrate'
'Alarm.Create'
'Alarm.Delete'
'Alarm.SetStatus'
'Authorization.ModifyRoles'
'Authorization.ReassignRolePermissions'
'Authorization.ModifyPermissions'
'Extension.Register'
'VApp.ApplicationConfig'
'VApp.Import'
'VApp.ExtractOvfEnvironment'
'Cryptographer.Access'
'Cryptographer.Decrypt'
'Cryptographer.Migrate'
'Authorization.ModifyPrivileges'
'StorageProfile.Update'
'StorageProfile.View'
)

$dcglobal = @(
'System.Anonymous'
'System.View'
'System.Read'
'Global.ManageCustomFields'
'Global.SetCustomField'
'Global.Settings'
'Folder.Create'
'Folder.Delete'
'Datastore.Rename'
'Network.Assign'
'DVSwitch.Create'
'DVSwitch.Modify'
'DVSwitch.HostOp'
'DVSwitch.ResourceManagement'
'DVPortgroup.Create'
'DVPortgroup.Modify'
'Host.Inventory.CreateCluster'
'Host.Inventory.AddHostToCluster'
'Host.Inventory.DeleteCluster'
'Host.Inventory.EditCluster'
'Host.Config.AutoStart'
'Host.Config.Storage'
'Host.Config.NetService'
'Host.Config.Network'
'VirtualMachine.Inventory.Create'
'VirtualMachine.Inventory.Delete'
'VirtualMachine.Inventory.Unregister'
'VirtualMachine.Interact.PowerOn'
'VirtualMachine.Interact.PowerOff'
'VirtualMachine.Config.AddNewDisk'
'VirtualMachine.Config.EditDevice'
'VirtualMachine.Config.Settings'
'Alarm.SetStatus'
'Extension.Register'
)

Write-Host "Create New $vxrailrole1 Role"`n -ForeGroundColor Cyan
New-VIRole -Name $vxrailrole1 -Privilege (Get-VIPrivilege -id $initglobal) | Out-Null
Write-Host "Create New $vxrailrole2 Role"`n -ForeGroundColor Cyan
New-VIRole -Name $vxrailrole2 -Privilege (Get-VIPrivilege -id $dcglobal) | Out-Null
Write-Host "Done"`n -ForeGroundColor Cyan

```

The rest of the procedure to apply the roles/permissionsa are available in Solve.
{: .text-justify}
Hope this helps.
