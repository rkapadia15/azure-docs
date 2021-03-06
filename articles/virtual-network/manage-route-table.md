---
title: Create, change, or delete an Azure route table
titlesuffix: Azure Virtual Network
description: Learn how to create, change, or delete a route table.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
---

# Create, change, or delete a route table

Azure automatically routes traffic between Azure subnets, virtual networks, and on-premises networks. If you want to change any of Azure's default routing, you do so by creating a route table. If you're new to routing in virtual networks, you can learn more about it in the [routing overview](virtual-networks-udr-overview.md) or by completing a [tutorial](tutorial-create-route-table-portal.md).

## Before you begin

Complete the following tasks before completing steps in any section of this article:

- If you don't already have an Azure account, sign up for a [free trial account](https://azure.microsoft.com/free).
- If using the portal, open https://portal.azure.com, and log in with your Azure account.
- If using PowerShell commands to complete tasks in this article, either run the commands in the [Azure Cloud Shell](https://shell.azure.com/powershell), or by running PowerShell from your computer. The Azure Cloud Shell is a free interactive shell that you can use to run the steps in this article. It has common Azure tools preinstalled and configured to use with your account. This tutorial requires the Azure PowerShell module version 5.7.0 or later. Run `Get-Module -ListAvailable AzureRM` to find the installed version. If you need to upgrade, see [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps). If you are running PowerShell locally, you also need to run `Connect-AzureRmAccount` to create a connection with Azure.
- If using Azure Command-line interface (CLI) commands to complete tasks in this article, either run the commands in the [Azure Cloud Shell](https://shell.azure.com/bash), or by running the CLI from your computer. This tutorial requires the Azure CLI version 2.0.31 or later. Run `az --version` to find the installed version. If you need to install or upgrade, see [Install Azure CLI](/cli/azure/install-azure-cli). If you are running the Azure CLI locally, you also need to run `az login` to create a connection with Azure.

The account you log into, or connect to Azure with, must be assigned to the [network contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) role or to a [custom role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) that is assigned the appropriate actions listed in [Permissions](#permissions).

## Create a route table

There is a limit to how many route tables you can create per Azure location and subscription. For details, see [Azure limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. In the top-left corner of the portal, select **+ Create a resource**.
2. Select **Networking**, then select **Route table**.
3. Enter a **Name** for the route table, select your **Subscription**, create a new **Resource group**, or select an existing resource group, select a **Location**, then select **Create**. If you plan to associate the route table to a subnet in a virtual network that is connected to your on-premises network through a VPN gateway, and you disable **BGP route propagation**, your on-premises routes are not propagated to the network interfaces in the subnet.

**Commands**

- Azure CLI: [az network route-table create](/cli/azure/network/route-table/route#az_network_route_table_create)
- PowerShell: [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)

## View route tables

In the search box at the top of the portal, enter *route tables* in the search box. When **Route tables** appear in the search results, select it. The route tables that exist in your subscription are listed.

**Commands**

- Azure CLI: [az network route-table list](/cli/azure/network/route-table/route#az_network_route_table_list)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## View details of a route table

1. In the search box at the top of the portal, enter *route tables* in the search box. When **Route tables** appear in the search results, select it.
2. Select the route table in the list that you want to view details for. Under **SETTINGS**, you can view the **Routes** in the route table and the **Subnets** the route table is associated to.
3. To learn more about common Azure settings, see the following information:
	*	[Activity log](../azure-monitor/platform/activity-logs-overview.md)
	*	[Access control (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
	*	[Tags](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
	*	[Locks](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
	*	[Automation script](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Commands**

- Azure CLI: [az network route-table show](/cli/azure/network/route-table/route#az_network_route_table_show)
- PowerShell: [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)

## Change a route table

1. In the search box at the top of the portal, enter *route tables* in the search box. When **Route tables** appear in the search results, select it.
2. Select the route table you want to change. The most common changes are [adding](#create-a-route) or [removing](#delete-a-route) routes and [associating](#associate-a-route-table-to-a-subnet) route tables to, or [dissociating](#dissociate-a-route-table-from-a-subnet) route tables from subnets.

**Commands**

- Azure CLI: [az network route-table update](/cli/azure/network/route-table/route#az_network_route_table_update)
- PowerShell: [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)

## Associate a route table to a subnet

A subnet can have zero or one route table associated to it. A route table can be associated to zero or multiple subnets. Since route tables are not associated to virtual networks, you must associate a route table to each subnet you want the route table associated to. All traffic leaving the subnet is routed based on routes you've created within route tables, [default routes](virtual-networks-udr-overview.md#default), and routes propagated from an on-premises network, if the virtual network is connected to an Azure virtual network gateway (ExpressRoute, or VPN, if using BGP with a VPN gateway). You can only associate a route table to subnets in virtual networks that exist in the same Azure location and subscription as the route table.

1. In the search box at the top of the portal, enter *virtual networks* in the search box. When **Virtual networks** appear in the search results, select it.
2. Select the virtual network in the list that contains the subnet you want to associate a route table to.
3. Select **Subnets** under **SETTINGS**.
4. Select the subnet you want to associate the route table to.
5. Select **Route table**, select the route table you want to associate to the subnet, then select **Save**.

If your virtual network is connected to an Azure VPN gateway, do not associate a route table to the [gateway subnet](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) that includes a route with a destination of 0.0.0.0/0. Doing so can prevent the gateway from functioning properly. For more information about using 0.0.0.0/0 in a route, see [Virtual network traffic routing](virtual-networks-udr-overview.md#default-route).

**Commands**

- Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## Dissociate a route table from a subnet

When you dissociate a route table from a subnet, Azure routes traffic based on its [default routes](virtual-networks-udr-overview.md#default).

1. In the search box at the top of the portal, enter *virtual networks* in the search box. When **Virtual networks** appear in the search results, select it.
2. Select the virtual network that contains the subnet you want to dissociate a route table from.
3. Select **Subnets** under **SETTINGS**.
4. Select the subnet you want to dissociate the route table from.
5. Select **Route table**, select **None**, then select **Save**.

**Commands**

- Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 

## Delete a route table

If a route table is associated to any subnets, it cannot be deleted. [Dissociate](#dissociate-a-route-table-from-a-subnet) a route table from all subnets before attempting to delete it.

1. In the search box at the top of the portal, enter *route tables* in the search box. When **Route tables** appear in the search results, select it.
2. Select **...** on the right-side of the route table you want to delete.
3. Select **Delete**, and then select **Yes**.

**Commands**

- Azure CLI: [az network route-table delete](/cli/azure/network/route-table/route#az_network_route_table_delete)
- PowerShell: [Remove-AzureRmRouteTable](https://docs.microsoft.com/powershell/module/azurerm.network/remove-azurermroutetable?view=azurermps-6.8.1) 

## Create a route

There is a limit to how many routes per route table can create per Azure location and subscription. For details, see [Azure limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. In the search box at the top of the portal, enter *route tables* in the search box. When **Route tables** appear in the search results, select it.
2. Select the route table from the list that you want to add a route to.
3. Select **Routes**, under **SETTINGS**.
4. Select **+ Add**.
5. Enter a unique **Name** for the route within the route table.
6. Enter the **Address prefix**, in CIDR notation, that you want to route traffic to. The prefix cannot be duplicated in more than one route within the route table, though the prefix can be within another prefix. For example, if you defined 10.0.0.0/16 as a prefix in one route, you can still define another route with the 10.0.0.0/24 address prefix. Azure selects a route for traffic based on longest prefix match. To learn more about how Azure selects routes, see [Routing overview](virtual-networks-udr-overview.md#how-azure-selects-a-route).
7. Select a **Next hop type**. For a detailed description of all next hop types, see [Routing overview](virtual-networks-udr-overview.md).
8. Enter an IP address for **Next hop address**. You can only enter an address if you selected *Virtual appliance* for **Next hop type**.
9. Select **OK**.

**Commands**

- Azure CLI: [az network route-table route create](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_create)
- PowerShell: [New-AzureRmRouteConfig](/powershell/module/azurerm.network/new-azurermrouteconfig)

## View routes

A route table contains zero or multiple routes. To learn more about the information listed when viewing routes, see [Routing overview](virtual-networks-udr-overview.md).

1. In the search box at the top of the portal, enter *route tables* in the search box. When **Route tables** appear in the search results, select it.
2. Select the route table from the list that you want to view routes for.
3. Select **Routes** under **SETTINGS**.

**Commands**

- Azure CLI: [az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## View details of a route

1. In the search box at the top of the portal, enter *route tables* in the search box. When **Route tables** appear in the search results, select it.
2. Select the route table you want to view details of a route for.
3. Select **Routes**.
4. Select the route you want to view details of.

**Commands**

- Azure CLI: [az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_show)
- PowerShell: [Get-AzureRmRouteConfig](/powershell/module/azurerm.network/get-azurermrouteconfig)

## Change a route

1. In the search box at the top of the portal, enter *route tables* in the search box. When **Route tables** appear in the search results, select it.
2. Select the route table you want to change a route for.
3. Select **Routes**.
4. Select the route you want to change.
5. Change existing settings to their new settings, then select **Save**.

**Commands**

- Azure CLI: [az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest)
- PowerShell: [Set-AzureRmRouteConfig](/powershell/module/azurerm.network/set-azurermrouteconfig)

## Delete a route

1. In the search box at the top of the portal, enter *route tables* in the search box. When **Route tables** appear in the search results, select it.
2. Select the route table you want to delete a route for.
3. Select **Routes**.
4. From the list of routes, select **...** on the right-side of the route you want to delete.
5. Select **Delete**, then select **Yes**.

**Commands**

- Azure CLI: [az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest#az_network_route_table_route_delete)
- PowerShell: [Remove-AzureRmRouteConfig](/powershell/module/azurerm.network/remove-azurermrouteconfig)

## View effective routes

The effective routes for each network interface attached to a virtual machine are a combination of route tables that you've created, Azure's default routes, and any routes propagated from on-premises networks via BGP through an Azure virtual network gateway. Understanding the effective routes for a network interface is helpful when troubleshooting routing problems. You can view the effective routes for any network interface that is attached to a running virtual machine.

1. In the search box at the top of the portal, enter the name of a virtual machine you want to view effective routes for. If you don't know the name of a virtual machine, enter *virtual machines* in the search box. When **Virtual machines** appear in the search results, select it and select a virtual machine from the list.
2. Select **Networking** under **SETTINGS**.
3. Select the name of a network interface.
4. Select **Effective routes** under **SUPPORT + TROUBLESHOOTING**.
5. Review the list of effective routes to determine if the correct route exists for where you want to route traffic to. Learn more about next hop types that you see in this list in [Routing overview](virtual-networks-udr-overview.md).

**Commands**

- Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) 

## Validate routing between two endpoints

You can determine the next hop type between a virtual machine and the IP address of another Azure resource, an on-premises resource, or a resource on the Internet. Determining Azure's routing is helpful when troubleshooting routing problems. To complete this task, you must have an existing Network Watcher. If you don't have an existing Network Watcher, create one by completing the steps in [Create a Network Watcher instance](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. In the search box at the top of the portal, enter *network watcher* in the search box. When **Network Watcher** appears in the search results, select it.
2. Select **Next hop** under **NETWORK DIAGNOSTIC TOOLS**.
3. Select your **Subscription** and the **Resource group** of the source virtual machine you want to validate routing from.
4. Select the **Virtual machine**, **Network interface** attached to the virtual machine, and **Source IP address** assigned to the network interface that you want to validate routing from.
5. Enter the **Destination IP address** that you want to validate routing to.
6. Select **Next hop**.
7. After a short wait, information is returned that tells you the next hop type and the ID of the route that routed the traffic. Learn more about next hop types that you see returned in [Routing overview](virtual-networks-udr-overview.md).

**Commands**

- Azure CLI: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_show_next_hop)
- PowerShell: [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 

## Permissions

To perform tasks on route tables and routes, your account must be assigned to the [network contributor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) role or to a [custom](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) role that is assigned the appropriate actions listed in the following table:

| Action                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Read a route table                                    |
| Microsoft.Network/routeTables/write                             |   Create or update a route table                        |
| Microsoft.Network/routeTables/delete                            |   Delete a route table                                  |
| Microsoft.Network/routeTables/join/action                       |   Associate a route table to a subnet                   |
| Microsoft.Network/routeTables/routes/read                       |   Read a route                                          |
| Microsoft.Network/routeTables/routes/write                      |   Create or update a route                              |
| Microsoft.Network/routeTables/routes/delete                     |   Delete a route                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Get the effective route table for a network interface |
| Microsoft.Network/networkWatchers/nextHop/action                |   Gets the next hop from a VM                           |

## Next steps

- Create a route table using [PowerShell](powershell-samples.md) or [Azure CLI](cli-samples.md) sample scripts, or using Azure [Resource Manager templates](template-samples.md)
- Create and apply [Azure policy](policy-samples.md) for virtual networks
