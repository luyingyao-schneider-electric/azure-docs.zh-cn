---
title: "教程：使用 .NET API 创建包含复制活动的管道 | Microsoft 文档"
description: "本教程使用 .NET API 创建包含复制活动的 Azure 数据工厂管道。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 58fc4007-b46d-4c8e-a279-cb9e479b3e2b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 0664888dbb14aaa353d5d126cdf799b62711d71f
ms.lasthandoff: 04/12/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>教程：使用 .NET API 创建包含复制活动的管道
> [!div class="op_single_selector"]
> * [概述与先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [复制向导](data-factory-copy-data-wizard-tutorial.md)
> * [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 模板](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

本教程演示如何使用 .NET API 创建和监视 Azure 数据工厂。 数据工厂中的管道使用复制活动将数据从 Azure Blob 存储复制到 SQL 数据库。

复制活动在 Azure 数据工厂中执行数据移动。 该活动由全球可用的服务提供支持，能以安全、可靠、可缩放的方式在各种数据存储区间复制数据。 有关复制活动的详细信息，请参阅 [Data Movement Activities](data-factory-data-movement-activities.md) （数据移动活动）。

> [!NOTE]
> 本文不会介绍所有数据工厂 .NET API。 有关数据工厂 .NET SDK 的详细信息，请参阅 [Data Factory .NET API Reference](https://msdn.microsoft.com/library/mt415893.aspx) （数据工厂 .NET API 参考）。
> 
> 本教程中的数据管道将数据从源数据存储复制到目标数据存储。 该管道并不通过转换输入数据来生成输出数据。 有关如何使用 Azure 数据工厂来转换数据的教程，请参阅[教程：生成使用 Hadoop 群集来转换数据的管道](data-factory-build-your-first-pipeline.md)。


## <a name="prerequisites"></a>先决条件
* 请阅读 [教程概述和先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 了解教程概述，并完成 **先决条件** 步骤。
* Visual Studio 2012、2013 或 2015
* 下载并安装 [Azure .NET SDK](http://azure.microsoft.com/downloads/)
* Azure PowerShell。 遵循 [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) （如何安装和配置 Azure PowerShell）一文中的说明，在计算机上安装 Azure PowerShell。 使用 Azure PowerShell 创建 Azure Active Directory 应用程序。

### <a name="create-an-application-in-azure-active-directory"></a>在 Azure Active Directory 中创建应用程序
创建一个 Azure Active Directory 应用程序，为该应用程序创建服务主体，然后将其分配到 **数据工厂参与者** 角色。

1. 启动 **PowerShell**。
2. 运行以下命令并输入用于登录 Azure 门户的用户名和密码。

    ```PowerShell
    Login-AzureRmAccount
    ```
3. 运行以下命令查看此帐户的所有订阅。

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. 运行以下命令选择要使用的订阅。 将 **&lt;NameOfAzureSubscription**&gt; 替换为 Azure 订阅的名称。

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```

   > [!IMPORTANT]
   > 记下此命令的输出中的 **SubscriptionId** 和 **TenantId**。

5. 在 PowerShell 中运行以下命令，创建名为 **ADFTutorialResourceGroup** 的 Azure 资源组。

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    如果资源组已存在，请指定是要更新它 (Y) 还是保留原样 (N)。

    如果使用不同的资源组，需使用该资源组的名称取代本教程中的 ADFTutorialResourceGroup。
6. 创建 Azure Active Directory 应用程序。

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
    ```

    如果看到以下错误，请指定不同的 URL，然后再次运行该命令。
    
    ```PowerShell
    Another object with the same value for property identifierUris already exists.
    ```
7. 创建 AD 服务主体。

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. 将服务主体添加到 **数据工厂参与者** 角色。

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. 获取应用程序 ID。

    ```PowerShell
    $azureAdApplication    
    ```
    记下输出中的应用程序 ID (applicationID)。

应该通过相应的步骤获取以下四个值：

* 租户 ID
* 订阅 ID
* 应用程序 ID
* 密码（在第一条命令中指定）

## <a name="walkthrough"></a>演练
1. 使用 Visual Studio 2012/2013/2015 创建 C# .NET 控制台应用程序。
   1. 启动 **Visual Studio** 2012/2013/2015。
   2. 单击“文件”，指向“新建”并单击“项目”。
   3. 展开“模板”，然后选择“Visual C#”。 本演练中使用的是 C#，但可以使用任何 .NET 语言。
   4. 从右侧项目类型列表中选择“控制台应用程序”。
   5. 在“名称”中输入 **DataFactoryAPITestApp** 。
   6. 在“位置”中选择“C:\ADFGetStarted”。
   7. 单击“确定”以创建该项目  。
2. 单击“工具”，指向“NuGet 包管理器”，然后单击“包管理器控制台”。
3. 在“包管理器控制台”中执行以下步骤：
   1. 运行以下命令安装数据工厂包：`Install-Package Microsoft.Azure.Management.DataFactories`
   2. 运行以下命令安装 Azure Active Directory 包（因为要在代码中使用 Active Directory API）：`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. 将以下 **appSettings** 节添加到 **App.config** 文件。 这些设置由以下帮助器方法使用： **GetAuthorizationHeader**。

    将 **&lt;Application ID&gt;**、**&lt;Password&gt;**、**&lt;Subscription ID&gt;** 和 **&lt;tenant ID&gt;** 的值替换为自己的值。

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```

5. 将以下 **using** 语句添加到项目中的源文件 (Program.cs)。

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```

6. 将以下用于创建 **DataPipelineManagementClient** 类实例的代码添加到 **Main** 方法。 将使用此对象创建数据工厂、链接服务、输入和输出数据集以及管道。 此外，在运行时将使用此对象监视数据集切片。

    ```csharp
    // create data factory management client
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > 将 **resourceGroupName** 的值替换为 Azure 资源组的名称。
   >
   > 将数据工厂的名称 (dataFactoryName) 更新为唯一名称。 数据工厂的名称必须全局唯一。 有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。

7. 将以下用于创建**数据工厂**的代码添加到 **Main** 方法。

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```

8. 将以下用于创建 **Azure 存储链接服务**的代码添加到 **Main** 方法。

   > [!IMPORTANT]
   > 将 **storageaccountname** 和 **accountkey** 分别替换为 Azure 存储帐户的名称和密钥。

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```

9. 将以下用于创建 **Azure SQL 链接服务**的代码添加到 **Main** 方法。

   > [!IMPORTANT]
   > 将 **servername**、**databasename**、**username** 和 **password** 分别替换为 Azure SQL 服务器名称、数据库名称、用户名和密码。

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

10. 将以下用于创建**输入和输出数据集**的代码添加到 **Main** 方法。

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetAzureSqlDestination";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },

                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
                    },
                    Availability = new Availability()
                    {
                        Frequency = SchedulePeriod.Hour,
                        Interval = 1,
                    },
                }
            }
        });
    ```

11. 将以下用于**创建和激活管道**的代码添加到 **Main** 方法。 此管道包含一个源为 **BlobSource**、接收器为 **BlobSink** 的 **CopyActivity**。

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "ADFTutorialPipeline";

    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new PipelineCreateOrUpdateParameters()
        {
            Pipeline = new Pipeline()
            {
                Name = PipelineName,
                Properties = new PipelineProperties()
                {
                    Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                    End = PipelineActivePeriodEndTime,

                    Activities = new List<Activity>()
                    {
                        new Activity()
                        {
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
                                    Name = Dataset_Source
                                }
                            },
                            Outputs = new List<ActivityOutput>()
                            {
                                new ActivityOutput()
                                {
                                    Name = Dataset_Destination
                                }
                            },
                            TypeProperties = new CopyActivity()
                            {
                                Source = new BlobSource(),
                                Sink = new BlobSink()
                                {
                                    WriteBatchSize = 10000,
                                    WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                }
                            }
                        }
                    }
                }
            }
        });
    ```

12. 将以下代码添加 **Main** 方法，获取输出数据集的数据切片状态。 本示例预期只有切片。

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;

    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");        
        // wait before the next status check
        Thread.Sleep(1000 * 12);

        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });

        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```

13. 将以下用于获取数据切片运行详细信息的代码添加到 **Main** 方法。

    ```csharp
    Console.WriteLine("Getting run details of a data slice");

    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();

    var datasliceRunListResponse = client.DataSliceRuns.List(
            resourceGroupName,
            dataFactoryName,
            Dataset_Destination,
            new DataSliceRunListParameters()
            {
                DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
            }
        );

    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }

    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```

14. 将 **Main** 方法使用的以下帮助器方法添加到 **Program** 类。

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. 在解决方案资源管理器中展开项目 (DataFactoryAPITestApp)，右键单击“引用”，然后单击“添加引用”。 选中“System.Configuration”程序集对应的复选框。 然后单击“确定”。
16. 生成控制台应用程序。 在菜单中单击“生成”，然后单击“生成解决方案”。
17. 确认 Azure Blob 存储中的 **adftutorial** 容器内至少有一个文件。 如果没有，请在记事本中创建包含以下内容的 **Emp.txt** 文件，然后将它上载到 adftutorial 容器。

    ```
    John, Doe
    Jane, Doe
    ```
18. 在菜单中单击“调试” -> “开始调试”运行示例。 看到“正在获取数据切片的运行详细信息”时，请等待几分钟，然后按 **ENTER**。
19. 使用 Azure 门户验证是否创建了包含以下项目的数据工厂 **APITutorialFactory** ：
   * 链接服务：**LinkedService_AzureStorage**
   * 数据集：**DatasetBlobSource** 和 **DatasetBlobDestination**。
   * 管道： **PipelineBlobSample**
20. 验证是否在指定的 Azure SQL 数据库中的“emp”表内创建了两条员工记录。

## <a name="next-steps"></a>后续步骤
| 主题 | 说明 |
|:--- |:--- |
| [管道](data-factory-create-pipelines.md) |帮助了解 Azure 数据工厂中的管道和活动 |
| [数据集](data-factory-create-datasets.md) |还有助于了解 Azure 数据工厂中的数据集。 |
| [计划和执行](data-factory-scheduling-and-execution.md) |本文介绍 Azure 数据工厂应用程序模型的计划方面和执行方面。 |
[数据工厂 .NET API 参考](/dotnet/api/) | 详细介绍了数据工厂 .NET SDK（查找树视图中的 Microsoft.Azure.Management.DataFactories.Models）。

