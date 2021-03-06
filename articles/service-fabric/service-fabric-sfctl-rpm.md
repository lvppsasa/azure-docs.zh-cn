---
title: Azure Service Fabric CLI - sfctl rpm | Microsoft Docs
description: "介绍 Service Fabric CLI sfctl rpm 命令。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 34e7693ea40df2bf12fd6e9be2ef627f30748bcd
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-rpm"></a>sfctl rpm
查询并向修复管理器服务发送命令。

## <a name="commands"></a>命令
|命令|说明|
| --- | --- |
|    approve-force| 强制批准给定的修复任务。|
|    delete       | 删除已完成的修复任务。|
|    list         | 获取与给定筛选器匹配的修复任务列表。|

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
删除已完成的修复任务。

此 API 支持 Service Fabric 平台；不应从代码直接使用它。 

### <a name="arguments"></a>参数
|参数|说明|
| --- | --- |
|    --task-id [必需]| 要删除的已完成修复任务的 ID。|
|    --version           | 修复任务的当前版本号。 如果值为非零，则仅当此值与修复任务的实际当前版本匹配时，请求才会成功。 如果值为零，则不执行版本检查。|

### <a name="global-arguments"></a>全局参数
|参数|说明|
| --- | --- |
|    --debug             | 提高日志记录详细程度，以显示所有调试日志。|
|    --help -h           | 显示此帮助消息并退出。|
|    --output -o         | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。
|    --query             | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
|    --verbose           | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|


## <a name="sfctl-rpm-list"></a>sfctl rpm list
获取与给定筛选器匹配的修复任务列表。

此 API 支持 Service Fabric 平台；不应从代码直接使用它。 

### <a name="arguments"></a>参数
|参数|说明|
| --- | --- |
|    --executor-filter| 应在列表中包含其声明任务的修复执行程序的名称。|
|    --state-filter   | 以下值的按位 OR 运算，指定应在结果列表中包含哪些任务状态。 - 1 - 已创建 - 2 - 已声明 - 4 - 正在准备 - 8 - 已批准 - 16 - 正在执行 - 32 - 正在还原 - 64 - 已完成。|
|    --task-id-filter | 要匹配的修复任务 ID 前缀。|

### <a name="global-arguments"></a>全局参数
|参数|说明|
| --- | --- |
|    --debug          | 提高日志记录详细程度，以显示所有调试日志。|
|    --help -h        | 显示此帮助消息并退出。|
|    --output -o      | 输出格式。  允许的值：json、jsonc、table、tsv。  默认| json。|
|    --query          | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
|    --verbose        | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。