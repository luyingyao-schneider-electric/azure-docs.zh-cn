---
title: "Azure Active Directory 目录的特征 | Microsoft 文档"
description: "通过了解你的目录将你的 Azure Active Directory 目录作为完全独立的资源进行管理"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 2b862b75-14df-45f2-a8ab-2a3ff1e2eb08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: f8b63e5831897d3a45298b0415bb2d6d44ab0de1
ms.openlocfilehash: 5ec00d5e8380f121dd9302cf08a0708c530aab9b
ms.lasthandoff: 03/01/2017


---
# <a name="understand-how-multiple-azure-active-directory-directories-interact"></a>了解多个 Azure Active Directory 目录如何交互
在 Azure Active Directory (Azure AD) 中，每个目录都是完全独立的资源：一个具有完整功能的对等方，在逻辑上独立于你所管理的其他目录。 目录之间不存在父子关系。 目录之间的这种独立性包括资源独立性、管理独立性和同步独立性。

## <a name="resource-independence"></a>资源独立性
在一个目录中创建或删除一个资源不影响另一个目录中的任何资源，但对于外部用户来说，情况并不完全如此，具体如下所述。 如果将自定义域“contoso.com”用于一个目录，则不能将它用于任何其他目录。

## <a name="administrative-independence"></a>管理独立性
如果目录“Contoso”的某个非管理用户创建了测试目录“Test”，那么：

* 默认情况下，会在该新目录中将创建目录的用户添加为外部用户，并在该目录中为其分配全局管理员角色。
* 目录“Contoso”的管理员对目录“Test”没有直接管理特权，除非“Test”的管理员专门向其授予了这些特权。 “Contoso”的管理员一旦控制了创建“Test”的用户帐户，就可以控制对目录“Test”的访问。
* 如果你更改（添加或删除）某个用户在一个目录中的管理员角色，这项更改不会影响该用户在另一个目录中可能具有的任何管理员角色。

## <a name="synchronization-independence"></a>同步独立性
可以独立配置每个 Azure AD 目录，以便通过下列任一工具的单个实例同步数据：

* 目录同步 (DirSync) 工具，用于将数据与单个 AD 林同步。
* 适用于 Forefront Identity Manager 的 Azure Active Directory 连接器，用于将数据与一个或多个本地林和/或非 Azure AD 数据源同步。

## <a name="add-an-azure-ad-directory"></a>添加 Azure AD 目录
若要在 Azure 经典门户中添加 Azure AD 目录，请选择左侧的 Azure Active Directory 扩展，然后点击“添加”。

> [!NOTE]
> 与其他 Azure 资源不同，目录不是 Azure 订阅的子资源。 如果取消 Azure 订阅或让其过期，仍可使用 Azure PowerShell、Azure 图形 API 或其他界面（例如 Office 365 管理中心）访问目录数据。 还可以将其他订阅与目录相关联。
>
>

## <a name="next-steps"></a>后续步骤
有关 Azure AD 许可问题和最佳实践的一般概述，请参阅[什么是 Azure Active Directory 许可？](active-directory-licensing-what-is.md)。

