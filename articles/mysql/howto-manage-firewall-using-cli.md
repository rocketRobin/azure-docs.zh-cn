---
title: "使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则 | Microsoft Docs"
description: "本文介绍了如何使用 Azure CLI 命令行创建和管理 Azure Database for MySQL 防火墙规则。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 66d192287eeaaaa82c0f61f8aa13b8bf7bf8cd47
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2017
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则
使用服务器级防火墙规则，管理员可以管理从指定的 IP 地址或某个范围的 IP 地址对 Azure Database for MySQL 服务器的访问权限。 使用便捷的 Azure CLI 命令，可创建、更新、删除、列出和显示防火墙规则，用于管理服务器。 有关 Azure Database for MySQL 的概述，请参阅 [Azure Database for MySQL server firewall rules](./concepts-firewall-rules.md)（Azure Database for MySQL 服务器防火墙规则）

## <a name="prerequisites"></a>先决条件
* [安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。
* 安装适用于 PostgreSQL 和 MySQL 服务的 Azure Python SDK。
* 安装适用于 PostgreSQL 和 MySQL 服务的 Azure CLI 组件。
* 创建 Azure Database for MySQL 服务器。

## <a name="firewall-rule-commands"></a>防火墙规则命令：
从 Azure CLI 中使用 az mysql server firewall-rule 命令，创建、删除、列出、显示和更新防火墙规则。

命令：
- create：创建 Azure MySQL 服务器防火墙规则。
- delete：删除 Azure MySQL 服务器防火墙规则。
- **list**：列出 Azure MySQL 服务器防火墙规则。
- **show**：显示 Azure MySQL 服务器防火墙规则的详细信息。
- update：更新 Azure MySQL 服务器防火墙规则。

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>登录到 Azure，并列出 Azure Database for MySQL 服务器
使用 az login 命令通过 Azure 帐户安全连接到 Azure CLI。

1. 从命令行运行以下命令：
```azurecli
az login
```
此命令将输出要在下一步骤中使用的代码。

2. 使用 Web 浏览器打开页面 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，然后输入该代码。

3. 出现提示时，请使用 Azure 凭据登录。

4. 获得登录授权后，控制台中会打印出订阅列表。 复制所需订阅的 ID，便于设置要使用的当前订阅。
   ```azurecli-interactive
   az account set --subscription {your subscription id}
   ```

5. 如果不确定其名称，请列出订阅和资源组的 Azure Databases for MySQL 服务器。

   ```azurecli-interactive
   az mysql server list --resource-group myResourceGroup
   ```

   请注意列表中的名称属性，需要该属性来指定要使用的 MySQL 服务器。 如果需要，请确认该服务器的详细信息，并使用名称属性来确保其正确：

   ```azurecli-interactive
   az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>列出 Azure Database for MySQL 服务器上的防火墙规则 
使用服务器名称和资源组名称，列出服务器上现有的服务器防火墙规则。 请注意，应在“--server”开关（而不是在“--name”开关）中指定服务器名称属性。
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo
```
输出会默认采用 JSON 格式列出规则（如果有）。 可使用“--output table”开关，以更具可读性的表格格式输出结果。
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器上的防火墙规则
使用 Azure MySQL 服务器名称和资源组名称，在服务器上创建新的防火墙规则。 提供规则名称以及规则的起始 IP 和结束 IP（对一系列 IP 地址提供访问权限）。
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
若要允许单个 IP 地址进行访问，请将相同的 IP 地址用作起始 IP 和结束 IP，如此示例中所示。
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
成功后，命令输出会列出已创建的防火墙规则的详细信息，默认采用 JSON 格式。 如果失败，输出会改为显示错误消息文本。

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>更新 Azure Database for MySQL 服务器上的防火墙规则 
使用 Azure MySQL 服务器名称和资源组名称，更新服务器上已有的防火墙规则。 输入现有防火墙规则的名称，并提供要更新的起始 IP 和结束 IP 属性。
```azurecli-interactive
az mysql server firewall-rule update --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
成功后，命令输出会列出更新后的防火墙规则的详细信息，默认采用 JSON 格式。 如果失败，输出会改为显示错误消息文本。

> [!NOTE]
> 如果不存在防火墙规则，更新命令将创建规则。

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>显示 Azure Database for MySQL 服务器上的防火墙规则的详细信息
使用 Azure MySQL 服务器名称和资源组名称，显示服务器上已有的防火墙规则的详细信息。 输入现有防火墙规则的名称。
```azurecli-interactive
az mysql server firewall-rule show --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
成功后，命令输出会列出指定的防火墙规则的详细信息，默认采用 JSON 格式。 如果失败，输出会改为显示错误消息文本。

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>删除 Azure Database for MySQL 服务器上的防火墙规则
使用 Azure MySQL 服务器名称和资源组名称，从服务器中删除已有的防火墙规则。 输入现有防火墙规则的名称。
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
成功后没有任何输出。 如果失败，会显示错误消息文本。

## <a name="next-steps"></a>后续步骤
- 了解有关 [Azure Database for MySQL 服务器防火墙规则](./concepts-firewall-rules.md)的详细信息。
- [使用 Azure 门户创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-portal.md)。
