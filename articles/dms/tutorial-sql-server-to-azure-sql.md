---
title: 使用 Azure 数据库迁移服务将 SQL Server 迁移到 Azure SQL 数据库 | Microsoft 文档
description: 了解如何使用 Azure 数据库迁移服务从本地 SQL Server 迁移到 Azure SQL。
services: dms
author: HJToland3
ms.author: jtoland
manager: jhubbard
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/29/2018
ms.openlocfilehash: b16c3666b932beb771c51bb8dec3ebd5fa36e8a0
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="migrate-sql-server-to-azure-sql-database"></a>将 SQL Server 迁移到 Azure SQL 数据库
可以使用 Azure 数据库迁移服务将数据库从本地 SQL Server 实例迁移到 Azure SQL 数据库。 在本教程中，你将通过使用 Azure 数据库迁移服务，将还原到 SQL Server 2016（或更高版本）的本地实例的 Adventureworks2012 数据库迁移到 Azure SQL 数据库。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 通过使用数据迁移助手来评估本地数据库。
> * 通过使用数据迁移助手迁移示例架构。
> * 创建 Azure 数据库迁移服务的实例。
> * 使用 Azure 数据库迁移服务创建迁移项目。
> * 运行迁移。
> * 监视迁移。

## <a name="prerequisites"></a>先决条件
要完成本教程，需要：

- 下载并安装 [SQL Server 2016 或更高版本](https://www.microsoft.com/sql-server/sql-server-downloads)（任意版本）。
- 按照[启用或禁用服务器网络协议](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文中的说明启用 TCP/IP 协议（在安装 SQL Server Express 时，会默认禁用它）。
- 按照[在 Azure 门户中创建 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)一文中的详细信息创建 Azure SQL 数据库实例。
- 下载并安装[数据迁移助手](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 或更高版本。
- 使用 Azure 资源管理器部署模型创建 Azure 数据库迁移服务的 VNET，它将使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 为本地源服务器提供站点到站点连接。
- 确保 Azure 虚拟网络 (VNET) 网络安全组规则未阻止通信端口 443、53、9354、445、12000。 有关 Azure VNET NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg)一文。
- 配置[针对数据库引擎访问的 Windows 防火墙](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 SQL Server（默认情况下为 TCP 端口 1433）。
- 如果使用动态端口运行多个命名 SQL Server 实例，则可能需要启用 SQL Browser 服务并允许通过防火墙访问 UDP 端口 1434，以便 Azure 数据库迁移服务可连接到源服务器上的命名实例。
- 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库。
- 为 Azure SQL 数据库服务器创建服务器级[防火墙规则](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure)，以允许 Azure 数据库迁移服务访问目标数据库。 提供用于 Azure 数据库迁移服务的 VNET 子网范围。
- 确保用于连接到源 SQL Server 实例的凭据具有 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 权限。
- 确保用于连接到目标 Azure SQL 数据库实例的凭据具有目标 Azure SQL 数据库的 CONTROL DATABASE 权限。

## <a name="assess-your-on-premises-database"></a>访问本地数据库
在将数据从本地 SQL Server 实例迁移到 Azure SQL 数据库之前，需要对 SQL Server 数据库进行评估，了解任何可能会阻止迁移的阻塞问题。 在数据迁移助手 v3.3 或更高版本中，按照[执行 SQL Server 迁移评估](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)一文中所述的步骤来完成对本地数据库的评估。 所需步骤汇总如下：
1.  在数据迁移助手中，选择“新建 (+)”图标，然后选择“评估”项目类型。
2.  指定项目名称，在“源服务器类型”文本框中，选择“SQL Server”，然后在“目标服务器类型”文本框中，选择“Azure SQL 数据库”。
3.  选择“创建”来创建项目。

    当你在评估迁移到 Azure SQL 数据库的源 SQL Server 数据库时，可以选择以下一种或两种评估报告类型：
    - 检查数据库兼容性
    - 检查功能奇偶校验

    默认情况下会选择这两种报告类型。
4.  在数据迁移助手的“选项”屏幕上，选择“下一步”。
5.  在“选择源”屏幕上的“连接到服务器”对话框中，向 SQL Server 提供连接详细信息，然后选择“连接”。
6.  在“添加源”对话框中，依次选择“AdventureWorks2012”、“添加”和“开始评估”。

    评估完成后，结果将如下图所示：

    ![评估数据迁移](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    对于 Azure SQL 数据库，评估结果可标识出迁移阻塞问题和功能奇偶校验问题。

7.  通过选择特定的选项来查看迁移阻塞问题和功能奇偶校验问题的评估结果。
    - SQL Server 功能奇偶校验类别在 Azure 中提供了一套全面的建议、可用的替代方法和缓解步骤，以帮助你在迁移项目中规划工作。
    - 兼容性问题类别说明了部分支持或完全不支持的功能，这些功能反映了可能会阻止本地 SQL Server 数据库迁移到 Azure SQL 数据库的兼容性问题。 此外，还提供了一些建议来帮助你解决这些问题。


## <a name="migrate-the-sample-schema"></a>迁移示例架构
如果你对评估感到满意，并确信所选数据库适合迁移到 Azure SQL 数据库，请使用数据迁移助手将架构迁移到 Azure SQL 数据库。

> [!NOTE]
> 在数据迁移助手中创建迁移项目之前，请确保已按照先决条件中的说明预配了 Azure SQL 数据库。 出于本教程的教学目的，假设 Azure SQL 数据库的名称是“AdventureWorksAzure”，但是你可以按照个人意愿使用其他名称进行命名。

若要将 AdventureWorks2012 架构迁移到 Azure SQL 数据库，请执行以下步骤：

1.  在数据迁移助手中，选择“新建 (+)”图标，然后在“项目类型”下选择“迁移”。
3.  指定项目名称，在“源服务器类型”文本框中，选择“SQL Server”，然后在“目标服务器类型”文本框中，选择“Azure SQL 数据库”。
4.  在“迁移范围”下，选择“仅架构”。

    在执行前面的步骤后，数据迁移助手界面应如下图所示：
    
    ![创建数据迁移助手项目](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  选择“创建”来创建项目。
6.  在数据迁移助手中，指定 SQL Server 的源连接详细信息，依次选择“连接”和“AdventureWorks2012”数据库。

    ![数据迁移助手源连接详细信息](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)
7.  在“连接到目标服务器”下选择“下一步”，指定 Azure SQL 数据库的目标连接详细信息，选择“连接”，然后选择在 Azure SQL 数据库中已预配的“AdventureWorksAzure”数据库。

    ![数据迁移助手目标连接详细信息](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)
8.  选择“下一步”，以转到“选择对象”屏幕，可以在其中指定需要部署到 Azure SQL 数据库的“AdventureWorks2012”中的架构对象。

    默认情况下，选择所有对象。

    ![生成 SQL 脚本](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)
9.  选择“生成 SQL 脚本”以创建 SQL 脚本，然后检查脚本是否有任何错误。

    ![架构脚本](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)
10. 选择“部署架构”以将架构部署到 Azure SQL 数据库，然后在部署架构后，检查目标服务器是否存在任何异常情况。

    ![部署架构](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序
1. 登录到 Azure 门户，依次选择“所有服务”和“订阅”。
 
   ![显示门户订阅](media\tutorial-sql-server-to-azure-sql\portal-select-subscription.png)
       
2. 选择要在其中创建 Azure 数据库迁移服务实例的订阅，再选择“资源提供程序”。
 
    ![显示资源提供程序](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)    
3.  搜索迁移服务，再选择“Microsoft.DataMigration”右侧的“注册”。
 
    ![注册资源提供程序](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    


## <a name="create-an-instance"></a>创建实例
1.  在 Azure 门户中，选择“+ 创建资源”，搜索 Azure 数据库迁移服务，然后从下拉列表选择“Azure 数据库迁移服务”。

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)
2.  在“Azure 数据库迁移服务（预览版）”屏幕上，选择“创建”。
 
    ![创建 Azure 数据库迁移服务实例](media\tutorial-sql-server-to-azure-sql\dms-create.png)
  
3.  在“数据库迁移服务”屏幕上，指定服务、订阅、虚拟网络和定价层的名称。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

     ![配置 Azure 数据库迁移服务实例设置](media\tutorial-sql-server-to-azure-sql\dms-settings.png)

4.  选择“创建”来创建服务。

## <a name="create-a-migration-project"></a>创建迁移项目
创建服务后，在 Azure 门户中找到它，然后创建一个迁移项目。
1. 在 Azure 门户中，选择“所有服务”，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”。
 
      ![查找 Azure 数据库迁移服务的所有实例](media\tutorial-sql-server-to-azure-sql\dms-search.png)
2. 在“Azure 数据库迁移服务”屏幕上，搜索你创建的 Azure DMS 实例名称，然后选择实例。
 
     ![查找 Azure 数据库迁移服务实例](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. 选择“+ 新建迁移项目”。
4. 在“新建迁移项目”屏幕上，指定项目名称，在“源服务器类型”文本框中，选择“SQL Server”，然后在“目标服务器类型”文本框中，选择“Azure SQL数据库”。

    ![创建数据库迁移服务项目](media\tutorial-sql-server-to-azure-sql\dms-create-project.png)

5.  选择“创建”来创建项目。


## <a name="specify-source-details"></a>指定源详细信息
1. 在“源详细信息”屏幕上，指定源 SQL Server 的连接详细信息。

    ![选择源](media\tutorial-sql-server-to-azure-sql\dms-select-source.png)

2. 选择“保存”，然后选择用于迁移的“AdventureWorks2012”数据库。

    ![选择源 DB](media\tutorial-sql-server-to-azure-sql\dms-select-source-db.png)

## <a name="specify-target-details"></a>指定目标详细信息
1. 选择“保存”，然后在“目标详细信息”屏幕中，指定目标的连接详细信息，这是使用数据迁移助手向其部署 AdventureWorks2012 架构的预配 Azure SQL 数据库。

    ![选择目标](media\tutorial-sql-server-to-azure-sql\dms-select-target.png)

2. 选择“保存”以保存项目。
3. 在“迁移摘要”屏幕上，查看并验证与迁移项目关联的详细信息。

    ![DMS 摘要](media\tutorial-sql-server-to-azure-sql\dms-summary.png)

4. 选择“保存”。

## <a name="run-the-migration"></a>运行迁移
1.  选择最近保存的项目，依次选择“+ 新建活动”和“运行数据迁移”。

    ![新建活动](media\tutorial-sql-server-to-azure-sql\dms-new-activity.png)

2.  出现提示时，输入源服务器和目标服务器的凭据，然后选择“保存”。
3.  在“映射到目标数据库”屏幕上，映射源和目标数据库以进行迁移。

    如果目标数据库包含的数据库名称与源数据库的相同，则 Azure DMS 会默认选择目标数据库。

    ![映射到目标数据库](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity.png)

4. 在“选择表”屏幕上选择“保存”，展开表列表并查看受影响字段的列表。

    ![选择表](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity.png)

5.  在“迁移摘要”屏幕上，选择“保存”，在“活动名称”文本框中，指定迁移活动的名称。

    在此屏幕上，还可以展开“选择验证选项”屏幕，可用于指定为以下项来验证已迁移的数据库：
    - 架构
    - 数据一致性
    - 查询正确性和性能

    ![选择“验证”选项](media\tutorial-sql-server-to-azure-sql\dms-configuration.png)

6.  选择“保存”，查看摘要以确保源和目标详细信息与你先前指定的内容匹配。

    ![迁移摘要](media\tutorial-sql-server-to-azure-sql\dms-run-migration.png)

7.  选择“运行迁移”以启动迁移活动，然后选择“刷新”查看当前状态。

    ![活动状态](media\tutorial-sql-server-to-azure-sql\dms-activity-status.png)

## <a name="monitor-the-migration"></a>监视迁移
1. 选择迁移活动以查看活动状态。
2. 在迁移完成后，验证目标 Azure SQL 数据库。

    ![已完成](media\tutorial-sql-server-to-azure-sql\dms-completed-activity.png)
