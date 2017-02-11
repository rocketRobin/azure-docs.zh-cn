---
title: "Azure 虚拟机的多个 IP 地址 - 模板 | Microsoft Docs"
description: "了解如何使用 Azure Resource Manager 模板将多个 IP 地址分配给虚拟机。"
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 2e7d60b453fec2ce4c78179419362eee30ab7cb2
ms.openlocfilehash: d3ac0587a89625501ea3d295ef19826205ab5cc8


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板将多个 IP 地址分配给虚拟机

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

本文介绍如何使用 Resource Manager 模板通过 Azure Resource Manager 部署模型创建虚拟机 (VM)。 通过经典部署模型部署 VM 时，不能将多个公共和专用 IP 地址分配给同一 NIC。 若要详细了解 Azure 部署模型，请阅读 [Understand deployment models](../resource-manager-deployment-model.md)（了解部署模型）一文。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>模板说明

部署模板后，即可使用不同配置值快速且一致地创建 Azure 资源。 如果不熟悉 Azure Resource Manager 模板，请阅读 [Resource Manager 模板演练](../azure-resource-manager/resource-manager-template-walkthrough.md)一文。 本文使用[部署具有多个 IP 地址的 VM](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig) 模板。

<a name="resources"></a>部署模板时，会创建以下资源：

|资源|名称|说明|
|---|---|---|
|网络接口|*myNic1*|在本文的方案部分描述的三个 IP 配置在创建后会分配给此 NIC。|
|公共 IP 地址资源|创建 2 项资源：*myPublicIP* 和 *myPublicIP2*|已向这些资源分配静态公共 IP 地址，并且将其分配到方案中描述的 *IPConfig-1* 和 *IPConfig-2* IP 配置。|
|VM|*myVM1*|标准 DS3 VM。|
|虚拟网络|*myVNet1*|虚拟网络，有一个名为 *mySubnet* 的子网。|
|存储帐户|特定于部署|存储帐户。|

<a name="parameters"></a>部署模板时，必须指定以下参数的值：

|名称|说明|
|---|---|
|adminUsername|管理员用户名。 该用户名必须符合 [Azure 用户名要求](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)。|
|adminPassword|管理员密码 该密码必须符合 [Azure 密码要求](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-password-requirements-when-creating-a-vm)。|
|dnsLabelPrefix|PublicIPAddressName1 的 DNS 名称。 DNS 名称将解析为分配给 VM 的公共 IP 地址之一。 在创建 VM 时所在的 Azure 区域（位置）内，名称必须是唯一的。|
|dnsLabelPrefix1|PublicIPAddressName2 的 DNS 名称。 DNS 名称将解析为分配给 VM 的公共 IP 地址之一。 在创建 VM 时所在的 Azure 区域（位置）内，名称必须是唯一的。|
|OSVersion|VM 的 Windows/Linux 版本。 操作系统是所选的给定 Windows/Linux 版本的完整修补映像。|
|imagePublisher|适用于所选 VM 的 Windows/Linux 映像发布者。|
|imageOffer|适用于所选 VM 的 Windows/Linux 映像。|

使用模板部署的每个资源都配置了多个默认设置。 可通过以下某个方法查看这些设置：

- **在 GitHub 上查看模板：**如果熟悉模板，可以在[模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json)中查看设置。 
- **查看部署后的设置：**如果不熟悉模板，则可使用以下某个部分的步骤来部署模板，在部署后查看设置。

可以使用 Azure 门户、PowerShell 或 Azure 命令行接口 (CLI) 部署模板。 所有方法都产生相同的结果。 若要部署模板，请完成以下某个部分的步骤：

## <a name="deploy-using-the-azure-portal"></a>使用 Azure 门户部署

若要使用 Azure 门户部署模板，请完成以下步骤：

1. 若要注册预览版，请向[多个 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 发送一封电子邮件，其中包含你的订阅 ID 和目标用途。 请不要尝试完成剩余的步骤：
    - 只有在收到一封告知允许你使用预览版的电子邮件之后，才这样做。
    - 遵守收到的电子邮件中的说明 
2. 根据需要修改模板。 模板部署本文的[资源](#resources)部分列出的资源和设置。 若要详细了解模板及其创作方法，请阅读[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)一文。
3. 使用以下方法之一部署模板：
    - **在门户中选择模板：**完成[从自定义模板部署资源](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)一文中的步骤。 选择名为 *101-vm-multiple-ipconfig* 的预先存在的模板。
    - **直接：**单击下面的按钮，在门户中直接打开模板：<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

无论选择哪种方法，都需要为本文前面列出的[参数](#parameters)提供值。 部署 VM 后，连接到 VM 并将专用 IP 地址添加到部署的操作系统，只需完成本文[将 IP 地址添加到 VM 操作系统](#os-config)部分的步骤即可。 请勿向操作系统添加公共 IP 地址。

## <a name="deploy-using-powershell"></a>使用 PowerShell 进行部署

若要使用 PowerShell 部署模板，请完成以下步骤：

1. 若要注册预览版，请向[多个 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 发送一封电子邮件，其中包含你的订阅 ID 和目标用途。 请不要尝试完成剩余的步骤：
    - 只有在收到一封告知允许你使用预览版的电子邮件之后，才这样做。
    - 如果不遵循所收到的电子邮件中的说明
2. 通过完成[使用 PowerShell 部署模板](../azure-resource-manager/resource-group-template-deploy-cli.md#deploy)一文中的步骤来部署模板。 本文介绍多个用于部署模板的选项。 如果选择使用 `-TemplateUri parameter` 部署，则此模板的 URI 为 *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*。 如果选择通过 `-TemplateFile` 参数进行部署，可将[模板文件](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json)的内容从 GitHub 复制到计算机的新文件中。 根据需要修改模板内容。 模板部署本文的[资源](#resources)部分列出的资源和设置。 若要详细了解模板及其创作方法，请阅读[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)一文。

    不管选择哪个选项来部署模板，都必须为本文[参数](#parameters)部分列出的参数提供值。 如果选择使用参数文件提供参数，请将[参数文件](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json)的内容从 GitHub 复制到计算机上的新文件中。 修改文件中的值。 使用所创建的文件作为 `-TemplateParameterFile` 参数的值。
    
    若要确定 OSVersion、ImagePublisher 和 imageOffer 参数的有效值，请完成[导航并选择 Windows VM 映像](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md#powershell)一文中的步骤。

    >[!TIP]
    >如果不确定 dnslabelprefix 是否可用，请输入 `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` 命令进行查找。 如果可用，该命令会返回 `True`。

3. 部署 VM 后，连接到 VM 并将专用 IP 地址添加到部署的操作系统，只需完成本文[将 IP 地址添加到 VM 操作系统](#os-config)部分的步骤即可。 请勿向操作系统添加公共 IP 地址。

## <a name="deploy-using-the-azure-cli"></a>使用 Azure CLI 进行部署

若要使用 Azure CLI 1.0 部署模板，请完成以下步骤：

1. 若要注册预览版，请向[多个 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 发送一封电子邮件，其中包含你的订阅 ID 和目标用途。 请不要尝试完成剩余的步骤：
    - 只有在收到一封告知允许你使用预览版的电子邮件之后，才这样做。
    - 如果不遵循所收到的电子邮件中的说明
2. 通过完成[使用 Azure CLI 部署模板](../azure-resource-manager/resource-group-template-deploy-cli.md#deploy)一文中的步骤来部署模板。 本文介绍多个用于部署模板的选项。 如果选择使用 `--template-uri` (-f) 部署，则此模板的 URI 为 *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*。 如果选择通过 `--template-file` (-f) 参数进行部署，则可将[模板文件](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json)的内容从 GitHub 复制到计算机的新文件中。 根据需要修改模板内容。 模板部署本文的[资源](#resources)部分列出的资源和设置。 若要详细了解模板及其创作方法，请阅读[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)一文。

    不管选择哪个选项来部署模板，都必须为本文[参数](#parameters)部分列出的参数提供值。 如果选择使用参数文件提供参数，请将[参数文件](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json)的内容从 GitHub 复制到计算机上的新文件中。 修改文件中的值。 使用所创建的文件作为 `--parameters-file` (-e) 参数的值。
    
    若要确定 OSVersion、ImagePublisher 和 imageOffer 参数的有效值，请完成[导航并选择 Windows VM 映像](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md#azure-cli)一文中的步骤。

3. 部署 VM 后，连接到 VM 并将专用 IP 地址添加到部署的操作系统，只需完成本文[将 IP 地址添加到 VM 操作系统](#os-config)部分的步骤即可。 请勿向操作系统添加公共 IP 地址。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]



<!--HONumber=Dec16_HO2-->

