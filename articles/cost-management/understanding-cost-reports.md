---
title: 了解 Azure 成本管理中的成本管理报表 | Microsoft Docs
description: 本文可帮助你了解 Cloudyn 成本管理报表的基本结构和功能。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/07/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: ''
ms.openlocfilehash: bc2c696dceb3ed4741c10a5c611bd2d438b71bd5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="understanding-cost-management-reports"></a>了解成本管理报表

本文可帮助你了解 Cloudyn 成本管理报表的基本结构和功能。 大多数 Cloudyn 报表都较为直观，具有统一的外观。 阅读本文后，即可使用所有成本管理报表。 许多标准功能在各种报表中都可用，使你能够轻松导航报表。 报表是可自定义的，你可以从多个选项中进行选择以计算和显示结果。

## <a name="report-fields-and-options"></a>报表字段和选项

下面我们来看看时段成本报表示例。 大多数 Cloudyn 报表的布局都比较相似。

![示例报表](./media/understanding-cost-reports/sample-report.png)

上图中每个带有相应编号的区域在以下信息中进行了详细说明：

1. **日期范围**

    使用“日期范围”列表，以便使用预设或自定义来定义报表时间间隔。
2. **保存的筛选器**

    使用“保存的筛选器”列表保存应用于报表的当前组和筛选器。 “保存的筛选器”可用于各种成本和性能报表，包括：

      - 成本分析
      - 分配
      - 资产管理
      - 优化

  键入筛选器名称，然后单击“保存”。

3. **标记**

    使用“标记”区域以按标记类别分组。 菜单中列出的标记是 Azure 部门或成本中心标记，或者它们是 Cloudyn 的成本实体和订阅标记。 选择标记来筛选结果。 你也可以键入要筛选结果的标记名称（关键字）。

    ![选择选项](./media/understanding-cost-reports/select-options.png)

    单击“添加”以添加新的筛选器。

    ![添加筛选器](./media/understanding-cost-reports/add-filter.png)

    标记分组或筛选与 Azure 资源或资源组标记无关。

    成本分摊标记分组和筛选可以在“组”菜单选项中找到。

4. **报表中的组**

    使用“成本分析”报表中的组可显示报表中帐单数据的标准明细类别。  但是，成本分摊报表中的组可以显示基于标记的视图类别。 基于标记的类别是在成本分摊模型中定义的，属于帐单数据中的标准明细类别。

    ![组标记](./media/understanding-cost-reports/groups-tags01.png)

    ![组标记](./media/understanding-cost-reports/groups-tags02.png)

    在成本分摊报表中，基于标记的组类别中的组可能包括：
      - 标记
      - 资源组标记
      - Cloudyn 成本实体标记
      - 用于成本分摊目的的订阅标记类别

  示例可能包括：
     - 成本中心
     - 系
     - Application
     - 环境
     - 成本代码

    以下为报表中包含的内置组：

    - **成本类型**
      - 选择一种、多种或全部成本类型。 成本类型包括：
        - 一次性收费
        - 支持
        - 使用成本
    - **客户**
        - 选择特定客户、多个客户或全部客户。
    - **帐户名**
        - 帐户或订阅名称。 即 Azure 中的 Azure 订阅的名称。
    - **帐户编号**
        - 选择一个、多个或全部帐户。 即 Azure 中的 Azure 订阅的 GUID。
    - **父级帐户**
        - 选择父级帐户、多个帐户或所有帐户。
    - **服务**
        - 选择一种服务、多个服务或所有服务。
    - **提供程序**
        - 关联资产和费用的云提供商。
    - **区域**
        - 托管资源的区域。
    - **可用性区域**
        - 区域中的 AWS 独立位置。
    - **资源类型**
        - 使用中的资源类型。
    - **子类型**
        - 选择子类型。
    - **操作**
        - 选择此操作或“全部显示”。
    - **定价模型**
        - 全部预付
        - 无需预付
        - 部分预付
        - 按需
        - 保留
        - 现付
    - **费用类型**
        - 选择保守或积极的费用类型（或者都选）。
    - **租户**
        - 计算机是否作为专用计算机运行。
    -   **使用类型**
          - 使用类型可以为一次性收费或周期性收费。

5. **筛选器**

    使用单选或多选筛选器将范围设置为选定的值。 若要设置筛选器，请单击“添加”，然后选择筛选器类别和值。

6. **成本模型**

    使用成本模型选择以前使用成本分摊 360 创建的成本模型。 你可能有多个 Cloudyn 成本模型，具体要取决于你的成本分摊要求。 某些组织团队的成本分摊要求可能与其他团队的不同。 每个团队可以有自己专用的成本模型。

    有关创建成本分摊模型定义的信息，请参阅[使用自定义标记分摊成本](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs)。

7. **分期付款**

    使用成本分摊报表中的分期付款，可查看非基于用量的服务费用或者一次性应付成本，并已根据服务整个生存期的不同时间段将成本均匀分摊。 一次性费用的示例可能包括：
    - 年度支持费用
    - 年度安全组件费用
    - 保留实例购买费用
    - 某些 Azure Marketplace 项目。

  在“分期付款”下，选择“摊销成本”或“实际成本”。

8. **解决方法**

    “解析度”用于选择所选日期范围内的时间解析度。 时间解析度将确定单位如何在报表中显示，并可以包括：
    - 每日
    - 每周
    - 每月
    - 每季度
    - 每年

9. **分摊规则**

    使用分摊规则来应用或禁用成本分摊成本重新计算。 你可以启用或禁用帐单数据的成本分摊重新计算。 重新计算适用于在报表中所选的类别。 它允许你针对原始帐单数据评估成本分摊重新计算的影响。

10. **未分类**

    使用“未分类”来包含或排除报表中的未分类成本。

11. **显示/隐藏字段**

    “显示/隐藏”选项在报表中没有任何影响。

12.   **显示格式**

    使用“显示格式”可选择各种图或表格视图。

    ![显示格式](./media/understanding-cost-reports/display-formats.png)

13. **多色**

    使用多色可在报表中设置图表的颜色。

14. **操作**

    使用操作可保存、导出或计划报表。

## <a name="save-and-schedule-reports"></a>保存和计划报表

创建报表后，可以将其保存供将来使用。 已保存的报表可在“我的工具” > “我的报表”中找到。 如果对现有报表进行更改并将其保存，则该报表将保存为新版本。 或者，可以将其另存为新报表。

### <a name="save-a-report-to-the-cloudyn-portal"></a>将报表保存到 Cloudyn 门户

查看任何报表时，单击“操作”，然后选择“保存到我的报表”。 为报表命名，然后添加自己的 URL 或使用自动创建的 URL。 可以选择与组织中的其他人公开**共享**报表，也可以将其共享到你的实体。 如果不共享报表，报表将始终是个人报表，只有你可以查看。 保存报表。


### <a name="save-a-report-to-cloud-provider-storage"></a>将报表保存到云提供商存储

若要将报表保存到云服务提供商，必须已配置存储帐户。 查看任何报表时，单击“操作”，然后选择“计划报表”。 为报表命名，然后添加自己的 URL 或使用自动创建的 URL。 选择“保存到存储”，然后选择存储帐户或添加一个新存储帐户。 输入将添加到报表文件名中的前缀。 选择 CSV 或 JSON 文件格式，然后保存报表。

### <a name="schedule-a-report"></a>计划报表

可以按计划的间隔运行报表，可以将报表发送到收件人列表或云服务提供商存储帐户。 查看任何报表时，单击“操作”，然后选择“计划报表”。 可以通过电子邮件发送报表，并保存到存储帐户。 在“计划”下，选择时间间隔（每日、每周或每月）。 对于每周和每月，选择要发送的天或日期，并选择时间。 保存计划的报表。 如果选择 Excel 报表格式，报表将作为附件发送。 选择电子邮件内容格式时，以图表格式显示的报表结果将作为图形发送。

### <a name="export-a-report-as-a-csv-file"></a>将报表导出为 CSV 文件

查看任何报表时，单击“操作”，然后选择“导出所有报表数据”。 将显示一个弹出窗口，并下载 CSV 文件。

## <a name="next-steps"></a>后续步骤

- 如果尚未完成有关成本管理的第一本教程，请阅读[查看使用情况和成本](tutorial-review-usage.md)。
