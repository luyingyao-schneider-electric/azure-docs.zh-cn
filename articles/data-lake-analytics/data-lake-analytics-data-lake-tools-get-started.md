---
title: "使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本 | Microsoft 文档"
description: "了解如何安装用于 Visual Studio 的 Data Lake 工具，以及如何开发和测试 U-SQL 脚本。 "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: edmaca, yanacai
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: c26ac89bd7ef494331ba309aacf87de03506ac4c
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-develop-u-sql-scripts-using-data-lake-tools-for-visual-studio"></a>教程：使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

使用用于 Visual Studio 的 Data Lake 工具编写和测试 U-SQL 脚本。

U-SQL 是高度可缩放、高度可扩展的语言，用于准备、转换和分析 Data Lake 中的所有数据，此外还提供其他功能。 有关详细信息，请参阅 [U-SQL Reference](http://go.microsoft.com/fwlink/p/?LinkId=691348)（U-SQL 参考）。

## <a name="prerequisites"></a>先决条件
* **Visual Studio 2017（使用数据存储和处理工作负荷）、Visual Studio 2015 Update 3、Visual Studio 2013 Update 4 或 Visual Studio 2012。支持 Enterprise (Ultimate/Premium)、Professional、Community 版本；不支持 Express 版本。**
* **用于 .NET 的 Microsoft Azure SDK 2.7.1 或更高版本**。  可以使用 [Web 平台安装程序](http://www.microsoft.com/web/downloads/platform.aspx)安装它。
* **[适用于 Visual Studio 的 Data Lake 工具](http://aka.ms/adltoolsvs)**。

    安装用于 Visual Studio 的 Data Lake 工具后，可以在服务器资源管理器的“Azure”节点下看到“Data Lake Analytics”节点（按 Ctrl+Alt+S 打开服务器资源管理器）。

* **Data Lake Analytics 帐户和示例数据** Data Lake 工具不支持创建 Data Lake Analytics 帐户。 请使用 Azure 门户、Azure PowerShell、.NET SDK 或 Azure CLI 创建帐户。
为方便起见，[附录 A：用于准备教程的 PowerShell 示例](data-lake-analytics-data-lake-tools-get-started.md#appx-a-powershell-sample-for-preparing-the-tutorial)部分中提供了用于创建 Data Lake Analytics 服务及上传源数据文件的 PowerShell 脚本。

    也可浏览[通过 Azure 门户开始使用 Azure Data Lake Analytics ](data-lake-analytics-get-started-portal.md)中的以下两个部分，手动创建帐户和上载数据：

    1. [创建 Azure Data Lake Analytics 帐户](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account)。
    2. [将 SearchLog.tsv 上载到默认的 Data Lake Storage 帐户](data-lake-analytics-get-started-portal.md#prepare-source-data)。

## <a name="connect-to-azure"></a>连接到 Azure
**连接到 Data Lake Analytics**

1. 打开 Visual Studio。
2. 在“视图”菜单中，单击“服务器资源管理器”打开服务器资源管理器。 **[CTRL]+[ALT]+S**。
3. 右键单击“Azure”，单击“连接到 Microsoft Azure 订阅”，然后按照说明操作。
4. 在“服务器资源管理器”中，展开“Azure”，然后展开“Data Lake Analytics”。 如果有 Data Lake Analytics 帐户，将看到其列表。 无法从 Visual Studio 创建 Data Lake Analytics 帐户。 若要创建帐户，请参阅[通过 Azure 门户开始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md) 或[通过 Azure PowerShell 开始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-powershell.md)。

## <a name="upload-source-data-files"></a>上载源数据文件
已在本教程前面的 **先决条件** 部分中上载了一些数据。  

若要使用自己的数据，请执行以下步骤，以便从 Data Lake 工具上载数据。

**将文件上载到依赖的 Azure Data Lake 帐户**

1. 在“服务器资源管理器”中依次展开“Azure”、“Data Lake Analytics”、Data Lake Analytics 帐户、“存储帐户”。 应会看到默认的 Data Lake Storage 帐户、链接的 Data Lake Storage 帐户和链接的 Azure 存储帐户。 默认 Data Lake 帐户具有“默认存储帐户”标签。
2. 右键单击默认的 Data Lake Storage 帐户，然后单击“资源管理器”。  此时将会打开“用于 Visual Studio 的 Data Lake 工具资源管理器”窗格。  左侧显示了树视图，右侧显示了内容视图。
3. 浏览到要上载的文件所在的文件夹。
4. 右键单击任何空白区域，然后单击“上载”。

    ![U-SQL Visual Studio 项目 U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-upload-files.png)

**将文件上载到链接的 Azure Blob 存储帐户**

1. 在“服务器资源管理器”中依次展开“Azure”、“Data Lake Analytics”、Data Lake Analytics 帐户、“存储帐户”。 应会看到默认的 Data Lake Storage 帐户、链接的 Data Lake Storage 帐户和链接的 Azure 存储帐户。
2. 展开“Azure 存储帐户”。
3. 右键单击文件要上载到的容器，然后单击“资源管理器”。 如果没有容器，必须先使用 Azure 门户、Azure PowerShell 或其他工具创建一个。
4. 浏览到要上载的文件所在的文件夹。
5. 右键单击任何空白区域，然后单击“上载”。

## <a name="develop-u-sql-scripts"></a>开发 U-SQL 脚本
Data Lake Analytics 作业使用 U-SQL 语言编写而成。 若要了解有关 U-SQL 的详细信息，请参阅 [U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)和 [U-SQL 语言参考](http://go.microsoft.com/fwlink/?LinkId=691348)。

**创建并提交 Data Lake Analytics 作业**

1. 在“文件”菜单中，单击“新建”，然后单击“项目”。
2. 选择“U-SQL 项目”类型。

    ![新建 U-SQL Visual Studio 项目](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. 单击 **“确定”**。 Visual Studio 将创建包含 **Script.usql** 文件的解决方案。
4. 在 **Script.usql**中输入以下脚本：

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();

        @res =
            SELECT *
            FROM @searchlog;        

        OUTPUT @res   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    此 U-SQL 脚本通过 **Extractors.Tsv()** 读取源数据文件，然后通过 **Outputters.Csv()** 创建 csv 文件。

    除非将源文件复制到了其他位置，否则不要修改这两条路径。  如果输出文件夹不存在，Data Lake Analytics 将创建一个。

    对于存储在默认 Data Lake 帐户中的文件而言，使用相对路径更为简单。 也可使用绝对路径。  例如

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    必须使用绝对路径来访问链接的存储帐户中的文件。  链接的 Azure 存储帐户中所储存文件的语法是：

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

   > [!NOTE]
   > 当前不支持对含公共 Blob 的 Azure Blob 容器或公共容器的访问权限。  
   >
   >

    请注意以下功能：

   * **IntelliSense**

       自动填写名称，并显示行集、类、数据库、架构和用户定义对象 (UDO) 的成员。

       目录实体（数据库、架构、表、UDO 等）的 IntelliSense 与计算帐户相关。 可以在顶部工具栏中检查当前活动的计算帐户、数据库和架构，通过下拉列表切换实体。
   * **展开 * 列**

       单击 * 的右侧，可以看到 * 下面出现蓝色下划线。 将鼠标光标悬停在蓝色下划线上，然后单击向下箭头。
       ![Data Lake visual studio 工具展开](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-expand-asterisk.png)

       单击“展开列”，工具会将 * 替换为列名称。
   * **自动套用格式**

       用户可以根据代码结构，在“编辑”->“高级”下面更改 U-SQL 脚本的缩进：

     * 设置文档的格式（Ctrl+E、D）：设置整个文档的格式   
     * 设置选定内容的格式（Ctrl+K、Ctrl+F）：设置选定内容的格式。 如果未做选择，此快捷键将设置光标所在行的格式。  

       可以在“工具”->“选项”->“文本编辑器”->“SIP”->“格式”下面设置所有格式规则。  
   * **智能缩进**

       用于 Visual Studio 的 Data Lake 工具可在编写脚本时自动缩进表达式。 此功能默认已禁用，用户需要通过选中“U-SQL”->“选项和设置”->“开关”->“启用智能缩进”来启用它。
   * **转到定义并查找所有引用**

       右键单击行集/参数/列/UDO 等的名称，然后单击“转到定义”(F12) 即可导航到其定义。 单击“查找所有引用”(Shift+F12) 会显示所有引用。
   * **插入 Azure 路径**

       用于 Visual Studio 的 Data Lake 工具提供一个简单的方法，使用户无需在编写脚本时记住 Azure 文件路径和手动键入：在编辑器中单击右键，然后单击“插入 Azure 路径”。 导航到“Azure Blob 浏览器”对话框中的文件。 单击 **“确定”**。 文件路径将插入到代码中。
5. 指定 Data Lake Analytics 帐户、数据库和架构。 为了测试，可以选择“(本地)”，在本地运行脚本。 有关详细信息，请参阅[在本地运行 U-SQL](#run-u-sql-locally)。

    ![提交 U-SQL Visual Studio 项目](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

    有关详细信息，请参阅 [使用 U-SQL 目录](data-lake-analytics-use-u-sql-catalog.md)。
6. 在“解决方案资源管理器”中，右键单击“Script.usql”，然后单击“生成脚本”。 验证“输出”窗格中的结果。
7. 在“解决方案资源管理器”中，右键单击“Script.usql”，然后单击“提交脚本”。  （可选）也可以在 Script.usql 窗格中单击“提交”。  请参阅上面的屏幕截图。  单击“提交”按钮旁边的向下箭头，使用高级选项提交脚本：
8. 指定“作业名称”，确认“Analytics 帐户”，然后单击“提交”。 完成提交后，“适用于 Visual Studio 的 Data Lake 工具结果”窗口中会出现提交结果和作业链接。

    ![提交 U-SQL Visual Studio 项目](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
9. 必须单击“刷新”按钮才能看到最新的作业状态和刷新屏幕。 作业成功时，将显示“作业图表”、“元数据操作”、“状态历史记录”和“诊断”：

    ![U-SQL Visual Studio Data Lake Analytics 作业性能图表](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   * 作业摘要。 显示当前作业的摘要信息，例如：状态、进度、执行时间、运行时名称、发送者，等等。   
   * 作业详细信息。 提供此作业的详细信息，包括脚本、资源、顶点执行视图。
   * 作业图表。 提供四个图表来可视化作业的信息：进度、数据读取时间、数据写入时间、执行时间、每个节点的平均执行时间、输入吞吐量、输出吞吐量。
   * 元数据操作。 显示了所有元数据操作。
   * 状态历史记录。
   * 诊断。 用于 Visual Studio 的 Data Lake 工具将自动诊断作业执行。 当作业中出现一些错误或性能问题时，将会收到警报。 有关详细信息，请参阅“Job Diagnostics”（作业诊断）（链接有待提供）。

**检查作业状态**

1. 在服务器资源管理器中依次展开“Azure”、“Data Lake Analytics”、Data Lake Analytics 帐户名
2. 双击“作业”可列出作业。
3. 单击作业可查看状态。

**查看作业输出**

1. 在“服务器资源管理器”中依次展开 “Azure”、“Data Lake Analytics”、Data Lake Analytics 帐户、“存储帐户”，右键单击默认 Data Lake Storage 帐户，然后单击“资源管理器”。
2. 双击“输出”打开文件夹 
3. 双击“SearchLog-From-adltools.csv”。

### <a name="job-playback"></a>作业播放
使用作业播放可以观看作业执行进度，直观地检测出性能异常和瓶颈。 此功能可以在作业完成执行之前（即，作业有效运行期间）以及完成执行之后使用。 在作业执行期间进行播放可让用户播放到目前为止的进度。

**查看作业执行进度**  

1. 单击右上角的“加载配置文件”。 请参阅上面的屏幕截图。
2. 单击左下角的“播放”按钮查看作业执行进度。
3. 在播放期间，可以单击“暂停”以暂停播放，或者直接将进度条拖到特定的位置。

### <a name="heat-map"></a>热度地图
用于 Visual Studio 的 Data Lake 工具允许用户在作业视图中选择颜色覆盖层来指示每个阶段的进度、数据 I/O、执行时间和 I/O 吞吐量。 通过此方法，用户可以直接且直观地找到潜在问题和作业属性的分布。 可以从下拉列表中选择要显示的数据源。  

## <a name="run-u-sql-locally"></a>在本地运行 U-SQL

与在 Azure Data Lake 服务中一样，可以使用用于 Visual Studio 的 Azure Data Lake 工具和 Azure Data Lake U-SQL SDK 在工作站上运行 U-SQL 作业。 这两个本地运行功能可节省测试和调试 U-SQL 作业的时间。 

* [使用本地运行和 Azure Data Lake U-SQL SDK 来测试及调试 U-SQL 作业](data-lake-analytics-data-lake-tools-local-run.md)


## <a name="see-also"></a>另请参阅
若要借助不同的工具开始使用 Data Lake Analytics，请参阅：

* [通过 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
* [通过 Azure PowerShell 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-powershell.md)
* [通过 .NET SDK 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-net-sdk.md)
* [Debug C# code in U-SQL jobs（在 U-SQL 作业中调试 C# 代码）](data-lake-analytics-debug-u-sql-jobs.md)

若要了解用于 Visual Studio 的 Data Lake 工具代码，请参阅[使用用于 Visual Studio 的 Data Lake 工具代码](data-lake-analytics-data-lake-tools-for-vscode.md)。

查看更多开发主题：

* [Analyze weblogs using Data Lake Analytics（使用 Data Lake Analytics 分析网络日志）](data-lake-analytics-analyze-weblogs.md)
* [使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)
* [为 Data Lake Analytics 作业开发 U-SQL 用户定义的运算符](data-lake-analytics-u-sql-develop-user-defined-operators.md)

## <a name="appx-a-powershell-sample-for-preparing-the-tutorial"></a>附录 A：用于准备教程的 PowerShell 示例
以下 PowerShell 脚本可以准备 Azure Data Lake Analytics 帐户和源数据，这样就可以直接跳到 [开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md#develop-u-sql-scripts)部分。

    #region - used for creating Azure service names
    $nameToken = "<Enter an alias>"
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion

    #region - service names
    $resourceGroupName = $namePrefix + "rg"
    $dataLakeStoreName = $namePrefix + "adas"
    $dataLakeAnalyticsName = $namePrefix + "adla"
    $location = "East US 2"
    #endregion


    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an Azure Data Lake Analytics service account
    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location

    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName

    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsName  
    #endregion

    #region - prepare the source data
    Write-Host "Import the source data ..."  -ForegroundColor Green
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

    Write-Host "List the source data ..."  -ForegroundColor Green
    Get-AzureRmDataLakeStoreChildItem -Account $dataLakeStoreName -Path  "/Samples/Data/"
    #endregion

