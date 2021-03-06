---
title: "Azure Active Directory B2C：Facebook 配置 | Microsoft Docs"
description: "在 Azure Active Directory B2C 保护的应用程序中向用户提供使用 Facebook 帐户的注册和登录功能。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: b875f235-a1d2-4abb-b9f0-b89beac38a32
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 4c45322573bd1e4b1711b56e03c1d297f1cd468e
ms.lasthandoff: 03/30/2017


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C：向用户提供使用 Facebook 帐户的注册和登录功能
## <a name="create-a-facebook-application"></a>创建 Facebook 应用程序
若要将 Facebook 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 Facebook 应用程序并向其提供合适的参数。 需要使用 Facebook 帐户来完成此操作。 如果没有此帐户，可以在 [https://www.Facebook.com/](https://www.facebook.com/) 上获取。

1. 转到 [Facebook 开发人员](https://developers.facebook.com/)网站，然后使用 Facebook 帐户凭据登录。
2. 如果以前没有登录过，需要注册为 Facebook 开发人员。 若要执行此操作，请单击“注册”（位于页面的右上角），接受 Facebook 的策略，然后完成注册步骤。
3. 单击“我的应用”，然后单击“添加新应用”。 
4. 在表单中，提供**显示名称**和有效的**联系人电子邮件**。
5. 单击“创建应用 ID”。 这会要求你接受 Facebook 平台策略并完成在线安全检查。
6. 在左侧列中，单击“设置”，然后选择“基本”（如果尚未选择）。
7. 选择一个**类别**。 
8. 单击“+ 添加平台”，然后选择“网站”。
   
    ![Facebook — 设置](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook — 设置 — 网站](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. 在“站点 URL”字段中输入 `https://login.microsoftonline.com/`，然后单击“保存更改”。
   
    ![Facebook — 站点 URL](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. 复制“应用程序 Id”的值。 单击“显示”，并复制“应用程序密码”的值。 将 Facebook 帐户配置为租户中的标识提供者时需要此两项。 **应用程序密码**是一个非常重要的安全凭据。
   
    ![Facebook — 应用程序 ID 和应用程序密码](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. 单击左侧导航窗格中的“+ 添加产品”，然后单击“Facebook 登录”旁边的“开始”按钮。
   
    ![Facebook — Facebook 登录](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. 选择“网站”，然后在“客户端 OAuth 设置”部分的“有效的 OAuth 重定向 URI”字段中输入 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`。 将 **{tenant}** 替换为你的租户名称（例如 contosob2c.onmicrosoft.com）。 单击页面底部的“保存更改”。
    
    ![Facebook — OAuth 重定向 URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
13. 若要使 Facebook 应用程序可供 Azure AD B2C 使用，需要使其公开可用。 若要执行此操作，可单击左侧导航窗格上的“应用程序查看”，将该页顶部的开关设置为“是”，并单击“确认”。
    
    ![Facebook — 应用程序公开](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>将 Facebook 配置为租户中的标识提供者
1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 在 B2C 功能边栏选项卡上，单击“标识提供者”。
3. 单击边栏选项卡顶部的“ **+添加** ”。
4. 为标识提供者配置提供一个友好**名称**。 例如，输入“FB”。
5. 单击“标识提供者类型”，选择“Facebook”，然后单击“确定”。
6. 单击“设置此标识提供者”，分别在“客户端 ID”和“客户端密码”字段中输入（之前创建的 Facebook 应用程序的）应用程序 ID 和应用程序密码。
7. 单击“确定”，然后单击“创建”以保存 Facebook 配置。


