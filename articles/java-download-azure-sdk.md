---
title: "下载 Azure SDK for Java | Microsoft Docs"
description: "了解如何下载 Azure SDK for Java，其中附带了为 Maven 项目提供的示例代码。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 4b8f8fe6-1b26-4bb4-9be9-6ae757a59e66
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;asirveda
translationtype: Human Translation
ms.sourcegitcommit: 0933eabf99ef94f2c4b425f1435530edfde65e3f
ms.openlocfilehash: 9e55eb52797f1363cbb65050574e549c575ca5d8
ms.lasthandoff: 01/14/2017


---
# <a name="download-the-azure-sdk-for-java"></a>下载 Azure SDK for Java
本文包含有关下载和安装适用于 Java 的 Azure 管理库的说明。

> [!NOTE]
> 根据 [Apache 许可证 2.0 版][license]分发适用于 Java 的 Azure 管理库。
>

## <a name="azure-libraries-for-java---manual-download"></a>适用于 Java 的 Azure 库 – 手动下载
若要手动安装适用于 Java 的 Azure 管理库，请单击 <http://go.microsoft.com/fwlink/?LinkId=690320> 下载包含所有库及所有依赖项的 ZIP 文件。

将 zip 文件下载到计算机之后，请提取其内容，然后使用以下选项之一将 JAR 文件添加你的项目中：

* 在 Eclipse 或 IntelliJ 中将 JAR 文件导入 Java 项目。
* 在 Eclipe 或 IntelliJ 中配置 Java 项目的生成路径，以包含 JAR 文件的路径。

> [!NOTE]
> 有关许可证和其他信息，请参阅 ZIP 文件中的 license.txt 和 ThirdPartyNotices.txt 文件。
>

## <a name="azure-management-libraries-for-java---building-with-maven"></a>适用于 Java 的 Azure 管理库 - 使用 Maven 生成
### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>步骤 1 - 将项目设置为使用 Maven 来生成
若要在 Eclipse 中创建使用适用于 Java 的 Azure 管理库的 Maven 项目，请遵循[适用于 Java 的 Azure 管理库入门][maven-getting-started]一文中的说明。

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>步骤 2 - 使用必要的依赖项配置 Maven 设置
将项目配置为使用 Maven 生成之后，可以使用类似于以下示例的语法，将必要的依赖项添加到 pom.xml 文件中。 请注意，你无需添加以下示例中所列的每个依赖项，而只需添加项目所需的特定依赖项。

> [!NOTE]
> 在以下示例中的每个 `<version>` 元素中，将此示例中的“n.n.n”占位符替换为有效版本号，可从 [Maven 上的 Azure 库存储库]获取此版本号。
>
>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

## <a name="see-also"></a>另请参阅
有关 Azure Toolkits for Java IDE 的详细信息，请参阅以下链接：

* [用于 Eclipse 的 Azure 工具包]
  * [安装用于 Eclipse 的 Azure 工具包]
  * [在 Eclipse 中创建 Azure 的 Hello World Web 应用]
  * [用于 Eclipse 的 Azure 工具包的新增功能]
* [用于 IntelliJ 的 Azure 工具包]
  * [安装用于 IntelliJ 的 Azure 工具包]
  * [在 IntelliJ 中创建 Azure 的 Hello World Web 应用]
  * [用于 IntelliJ 的 Azure 工具包的新增功能]

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]。

> [!NOTE]
> 有关在 Eclipse 中设置生成路径的详细信息，请参阅 Eclipse 网站上的 [Java 生成路径]一文。
>

<!-- URL List -->

[用于 Eclipse 的 Azure 工具包]: ./azure-toolkit-for-eclipse.md
[用于 IntelliJ 的 Azure 工具包]: ./azure-toolkit-for-intellij.md
[在 Eclipse 中创建 Azure 的 Hello World Web 应用]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[在 IntelliJ 中创建 Azure 的 Hello World Web 应用]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[安装用于 Eclipse 的 Azure 工具包]: ./azure-toolkit-for-eclipse-installation.md
[安装用于 IntelliJ 的 Azure 工具包]: ./azure-toolkit-for-intellij-installation.md
[用于 Eclipse 的 Azure 工具包的新增功能]: ./azure-toolkit-for-eclipse-whats-new.md
[用于 IntelliJ 的 Azure 工具包的新增功能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 开发人员中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[Maven 上的 Azure 库存储库]: http://go.microsoft.com/fwlink/?LinkID=286274
[Java 生成路径]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998

