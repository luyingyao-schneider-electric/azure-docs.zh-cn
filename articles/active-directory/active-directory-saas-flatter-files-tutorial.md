---
title: "教程：Azure Active Directory 与 Flatter Files 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Flatter Files 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 488a3c5f0aa05c5b71bf5d72539cbc4b7c6de1b5
ms.openlocfilehash: 062878ad877b501ce7f0d5c4f8ce9ca939ffe64d
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>教程：Azure Active Directory 与 Flatter Files 的集成
本教程的目的是展示如何将 Flatter Files 与 Azure Active Directory (Azure AD) 进行集成。  

将 Flatter Files 与 Azure AD 集成可提供以下优势： 

* 可以在 Azure AD 中控制谁有权访问 Flatter Files 
* 可以让用户使用其 Azure AD 帐户自动登录到 Flatter Files 单一登录 (SSO)
* 可以在一个中心位置（即 Azure Active Directory 经典门户）管理帐户

如果想要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [What is application access and single sign-on with Azure Active Directory](active-directory-appssoaccess-whatis.md)（什么是使用 Azure Active Directory 的应用程序访问和单一登录）。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Flatter Files 的集成，需要具有以下项：

* Azure AD 订阅
* 启用了 Flatter Files 单一登录的订阅

>[!NOTE]
>不建议使用生产环境测试本教程中的步骤。 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。 

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。  

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Flatter Files 
2. 配置和测试 Azure AD 单一登录

## <a name="add-flatter-files-from-the-gallery"></a>从库中添加 Flatter Files
若要配置 Flatter Files 与 Azure AD 的集成，需要从库中将 Flatter Files 添加到托管 SaaS 应用列表。

**若要从库中添加 Flatter Files，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Flatter Files”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)

7. 在结果窗格中，选择“Flatter Files”，然后单击“完成”以添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分的目的是基于名为“Britta Simon”的测试用户展示如何配置并测试 Flatter Files 的 Azure AD 单一登录。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 Flatter Files 用户。 换句话说，需要在 Azure AD 用户与 Flatter Files 中的相关用户之间建立链接关系。  

可以通过将 Azure AD 中“用户名”的值分配为 Flatter Files 中“用户名”的值来建立此链接关系。

若要配置并测试 Flatter Files 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Flatter Files 测试用户](#creating-a-halogen-software-test-user)** - 在 Flatter Files 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分的目的是在 Azure AD 经典门户中启用 Azure AD 单一登录并在 Flatter Files 应用程序中配置单一登录。 在此过程中，需要创建 base-64 编码的证书文件。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。
若要为 Flatter Files 配置单一登录，需要已注册域。 如果尚没有已注册域，请通过 [support@flatterfiles.com](mailto:support@flatterfiles.com) 联系 Flatter Files 支持团队。  

**若要配置 Flatter Files 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure AD 经典门户中，在 **Flatter Files** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录到 Flatter Files”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
       ![配置单一登录](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) 
3. 在“配置应用设置”对话框页上，单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) 
   >[!NOTE]
   >Flatter Files 为所有客户使用相同的 SSO 登录 URL：[https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/)
   > 
   
4. 在“在 Flatter Files 处配置单一登录”页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)  
    1. 单击“下载证书”，然后将文件保存在计算机上。
    2. 单击“资源组名称” 的 Azure 数据工厂。
5. 以管理员身份登录到 Flatter Files 应用程序。
6. 单击“仪表板”。 
   
    ![配置单一登录](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  
7. 单击“设置”，然后在“公司”选项卡上执行以下步骤： 
   
    ![配置单一登录](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  
    1. 选择“使用 SAML 2.0 进行身份验证”。
    2. 单击“配置 SAML”。
8. 在“SAML 配置”对话框中，执行以下步骤： 
   
    ![配置单一登录](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  
   1. 在“域”文本框中，键入你的已注册域。
   
    >[!NOTE]
    >如果尚没有已注册域，请通过 [support@flatterfiles.com] (mailto:support@flatterfiles.com) 联系 Flatter Files 支持团队。 
    >    
   2. 在 Azure 经典门户中，在“在 Flatter Files 处配置单一登录”对话框中，复制“单一登录服务 URL”，然后将其粘贴到“标识提供者 URL”文本框中。
   3.  基于下载的证书创建一个 **base-64 编码**的文件。  
 
   >[!TIP]
   >有关详细信息，请参阅 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)（如何将二进制证书转换为文本文件）。
   >  
   4.  在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后再粘贴到“FlatterFiles 标识提供者证书”文本框中。
   5. 单击“更新”。
9. 在 Azure AD 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。 
   
    ![Azure AD 单一登录][10]
10. 在“单一登录确认”页上，单击“完成”。  
    
     ![Azure AD 单一登录][11]

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤： 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)  
   1. 在“用户类型”中，选择“你的组织中的新用户”。
   2. 在“用户名”文本框中，键入“BrittaSimon”。
   3. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤： 
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
   1. 在“名字”文本框中，键入“Britta”。  
   2. 在“姓氏”文本框中，键入“Simon”。
   3. 在“显示名称”文本框中，键入“Britta Simon”。
   4. 在“角色”列表中，选择“用户”。
   5. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
   1. 写下“新密码”的值。
   2. 单击“完成”。   

### <a name="create-a-flatter-files-test-user"></a>创建 Flatter Files 测试用户
本部分的目的是在 Flatter Files 中创建名为 Britta Simon 的用户。

**若要在 Flatter Files 中创建名为 Britta Simon 的用户，请执行以下步骤：**

1. 以管理员身份登录到你的 **Flatter Files** 公司站点。
2. 在左侧导航窗格中，单击“设置”，然后单击“用户”选项卡。
   
    ![创建 Flatter Files 用户](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)
3. 单击“添加用户”。 
4. 在“添加用户”对话框中，执行以下步骤：
   
    ![创建 Flatter Files 用户](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)
   1. 在“名字”文本框中，键入“Britta”。
   2. 在“姓氏”文本框中，键入“Simon”。 
   3. 在“电子邮件地址”文本框中，键入 Britta 在 Azure 经典门户中的电子邮件地址。
   4. 单击“提交”。   

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过向 Britta Simon 授予对 Flatter Files 的访问权限，使她能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Flatter Files，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“Flatter Files”。
   
    ![分配用户](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203] 
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="test-single-sign-on"></a>测试单一登录
本部分的目的是使用访问面板测试 Azure AD 单一登录配置。  

当在访问面板中单击 Flatter Files 磁贴时，应当会自动登录到 Flatter Files 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png







