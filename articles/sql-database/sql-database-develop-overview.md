---
title: "SQL 数据库开发概述 | Microsoft 文档"
description: "了解可用于连接到 SQL 数据库的连接库和最佳实践。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: genemi
ms.assetid: 67c02204-d1bd-4622-acce-92115a7cde03
ms.service: sql-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: df13648c8a76b216f596df49dd3ef617d0b35ccc


---
# <a name="sql-database-development-overview"></a>SQL 数据库开发概述
本文逐步讲解开发人员在编写代码以连接到 Azure SQL 数据库时应考虑的基本注意事项。

## <a name="language-and-platform"></a>语言和平台
为各种编程语言和平台提供了代码示例。 可在以下位置找到代码示例的链接： 

* 详细信息：[用于 SQL 数据库和 SQL Server 的连接库](sql-database-libraries.md)

## <a name="resource-limitations"></a>资源限制
Azure SQL 数据库使用两种不同的机制管理可用于数据库的资源：资源调控和强制限制。

* 详细信息：[Azure SQL 数据库资源限制](sql-database-resource-limits.md)

## <a name="security"></a>“安全”
Azure SQL 数据库提供用于在 SQL 数据库中限制访问、保护数据和监视活动的资源。

* 详细信息：[保护 SQL 数据库](sql-database-security.md)

## <a name="authentication"></a>身份验证
* Azure SQL 数据库支持 SQL Server 身份验证用户和登录名，以及 [Azure Active Directory 身份验证](sql-database-aad-authentication.md)用户和登录名。
* 需要指定一个特定的数据库，而不要默认使用 master 数据库。
* 你无法使用 SQL 数据库上的 Transact-SQL **USE myDatabaseName;** 语句切换到其他数据库。
* 详细信息：[SQL 数据库安全：管理数据库的访问和登录安全](sql-database-manage-logins.md)

## <a name="resiliency"></a>复原能力
如果在连接到 SQL 数据库时发生暂时性错误，你的代码应重试调用。  建议让重试逻辑使用退让逻辑，这样就不会因为多个客户端同时重试而对 SQL 数据库造成混乱。

* 代码示例：有关演示重试逻辑的代码示例，请在以下位置参阅所选语言的示例：[用于 SQL 数据库和 SQL Server 的连接库](sql-database-libraries.md)
* 详细信息：[SQL 数据库客户端程序的错误消息](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>管理连接
* 在你的客户端连接逻辑中，将默认超时替换为 30 秒。  默认值 15 秒对于依赖于 Internet 的连接而言太短。
* 如果你在使用[连接池](http://msdn.microsoft.com/library/8xx3tyca.aspx)，请确保在程序不活跃地使用连接时将其关闭，而不是准备重用它。

## <a name="network-considerations"></a>网络注意事项
* 在托管你的客户端程序的计算机上，确保防火墙允许端口 1433 上的传出 TCP 通信。  详细信息：[配置 Azure SQL 数据库防火墙](sql-database-configure-firewall-settings.md)
* 如果客户端程序连接到 SQL 数据库 V12，而客户端运行在 Azure 虚拟机 (VM) 上，则必须打开 VM 上的某些端口范围。 详细信息：[用于 ADO.NET 4.5 和 SQL 数据库 V12 的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)
* 与 Azure SQL 数据库 V12 建立的客户端连接有时会绕过代理直接与数据库交互。 除 1433 以外的端口变得非常重要。 详细信息：[用于 ADO.NET 4.5 和 SQL 数据库 V12 的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>数据分片和弹性缩放
弹性缩放简化了扩展（和缩减）过程。 

* [包含 Azure SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [数据依赖型路由](sql-database-elastic-scale-data-dependent-routing.md)
* [Azure SQL 数据库 Elastic Scale 预览版入门](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>后续步骤
浏览所有 [SQL 数据库的功能](https://azure.microsoft.com/services/sql-database/)。




<!--HONumber=Nov16_HO3-->

