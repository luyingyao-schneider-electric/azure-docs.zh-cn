---
title: "教程：Azure Active Directory 与 Zscaler Private Access (ZPA) 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Zscaler Private Access (ZPA) 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: c81f23603cfbbc916c148c61bd3de52ef1db36ac
ms.openlocfilehash: 5c598bfa5b6725d21a89df54dbcb3314cc631d80
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>教程：Azure Active Directory 与 Zscaler Private Access (ZPA) 集成

本教程介绍如何将 Zscaler Private Access (ZPA) 与 Azure Active Directory (Azure AD) 集成。

将 Zscaler Private Access (ZPA) 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Zscaler Private Access (ZPA)
- 可以让用户使用其 Azure AD 帐户自动登录到 Zscaler Private Access (ZPA)（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Zscaler Private Access (ZPA) 的集成，需具备以下项：

- Azure AD 订阅
- 已启用 Zscaler Private Access (ZPA) 单一登录的订阅


> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Zscaler Private Access (ZPA)
2. 配置和测试 Azure AD 单一登录


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>从库中添加 Zscaler Private Access (ZPA)
若要配置 Zscaler Private Access (ZPA) 与 Azure AD 的集成，需要从库中将 Zscaler Private Access (ZPA) 添加到托管 SaaS 应用列表。

**若要从库中添加 Zscaler Private Access (ZPA)，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Zscaler Private Access (ZPA)”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

5. 在结果窗格中，选择“Zscaler Private Access (ZPA)”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Zscaler Private Access (ZPA) 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Zscaler Private Access (ZPA) 用户。 换句话说，需要建立 Azure AD 用户与 Zscaler Private Access (ZPA) 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值分配为 Zscaler Private Access (ZPA) 中“用户名”的值来建立此链接关系。

若要配置和测试 Zscaler Private Access (ZPA) 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Zscaler Private Access (ZPA) 测试用户](#creating-a-zscaler-private-access-(zpa)-test-user)** - 在 Zscaler Private Access (ZPA) 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 管理门户中启用 Azure AD 单一登录并在 Zscaler Private Access (ZPA) 应用程序中配置单一登录。

**若要配置 Zscaler Private Access (ZPA) 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“Zscaler Private Access (ZPA)”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
3. 在“Zscaler Private Access (ZPA) 域和 URL”部分中，执行以下步骤：
    
    ![配置单一登录](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    b.保留“数据库类型”设置，即设置为“共享”。 在“标识符”文本框中，键入 `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE] 
    > 请注意，这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 此处我们建议在“标识符”中使用 URL 的唯一值。 若要获取这些值，请联系 [Zscaler Private Access (ZPA) 支持团队](https://help.zscaler.com/zpa-submit-ticket)。

4. 在“SAML 签名证书”部分中，单击“创建新证书”。

    ![配置单一登录](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_400.png)     

5. 在“创建新证书”对话框中，单击日历图标，然后选择“到期日期”。 然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_500.png)

6. 在“SAML 签名证书”部分中，选择“激活新证书”，然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

7. 在弹出的“滚动更新证书”窗口中，单击“确定”。

    ![配置单一登录](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_600.png)

8. 在“SAML 签名证书”部分中，单击“元数据 XML”，然后在计算机上保存元数据文件。

    ![配置单一登录](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

9. 在另一个 Web 浏览器窗口中，以管理员身份登录 Zscaler Private Access (ZPA) 公司站点。

10. 导航到“管理员”，然后单击“Idp 配置”。

    ![在应用端配置单一登录](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

11. 在“Idp 配置”部分，单击“添加新的 IDP 配置”。

    ![在应用端配置单一登录](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

12. 在“新增 IDP 配置”部分，执行以下步骤：

    ![在应用端配置单一登录](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“选择文件”，然后上传已下载的元数据文件。

    b. 单击“保存”按钮。
    


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>创建 Zscaler Private Access (ZPA) 测试用户

在本部分中，将在 Zscaler Private Access (ZPA) 中创建一个名为“Britta Simon”的用户。 请与 [Zscaler Private Access (ZPA) 支持团队](https://help.zscaler.com/zpa-submit-ticket)协作，将用户添加到 Zscaler Private Access (ZPA) 平台。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Zscaler Private Access (ZPA) 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Zscaler Private Access (ZPA)，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Zscaler Private Access (ZPA)”。

    ![配置单一登录](./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    


### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Zscaler Private Access (ZPA)”磁贴时，应当会自动登录到 Zscaler Private Access (ZPA) 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccess-tutorial/tutorial_general_203.png
