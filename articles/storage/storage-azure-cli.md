---
title: "将 Azure CLI 2.0 用于 Azure 存储 | Microsoft 文档"
description: "了解如何将 Azure 命令行接口 (Azure CLI) 2.0 用于 Azure 存储，以便创建和管理存储帐户并处理 Azure blob 和文件。 Azure CLI 2.0 是使用 Python 编写的跨平台工具。"
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 02/18/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 5b77598e76de3508d90b35ce5a1f2ee338aca0c8
ms.lasthandoff: 04/25/2017


---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>将 Azure CLI 2.0 用于 Azure 存储

开源且跨平台的 Azure CLI 2.0 提供了一组可在 Azure 平台上运行的命令。 它提供 [Azure 门户](https://portal.azure.com)所提供的很多相同功能，包括各种数据访问功能。

本指南介绍如何使用 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) 执行多个使用 Azure 存储帐户中的资源的任务。 我们建议在使用本指南之前下载并安装或者升级到 CLI 2.0 的最新版。

指南中的示例假定在 Ubuntu 上使用 Bash shell，但其他平台的执行情况应与此类似。 

[!INCLUDE [storage-cli-versions](../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>先决条件
本指南假定你了解 Azure 存储空间的基本概念。 本指南还假定，用户能够满足下面为 Azure 和存储服务指定的帐户创建要求。

### <a name="accounts"></a>帐户
* **Azure 帐户**：如果用户没有 Azure 订阅，可以[创建免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **存储帐户**：请参阅[关于 Azure 存储帐户](../storage/storage-create-storage-account.md)中的[创建存储帐户](../storage/storage-create-storage-account.md#create-a-storage-account)。

### <a name="install-the-azure-cli-20"></a>安装 Azure CLI 2.0

按照[安装 Azure CLI 2.0](/cli/azure/install-az-cli2) 中的概要说明，下载和安装 Azure CLI 2.0。

> [!TIP]
> 如果在安装时遇到问题，请查看本文的[安装故障排除](/cli/azure/install-az-cli2#installation-troubleshooting)部分以及 GitHub 上的 [Install Troubleshooting](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md)（安装故障排除）指南。
>

## <a name="working-with-the-cli"></a>使用 CLI

安装 CLI 之后，可以在命令行界面（Bash、终端、命令提示符）中使用 `az` 命令访问 Azure CLI 命令。 键入 `az` 命令，然后应会显示类似于以下内容的输出：

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account   : Commands to manage subscriptions.
    acr       : Commands to manage Azure container registries.
    acs       : Commands to manage Azure container services.
    ad        : Synchronize on-premises directories and manage Azure Active Directory (AAD)
                resources.
    appservice: Commands to manage your Azure web apps and App Service plans.
    cloud     : Manage the Azure clouds registered.
    component : Commands to manage and update Azure CLI 2.0 components.
    configure : Configure Azure CLI 2.0 or view your configuration. The command is
                interactive, so just type `az configure` and respond to the prompts.
    container : Set up automated builds and deployments for multi-container Docker applications.
    disk      : Commands to manage 'Managed Disks'.
    feature   : Commands to manage resource provider features, such as previews.
    feedback  : Loving or hating the CLI?  Let us know!
    group     : Commands to manage resource groups.
    image     : Commands to manage custom virtual machine images based on managed disks/snapshots.
    lock
    login     : Log in to access Azure subscriptions.
    logout    : Log out to remove access to Azure subscriptions.
    network   : Manages Network resources.
    policy    : Commands to manage resource policies.
    provider  : Manage resource providers.
    resource  : Generic commands to manage Azure resources.
    role      : Use role assignments to manage access to your Azure resources.
    snapshot  : Commands to manage snapshots.
    storage   : Durable, highly available, and massively scalable cloud storage.
    tag       : Manage resource tags.
    vm        : Provision Linux and Windows virtual machines in minutes.
    vmss      : Create highly available, auto-scalable Linux or Windows virtual machines.
```

在命令行界面中，执行命令 `az storage -h` 列出 `storage` 组命令及其子组。 子组说明概述了 Azure CLI 提供的用于使用存储资源的功能。

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Orgin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Effectively scale apps according to traffic using queues.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>将 CLI 连接到 Azure 订阅

若要使用 Azure 订阅中的资源，必须首先使用 `az login` 登录到 Azure 帐户。 登录方法有多种：

* **交互式登录**：`az login`
* **使用用户名和密码登录**：`az login -u johndoe@contoso.com -p VerySecret`
  * 这不能用于 Microsoft 帐户或使用多重身份验证的帐户。
* **使用服务主体登录**：`az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-20-sample-script"></a>Azure CLI 2.0 示例脚本

接下来，我们将使用一个小型 shell 脚本，该脚本会发出一些基本的 Azure CLI 2.0 命令与 Azure 存储资源进行交互。 该脚本首先在存储帐户中创建新容器，然后将现有文件（作为 Blob）上载到该容器。 然后，它列出容器中的所有 Blob，最后，将文件下载到你指定的本地计算机上的目标。

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create -n $container_name

echo "Uploading the file..."
az storage blob upload -f $file_to_upload -c $container_name -n $blob_name

echo "Listing the blobs..."
az storage blob list -c $container_name

echo "Downloading the file..."
az storage blob download -c $container_name -n $blob_name -f $destination_file

echo "Done"
```

**配置并运行脚本**

1. 打开最喜欢的文本编辑器，然后将前面的脚本复制并粘贴到编辑器中。

2. 接下来，更新脚本的变量以反映用户的配置设置。 按照明确的说明替换以下值：

   * **\<storage_account_name\>**：存储帐户的名称。
   * **\<storage_account_key\>**：存储帐户的主访问密钥或辅助访问密钥。
   * **\<container_name\>**：要创建的新容器的名称，例如“azure-cli-sample-container”。
   * **\<blob_name\>**：容器中的目标 Blob 的名称。
   * **\<file_to_upload\>**：本地计算机上小文件的路径，例如：“~/images/HelloWorld.png”。
   * **\<destination_file\>**：目标文件路径，如“~/downloadedImage.png”。

3. 更新了必要的变量后，保存脚本并退出编辑器。 后续步骤假定已将脚本命名为 **my_storage_sample.sh**。

4. 如有必要，将脚本标记为可执行文件：`chmod +x my_storage_sample.sh`

5. 执行脚本。 例如，在 Bash 中：`./my_storage_sample.sh`

应看到类似于以下内容的输出，在脚本中指定的 **\<destination_file\>** 应出现在本地计算机上。

```
Creating the container...
Success
---------
True
Uploading the file...                                           Percent complete: %100.0
Listing the blobs...
Name           Blob Type      Length  Content Type              Last Modified
-------------  -----------  --------  ------------------------  -------------------------
test_blob.txt  BlockBlob         771  application/octet-stream  2016-12-21T15:35:30+00:00
Downloading the file...
Name
-------------
test_blob.txt
Done
```

> [!TIP]
> 前面的输出采用**表**格式。 可以通过在 CLI 命令中指定 `--output` 参数来指定要使用的输出格式，或使用 `az configure` 进行全局设置。
>

## <a name="manage-storage-accounts"></a>管理存储帐户

### <a name="create-a-new-storage-account"></a>新建存储帐户
若要使用 Azure 存储，用户需要一个存储帐户。 可以在将计算机配置为[连接到订阅](#connect-to-your-azure-subscription)之后，创建新的 Azure 存储帐户。

```azurecli
az storage account create -l <location> -n <account_name> -g <resource_group> --sku <account_sku>
```

* `-l` [必填]：位置。 例如，“West US”。
* `-n` [必填]：存储帐户名称。 名称长度必须为 3 到 24 个字符，并且名称只能包含小写字母数字字符。
* `-g` [必填]：资源组的名称。
* `--sku` [必填]：存储帐户 SKU。 允许的值：
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`

### <a name="set-default-azure-storage-account-environment-variables"></a>设置默认的 Azure 存储帐户环境变量
可以在 Azure 订阅中设置多个存储帐户。 若要选择其中一个帐户用于所有后续存储命令，可以设置这些环境变量：

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

设置默认存储帐户的另一种方法是使用连接字符串。 首先，使用 `show-connection-string` 命令获取连接字符串：

```azurecli
az storage account show-connection-string -n <account_name> -g <resource_group>
```

然后复制输出连接字符串，并且设置 `AZURE_STORAGE_CONNECTION_STRING` 环境变量（可能需要将连接字符串括在引号中）：

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

> [!NOTE]
> 本文下列部分中的所有示例均假定已设置 `AZURE_STORAGE_ACCOUNT` 和 `AZURE_STORAGE_ACCESS_KEY` 环境变量。
>

## <a name="create-and-manage-blobs"></a>创建并管理 blob
Azure Blob 存储是用于存储大量非结构化数据（例如文本或二进制数据）的服务，这些数据可通过 HTTP 或 HTTPS 从世界各地进行访问。 本部分假设你已熟悉 Azure Blob 存储概念。 有关详细信息，请参阅[通过 .NET 开始使用 Azure Blob 存储](storage-dotnet-how-to-use-blobs.md)和 [Blob 服务概念](/rest/api/storageservices/blob-service-concepts)。

### <a name="create-a-container"></a>创建容器
Azure 存储空间中的每个 Blob 都必须在容器中。 可以使用 `az storage container create` 命令创建容器：

```azurecli
az storage container create -n <container_name>
```

可以通过指定可选 `--public-access` 参数，为新容器设置读取访问权限的三个级别之一：

* `off`（默认值）：容器数据是帐户所有者私有的。
* `blob`：Blob 的公共读取访问权限。
* `container`：对整个容器的公共读取和列表访问权限。

有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](storage-manage-access-to-resources.md)。

### <a name="upload-a-blob-to-a-container"></a>将 Blob 上载到容器中
Azure Blob 存储支持块 Blob、追加 Blob 和页 Blob。 使用 `blob upload` 命令将 Blob 上载到容器：

```azurecli
az storage blob upload -f <local_file_path> -c <container_name> -n <blob_name>
```

 默认情况下，`blob upload` 命令将 *.vhd 文件上载到页 Blob 或块 Blob。 若要在上载 Blob 时指定另一种类型，可以使用 `--type` 参数，允许的值为 `append`、`block` 和 `page`。

 有关不同 Blob 类型的详细信息，请参阅 [Understanding Block Blobs, Append Blobs, and Page Blobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)（了解块 Blob、追加 Blob 和页 Blob）。

### <a name="download-blobs-from-a-container"></a>从容器下载 Blob
此示例演示如何从容器下载 Blob：

```azurecli
az storage blob download -c mycontainer -n myblob.png -f ~/mydownloadedblob.png
```

### <a name="copy-blobs"></a>复制 blob
你可以在存储帐户和区域内或跨存储帐户和区域异步复制 Blob。

以下示例演示如何将一个存储帐户中的 Blob 复制到另一个存储帐户。 我们首先在另一个帐户中创建容器，指定其 Blob 为可公开访问、可匿名访问的 Blob。 接下来，我们将文件上载到该容器，最后，将 Blob 从该容器复制到当前帐户中的 **mycontainer** 容器。

```azurecli
az storage container create -n mycontainer2 --account-name <accountName2> --account-key <accountKey2> --public-access blob

az storage blob upload -f ~/Images/HelloWorld.png -c mycontainer2 -n myBlockBlob2 --account-name <accountName2> --account-key <accountKey2>

az storage blob copy start -u https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2 -b myBlobBlob -c mycontainer
```

源 Blob URL（由 `-u` 指定）必须可公开访问，或者包含共享访问签名 (SAS) 令牌。

### <a name="delete-a-blob"></a>删除 Blob
若要删除 Blob，请使用 `blob delete` 命令：

```azurecli
az storage blob delete -c <container_name> -n <blob_name>
```

## <a name="create-and-manage-file-shares"></a>创建和管理文件共享
Azure 文件存储使用服务器消息块 (SMB) 协议为应用程序提供共享存储。 Microsoft Azure 虚拟机和云服务以及本地应用程序可以通过装载的共享来共享文件数据。 你可以通过 Azure CLI 管理文件共享和文件数据。 有关 Azure 文件存储的详细信息，请参阅 [在 Windows 上开始使用 Azure 文件存储](storage-dotnet-how-to-use-files.md)或[如何通过 Linux 使用 Azure 文件存储](storage-how-to-use-files-linux.md)。

### <a name="create-a-file-share"></a>创建文件共享
Azure 文件共享是 Azure 中的 SMB 文件共享。 所有目录和文件都必须在文件共享中创建。 一个帐户可以包含无限数量的共享，一个共享可以存储无限数量的文件，直到达到存储帐户的容量限制为止。 下面的示例创建名为 **myshare** 的文件共享。

```azurecli
az storage share create -n myshare
```

### <a name="create-a-directory"></a>创建目录
目录提供了进行 Azure 文件共享时可以选择的层次结构。 以下示例在文件共享中创建名为 **myDir** 的目录。

```azurecli
az storage directory create -n myDir -s myshare
```

请注意，目录路径可以包括多个级别，*例如* **a/b**。 但是，必须确保所有父目录都存在。 例如，对于路径 **a/b**，必须先创建目录 **a**，然后创建目录 **b**。

### <a name="upload-a-local-file-to-a-share"></a>将本地文件上载到共享
以下示例将文件从 **~/temp/samplefile.txt** 上载到 **myshare** 文件共享的根目录。 `--source` 参数指定要上载的现有本地文件。

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

与创建目录一样，可以指定共享内的目录路径，将文件上载到共享内的现有目录：

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

共享中的文件最大可以为 1 TB。

### <a name="list-the-files-in-a-share"></a>列出共享中的文件
可以通过使用 `az storage file list` 命令列出共享中的文件和目录：

```azurecli
# List the files in the root of a share
az storage file list -s myshare

# List the files in a directory within a share
az storage file list -s myshare/myDir

# List the files in a path within a share
az storage file list -s myshare -p myDir/mySubDir/MySubDir2
```

### <a name="copy-files"></a>复制文件        
可以将一个文件复制到另一个文件，将一个文件复制到一个 Blob，或将一个 Blob 复制到一个文件。 例如，若要将文件复制到不同共享中的目录，请执行以下操作：        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt        
```

## <a name="next-steps"></a>后续步骤
以下是一些用于详细了解如何使用 Azure CLI 2.0 的其他资源。

* [Azure CLI 2.0 入门](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Azure CLI 2.0 命令参考](/cli/azure)
* [GitHub 上的 Azure CLI 2.0](https://github.com/Azure/azure-cli)

