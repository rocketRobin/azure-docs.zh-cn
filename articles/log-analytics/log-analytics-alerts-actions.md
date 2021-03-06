---
title: "响应 OMS Log Analytics 中的警报 | Microsoft 文档"
description: "Log Analytics 中的警报标识 OMS 存储库中的重要信息，还可主动通知你存在问题或调用操作以尝试更正问题。  本文介绍如何创建警报规则和它们可执行的不同操作的详细信息。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d936cf467ee7043b171cfc845f247f891f52f599
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="add-actions-to-alert-rules-in-log-analytics"></a>将操作添加到 Log Analytics 中的警报规则
[在 Log Analytics 中创建警报](log-analytics-alerts.md)时，可以选择[配置警报规则](log-analytics-alerts.md)以执行一个或多个操作。  本文介绍可用的不同操作，并详述如何配置每种类型的操作。

| 操作 | 说明 |
|:--|:--|
| [电子邮件](#email-actions) | 将内含警报详细信息的电子邮件发送给一位或多位收件人。 |
| [Webhook](#webhook-actions) | 通过单个 HTTP POST 请求调用外部进程。 |
| [Runbook](#runbook-actions) | 在 Azure 自动化中启动 Runbook。 |


## <a name="email-actions"></a>电子邮件操作
电子邮件操作会将内含警报详细信息的电子邮件发送给一位或多位收件人。  可以指定邮件主题，但其内容是由 Log Analytics 构造的标准格式。  除了日志搜索所返回的最多 10 条记录的详细信息之外，邮件内容还包括摘要信息（如警报名称）。  此外，还包含 Log Analytics 中将从查询中返回整个记录集的日志搜索的链接。   邮件发件人为 *Microsoft Operations Management Suite (OMS) 团队&lt;noreply@oms.microsoft.com&gt;*。 

电子邮件操作需要下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| 使用者 |电子邮件主题。  不能修改邮件正文。 |
| 收件人 |所有电子邮件收件人的地址。  如果指定多个地址，则用分号 (;) 分隔地址。 |


## <a name="webhook-actions"></a>Webhook 操作

使用 Webhook 操作可通过单个 HTTP POST 请求调用外部进程。  被调用的服务应支持 Webhook，并确定将如何使用接收的任何负载。  只要此请求采用 API 理解的格式，还可以调用不专门支持 Webhook 的 REST API。  使用 webhook 响应警报的示例是在 [Slack](http://slack.com) 中发送消息或在 [PagerDuty](http://pagerduty.com/) 中创建事件。  有关使用 webhook 来调用 Slack，以创建警报规则的完整演练步骤，请参阅 [Log Analytics 警报中的 Webhook](log-analytics-alerts-webhooks.md)。

Webhook 操作需要下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| Webhook URL |Webhook 的 URL。 |
| 自定义 JSON 负载 |要使用 webhook 发送的自定义负载。  有关详细信息，请参阅下文。 |


Webhooks 包括 URL 和 JSON 格式的负载（即发送到外部服务的数据）。  默认情况下，负载将包含下表中的值。  可以选择将此负载替换成自己的自定义负载。  在这种情况下，可以使用下表中每个参数的变量，将其值包含在自定义负载中。

>[!NOTE]
> 如果工作区已升级到[新 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则 webook 有效负载已更改。  [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse) 中提供了格式的详细信息。  在下面的[示例](#sample-payload)中可以看到示例。

| 参数 | 变量 | 说明 |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |警报规则的名称。 |
| AlertThresholdOperator |#thresholdoperator |警报规则的阈值运算符。  *大于*或*小于*。 |
| AlertThresholdValue |#thresholdvalue |警报规则的阈值。 |
| LinkToSearchResults |#linktosearchresults |指向 Log Analytics 日志搜索的链接，该搜索会从创建警报的查询返回记录。 |
| ResultCount |#searchresultcount |搜索结果中的记录数。 |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |查询结束时间（UTC 格式）。 |
| SearchIntervalInSeconds |#searchinterval |警报规则的时间范围。 |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |查询开始时间（UTC 格式）。 |
| SearchQuery |#searchquery |警报规则所使用的日志搜索查询。 |
| SearchResults |请参阅下文 |查询所返回的 JSON 格式的记录。  限制为前 5000 条记录。 |
| WorkspaceID |#workspaceid |OMS 工作区的 ID。 |

例如，可以指定以下自定义负载，其中包含名为 *text* 的单一参数。  该 Webhook 调用的服务将需要此参数。

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

此示例的有效负载会在发送到 Webhook 时解析如下。

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

若在自定义有效负载中包含搜索结果，请将下行添加为 json 负载中的最高级别属性。  

    "IncludeSearchResults":true

例如，若要创建只包含警报名称和搜索结果的自定义负载，则可以使用以下代码。 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


可以在 [在 OMS Log Analytics 中创建警报 webhook 操作以将消息发送到 Slack](log-analytics-alerts-webhooks.md) 中使用 webhook 来启动外部服务，逐步完成创建警报规则的完整示例。


## <a name="runbook-actions"></a>Runbook 操作
Runbook 操作可在 Azure 自动化中启动 Runbook。  若要使用此类型的操作，必须在 OMS 工作区中安装和配置[自动化解决方案](log-analytics-add-solutions.md)。  可以从在自动化解决方案中配置的自动化帐户中的 Runbook 进行选择。

Runbook 操作需要下表中的属性。

| 属性 | 说明 |
|:--- |:---|
| Runbook | 创建警报时要启动的 Runbook。 |
| 运行位置 | 指定“Azure”以在云中运行 Runbook。  选择“混合辅助角色”以在安装了[混合 Runbook 辅助角色](../automation/automation-hybrid-runbook-worker.md )的代理上运行 Runbook。  |

Runbook 操作使用 [Webhook](../automation/automation-webhooks.md) 来启动 Runbook。  在创建警报规则时，系统会自动为 Runbook 创建新的 Webhook，其名称为 **OMS Alert Remediation** 且后跟一个 GUID。  

无法直接填充 Runbook 的任何参数，但 [$WebhookData 参数](../automation/automation-webhooks.md)将包含警报的详细信息，包括创建该警报的日志搜索结果。  Runbook 需要将 **$WebhookData** 定义为参数，以使其访问警报属性。  可在 **$WebhookData** 的 **RequestBody** 属性的名为 **SearchResult**（适用于带有标准有效负载的 runbook 操作和 webhook 操作）或 **SearchResults**（带有自定义有效负载的 webhook 操作，包括 **IncludeSearchResults":true**）的单一属性中获得 json 格式的警报数据。  这些数据具有下表所列的属性。

>[!NOTE]
> 如果工作区已升级到[新 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则 runbook 有效负载已更改。  [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse) 中提供了格式的详细信息。  在下面的[示例](#sample-payload)中可以看到示例。  

| 节点 | 说明 |
|:--- |:--- |
| id |搜索的路径和 GUID。 |
| __metadata |有关警报的信息，包括记录数和搜索结果状态。 |
| value |搜索结果中每个记录的单独条目。  条目的详细信息将匹配记录的属性和值。 |

例如，下面的 Runbook 会提取日志搜索返回的记录，并基于每个记录的类型分配不同属性。  请注意，该 Runbook 以从 json 转换 **RequestBody** 开始，因此，它可以作为 PowerShell 中的对象。

>[!NOTE]
> 这两个 runbook 都使用 **SearchResult**（此属性包含带有标准有效负载的 runbook 操作和 webhook 操作的结果）。  如果从使用自定义有效负载的 webhook 响应调用 runbook，则需要将此属性更改为 **SearchResults**。

以下 runbook 将处理来自[旧版 Log Analytics 工作区](log-analytics-log-search-upgrade.md)的有效负载。

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody
    $Records     = $RequestBody.SearchResult.value

    foreach ($Record in $Records)
    {
        $Computer = $Record.Computer

        if ($Record.Type -eq 'Event')
        {
            $EventNo    = $Record.EventID
            $EventLevel = $Record.EventLevelName
            $EventData  = $Record.EventData
        }

        if ($Record.Type -eq 'Perf')
        {
            $Object    = $Record.ObjectName
            $Counter   = $Record.CounterName
            $Instance  = $Record.InstanceName
            $Value     = $Record.CounterValue
        }
    }

以下 runbook 将处理来自[已升级 Log Analytics 工作区](log-analytics-log-search-upgrade.md)的有效负载。

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody

    # Get all metadata properties    
    $AlertRuleName = $RequestBody.AlertRuleName
    $AlertThresholdOperator = $RequestBody.AlertThresholdOperator
    $AlertThresholdValue = $RequestBody.AlertThresholdValue
    $AlertDescription = $RequestBody.Description
    $LinktoSearchResults =$RequestBody.LinkToSearchResults
    $ResultCount =$RequestBody.ResultCount
    $Severity = $RequestBody.Severity
    $SearchQuery = $RequestBody.SearchQuery
    $WorkspaceID = $RequestBody.WorkspaceId
    $SearchWindowStartTime = $RequestBody.SearchIntervalStartTimeUtc
    $SearchWindowEndTime = $RequestBody.SearchIntervalEndtimeUtc
    $SearchWindowInterval = $RequestBody.SearchIntervalInSeconds

    # Get detailed search results
    if($RequestBody.SearchResult -ne $null)
    {
        $SearchResultRows    = $RequestBody.SearchResult.tables[0].rows 
        $SearchResultColumns = $RequestBody.SearchResult.tables[0].columns;

        foreach ($SearchResultRow in $SearchResultRows)
        {   
            $Column = 0
            $Record = New-Object –TypeName PSObject 
        
            foreach ($SearchResultColumn in $SearchResultColumns)
            {
                $Name = $SearchResultColumn.name
                $ColumnValue = $SearchResultRow[$Column]
                $Record | Add-Member –MemberType NoteProperty –Name $name –Value $ColumnValue -Force
                        
                $Column++
            }

            # Include code to work with the record. 
            # For example $Record.Computer to get the computer property from the record.
            
        }
    }



## <a name="sample-payload"></a>示例有效负载
此部分显示旧的和[已升级 Log Analytics 工作区](log-analytics-log-search-upgrade.md)中 webhook 和 runbook 操作的示例有效负载。

### <a name="webhook-actions"></a>Webhook 操作
这两个示例都使用 **SearchResult**（此属性包含带有标准有效负载的 webhook 操作的结果）。  如果 webhook 使用了包含搜索结果的自定义有效负载，则此属性将为 **SearchResults**。

#### <a name="legacy-workspace"></a>旧工作区。
下面是旧工作区中 webhook 操作的示例有效负载。

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Type=Usage",
    "SearchResult": {
        "id": "subscriptions/subscriptionID/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspace-workspaceID/search/SearchGUID|10.1.0.7|2017-09-27T10-30-38Z",
        "__metadata": {
        "resultType": "raw",
        "total": 1,
        "top": 2147483647,
        "RequestId": "SearchID|10.1.0.7|2017-09-27T10-30-38Z",
        "CoreSummaries": [
            {
            "Status": "Successful",
            "NumberOfDocuments": 135000000
            }
        ],
        "Status": "Successful",
        "NumberOfDocuments": 135000000,
        "StartTime": "2017-09-27T10:30:38.9453282Z",
        "LastUpdated": "2017-09-27T10:30:44.0907473Z",
        "ETag": "636421050440907473",
        "sort": [
            {
            "name": "TimeGenerated",
            "order": "desc"
            }
        ],
        "requestTime": 361
        },
        "value": [
        {
            "Computer": "-",
            "SourceSystem": "OMS",
            "TimeGenerated": "2017-09-26T13:59:59Z",
            "ResourceUri": "/subscriptions/df1ec963-d784-4d11-a779-1b3eeb9ecb78/resourcegroups/mms-eus/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
            "DataType": "Operation",
            "StartTime": "2017-09-26T13:00:00Z",
            "EndTime": "2017-09-26T13:59:59Z",
            "Solution": "LogManagement",
            "BatchesWithinSla": 8,
            "BatchesOutsideSla": 0,
            "BatchesCapped": 0,
            "TotalBatches": 8,
            "AvgLatencyInSeconds": 0.0,
            "Quantity": 0.002502,
            "QuantityUnit": "MBytes",
            "IsBillable": false,
            "MeterId": "a4e29a95-5b4c-408b-80e3-113f9410566e",
            "LinkedMeterId": "00000000-0000-0000-0000-000000000000",
            "id": "954f7083-cd55-3f0a-72cb-3d78cd6444a3",
            "Type": "Usage",
            "MG": "00000000-0000-0000-0000-000000000000",
            "__metadata": {
            "Type": "Usage",
            "TimeGenerated": "2017-09-26T13:59:59Z"
            }
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Type%3DUsage",
    "Description": null,
    "Severity": "Low"
    }


#### <a name="upgraded-workspace"></a>已升级工作区。
下面是已升级工作区中 webhook 操作的示例有效负载。

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "workspaceID",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
                "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }


### <a name="runbooks"></a>Runbook

#### <a name="legacy-workspace"></a>旧工作区
下面是旧工作区中 runbook 操作的示例有效负载。

    {
        "SearchResult": {
            "id": "subscriptions/subscriptionID/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspace-workspaceID/search/searchGUID|10.1.0.7|TimeStamp",
            "__metadata": {
                "resultType": "raw",
                "total": 1,
                "top": 2147483647,
                "RequestId": "searchGUID|10.1.0.7|2017-09-27T10-51-43Z",
                "CoreSummaries": [{
                    "Status": "Successful",
                    "NumberOfDocuments": 135000000
                }],
                "Status": "Successful",
                "NumberOfDocuments": 135000000,
                "StartTime": "2017-09-27T10:51:43.3075124Z",
                "LastUpdated": "2017-09-27T10:51:51.1002092Z",
                "ETag": "636421063111002092",
                "sort": [{
                    "name": "TimeGenerated",
                    "order": "desc"
                }],
                "requestTime": 511
            },
            "value": [{
                "Computer": "-",
                "SourceSystem": "OMS",
                "TimeGenerated": "2017-09-26T13:59:59Z",
                "ResourceUri": "/subscriptions/AnotherSubscriptionID/resourcegroups/SampleResourceGroup/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                "DataType": "Operation",
                "StartTime": "2017-09-26T13:00:00Z",
                "EndTime": "2017-09-26T13:59:59Z",
                "Solution": "LogManagement",
                "BatchesWithinSla": 8,
                "BatchesOutsideSla": 0,
                "BatchesCapped": 0,
                "TotalBatches": 8,
                "AvgLatencyInSeconds": 0.0,
                "Quantity": 0.002502,
                "QuantityUnit": "MBytes",
                "IsBillable": false,
                "MeterId": "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "LinkedMeterId": "00000000-0000-0000-0000-000000000000",
                "id": "954f7083-cd55-3f0a-72cb-3d78cd6444a3",
                "Type": "Usage",
                "MG": "00000000-0000-0000-0000-000000000000",
                "__metadata": {
                    "Type": "Usage",
                    "TimeGenerated": "2017-09-26T13:59:59Z"
                }
            }]
        }
    }

#### <a name="upgraded-workspace"></a>已升级工作区
下面是已升级工作区中 runbook 操作的示例有效负载。

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "AutomationAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "861bd466-5400-44be-9552-5ba40823c3aa",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
            "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Critical"
    }



## <a name="next-steps"></a>后续步骤
- 完成[配置 Webook](log-analytics-alerts-webhooks.md) 和警报规则的演练步骤。  
- 了解如何[在 Azure 自动化中编写 Runbook](https://azure.microsoft.com/documentation/services/automation)，以修正警报标识的问题。