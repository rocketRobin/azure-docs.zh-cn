---
title: "Azure 堆栈 1710年更新 （生成 20171020.1） |Microsoft 文档"
description: "了解什么是 Azure 堆栈 1710年更新中集成的系统、 已知的问题和下载更新的位置。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: twooley
ms.openlocfilehash: d91a23ae4eb5aee14d3d2fef74467e7f33c458cc
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="azure-stack-1710-update-build-201710201"></a>Azure 堆栈 1710年更新 （生成 20171020.1）

*适用范围： Azure 堆栈集成系统*

本文介绍了改进，并修复的已知问题有关此版本中，以及在何处下载更新此更新包中。 已知问题分为已知问题直接相关的更新过程中，并生成 （安装后） 的已知问题。

> [!IMPORTANT]
> 此更新包项仅用于集成 Azure 堆栈系统。 不适用于此更新包 Azure 堆栈开发工具包。

## <a name="improvements-and-fixes"></a>改进和修复

此更新包括以下质量改进和修复 Azure 堆栈。
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Windows Server 2016 改进和修补程序

- Windows Server 2016 的更新： 2017 年 10 月 10 日-KB4041691 （OS 内部 14393.1770。 请参阅[https://support.microsoft.com/help/4041691](https://support.microsoft.com/help/4041691)有关详细信息。

### <a name="additional-quality-improvements-and-fixes"></a>其他质量的改进和修补程序

- 添加特权终结点 PowerShell cmdlet 来帮助进行故障排除和更新的网络时间协议 (NTP) 服务器。
- 添加了对更新的特权的终结点 Just Enough Administration (JEA) 终结点模块和 cmdlet 白名单的支持。 
- 固定的本地语言特权终结点中的错误。
- 添加旋转网关凭据的能力。
- 删除 CBLocalAdmin 本地管理员帐户。 
- 固定的检测信号警报模板内容以确保检测信号警报工作正确更新后。
- 修复构造资源提供程序在 FRU 操作期间处理超时。 
- 添加供云开发人员在 Azure 堆栈上使用 Azure 资源管理器 API 配置文件的功能。
- 禁用部署虚拟机 (DVM) 上的 Windows Update 服务。 
- 从用户界面中删除节点电源打开/关闭操作。
- 各种其他性能和稳定性修补程序。 
 
## <a name="known-issues-with-the-update-process"></a>在更新过程的已知的问题

本部分包含 1710年更新安装过程中可能遇到的已知的问题。

> [!IMPORTANT]
> 如果更新失败，当你稍后尝试恢复必须使用的更新`Resume-AzureStackUpdate`cmdlet 从特权终结点。 不恢复由使用管理员门户的更新。 （这是此版本中的已知的问题）。有关详细信息，请参阅[监视 Azure 堆栈使用特权终结点中的更新](azure-stack-monitor-update.md)。

| 症状  | 原因  | 解决方法 |
|---------|---------|---------|
|当执行更新时，在更新操作计划的"存储主机重新启动存储节点"步骤可能会错误类似于以下的错误。<br><br>**{"name":"重新启动存储主机"，"说明":"重新启动存储的主机。"，"errorMessage":"类型的角色 BareMetal ' Restart' 引发的异常： \n\nThe 计算机跳过主机名-05。无法检索具有以下错误消息的 WMI 服务通过其 LastBootUpTime: RPC 服务器不可用。(异常来自 HRESULT: 0x800706BA)。 \nat 重新启动主机** | 在某些配置中存在的潜在故障驱动导致此问题。 | 1.登录到基板管理控制器 (BMC) 的 web 接口并重新启动错误消息中的主机标识。<br><br>2.通过使用特权终结点来继续更新。 |
| 当执行更新时，更新过程看起来停止，在步骤后使进度"步骤： 运行步骤 2.4-安装更新"的更新操作计划。<br><br>内部基础结构，文件共享进行一系列的.nupkg 文件的复制过程的此步骤之后。 例如：<br><br>**将从 content\PerfCollector\VirtualMachines 的 1 文件复制到 \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\PerfCounterConfiguration**  | 问题是由填满基础结构的虚拟机并在 Windows Server 横向扩展文件服务器 (SOFS) 中的后续更新时将交付的问题上的磁盘的日志文件引起的。 | 请与 Microsoft 客户服务和支持 (CSS) 联系以获取帮助。 | 
| 当执行更新时，步骤过程中可能出现类似于以下错误"步骤： 运行步骤 2.13.2-更新*VM_Name*"的更新操作计划。 （虚拟机名称可能有所不同。）<br><br>**ActionPlanInstanceWarning ece/MachineName: WarningMessage:Task： 的接口 LiveUpdate 角色 Cloud\Fabric\WAS 失败的调用：<br>的角色 WAS 类型 LiveUpdate 引发了异常：<br>存储过程中的出错初始化： 尝试建立到 Microsoft 存储服务 API 调用时发生错误: {"消息":"与 Service Fabric 通信时发生超时。异常类型： TimeoutException。异常消息： 操作已超时。"}**  | 问题是由 Windows server 中将在后续的更新中修复 I/O 超时导致的。 | 请与 Microsoft CSS 联系以获取帮助。
| 当执行更新时，可能发生类似于以下错误，在步骤"单步跳 21 的重新启动 SQL server 虚拟机。"<br><br>**类型 LiveUpdateRestart 的角色 VirtualMachines 引发了异常：<br>VerboseMessage: VM MachineName 的 [VirtualMachines:LiveUpdateRestart] 查询-Sql01。-10/13/2017年 5:11:50 PM VerboseMessage: [VirtualMachines:LiveUpdateRestart] VM 被标记为 HighlyAvailable。-10/13/自 2017 年 5:11:50 PM VerboseMessage: [VirtualMachines:LiveUpdateRestart] ms。在 MS Internal.ServerClusters.ExceptionHelp.Build。在 Microsoft.FailoverClusters.PowerShell.StopClusterResourceCommand.BeginTimedOperation() Internal.ServerClusters.ClusterResource.BeginTakeOffline (布尔 force)在 Microsoft.FailoverClusters.PowerShell.FCCmdlet.ProcessRecord()-10/13/2017年 5:11:50 PM WarningMessage:Task Microsoft.FailoverClusters.PowerShell.TimedCmdlet.WrappedProcessRecord()： 调用的接口 LiveUpdateRestart 的角色Cloud\Fabric\VirtualMachines 的失败：** | 如果虚拟机无法重新启动，则可能出现此问题。 | 请与 Microsoft CSS 联系以获取帮助。
| 当执行更新时，可能会出现错误：<br><br>**自 2017 年 1-10-22T01:37:37.5369944Z 角色 'SQL' 的类型关闭引发了异常： 出错暂停的节点 s45r1004 Sql01 站点参阅停止 SQL C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\Applications\EnterpriseCloudEngineApplicationType_App1\EnterpriseCloudEngineServicePkg.Code.1.0.597.18\CloudDeployment\Roles\SQL\SQL.psm1： 行 542at 关闭，C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\Applications\EnterpriseCloudEngineApplicationType_App1\EnterpriseCloudEngineServicePkg.Code.1.0.597.18\CloudDeployment\Classes\SQL\SQL.psm1： 行 50at <ScriptBlock>， <No file>： 行 18at <ScriptBlock>， <No file>： 行 16** | 如果无法将虚拟机置于挂起状态，以排出角色，则可能出现此问题。 | 请与 Microsoft CSS 联系以获取帮助。
| 当执行更新时，可能会出现以下错误之一：<br><br>**类型验证的角色 ADFS 引发了异常： ADFS/Graph 角色的验证失败，错误： 错误检查 ADFS 探测终结点*endpoint_URI*： 带"0"的参数调用"GetResponse"的异常:"远程服务器返回了一个错误: (503) 服务器不可用。"在 Invoke ADFSGraphValidation**<br><br>**类型验证的角色 ADFS 引发了异常： ADFS/Graph 角色的验证失败，错误： 提取 ADFS 属性的错误： 无法连接到 net.tcp: //localhost: 1500年/策略。00:00:02.0498923 的时间范围为持续连接尝试。TCP 错误代码 10061： 由于目标计算机主动拒绝 127.0.0.1:1500 无法建立连接。在调用 ADFSGraphValidation** | 更新操作计划无法验证 Active Directory 联合身份验证服务 (AD FS) 运行状况。 | 请与 Microsoft CSS 联系以获取帮助。

## <a name="known-issues-post-installation"></a>已知的问题 （安装后）

本部分包含的已知问题生成 20171020.1 的安装后。

### <a name="portal"></a>门户

- 它不能在管理员门户中查看计算或存储资源。 这表示安装更新期间出现错误，更新已正确地报告为成功。 如果出现此问题，请与 Microsoft CSS 联系以获取帮助。
- 你可能会看到在门户中的空白仪表板。 若要恢复仪表板，门户中，右上角选择齿轮图标，然后选择**还原默认设置**。
- 用户可以浏览没有订阅，但完整的应用商店，并可以看到等计划，并提供管理项目。 这些项是向用户无法正常工作。
- **移动**在查看资源组的属性时，将禁用按钮。 此行为被预期行为。 当前不支持订阅之间移动资源组。
- 你不能查看你的订阅使用 Azure 堆栈门户的权限。 一种解决方法，可以通过使用 PowerShell 来验证权限。
-  对于任何工作流，其中在下拉列表中选择订阅、 资源组或位置，你可能会遇到一个或多个以下问题：

   - 你可能会看到在列表顶部的空行。 你仍应将能够按预期方式选择一个项。
   - 如果下拉列表中项的列表很短，可能无法查看任何项名称。
   - 如果你有多个用户订阅，资源组下拉列表可能为空。 

   若要解决的最后两个问题，你可键入名称的订阅或资源组 （如果你知道它），也可以改为使用 PowerShell。
  
### <a name="backup"></a>备份

- 不要在上启用基础结构备份**基础结构备份**边栏选项卡。

### <a name="health-and-monitoring"></a>运行状况和监视

- 如果重新启动基础结构角色实例，你可能会收到一条消息指出在重新启动失败。 但是，在重新启动实际成功。

### <a name="services"></a>服务

**应用商店**
- 当你尝试通过将项添加到 Azure 堆栈市场**从 Azure 中的添加**选项，并非所有的项可能下载可见。
- 没有任何应用商店体验，以创建虚拟机规模集。 你可以创建缩放集使用的模板。
- 在订阅中创建其第一个 Azure 函数之前，租户必须注册存储资源提供程序。
- 删除孤立资源导致的用户订阅。 解决方法是，首先删除用户资源或整个资源组中，，然后再删除用户订阅。 

**SQL/MySQL**
- 它可能需要一小时之前租户可以在新的 SQL 或 MySQL SKU 中创建数据库。 
- 直接在 SQL 和 MySQL 宿主服务器不由资源提供程序执行上的项目创建不支持，并且可能会导致不匹配的状态。

**计算**
- 为用户提供的选项以使用地域冗余存储创建虚拟机。 此配置导致虚拟机创建失败。
- 你可以配置虚拟机的可用性仅使用容错域之一，以及一个更新域设置。
 
**网络**
- 使用门户，不能具有公共 IP 地址创建负载平衡器。 一种解决方法，你可以使用 PowerShell 创建负载平衡器。
- 创建一个网络负载平衡时，必须创建网络地址转换 (NAT) 规则。 如果没有，你将收到错误，当你尝试创建负载平衡器后添加的 NAT 规则。
 
### <a name="field-replaceable-unit-fru-procedures"></a>字段可更换部件 (FRU) 过程

- 在更新期间运行，脱机映像不需要进行修正。 如果你需要更换缩放单元节点，使用你的 OEM 硬件供应商，以确保被替换的节点具有最新的修补程序级别。

## <a name="download-the-update"></a>下载更新

你可以下载 1710年更新包[此处](https://aka.ms/azurestackupdatedownload)。

## <a name="next-steps"></a>后续步骤

- 有关 Azure 堆栈中的更新管理的概述，请参阅[管理 Azure 堆栈概述中的更新](azure-stack-updates.md)。
- 有关如何应用更新的信息，请参阅[在 Azure 堆栈中应用更新](azure-stack-apply-updates.md)。