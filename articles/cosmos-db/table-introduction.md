---
title: "Azure Cosmos DB 的表 API 简介 | Microsoft Docs"
description: "了解如何通过常用 OSS MongoDB API 使用 Azure Cosmos DB 以低延迟存储和查询大量键值对数据。"
services: cosmos-db
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/01/2017
ms.author: arramac
ms.openlocfilehash: 6a399a3a7979f6165d26eb48505242976d51e64f
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2017
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB：表 API 简介

[Azure Cosmos DB](introduction.md) 提供的表 API（预览）适用于为 Azure 表存储编写且需要以下高级功能的应用程序：

* [统包式全局分发](distribute-data-globally.md)。
* 全球范围内[专用的吞吐量](partition-data.md)。
* 99% 的情况下低至个位数的毫秒级延迟。
* 保证高可用性。
* [自动编制辅助索引](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)。

这些应用程序无需更改代码便可使用表 API 迁移到 Azure Cosmos DB，并可充分利用高级功能。 表 API 适用于 .NET 和 Python。

建议观看以下视频。在视频中，Aravind Ramachandran 介绍了适用于 Azure Cosmos DB 的表 API 的入门方法：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="table-offerings"></a>表产品/服务
如果当前使用 Azure 表存储，可以通过移到 Azure Cosmos DB 表 API（预览版）获得以下好处：

| | Azure 表存储 | Azure Cosmos DB 表 API（预览版） |
| --- | --- | --- |
| 延迟 | 快速，但对延迟没有上限。 | 读取操作和写入操作的低至个位数的毫秒级延迟（通过以下统计数据提供支持：在全球任何位置，对于任何规模，99% 的情况下读取操作的延迟 < 10 毫秒、写入操作的延迟 < 15 毫秒）。 |
| 吞吐量 | 可变吞吐量模型。 表的可伸缩性限制为 20,000 个操作/秒。 | 使用 SLA 支持的[每个表专用保留吞吐量](request-units.md)实现高度可缩放。 帐户没有吞吐量上限，每个表支持 >1000 万个操作/秒。 |
| 全局分发 | 具有一个可选可读辅助读取区域以实现高可用性的单一区域。 不能启动故障转移。 | 从 1 个到 30 多个区域进行[统包式全局分发](distribute-data-globally.md)。 支持在全球各地随时[自动和手动故障转移](regional-failover.md)。 |
| 索引 | 仅对 PartitionKey 和 RowKey 建立主索引。 没有辅助索引。 | 自动对所有属性完成编制索引，没有索引管理。 |
| 查询 | 执行查询时使用主键的索引，否则进行扫描。 | 查询可以利用属性的自动索引缩短查询时间。 Azure Cosmos DB 数据库引擎能够支持聚合查询、地理空间查询和排序查询。 |
| 一致性 | 在主要区域内实现强一致性。 在辅助区域内实现最终一致性。 | [五个定义完善的一致性级别](consistency-levels.md)可基于应用程序需要权衡可用性、延迟、吞吐量和一致性。 |
| 定价 | 存储优化。 | 吞吐量优化。 |
| SLA | 99.99% 可用性。 | 在单个区域中达到 99.99% 可用性，并能够添加更多区域以实现更高的可用性。 针对通用版本的[行业领先综合 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)。 |

## <a name="get-started"></a>入门

在 [Azure 门户](https://portal.azure.com)中创建 Azure Cosmos DB 帐户。 然后开始使用我们的[通过 .NET 使用表 API 的快速入门](create-table-dotnet.md)。 

## <a name="next-steps"></a>后续步骤

下面是一些可帮助你入门的指南：
* [使用表 API 生成 .NET 应用程序](create-table-dotnet.md)
* [在 .NET 中使用表 API 进行开发](tutorial-develop-table-dotnet.md)
* [使用表 API 查询表数据](tutorial-query-table.md)
* [了解如何使用表 API 设置 Azure Cosmos DB 全局分发](tutorial-global-distribution-table.md)
* [Azure Cosmos DB 表 .NET API](table-sdk-dotnet.md)
* [用于 Python 的 Azure Cosmos DB 表 SDK](table-sdk-python.md)

