---
title: "Azure 快速入门 - 创建 Windows VM CLI | Microsoft 文档"
description: "快速了解如何使用 Azure CLI 创建 Windows 虚拟机。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: a1ccebd6d53c7f02517c7655bdfb5b3ce3f26090
ms.lasthandoff: 04/12/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli"></a>使用 Azure CLI 创建 Windows 虚拟机

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本指南详细介绍如何使用 Azure CLI 部署运行 Windows Server 2016 的虚拟机。 部署完成后，我们将连接到服务器并安装 IIS。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) 。

另请确保已安装 Azure CLI。 有关详细信息，请参阅 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="log-in-to-azure"></a>登录 Azure 

使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在 `westeurope` 位置创建名为 `myResourceGroup` 的资源组。

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#create) 创建 VM。 

以下示例创建一个名为 `myVM` 的 VM。 此示例使用 `azureuser` 作为管理用户名，使用 ` myPassword12` 作为密码。 更新这些值，使其适用于你的环境。 创建与虚拟机的连接时，需要这些值。

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
```

创建 VM 后，Azure CLI 将显示类似于以下示例的信息。 记下公共 IP 地址。 此地址用于访问 VM。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80 

默认情况下，仅允许通过 RDP 连接登录到 Azure 中部署的 Windows 虚拟机。 如果此 VM 将用作 Web 服务器，则需要从 Internet 打开端口 80。  需要使用单个命令打开所需的端口。  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```


## <a name="connect-to-virtual-machine"></a>连接到虚拟机

使用以下命令创建与虚拟机的远程桌面会话。 将 IP 地址替换为你的虚拟机的公共 IP 地址。 出现提示时，输入创建虚拟机时使用的凭据。

```bash 
mstsc /v:<Public IP Address>
```

## <a name="install-iis-using-powershell"></a>使用 PowerShell 安装 IIS

现在，你已登录到 Azure VM，可以使用单行 PowerShell 安装 IIS，并启用本地防火墙规则以允许 Web 流量。  打开 PowerShell 提示符并运行以下命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>查看 IIS 欢迎页

IIS 已安装，并且现在已从 Internet 打开 VM 上的端口 80 - 你可以使用所选的 Web 浏览器查看默认的 IIS 欢迎页。 请务必使用前面记录的 `publicIpAddress` 访问默认页面。 

![IIS 默认站点](./media/quick-create-powershell/default-iis-website.png) 
## <a name="delete-virtual-machine"></a>删除虚拟机

如果不再需要资源组、VM 和所有相关的资源，可以使用以下命令将其删除。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

[安装角色和配置防火墙教程](hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[浏览 VM 部署 CLI 示例](cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
