---
title: "教程：Azure Active Directory 与 Marketo 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Marketo 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1a2ff324cfa65977685f6a0eee9cd398b0717519
ms.openlocfilehash: 20f3a8b006e45e3a94e95b516bca292a82c5fd03


---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>教程：Azure Active Directory 与 Marketo 的集成
本教程介绍了如何将 Marketo 与 Azure Active Directory (Azure AD) 进行集成。

将 Marketo 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Marketo
* 可以让用户使用其 Azure AD 帐户自动登录到 Marketo（单一登录）
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Marketo 的集成，需要具有以下项：

* Azure AD 订阅
* 启用了 Marketo 单一登录的订阅

> [!NOTE]
> 测试本教程中的步骤时，建议不要使用生产环境。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Marketo
2. 配置并测试 Azure AD 单一登录

## <a name="adding-marketo-from-the-gallery"></a>从库中添加 Marketo
若要配置 Marketo 与 Azure AD 的集成，需要从库中将 Marketo 添加到托管 SaaS 应用列表。

**若要从库中添加 Marketo，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Marketo”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_01.png)
7. 在结果窗格中，选择“Marketo”，然后单击“完成”以添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Marketo 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Marketo 用户。 换句话说，需要在 Azure AD 用户与 Marketo 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Marketo 中“用户名”的值来建立此链接关系。

若要配置并测试 Marketo 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Marketo 测试用户](#creating-a-predictix-price-reporting-test-user)** - 在 Marketo 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，将在经典门户中启用 Azure AD 单一登录并在 Marketo 应用程序中配置单一登录。

**若要配置 Marketo 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **Marketo** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录到 Marketo”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_04.png) 
   
    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://saml.marketo.com/sp`
   
    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://login.marketo.com/saml/assertion/\<munchkinid\>`
   
    c. 单击“下一步”
4. 在“在 Marketo 处配置单一登录”页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_05.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“下载证书”，然后将文件保存在计算机上。
   
    b. 单击“下一步”。
5. 若要获取应用程序的 Munchkin Id，请使用管理员凭据登录到 Marketo 并执行以下操作：
   
    a. 使用管理员凭据登录到 Marketo。
   
    b. 单击顶部导航窗格上的“管理”按钮。
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 导航到“集成”菜单，然后单击 Munchkin 链接
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_11.png)
   
    d. 复制屏幕上显示的 Munchkin Id，然后在 Azure AD 配置向导中填写回复 URL。
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_12.png)
6. 若要在应用程序中配置 SSO，请执行以下步骤：
   
    a. 使用管理员凭据登录到 Marketo。
   
    b. 单击顶部导航窗格上的“管理”按钮。
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 导航到“集成”菜单，然后单击“单一登录”
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_07.png) 
   
    d. 若要启用 SAML 设置，请单击“编辑”按钮
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **启用**单一登录设置
   
    f. 输入你从 Azure AD 配置向导复制的颁发者 ID。
   
    g. 在“实体 ID”文本框中，输入 **http://saml.marketo.com/sp** 作为 URL
   
    h. 对于“名称标识符元素”，选择“用户 ID 位置”
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > 如果用户标识符不是 UPN 值，则在“属性”选项卡中更改该值。
    > 
    > 
   
    i. 上载你从 Azure AD 配置向导下载的证书。 保存设置。
   
    j. 编辑重定向页面设置
   
    k. 将 Azure AD 配置向导中的登录 URL 复制到“登录 URL”文本框中。
   
    l. 将 Azure AD 配置向导中的注销 URL 复制到“注销 URL”文本框中。
   
    m. 在“错误 URL”中，复制 Marketo 实例 URL 并单击“保存”按钮以保存设置。
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_10.png)

7. 若要为用户启用 SSO，请完成以下操作：
   
    a. 使用管理员凭据登录到 Marketo。
   
    b. 单击顶部导航窗格上的“管理”按钮。
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
   
    c. 导航到“安全性”菜单，并单击“登录设置”。 
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_13.png)
   
    d. 选中“需要 SSO”选项并保存设置。
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_14.png)
8. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
9. 在“单一登录确认”页上，单击“完成”。  
   
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-marketo-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-marketo-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-marketo-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页中，执行以下步骤： ![创建 Azure AD 测试用户](./media/active-directory-saas-marketo-tutorial/create_aaduser_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页面上，执行以下步骤： ![创建 Azure AD 测试用户](./media/active-directory-saas-marketo-tutorial/create_aaduser_06.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
    b. 在“姓氏”文本框中，键入“Simon”。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-marketo-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-marketo-tutorial/create_aaduser_08.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-an-marketo-test-user"></a>创建 Marketo 测试用户
在本部分中，将在 Marketo 中创建一个名为 Britta Simon 的用户。 请按照以下步骤在 Marketo 平台中创建用户。

1. 使用管理员凭据登录到 Marketo。
2. 单击顶部导航窗格上的“管理”按钮。
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_06.png) 
3. 导航到“安全性”菜单，并单击“用户和角色”
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_19.png)  
4. 在“用户”选项卡上单击“邀请新用户”
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_15.png) 
5. 在“邀请新用户”向导中，填写以下信息
   
    a. 在文本框中输入用户**电子邮件**地址
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_16.png)
   
    b. 在文本框中输入**名字**
   
    c. 在文本框中输入**姓氏**
   
    d. 单击“下一步”
6. 在“权限”选项卡中，选择用户角色并单击“下一步”
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_17.png)
7. 单击“发送”按钮以发送用户邀请
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_18.png)
8. 用户将收到电子邮件通知，并且必须单击链接并更改密码以激活帐户。 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，将通过向 Britta Simon 授予对 Marketo 的访问权限使她能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Marketo，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“Marketo”。
   
    ![配置单一登录](./media/active-directory-saas-marketo-tutorial/tutorial_marketo_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
在本部分中，将使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Marketo 磁贴时，应当会自动登录到 Marketo 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-marketo-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


