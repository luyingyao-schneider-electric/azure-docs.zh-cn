---
title: "Azure Automation DSC 入门 | Microsoft Docs"
description: "Azure 自动化所需状态配置 (DSC) 中最常见任务的说明和示例"
services: automation
documentationcenter: na
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: a3816593-70a3-403b-9a43-d5555fd2cee2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/21/2016
ms.author: magoedte;eslesar
translationtype: Human Translation
ms.sourcegitcommit: 98f5a017221f0aaba04e2b90afc674e4c42d1bb3
ms.openlocfilehash: 282cb0a7705d0650852699ed5fe09f61fda42d0f


---
# <a name="getting-started-with-azure-automation-dsc"></a>Azure Automation DSC 入门
本主题说明如何使用 Azure Automation Desired State Configuration (DSC) 执行最常见的任务，例如创建、导入、编译配置，载入要管理的计算机和查看报表。 若要大致了解什么是 Azure Automation DSC，请参阅 [ DSC 概述](automation-dsc-overview.md)。 有关 DSC 文档，请参阅 [Windows PowerShell Desired State Configuration 概述](https://msdn.microsoft.com/PowerShell/dsc/overview)。

本主题分步介绍了如何使用 Azure Automation DSC。 如果你需要一个已经设置好的示例环境，而不想按照本主题所述步骤进行操作，则可使用[以下 ARM 模板](https://github.com/azureautomation/automation-packs/tree/master/102-sample-automation-setup)。 此模板设置了一个完整的 Azure Automation DSC 环境，包括一个由 Azure Automation DSC 管理的 Azure VM。

## <a name="prerequisites"></a>先决条件
若要完成本主题中的示例，需要具备以下条件：

* 一个 Azure 自动化帐户。 有关如何创建 Azure 自动化运行方式帐户的说明，请参阅 [Azure 运行方式帐户](automation-sec-configure-azure-runas-account.md)。
* 一个运行 Windows Server 2008 R2 或更高版本的 Azure Resource Manager VM（非经典）。 如需创建 VM 的说明，请参阅[在 Azure 门户中创建第一个 Windows 虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>创建 DSC 配置
我们将创建一个简单的 [DSC 配置](https://msdn.microsoft.com/powershell/dsc/configurations)，以确保存在（或不存在）**Web-Server** Windows 功能 (IIS)，具体取决于分配节点的方式。

1. 启动 Windows PowerShell ISE（或任何文本编辑器）。
2. 键入以下文本：
   
    ```powershell
    configuration TestConfig
    {
        Node WebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
   
            }
        }
   
        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
   
            }
        }
        }
    ```
3. 将文件另存为 `TestConfig.ps1`。

此配置调用每个节点块中的一个资源（[WindowsFeature 资源](https://msdn.microsoft.com/powershell/dsc/windowsfeatureresource)），以确保存在（或不存在）**Web-Server** 功能。

## <a name="importing-a-configuration-into-azure-automation"></a>将配置导入 Azure 自动化
接下来，我们会将配置导入自动化帐户。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单中，单击“所有资源”，然后单击自动化帐户的名称。
3. 在“自动化帐户”边栏选项卡中，单击“DSC 配置”。
4. 在“DSC 配置”边栏选项卡中，单击“添加配置”。
5. 在“导入配置”边栏选项卡中，浏览到计算机上的 `TestConfig.ps1` 文件。
   
    ![**导入配置**边栏选项卡的屏幕截图](./media/automation-dsc-getting-started/AddConfig.png)
6. 单击 **“确定”**。

## <a name="viewing-a-configuration-in-azure-automation"></a>查看 Azure 自动化中的配置
在导入配置以后，即可在 Azure 门户中查看该配置。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单中，单击“所有资源”，然后单击自动化帐户的名称。
3. 在“自动化帐户”边栏选项卡中，单击“DSC 配置”
4. 在“DSC 配置”边栏选项卡中，单击“TestConfig”（这是你在前一过程中导入的配置的名称）。
5. 在“TestConfig 配置”边栏选项卡中，单击“查看配置源”。
   
    ![“TestConfig 配置”边栏选项卡的屏幕快照](./media/automation-dsc-getting-started/ViewConfigSource.png)
   
    此时会打开“TestConfig 配置源”边栏选项卡，显示配置的 PowerShell 代码。

## <a name="compiling-a-configuration-in-azure-automation"></a>编译 Azure 自动化中的配置
在将所需状态应用到某个节点之前，必须将定义该状态的 DSC 配置编译成一个或多个节点配置（MOF 文档），然后放置在自动化 DSC 拉取服务器上。 如需在 Azure Automation DSC 中进行配置编译的更详细说明，请参阅[在 Azure Automation DSC 中编译配置](automation-dsc-compile.md)。 有关编译配置的详细信息，请参阅 [DSC 配置](https://msdn.microsoft.com/PowerShell/DSC/configurations)。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单中，单击“所有资源”，然后单击自动化帐户的名称。
3. 在“自动化帐户”边栏选项卡中，单击“DSC 配置”
4. 在“DSC 配置”边栏选项卡中，单击“TestConfig”（以前导入的配置的名称）。
5. 在“TestConfig 配置”边栏选项卡中，单击“编译”，然后单击“是”。 此时会启动编译作业。
   
    ![“TestConfig 配置”边栏选项卡的屏幕快照，突出显示编译按钮](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> 在 Azure 自动化中编译配置时，会自动将任何创建的节点配置 MOF 部署到拉取服务器。
> 
> 

## <a name="viewing-a-compilation-job"></a>查看编译作业
启动编译作业以后，即可在“配置”边栏选项卡的“编译作业”磁贴中查看该作业。 “编译作业”磁贴显示当前正在运行的、已完成的以及失败的作业。 打开编译作业边栏选项卡时，将会显示有关该作业的信息，包括遇到的任何错误或警告、在配置中使用的输入参数，以及编译日志。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单中，单击“所有资源”，然后单击自动化帐户的名称。
3. 在“自动化帐户”边栏选项卡中，单击“DSC 配置”。
4. 在“DSC 配置”边栏选项卡中，单击“TestConfig”（以前导入的配置的名称）。
5. 在“TestConfig 配置”边栏选项卡的“编译作业”磁贴上，单击列出的任何作业。 此时会打开“编译作业”边栏选项卡，其中标记了启动编译作业的日期。
   
    ![“编译作业”边栏选项卡的屏幕快照](./media/automation-dsc-getting-started/CompilationJob.png)
6. 单击“编译作业”边栏选项卡中的任意磁贴，了解有关该作业的更多详细信息。

## <a name="viewing-node-configurations"></a>查看节点配置
成功完成编译作业以后，将会创建一个或多个新的节点配置。 节点配置是一个 MOF 文档，该文档部署到拉取服务器上，可供一个或多个节点拉取和应用。 你可以在“DSC 节点配置”边栏选项卡中查看自动化帐户中的节点配置。 节点配置的名称格式为 *ConfigurationName*.*NodeName*。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单中，单击“所有资源”，然后单击自动化帐户的名称。
3. 在“自动化帐户”边栏选项卡中，单击“DSC 节点配置”。
   
    ![“DSC 节点配置”边栏选项卡的屏幕快照](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-dsc"></a>登记使用 Azure Automation DSC 管理的 Azure VM
你可以使用 Azure Automation DSC 来管理 Azure VM（包括经 VM 和 Resource Manager VM）、本地 VM、Linux 计算机、AWS VM，以及本地物理机。 在本主题中，我们将介绍如何仅登记 Azure Resource Manager VM。 有关如何登记其他类型的计算机的信息，请参阅[登记由 Azure Automation DSC 管理的计算机](automation-dsc-onboarding.md)。

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-dsc"></a>登记由 Azure Automation DSC 管理的 Azure Resource Manager VM
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单中，单击“所有资源”，然后单击自动化帐户的名称。
3. 在“自动化帐户”边栏选项卡中，单击“DSC 节点”。
4. 在“DSC 节点”边栏选项卡中，单击“添加 Azure VM”。
   
    ![“DSC 节点”边栏选项卡的屏幕快照，突出显示“添加 Azure VM”按钮](./media/automation-dsc-getting-started/OnboardVM.png)
5. 在“添加 Azure VM”边栏选项卡中，单击“选择要登记的虚拟机”。
6. 在“选择 VM”边栏选项卡中，选择要登记的 VM，然后单击“确定”。
   
   > [!IMPORTANT]
   > 该 VM 必须是运行 Windows Server 2008 R2 或更高版本的 Azure Resource Manager VM。
   > 
   > 
7. 在“添加 Azure VM”边栏选项卡中，单击“配置注册数据”。
8. 在“注册”边栏选项卡的“节点配置名称”框中，输入要应用到 VM 的节点配置的名称。 该名称必须与自动化帐户中节点配置的名称完全匹配。 你可以选择在此时提供名称。 你可以在登记节点以后更改分配的节点配置。
   勾选“根据需要重新启动节点”，然后单击“确定”。
   
    ![“注册”边栏选项卡的屏幕快照](./media/automation-dsc-getting-started/RegisterVM.png)
   
    你指定的节点配置将按“配置模式频率”指定的时间间隔应用到 VM，而 VM 则会按“刷新频率”指定的时间间隔检查节点配置的更新。 有关如何使用这些值的详细信息，请参阅[配置本地配置管理器](https://msdn.microsoft.com/PowerShell/DSC/metaConfig)。
9. 在“添加 Azure VM”边栏选项卡中，单击“创建”。

Azure 将启动登记 VM 的过程。 完成后，VM 会显示在自动化帐户中的“DSC 节点”边栏选项卡中。

## <a name="viewing-the-list-of-dsc-nodes"></a>查看 DSC 节点列表
在“DSC 节点”边栏选项卡中，你可以查看所有已登记并可在自动化帐户中进行管理的计算机的列表。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单中，单击“所有资源”，然后单击自动化帐户的名称。
3. 在“自动化帐户”边栏选项卡中，单击“DSC 节点”。

## <a name="viewing-reports-for-dsc-nodes"></a>查看 DSC 节点的报告
每次由 Azure Automation DSC 在托管节点上执行一致性检查时，该节点会将状态报告发送回拉取服务器。 你可以在边栏选项卡上查看该节点的这些报告。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单中，单击“所有资源”，然后单击自动化帐户的名称。
3. 在“自动化帐户”边栏选项卡中，单击“DSC 节点”。
4. 在“报告”磁贴中，单击列表中的任意报告。
   
    ![“报告”边栏选项卡的屏幕快照](./media/automation-dsc-getting-started/NodeReport.png)

在单个报告的边栏选项卡中，你可以看到对应的一致性检查的下列状态信息：

* 报告状态 - 节点是否“合规”、配置是否“失败”、节点是否“不合规”（当节点处于 **applyandmonitor** 模式且计算机不在所需状态时）。
* 一致性检查的开始时间。
* 一致性检查的总运行时间。
* 一致性检查的类型。
* 任何错误，包括错误代码和错误消息。 
* 在配置中使用的任何 DSC 资源，以及每个资源的状态（节点是否处于该资源的期望状态）- 可以单击每个资源以获取该资源的更多详细信息。
* 节点的名称、IP 地址和配置模式。

还可以通过单击“查看原始报告”来查看节点发送到服务器的实际数据。 有关如何使用该数据的详细信息，请参阅[使用 DSC 报表服务器](https://msdn.microsoft.com/powershell/dsc/reportserver)。

在登记节点以后，可能需要一段时间才会提供第一个报告。 在登记节点以后，可能需要等待长达 30 分钟的时间才能获得第一个报告。

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>为节点重新分配其他节点配置
为节点分配配置时，你可以让其使用不同于初始配置的节点配置。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单中，单击“所有资源”，然后单击自动化帐户的名称。
3. 在“自动化帐户”边栏选项卡中，单击“DSC 节点”。
4. 在“DSC 节点”边栏选项卡中，单击要重新分配的节点的名称。
5. 在该节点的边栏选项卡中，单击“分配节点”。
   
    ![“节点”边栏选项卡的屏幕快照，突出显示“分配节点”按钮](./media/automation-dsc-getting-started/AssignNode.png)
6. 在“分配节点配置”边栏选项卡中，选择要向其分配节点的节点配置，然后单击“确定”。
   
    ![“分配节点配置”边栏选项卡的屏幕快照](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>注销节点
如果你不想让某个节点继续受 Azure Automation DSC 管理，则可注销该节点。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单中，单击“所有资源”，然后单击自动化帐户的名称。
3. 在“自动化帐户”边栏选项卡中，单击“DSC 节点”。
4. 在“DSC 节点”边栏选项卡中，单击要注销的节点的名称。
5. 在该节点的边栏选项卡中，单击“注销”。
   
    ![“节点”边栏选项卡的屏幕快照，突出显示“注销”按钮](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>相关文章
* [Azure Automation DSC 概述](automation-dsc-overview.md)
* [登记由 Azure Automation DSC 管理的计算机](automation-dsc-onboarding.md)
* [Windows PowerShell Desired State Configuration 概述](https://msdn.microsoft.com/powershell/dsc/overview)
* [Azure Automation DSC cmdlet](https://msdn.microsoft.com/library/mt244122.aspx)
* [ 定价](https://azure.microsoft.com/pricing/details/automation/)




<!--HONumber=Nov16_HO4-->


