---
title: "无法建立到 Azure VM 的 RDP |Microsoft Docs"
description: "解决当使用远程桌面时无法在 Azure 中连接到 Windows 虚拟机的问题"
keywords: "远程桌面错误,远程桌面连接错误,无法连接到 VM,远程桌面故障排除"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: support-article
ms.date: 10/26/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 592d6b0168b9ebb47ed841f7e2b55d55a07500aa


---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>对 Azure 虚拟机的远程桌面连接进行故障排除
与基于 Windows 的 Azure 虚拟机 (VM) 的远程桌面协议 (RDP) 连接可能会因各种原因而失败，使用户无法访问 VM。 问题可能出在 VM 上的远程桌面服务、网络连接或主计算机上的远程桌面客户端。 本文介绍解决 RDP 连接问题的一些最常见方法。 

如果你对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>快速故障排除步骤
在执行每个故障排除步骤之后，请尝试连接到 VM：

1. 重置远程桌面配置。
2. 检查网络安全组规则/云服务终结点。
3. 查看 VM 控制台日志。
4. 查看 VM 资源运行状况。
5. 重置 VM 密码。
6. 重新启动 VM。
7. 重新部署 VM。

如果需要更详细的步骤和说明，请继续阅读。

> [!TIP]
> 如果门户中 VM 的“连接”按钮不可用，并且用户未通过 [Express Route](../expressroute/expressroute-introduction.md) 或[站点到站点 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 连接来连接到 Azure，则必须先为 VM 创建并分配一个公共 IP 地址，然后才能使用 RDP。 详细了解 [Azure 中的公共 IP 地址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)。
> 
> 

## <a name="ways-to-troubleshoot-rdp-issues"></a>解决 RDP 问题的方法
可通过以下方法之一，对使用 Resource Manager 部署模型创建的 VM 进行故障排除：

* [Azure 门户](#using-the-azure-portal) - 如果需要快速重置 RDP 配置或用户凭据，并且没有安装 Azure 工具，则很适合使用此方法。
* [Azure PowerShell](#using-azure-powershell) - 如果你熟悉 PowerShell 提示符，可使用 Azure PowerShell cmdlet 快速重置 RDP 配置或用户凭据。

还可以找到有关对使用[经典部署模型](#troubleshoot-vms-created-using-the-classic-deployment-model)创建的 VM 进行故障排除的步骤。

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>使用 Azure 门户进行故障排除
在执行每个故障排除步骤之后，请尝试再次连接到 VM。 如果仍然无法连接，请尝试下一步。

1. **重置 RDP 连接**。 例如，如果远程连接已禁用或 Windows 防火墙规则正在阻止 RDP，此故障排除步骤可重置 RDP 配置。
   
    在 Azure 门户中选择 VM。 在“设置”窗格中向下滚动到靠近列表底部的“支持 + 故障排除”部分。 单击“重置密码”按钮。 将“模式”设置为“仅重置配置”，然后单击“更新”按钮：
   
    ![在 Azure 门户中重置 RDP 配置](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-rdp.png)
2. **验证网络安全组规则**。 此故障排除步骤验证网络安全组中是否有允许 RDP 通信的规则。 RDP 的默认端口为 TCP 端口 3389。 允许 RDP 通信的规则可能无法在创建 VM 时自动创建。
   
    在 Azure 门户中选择 VM。 从“设置”窗格单击“网络接口”。
   
    ![在 Azure 门户中查看 VM 的网络接口](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-network-interfaces.png)
   
    从列表中选择网络接口（通常只有一个）：
   
    ![在 Azure 门户中选择网络接口](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-interface.png)
   
    选择“网络安全组”以查看与网络接口关联的网络安全组：
   
    ![在 Azure 门户中选择“网络安全组”](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-nsg.png)
   
    验证入站规则存在，该规则允许 TCP 端口 3389 上的 RDP 通信。 以下示例显示允许 RDP 通信的有效安全规则。 可以看到 `Service` 和 `Action` 已正确配置：
   
    ![验证 Azure 门户中的 RDP NSG 规则](./media/virtual-machines-windows-troubleshoot-rdp-connection/verify-nsg-rules.png)
   
    如果不存在允许 RDP 通信的规则，请[创建网络安全组规则](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 允许 TCP 端口 3389。
3. **查看 VM 启动诊断**。 此故障排除步骤通过查看 VM 控制台日志确定 VM 是否报告问题。 并非所有 VM 均已启用启动诊断，所以此故障排除步骤是可选项。
   
    具体的故障排除步骤不在本文范围之内，但可能指示影响 RDP 连接的更广泛问题。 有关查看控制台日志和 VM 屏幕截图的详细信息，请参阅 [VM 启动诊断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。
4. **查看 VM 资源运行状况**。 此故障排除步骤验证 Azure 平台不存在可能影响 VM 连接性的已知问题。
   
    在 Azure 门户中选择 VM。 在“设置”窗格中向下滚动到靠近列表底部的“支持 + 故障排除”部分。 单击“资源运行状况”按钮。 正常的 VM 报告为**可用**：
   
    ![在 Azure 门户中查看 VM 资源运行状况](./media/virtual-machines-windows-troubleshoot-rdp-connection/check-resource-health.png)
5. **重置用户凭据**。 当你不确定或忘记凭据时，此故障排除步骤将重置本地管理员帐户的密码。
   
    在 Azure 门户中选择 VM。 在“设置”窗格中向下滚动到靠近列表底部的“支持 + 故障排除”部分。 单击“重置密码”按钮。 确保“模式”已设置为“重置密码”，然后输入用户名和新密码。 最后，单击“更新”按钮：
   
    ![在 Azure 门户中重置用户凭据](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-password.png)
6. **重新启动 VM**。 此故障排除步骤可以更正 VM 本身具有的任何基础问题。
   
    在 Azure 门户中选择 VM，并单击“概述”选项卡。 单击“重新启动”按钮：
   
    ![在 Azure 门户中重新启动 VM](./media/virtual-machines-windows-troubleshoot-rdp-connection/restart-vm.png)
7. **重新部署 VM**。 此故障排除步骤将 VM 重新部署到 Azure 内的另一台主机，以更正任何基础平台或网络问题。
   
    在 Azure 门户中选择 VM。 在“设置”窗格中向下滚动到靠近列表底部的“支持 + 故障排除”部分。 单击“重新部署”按钮，然后单击“重新部署”：
   
    ![在 Azure 门户中重新部署 VM](./media/virtual-machines-windows-troubleshoot-rdp-connection/redeploy-vm.png)
   
    完成此操作后，临时磁盘数据将丢失，系统将更新与 VM 关联的动态 IP 地址。

如果仍遇到 RDP 问题，可以[开具支持请求](https://azure.microsoft.com/support/options/)或阅读[更详细的 RDP 故障排除概念和步骤](virtual-machines-windows-detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="troubleshoot-using-azure-powershell"></a>使用 Azure PowerShell 进行故障排除
如果尚未执行该操作，请[安装并配置最新的 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

以下示例使用 `myResourceGroup`、`myVM`、`myVMAccessExtension` 之类的变量。 将这些变量名称和位置替换为自己的值。

> [!NOTE]
> 使用 [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell cmdlet 重置用户凭据和 RDP 配置。 在以下示例中，`myVMAccessExtension` 是过程中所指定的名称。 如果以前使用过 VMAccessAgent，可以使用 `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` 检查 VM 的属性，从而获取现有的扩展名称。 若要查看名称，请在输出的“Extensions”部分下查找。
> 
> 

在执行每个故障排除步骤之后，请尝试再次连接到 VM。 如果仍然无法连接，请尝试下一步。

1. **重置 RDP 连接**。 例如，如果远程连接已禁用或 Windows 防火墙规则正在阻止 RDP，此故障排除步骤可重置 RDP 配置。
   
    以下示例将在 `WestUS` 位置和名为 `myResourceGroup` 资源组中重置名为 `myVM` 的 VM 上的 RDP 连接：
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **验证网络安全组规则**。 此故障排除步骤验证网络安全组中是否有允许 RDP 通信的规则。 RDP 的默认端口为 TCP 端口 3389。 允许 RDP 通信的规则可能无法在创建 VM 时自动创建。
   
    首先，将网络安全组的所有配置数据都指定给 `$rules` 变量。 以下示例将在名为 `myResourceGroup` 的资源组中获取关于名为 `myNetworkSecurityGroup` 的网络安全组的信息：
   
    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    现在，查看为此网络安全组配置的规则。 验证存在规则以允许入站连接的 TCP 端口 3389，如下所示：
   
    ```powershell
    $rules.SecurityRules
    ```
   
    以下示例显示允许 RDP 通信的有效安全规则。 可以看到 `Protocol`、`DestinationPortRange`、`Access` 和 `Direction` 已正确配置：
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    如果不存在允许 RDP 通信的规则，请[创建网络安全组规则](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 允许 TCP 端口 3389。
3. **重置用户凭据**。 当你不确定或忘记凭据时，此故障排除步骤将重置所指定本地管理员帐户的密码。
   
    首先，通过为 `$cred` 变量分配凭据来指定用户名和新密码，如下所示：
   
    ```powershell
    $cred=Get-Credential
    ```
   
    现在，更新 VM 上的凭据。 以下示例将在 `WestUS` 位置中和名为 `myResourceGroup` 的资源组中更新名为 `myVM` 的 VM 上的凭据：
   
    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **重新启动 VM**。 此故障排除步骤可以更正 VM 本身具有的任何基础问题。
   
    以下示例重新启动 `myResourceGroup` 资源组中名为 `myVM` 的 VM：
   
    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **重新部署 VM**。 此故障排除步骤将 VM 重新部署到 Azure 内的另一台主机，以更正任何基础平台或网络问题。
   
    以下示例重新部署 `WestUS` 位置和 `myResourceGroup` 资源组中名为 `myVM` 的 VM：
   
    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

如果仍遇到 RDP 问题，可以[开具支持请求](https://azure.microsoft.com/support/options/)或阅读[更详细的 RDP 故障排除概念和步骤](virtual-machines-windows-detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>对使用经典部署模型创建的 VM 进行故障排除
执行每个故障排除步骤后，请尝试重新连接到 VM。

1. **重置 RDP 连接**。 例如，如果远程连接已禁用或 Windows 防火墙规则正在阻止 RDP，此故障排除步骤可重置 RDP 配置。
   
    在 Azure 门户中选择 VM。 单击“...更多”按钮，然后单击“重置远程访问”：
   
    ![在 Azure 门户中重置 RDP 配置](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **验证云服务终结点**。 此故障排除步骤验证云服务中是否具有允许 RDP 通信的终结点。 RDP 的默认端口为 TCP 端口 3389。 允许 RDP 通信的规则可能无法在创建 VM 时自动创建。
   
   在 Azure 门户中选择 VM。 单击“终结点”按钮以查看当前为 VM 配置的终结点。 验证终结点存在，它们允许 TCP 端口 3389 上的 RDP 通信。
   
   以下示例显示允许 RDP 通信的有效终结点：
   
   ![在 Azure 门户中验证云服务终结点](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   如果不存在允许 RDP 通信的终结点，请[创建云服务终结点](virtual-machines-windows-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 允许专用端口 3389 的 TCP。
3. **查看 VM 启动诊断**。 此故障排除步骤通过查看 VM 控制台日志确定 VM 是否报告问题。 并非所有 VM 均已启用启动诊断，所以此故障排除步骤是可选项。
   
    具体的故障排除步骤不在本文范围之内，但可能指示影响 RDP 连接的更广泛问题。 有关查看控制台日志和 VM 屏幕截图的详细信息，请参阅 [VM 启动诊断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。
4. **查看 VM 资源运行状况**。 此故障排除步骤验证 Azure 平台不存在可能影响 VM 连接性的已知问题。
   
    在 Azure 门户中选择 VM。 在“设置”窗格中向下滚动到靠近列表底部的“支持 + 故障排除”部分。 单击“资源运行状况”按钮。 正常的 VM 报告为**可用**：
   
    ![在 Azure 门户中查看 VM 资源运行状况](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **重置用户凭据**。 当你不确定或忘记凭据时，此故障排除步骤将重置所指定本地管理员帐户的密码。
   
    在 Azure 门户中选择 VM。 在“设置”窗格中向下滚动到靠近列表底部的“支持 + 故障排除”部分。 单击“重置密码”按钮。 输入用户名和新密码。 最后，单击“保存”按钮：
   
    ![在 Azure 门户中重置用户凭据](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-password.png)
6. **重新启动 VM**。 此故障排除步骤可以更正 VM 本身具有的任何基础问题。
   
    在 Azure 门户中选择 VM，并单击“概述”选项卡。 单击“重新启动”按钮：
   
    ![在 Azure 门户中重新启动 VM](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-restart-vm.png)

如果仍遇到 RDP 问题，可以[开具支持请求](https://azure.microsoft.com/support/options/)或阅读[更详细的 RDP 故障排除概念和步骤](virtual-machines-windows-detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="troubleshoot-specific-rdp-errors"></a>解决特定 RDP 错误
尝试通过 RDP 连接到 VM 时，可能出现特定错误消息。 以下是最常见的错误消息：

* [由于没有可用于提供许可证的远程桌面授权服务器，远程会话已断开连接](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdplicense)。
* [远程桌面找不到计算机“名称”](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpname)。
* [身份验证出错。无法联系本地安全机构](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpauth)。
* [Windows 安全性错误：凭据无效](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#wincred)。
* [此计算机无法连接到远程计算机](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpconnect)。

## <a name="additional-resources"></a>其他资源
如果未发生上述任何错误但仍无法通过远程桌面连接到 VM，请阅读详细的[远程桌面故障排除指南](virtual-machines-windows-detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

* [Azure IaaS (Windows) 诊断程序包](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
* 有关用于访问 VM 上运行的应用程序的故障排除步骤，请参阅[对在 Azure VM 上运行的应用程序的访问进行故障排除](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 如果在 Azure 中使用 Secure Shell (SSH) 连接到 Linux VM 时遇到问题，请参阅[对 Azure 中到 Linux VM 的 SSH 连接进行故障排除](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。




<!--HONumber=Dec16_HO2-->

