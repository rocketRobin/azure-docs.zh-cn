---
title: "Microsoft Azure 堆栈疑难解答 |Microsoft 文档"
description: "Azure 堆栈故障排除。"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 3b40a657ee8eb391d14a38cb95acc0729a8dda21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Microsoft Azure 堆栈故障排除

*适用范围： Azure 堆栈开发工具包*

本文档提供 Azure 堆栈的常见故障排除信息。 

Azure 堆栈技术开发工具包中作为评估环境，因为没有从 Microsoft 客户支持服务正式支持。  如果遇到未记录的问题，请务必检查[Azure 堆栈 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)需进一步的帮助和信息。  

此部分所述的问题疑难解答的建议派生自多个源和可能或可能不会解析你的特定问题。 代码示例按原样提供，不能保证预期的结果。 本部分受到于频繁的编辑和更新都可以实现对产品的改进。

## <a name="deployment"></a>部署
### <a name="deployment-failure"></a>部署失败
如果在安装过程中遇到失败，则可以使用部署脚本重新运行的选项用于重新启动失败的步骤中的部署。  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>在部署结束时，PowerShell 会话仍处于打开状态，并不显示任何输出
选择了时，此行为可能是只需 PowerShell 命令窗口中，默认行为的结果。 开发工具包部署实际上已成功，但选择窗口时，该脚本已暂停。 你可以验证这是通过查找单词"select"命令窗口的标题栏中的这种情况。  按 ESC 键取消选择它，并在它后面应显示完成消息。

## <a name="templates"></a>模板
### <a name="azure-template-wont-deploy-to-azure-stack"></a>Azure 模板会将部署到 Azure 堆栈
请确保：

* 模板必须使用 Microsoft Azure 服务已可用或在 Azure 堆栈中的预览。
* 用于特定资源的 Api 支持的本地 Azure 堆栈实例，以及您的目标 ("local"在 Azure 堆栈开发工具包中，"East US"或"印度南部"在 Azure 中的 vs) 的有效位置。
* 你查看[本文](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md)Test-azurermresourcegroupdeployment cmdlet，有关该捕获 Azure 资源管理器语法中的细微差异。

你还可以使用中已提供的 Azure 堆栈模板[GitHub 存储库](http://aka.ms/AzureStackGitHub/)来帮助你开始。

## <a name="virtual-machines"></a>虚拟机
### <a name="default-image-and-gallery-item"></a>默认映像和库项
部署 Azure 堆栈中的 Vm 之前，必须首先添加 Windows Server 映像和库项。

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>重新启动后我 Azure 堆栈的主机，一些虚拟机可能无法自动启动。
在重新启动后你的主机，您可能发现 Azure 堆栈服务不立即可用。  这是因为 Azure 堆栈[基础结构 Vm](azure-stack-architecture.md#virtual-machine-roles)和 RPs 很少需要一些时间来检查一致性，但最终将自动启动。

你可能会注意到该 Vm 不自动重新启动后的 Azure 堆栈开发工具包主机的租户。  这是一个已知的问题，只需几个手动步骤，以使它们联机：

1.  在 Azure 堆栈开发工具包主机上，启动**故障转移群集管理器**从开始菜单。
2.  选择群集**S Cluster.azurestack.local**。
3.  选择“角色”。
4.  租户的 Vm 将显示在*保存*状态。  基础结构的所有 Vm 都运行后，右键单击虚拟机，然后选择**启动**恢复 VM。

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>我已删除了某些虚拟机，但仍然看到磁盘上的 VHD 文件。 此行为被预期？
可以，这是预期行为。 因为，它旨在这种方式：

* 在删除 VM 时，不会删除 Vhd。 磁盘是资源组中的单独资源。
* 时将删除存储帐户，删除是立即通过 Azure 资源管理器中可见 （门户、 PowerShell），但它可能包含的磁盘仍保留在存储中，直到垃圾收集操作运行。

如果你看到"孤立"Vhd，务必知道它们是否已删除的存储帐户的文件夹的一部分。 如果不删除存储帐户，是正常它们仍存在。

你可以阅读更多有关配置中的在保留阈值和按需回收[管理存储帐户](azure-stack-manage-storage-accounts.md)。

## <a name="storage"></a>存储
### <a name="storage-reclamation"></a>存储回收
它可能需要长达十四个小时回收容量才会显示在门户中。 空间回收取决于各种因素包括在块 blob 存储区的使用情况百分比为内部的容器文件。 因此，具体取决于将删除多少数据，就不能保证的垃圾回收器运行时无法回收的空间量。

## <a name="powershell"></a>PowerShell
### <a name="resource-providers-not-registered"></a>资源提供程序未注册
连接到租户订阅使用 PowerShell 时，你将注意到资源提供程序不会自动注册。 使用[Connect 模块](https://github.com/Azure/AzureStack-Tools/tree/master/Connect)，或从 PowerShell 运行以下命令 (后[安装并连接](azure-stack-connect-powershell.md)作为租户): 
  
       Get-AzureRMResourceProvider | Register-AzureRmResourceProvider

## <a name="cli"></a>CLI

* CLI 交互模式即 `az interactive` Azure 堆栈中尚不支持命令。
* 若要获取 Azure 堆栈中可用的虚拟机映像的列表，请使用`az vm images list --all`命令而不是`az vm image list`命令。 指定`--all`选项可确保响应返回仅 Azure 堆栈环境中可用的映像。 
* 在 Azure 中提供的虚拟机映像别名不可能适用于 Azure 堆栈。 在使用虚拟机映像，你必须使用整个 URN 参数 (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) 而不是图像别名。 和此 URNmust 派生自一样映像规格`az vm images list`命令。
* 默认情况下，CLI 2.0 中"Standard_DS1_v2"用作默认虚拟机映像大小。 但是，此大小尚不位于 Azure 堆栈，因此，你需要指定`--size`参数显式时创建虚拟机。 你可以通过使用 Azure 堆栈中可用的虚拟机大小的列表`az vm list-sizes --location <locationName>`命令。


## <a name="windows-azure-pack-connector"></a>Windows Azure 包连接器
* 如果在部署 Azure 堆栈开发工具包后，你可以更改 azurestackadmin 帐户的密码，则不再可以配置多云模式。 因此，它将不能连接到目标 Windows Azure Pack 环境。
* 之后，你将设置多云模式：
    * 只有在他们重置门户的设置后，用户可以看到仪表板。 （在用户门户中，单击门户设置图标 （右上角的齿轮图标）。 下**还原默认设置**，单击**应用**。)
    * 仪表板标题可能不会显示。 如果出现此问题，你必须手动将它们添加回。
    * 当首先将它们添加到仪表板时，一些磁贴可能无法正确显示。 若要解决此问题，请刷新浏览器。



