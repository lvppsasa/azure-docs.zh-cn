---
title: Azure 服务的虚拟网络 | Microsoft Docs
description: 了解向虚拟网络部署资源的好处。 虚拟网络中的资源可以彼此信，也可与本地资源通信，而无需遍历 Internet 的流量。
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 15a5ede13aebb039c1d17f118ee1acdcf9031d50
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="virtual-network-integration-for-azure-services"></a>Azure 服务的虚拟网络集成

通过将 Azure 服务集成到 Azure 虚拟网络，可从虚拟网络中部署的服务实例进行专用访问。

可通过以下选项将 Azure 服务与虚拟网络集成：
- 将服务的专用实例直接部署到虚拟网络中。 可在虚拟网络内以及从本地网络私密访问这些服务的专用实例。
- 通过服务终结点将虚拟网络扩展到服务。 服务终结点可使单个服务资源在虚拟网络中得到保护。
 
## <a name="deploy-azure-services-into-virtual-networks"></a>将 Azure 服务部署到虚拟网络

可使用公共 IP 地址通过 Internet 与大多数 Azure 资源进行通信。 在[虚拟网络](virtual-networks-overview.md)中部署 Azure 服务时，可通过专用 IP 地址与服务资源进行私密通信。

![虚拟网络中部署的服务](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

在虚拟网络中部署服务可提供以下功能：

- 虚拟网络内的资源可以通过专用 IP 地址彼此进行私密通信。 例如，在虚拟网络中，在虚拟机上运行的 HDInsight 与 SQL Server 之间可直接传输数据。
- 本地资源可通过[站点到站点 VPN（VPN 网关）](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti)或 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 使用专用 IP 地址访问虚拟网络中的资源。
- 虚拟网络可使用专用 IP 地址进行[对等互连](virtual-network-peering-overview.md)，实现虚拟网络中资源之间的彼此通信。
- 虚拟网络中的服务实例由 Azure 服务完全托管，用于监视实例的运行状况并根据负载提供所需规模。
- 服务实例部署在虚拟网络的专用子网中。 根据服务提供的指南，必须通过[网络安全组](security-overview.md#network-security-groups)对子网开放入站和出站网络访问。

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>可部署到虚拟网络中的服务

对于必须允许进出子网的路由和流量类型，直接部署到虚拟网络中的每项服务都有特定要求。 有关详细信息，请参阅： 
 
- 虚拟机：[Windows](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service Fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [应用服务环境](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API 管理](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [应用程序网关（内部）](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 容器服务引擎](../container-service/container-service-intro.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：Azure 容器服务可创建默认虚拟网络。 可创建自定义虚拟网络，与 [Azure 容器服务引擎](https://github.com/Azure/acs-engine/tree/master/examples/vnet)配合使用。
- [Azure Active Directory 域服务](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：仅限虚拟网络（经典）
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [云服务](https://msdn.microsoft.com/library/azure/jj156091)：仅限虚拟网络（经典）

可部署[内部 Azure 负载均衡器](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，实现前面列出的多种资源的负载均衡。 在某些情况下，服务在创建资源时自动创建和部署负载均衡器。

## <a name="service-endpoints-for-azure-services"></a>Azure 服务的服务终结点

某些 Azure 服务无法部署在虚拟网络中。 可选择通过启用虚拟网络服务终结点，将某些服务资源的访问限制为仅特定虚拟网络子网。 详细了解[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)。

目前，以下服务支持服务终结点： 
- Azure 存储：[在虚拟网络中保护 Azure 存储帐户](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Azure SQL 数据库：[在虚拟网络中保护 Azure SQL 数据库](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="virtual-network-integration-across-multiple-azure-services"></a>跨多个 Azure 服务的虚拟网络集成

可在虚拟网络的子网中部署 Azure 服务，并保护该子网的关键服务资源。 例如，可将 HDInsight 部署到虚拟网络中，并保护该 HDInsight 子网的存储帐户。





