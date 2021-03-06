---
title: "Azure 数据工厂中复制活动的容错 | Microsoft Docs"
description: "了解如何通过跳过不兼容行向 Azure 数据工厂中的复制活动添加容错。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jingwang
ms.openlocfilehash: 5b2658cecba80ef871cc38b930b0e52bc3952530
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Azure 数据工厂中复制活动的容错
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](v1/data-factory-copy-activity-fault-tolerance.md)
> * [版本 2 - 预览版](copy-activity-fault-tolerance.md)

在源数据存储与接收器数据存储之间复制数据时，可通过 Azure 数据工厂中的复制活动提供的两种方式处理不兼容行：

- 遇到不兼容数据时，可以中止复制活动并让其失败（默认行为）。
- 通过添加容错并跳过不兼容数据行，可继续复制所有数据。 此外，还可将不兼容行记录在 Azure Blob 存储中。 然后，可以检查日志了解失败原因，修复数据源上的数据，并重试复制活动。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [V1 中的复制活动容错](v1/data-factory-copy-activity-fault-tolerance.md)。


 ## <a name="supported-scenarios"></a>支持的方案
复制活动支持三种检测、跳过和记录不兼容数据的方案：

- **源数据类型与接收器本机类型不兼容**。 <br/><br/> 例如：将数据从 Blob 存储中的 CSV 文件复制到具有架构定义的包含三个 INT 类型列的 SQL 数据库。 包含数值数据的 CSV 文件行（如 123,456,789）成功复制到接收器存储。 但是，包含非数字值的行（如 123,456, abc ）检测为不兼容，并被跳过。
- **源与接收器之间的列数不匹配**。 <br/><br/> 例如：使用包含六个列的架构定义，将数据从 Blob 存储中的 CSV 文件复制到 SQL 数据库。 包含六个列的 CSV 文件行会成功复制到接收器存储。 包含多于或少于六个列的 CSV 文件行会被检测为不兼容，并被跳过。
- **写入关系数据库时发生主键冲突**。<br/><br/> 例如：将数据从 SQL 服务器复制到 SQL 数据库。 接收器 SQL 数据库中定义了主键，但源 SQL 服务器中未定义此类主键。 源中的重复行无法复制到接收器。 复制活动仅将源数据的第一行复制到接收器。 包含重复主键值的后续源行会被检测为不兼容，并被跳过。

## <a name="configuration"></a>配置
下面的 JSON 定义示例用于配置在复制活动中跳过不兼容行：

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "AzureBlobLinkedService",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```
属性 | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | 指定是否在复制期间跳过不兼容的行。 | True<br/>False（默认值） | 否
redirectIncompatibleRowSettings | 若要记录不兼容行，可以指定的一组属性。 | &nbsp; | 否
linkedServiceName | Azure 存储的链接服务，用于存储包含跳过的行的记录。 | AzureStorage 或 AzureStorageSas 链接服务的名称，指代要用于存储日志文件的存储实例。 | 否
path | 包含跳过行的日志文件的路径。 | 指定要用于记录不兼容数据的 Blob 存储路径。 如果未提供路径，服务会为用户创建一个容器。 | 否

## <a name="monitor-skipped-rows"></a>监视跳过的行
复制活动运行完成后，可以在复制活动输出中看到跳过的行数：

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
如果配置为记录不兼容的行，可以通过此路径找到日志文件：`https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`。 

日志文件只能是 csv 文件。 所跳过的原始数据必要时将以逗号作为列分隔符进行记录。 除了日志文件中的原始源数据外，我们还另外添加两列“ErrorCode”和“ErrorMessage”，可从中查看不兼容的根本原因。 ErrorCode 和 ErrorMessage 将用双引号引起来。 

下面的示例展示了日志文件内容：

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>后续步骤
请参阅其他复制活动文章：

- [复制活动概述](copy-activity-overview.md)
- [复制活动性能](copy-activity-performance.md)


