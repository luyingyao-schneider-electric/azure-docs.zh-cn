---
title: "创建 Resource Manager 模板的最佳做法 | Microsoft Docs"
description: "有关简化 Azure Resource Manager 模板的指导。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: fe52ced5e4dc829b5d1421bf0edcd58cffcaad19
ms.lasthandoff: 04/03/2017


---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>创建 Azure Resource Manager 模板的最佳做法
本文中的指导可帮助你创建可靠且易于使用的 Azure Resource Manager 模板。 这些指导只属于建议， 除非有明确的规定，否则不一定非要遵循。 在具体的场合下，可能需要对以下方法或示例之一做出变通。

## <a name="resource-names"></a>资源名称
通常，你会在 Resource Manager 中使用三种类型的资源名称：

* 必须唯一的资源名称。
* 不一定要唯一的资源名称，不过，提供的名称应可帮助根据上下文识别资源。
* 通用的资源名称。

有关建立命名约定的帮助，请参阅 [Azure 基础结构命名准则](../virtual-machines/windows/infrastructure-naming-guidelines.md)。 有关资源名称限制的信息，请参阅 [Azure 资源的建议命名约定](../guidance/guidance-naming-conventions.md)。

### <a name="unique-resource-names"></a>唯一的资源名称
对于具有数据访问终结点的任何资源类型，必须提供唯一的资源名称。 需要唯一名称的一些常见资源类型包括：

* Azure 存储<sup>1</sup> 
* Azure 应用服务的 Web 应用功能
* SQL Server
* Azure 密钥保管库
* Azure Redis Cache
* Azure 批处理
* Azure 流量管理器
* Azure 搜索
* Azure HDInsight

<sup>1</sup> 存储帐户名必须使用小写字母，包含 24 个或更少的字符，并且不包含任何连字符。

如果为某个资源名称提供参数，必须在部署该资源时提供唯一的名称。 或者，可以改为创建使用 [uniqueString()](resource-group-template-functions.md#uniquestring) 函数的变量来生成名称。 

可能还需要在 **uniqueString** 结果中添加一个前缀或后缀。 修改唯一的名称可以更方便地通过名称识别资源类型。 例如，可以使用以下变量生成存储帐户的唯一名称：

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>用于标识的资源名称
某些资源类型可能需要命名，但它们的名称不一定非要唯一。 对于这些资源类型，可以提供一个用于标识资源上下文和资源类型的名称。 请提供一个描述性名称，帮助在资源列表中识别该资源。 如果需要为不同的部署使用不同的资源名称，可以对名称使用参数：

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

如果在部署期间不需要传入名称，可以使用变量： 

```json
"variables": {
    "vmName": "demoLinuxVM"
}
```

还可以使用硬编码的值：

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "demoLinuxVM",
  ...
}
```

### <a name="generic-resource-names"></a>通用资源名称
对于主要通过其他资源访问的资源类型，可以在模板中使用硬编码的通用名称。 例如，可为 SQL Server 上的防火墙规则设置一个标准的通用名称：

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="parameters"></a>parameters
使用参数时，以下信息可以提供帮助：

* 尽量不要使用参数。 尽可能地使用变量或文本值。 只针对以下场合使用参数：
   
   * 想要根据环境使用不同变体的设置（SKU、大小、容量）。
   * 想要方便识别而指定的资源名称。
   * 经常用来完成其他任务的值（例如管理员用户名）。
   * 机密（例如密码）。
   * 创建资源类型的多个实例时要使用的值的数目或数组。
* 对参数名称使用混合大小写。
* 对元数据中提供每个参数的说明。

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* 定义参数（密码和 SSH 密钥除外）的默认值：
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* 对所有密码和机密使用 **SecureString**： 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* 尽量避免使用参数来指定位置。 改用资源组的 **location** 属性。 如果对所有资源使用 **resourceGroup().location** 表达式，请将模板中的资源部署在资源组所在的同一位置：
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   如果只有有限数量的位置支持某种资源类型，你可能需要在模板中直接指定有效的位置。 如果必须使用 **location** 参数，请尽量与可能需要位于同一位置的资源共享该参数值。 这样可以最大程度地减少用户必须提供位置信息的次数。
* 避免对资源类型的 API 版本使用参数或变量。 资源的属性和值可能会因版本号的不同而异。 如果将 API 版本设置为参数或变量，代码编辑器中的 IntelliSense 无法确定正确架构。 并且会在模板中将 API 版本硬编码。

## <a name="variables"></a>变量
使用变量时，以下信息可以提供帮助：

* 针对需要在模板中多次使用的值使用变量。 如果一次只使用一个值，则硬编码值可使模板更易于阅读。
* 不能在模板的 **variables** 节中使用 [reference](resource-group-template-functions.md#reference) 函数。 **reference** 函数从资源的运行时状态中派生其值。 但是，变量是在初始模板分析期间解析的。 直接在模板的 **resources** 或 **outputs** 节中构造需要 **reference** 函数的值。
* 根据[资源名称](#resource-names)中所述，针对必须保持唯一的资源名称包含变量。
* 可以将变量组合成复杂对象。 使用 **variable.subentry** 格式引用复杂对象中的值。 组合变量有助于跟踪相关的变量。 此外，还可以提高模板的可读性。 下面是一个示例：
   
   ```json
   "variables": {
       "storage": {
           "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
           "type": "Standard_LRS"
       }
   },
   "resources": [
     {
         "type": "Microsoft.Storage/storageAccounts",
         "name": "[variables('storage').name]",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "sku": {
             "name": "[variables('storage').type]"
         },
         ...
     }
   ]
   ```
   
   > [!NOTE]
   > 复杂对象不能包含从复杂对象引用值的表达式。 若要进行这种引用，可以定义一个单独的变量。
   > 
   > 
   
     有关使用复杂对象作为变量的高级示例，请参阅[在 Azure Resource Manager 模板中共享状态](best-practices-resource-manager-state.md)。

## <a name="resources"></a>资源
使用资源时，以下信息可以提供帮助：

* 为了帮助其他参与者理解该资源的用途，请为模板中的每个资源指定**注释**：
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* 可以使用标记将元数据添加到资源。 使用元数据添加有关资源的信息。 例如，可以添加元数据来记录资源的计费详细信息。 有关详细信息，请参阅[使用标记来组织 Azure 资源](resource-group-using-tags.md)。
* 如果在模板中使用*公共终结点*（例如 Azure Blob 存储公共终结点），请*不要*将命名空间硬编码。 使用 **reference** 函数可动态检索命名空间。 可以使用此方法将模板部署到不同的公共命名空间环境，而无需在模板中手动更改终结点。 在模板中将 API 版本设置为用于存储帐户的同一版本：
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   如果在创建的同一模板中部署存储帐户，则引用资源时不需要指定提供程序命名空间。 下面是简化的语法：
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   如果在模板中包含配置为使用公共命名空间的其他值，请更改这些值以反映相同的 **reference** 函数。 例如，可以设置虚拟机诊断配置文件的 **storageUri** 属性：
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   还可以引用不同资源组中的现有存储帐户：

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* 仅当应用程序有需要时，才将公共 IP 地址分配到虚拟机。 若要连接到虚拟机 (VM) 进行调试或管理，请使用出站 NAT 规则、虚拟网络网关或 jumpbox。
   
     有关连接到虚拟机的详细信息，请参阅：
   
   * [在 Azure 中运行用于 N 层体系结构的 VM](../guidance/guidance-compute-n-tier-vm.md)
   * [在 Azure Resource Manager 中设置对 VM 的 WinRM 访问](../virtual-machines/windows/winrm.md)
   * [使用 Azure 门户实现对 VM 的外部访问](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [使用 PowerShell 实现对 VM 的外部访问](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [使用 Azure CLI 实现对 Linux VM 的外部访问](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* 公共 IP 地址的 **domainNameLabel** 属性必须唯一。 **domainNameLabel** 值的长度必须为 3 到 63 个字符，并遵循正则表达式 `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` 指定的规则。 由于 **uniqueString** 函数生成长度为 13 个字符的字符串，因此 **dnsPrefixString** 参数限制为不超过 50 个字符：

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* 将密码添加到自定义脚本扩展时，请在 **protectedSettings** 属性中使用 **commandToExecute** 属性：
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > 为了确保机密内容作为参数传递给 VM 和扩展时经过加密，请使用相关扩展的 **protectedSettings** 属性。
   > 
   > 

## <a name="outputs"></a>Outputs
如果使用模板创建公共 IP 地址，请包含 **outputs** 节，用于返回 IP 地址和完全限定域名 (FQDN) 的详细信息。 部署后，可以使用输出值轻松检索有关公共 IP 地址和 FQDN 的详细信息。 引用资源时，请使用创建该资源时所用的 API 版本： 

```json
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## <a name="single-template-vs-nested-templates"></a>单个模板与嵌套模板
若要部署解决方案，可以使用单个模板，或者使用包含多个嵌套模板的主模板。 嵌套模板在较高级方案中很常见。 使用嵌套模板可获得以下优势：

* 可将解决方案分解为目标组件。
* 可在不同的主模板中重复使用嵌套模板。

如果你选择使用嵌套模板，以下指导可帮助你标准化模板设计。 这些指导基于[用于设计 Azure Resource Manager 模板的模式](best-practices-resource-manager-design-templates.md)。 我们建议在设计中包含以下模板：

* **主模板** (azuredeploy.json)。 用于输入参数。
* **共享的资源模板**。 用于部署其他所有资源使用的共享资源（例如虚拟网络和可用性集）。 使用 **dependsOn** 表达式可确保在其他模板之前部署此模板。
* **可选资源模板**。 用于根据某个参数（例如 jumpbox）有条件地部署资源。
* **成员资源模板**。 应用程序层中的每个实例类型都有其自身的配置。 在一个层中，可以定义不同的实例类型。 （例如，第一个实例创建群集，其他实例将添加到现有群集。）每个实例类型都有其自身的部署模板。
* **脚本**。 广泛可重用的脚本适用于每个实例类型（例如，初始化和格式化其他磁盘）。 为特定自定义目的创建的自定义脚本根据实例类型的不同而异。

![嵌套模板](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

有关详细信息，请参阅[将链接的模板与 Azure Resource Manager 配合使用](resource-group-linked-templates.md)。

## <a name="conditionally-link-to-nested-templates"></a>有条件地链接到嵌套模板
可以使用某个参数有条件地链接到嵌套模板。 该参数将成为模板 URI 的一部分：

```json
"parameters": {
    "newOrExisting": {
        "type": "String",
        "allowedValues": [
            "new",
            "existing"
        ]
    }
},
"variables": {
    "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
},
"resources": [
    {
        "apiVersion": "2015-01-01",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "[variables('templatelink')]",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
            }
        }
    }
]
```

## <a name="template-format"></a>模板格式
一种不错的做法是通过 JSON 验证程序传递模板。 验证程序可帮助删除多余的逗号、圆括号和方括号，避免部署期间出错。 尝试根据偏好的编辑环境（Visual Studio Code、Atom、Sublime Text、Visual Studio）使用 [JSONLint](http://jsonlint.com/) 或 linter 包。

另外，一个不错的想法是设置 JSON 的格式以以提高可读性。 可以为本地编辑器使用 JSON 格式化程序包。 在 Visual Studio 中，按 **Ctrl+K、Ctrl+D** 设置文档的格式。 在 Visual Studio Code 中，按 **Alt+Shift+F**。 如果本地编辑器无法设置文档格式，可以使用[联机格式化程序](https://www.bing.com/search?q=json+formatter)。

## <a name="next-steps"></a>后续步骤
* 有关为虚拟机构建解决方案的指导，请参阅[在 Azure 中运行 Windows VM](../guidance/guidance-compute-single-vm.md) 和[在 Azure 中运行 Linux VM](../guidance/guidance-compute-single-vm-linux.md)。
* 有关设置存储帐户的指导，请参阅 [Azure 存储性能和可伸缩性清单](../storage/storage-performance-checklist.md)。
* 有关虚拟网络的帮助，请参阅[网络基础结构指南](../virtual-machines/windows/infrastructure-networking-guidelines.md)。
* 若要了解企业如何使用 Resource Manager 有效管理订阅，请参阅 [Azure 企业基架：出于合规目的监管订阅](resource-manager-subscription-governance.md)。


