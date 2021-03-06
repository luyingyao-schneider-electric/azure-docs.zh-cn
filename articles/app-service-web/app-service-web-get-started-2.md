---
title: "在第一个 Web 应用中添加功能 | Microsoft 文档"
description: "在短时间内将一些新奇功能添加到你的第一个 Web 应用。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 542671c2-22f0-4f20-8b4b-fa477264c492
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/12/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 34cccf6a0962f72e04a90516fa7643db2533d85d
ms.lasthandoff: 04/06/2017


---
# <a name="add-functionality-to-your-first-web-app"></a>在第一个 Web 应用中添加功能
通过[在 5 分钟内将第一个 Web 应用部署到 Azure](app-service-web-get-started-dotnet.md) 教程，你已将一个示例 Web 应用部署到 [Azure 应用服务](../app-service/app-service-value-prop-what-is.md)。 在本文中，你将会快速地在所部署的 Web 应用中添加一些强大功能。 只需几分钟，你就能够：

* 强制实施用户身份验证
* 自动缩放应用
* 接收有关应用性能的警报

无论在前一篇文章中部署哪一个示例应用，都可以遵循以下的操作。

本教程中的三个活动只是你在将 Web 应用放入 App Service 时可以使用的众多有用功能中的几个例子。 许多功能已在 **免费** 层（运行你的第一个 Web 应用的层）中提供，可以使用你的试用信用额度来试用只能在更高定价层中使用的功能。 除非你显式将 **免费** 层更改为其他定价层，否则你的 Web 应用将保留在免费层，因此，你可以高枕无忧。

> [!NOTE]
> 使用 Azure CLI 创建的 Web 应用在 **免费** 层中运行，该层只允许一个存在资源配额的共享 VM 实例。 有关 **免费** 层提供的功能的详细信息，请参阅 [App Service 限制](../azure-subscription-service-limits.md#app-service-limits)。
> 
> 

## <a name="authenticate-your-users"></a>对用户进行身份验证
现在，让我们看看将身份验证添加到应用有多么容易（有关更多信息，请阅读 [App Service](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/)。

1. 在应用的门户边栏选项卡（刚打开）中，单击“设置” > “身份验证/授权”。  
    ![身份验证 - 设置边栏选项卡](./media/app-service-web-get-started/aad-login-settings.png)
2. 单击“ **打开** ”，以打开身份验证。  
3. 在“身份验证提供程序”中，单击“Azure Active Directory”。  
    ![身份验证 - 选择 Azure AD](./media/app-service-web-get-started/aad-login-config.png)
4. 在“Azure Active Directory 设置”边栏选项卡中，单击“快速”，然后单击“确定”。 默认设置在默认目录中创建新的 Azure AD 应用程序。  
    ![身份验证 - 快速配置](./media/app-service-web-get-started/aad-login-express.png)
5. 单击“ **保存**”。  
    ![身份验证 - 保存配置](./media/app-service-web-get-started/aad-login-save.png)
   
    成功更改之后，你将看到通知铃铛变成绿色，以及一条友好的消息。
6. 返回应用的门户边栏选项卡，单击“URL”链接（或菜单栏中的“浏览”）。 此链接是 HTTP 地址。  
    ![身份验证 - 浏览到 URL](./media/app-service-web-get-started/aad-login-browse-click.png)  
    但是在新的选项卡中打开应用后，URL 框将重定向数次并通过 HTTPS 地址到达应用。 现在看到你已登录到 Azure 订阅，并且将在应用中自动对你执行自动身份验证。  
    ![身份验证 - 已登录](./media/app-service-web-get-started/aad-login-browse-http-postclick.png)  
    因此，如果你在另一个浏览器中打开一个未经身份验证的会话，将在导航到同一 URL 时看到登录屏幕。  
    <!-- ![Authenticate - login page](./media/app-service-web-get-started/aad-login-browse.png)  -->
    如果你从未使用过 Azure Active Directory，默认目录可能没有任何 Azure AD 用户。 在这种情况下，此处的唯一帐户可能就是 Azure 订阅所用的 Microsoft 帐户。 这就是为什么前面能在相同的浏览器中自动登录此应用。
   你也可以使用相同的 Microsoft 帐户在此登录页面上登录。

祝贺你，现在你可以对传入 Web 应用的所有流量进行身份验证。

你可能已注意到在“ **身份验证/授权** ”边栏选项卡中能做的不止于此，例如：

* 启用社交登录
* 启用多个登录选项
* 更改当用户首次导航到应用时的默认行为

App Service 针对某些常见的身份验证要求提供周全的解决方案，所以不需要自行提供身份验证逻辑。
有关详细信息，请参阅 [App Service 身份验证/授权](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/)。

## <a name="scale-your-app-automatically-based-on-demand"></a>根据需要自动缩放应用
接下来，让我们自动缩放应用，以便它能够自动调整其容量来响应用户需求（有关更多信息，请参阅[在 Azure 中增加应用](web-sites-scale.md)和[手动或自动缩放实例计数](../monitoring-and-diagnostics/insights-how-to-scale.md)）。

简单而言，可以通过两种方式缩放 Web 应用：

* [向上缩放](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：获取更多 CPU、内存、磁盘空间、专用 VM 等附加功能、自定义域和证书、过渡槽、自动缩放，等等。 可以通过更改应用所属的应用服务计划的定价层来向上缩放。
* [向外缩放](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：增加运行应用的 VM 实例数。
  可以根据定价层，最多向外缩放到 50 个实例。

毋需赘述，让我们马上设置自动缩放。

1. 首先，向上缩放以启用自动缩放。 在应用的门户边栏选项卡中，单击“设置” > “向上缩放(应用服务计划)”。  
    ![向上缩放 - 设置边栏选项卡](./media/app-service-web-get-started/scale-up-settings.png)
2. 滚动并选择“S1 标准”层（这是支持自动缩放的最低层（屏幕截图中所圈选）），然后单击“选择”。  
    ![向上缩放 - 选择层](./media/app-service-web-get-started/scale-up-select.png)
   
    现已完成向上缩放。
   
   > [!IMPORTANT]
   > 这一层将消耗你的免费试用额度。 如果你使用即用即付帐户，将从你的帐户中扣费。
   > 
   > 
3. 接下来，配置自动缩放。 在应用的门户边栏选项卡中，单击“设置” > “向外缩放(应用服务计划)”。  
    ![向外缩放 - 设置边栏选项卡](./media/app-service-web-get-started/scale-out-settings.png)
4. 将“缩放依据”更改为“CPU 百分比”。 下拉列表下面的滑块会相应地更新。 然后，定义介于 **1** 与 **2** 之间的“实例”范围，以及介于 **40** 与 **80** 之间的“目标范围”。 为此，可以在框中键入值或移动滑块。  
    ![向外缩放 - 配置自动缩放](./media/app-service-web-get-started/scale-out-configure.png)
   
    根据此配置，当 CPU 使用率高于 80% 时，应用将自动向外缩放；当 CPU 使用率低于 40% 时则缩小。
5. 单击菜单栏中的“ **保存** ”。

祝贺你，你的应用已在自动缩放。

你可能已注意到在“ **缩放设置** ”边栏选项卡中能做的不止于此，例如：

* 手动缩放到特定的实例数
* 根据其他性能度量值（例如内存百分比或磁盘队列）进行缩放
* 自定义触发性能规则时的缩放行为
* 按计划自动缩放
* 设置未来事件的自动缩放行为

有关增加应用的详细信息，请参阅 [在 Azure 中增加应用](web-sites-scale.md)。 有关向外缩放的详细信息，请参阅 [Scale instance count manually or automatically](../monitoring-and-diagnostics/insights-how-to-scale.md)（手动或自动缩放实例计数）。

## <a name="receive-alerts-for-your-app"></a>接收应用的警报
既然你的应用正在自动缩放，当它达到最大实例计数 (2) 且 CPU 使用率超过所需的百分比 (80%) 时，会发生什么情况？
例如，可以设置警报（有关更多信息，请阅读[接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)）来通知这种情况，以便进一步增加/扩大应用。 让我们快速为此方案设置警报。

1. 在应用的边栏选项卡中，单击“ **工具** > **警报**。  
    ![警报 - 设置边栏选项卡](./media/app-service-web-get-started/alert-settings.png)
2. 单击“ **添加警报**”。 然后，在“资源”框中，选择以 (serverfarms) 结尾的资源。 这是你的 App Service 计划。  
    ![警报 - 为应用服务计划添加警报](./media/app-service-web-get-started/alert-add.png)
3. 将“名称”指定为 `CPU Maxed`、将“度量值”指定为“CPU 百分比”，将“阈值”指定为 `90`，选择“电子邮件所有者、参与者和读者”，然后单击“确定”。   
    ![警报 - 配置警报](./media/app-service-web-get-started/alert-configure.png)
   
    当 Azure 完成警报创建时，你会在“ **警报** ”边栏选项卡中看到该警报。  
    ![警报 - 完成的视图](./media/app-service-web-get-started/alert-done.png)

祝贺你，你现在可以接收警报。

此警报设置将每隔 5 分钟检查一次 CPU 使用率。 如果该数字高于 90%，以及获得授权的人员都收到电子邮件警报。 若要查看有权接收警报的人员，请回到应用的边栏选项卡，然后单击“ **访问** ”按钮。  
![查看谁会收到警报](./media/app-service-web-get-started/alert-rbac.png)

应会看到“订阅管理员”已是应用的“所有者”。 如果你是 Azure 订阅（例如你的试用订阅）的帐户管理员，该组将包括你。 有关 Azure 基于角色的访问控制的详细信息，请参阅 [Azure 基于角色的访问控制](../active-directory/role-based-access-control-configure.md)。

> [!NOTE]
> 警报规则是一项 Azure 功能。 有关详细信息，请参阅[接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。
> 
> 

## <a name="next-steps"></a>后续步骤
当你配置警报时，可能已注意到“ **工具** ”边栏选项卡中有一组丰富的工具。 这些工具可让你排查问题、监视性能、测试漏洞、管理资源、与 VM 控制台交互，以及添加有用的扩展。 我们希望你单击每个工具，以探索这些简单却功能强大的工具。

此外，请了解如何使用部署的应用执行更多操作。 以下只是一份不完整的列表：

* [购买和配置自定义域名](custom-dns-web-site-buydomains-web-app.md) - 为你的 Web 应用购买有吸引力的域，而不是使用 *.azurewebsites.net 域。 或使用你已拥有的域。
* [设置过渡环境](web-sites-staged-publishing.md) - 将应用投放到生产环境之前，先将它部署到过渡 URL。 有把握地更新你的实时 Web 应用。 使用多个部署槽设置一个精巧的 DevOps 解决方案。
* [设置连续部署](app-service-continuous-deployment.md) - 将应用部署集成到源代码控制系统。 通过每次提交部署到 Azure。
* [访问本地资源](web-sites-hybrid-connection-get-started.md) - 访问现有的本地数据库或 CRM 系统。
* [备份应用](web-sites-backup.md) - 为 Web 应用设置备份和还原。 针对意外的故障做好准备，并能够从中恢复。
* [启用诊断日志](web-sites-enable-diagnostic-log.md) - 从 Azure 或应用程序跟踪中读取 IIS 日志。 以串流的形式读取日志、下载日志，或者将日志移植到 [Application Insights](../application-insights/app-insights-overview.md) 以进行全方位的分析。
* [扫描应用中的漏洞](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) -
  使用 [Tinfoil Security](https://www.tinfoilsecurity.com/) 提供的服务扫描 Web 应用，以查出新型威胁。
* [运行后台作业](../azure-functions/functions-overview.md) - 运行作业来进行数据处理、报告，等等。
* [了解 App Service 的工作方式](../app-service/app-service-how-works-readme.md)


