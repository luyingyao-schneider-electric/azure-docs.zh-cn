---
title: "配置灾难恢复的 Azure SQL 数据库安全性 | Microsoft Docs"
description: "本主题介绍在发生数据中心中断或其他灾难时数据库还原或故障转移到辅助服务器后配置和管理安全性的注意事项"
services: sql-database
documentationcenter: na
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: c7c898c9-69d4-4e16-8b7e-720bbb3353dd
ms.service: sql-database
ms.custom: business continuity
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 10/13/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: ae06e6855a11f91ce18e3b12698b3d01e23a6a2c
ms.lasthandoff: 01/07/2017


---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>针对异地还原或故障转移配置和管理 Azure SQL 数据库的安全性 

> [!NOTE]
> [活动异地复制](sql-database-geo-replication-overview.md)现在可供所有服务层中的所有数据库使用。
>  

## <a name="overview-of-authentication-requirements-for-disaster-recovery"></a>灾难恢复身份验证要求概述
本主题介绍了配置和控制[活动异地复制](sql-database-geo-replication-overview.md)所需的身份验证要求，以及设置辅助数据库的用户访问权限所需的步骤。 它还介绍了使用[异地还原](sql-database-recovery-using-backups.md#geo-restore)后如何启用对已恢复数据库的访问权限。 相关详细信息，请参阅[业务连续性概述](sql-database-business-continuity.md)。

## <a name="disaster-recovery-with-contained-users"></a>使用包含的用户进行灾难恢复
不同于必须映射到 master 数据库中登录名的传统用户，包含的用户完全由数据库自身管理。 这带来了两个好处。 在灾难恢复方案中，用户可以继续连接到新的主数据库或使用异地还原恢复的数据库，不需进行任何额外的配置，因为数据库会管理用户。 从登录的立场来看，此配置还有潜在的缩放性和性能优势。 有关详细信息，请参阅[包含数据库用户 - 使你的数据库可移植](https://msdn.microsoft.com/library/ff929188.aspx)。 

主要的不足是，在规模较大的情况下，管理灾难恢复过程更具挑战性。 当你有多个使用同一登录名的数据库时，在多个数据库中使用包含用户来维护凭据可能会抵消包含用户的好处。 例如，密码轮换策略要求在多个数据库中进行一致性的更改，而不是在 master 数据库中更改登录名的密码一次。 因此，如果你的多个数据库使用同一用户名和密码，则不建议你使用包含用户。 

## <a name="how-to-configure-logins-and-users"></a>如何配置登录名和用户
如果你要使用登录名和用户（而不是包含用户），必须采取额外的步骤以确保相同的登录名存在于 master 数据库中。 以下部分概述了相关的步骤和其他注意事项。

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>设置对辅助数据库或已恢复数据库的用户访问权限
为了确保能够将辅助数据库用作只读辅助数据库，以及确保使用异地还原对新的主数据库或已恢复数据库进行适当的访问，必须在恢复之前对目标服务器的 master 数据库进行适当的安全配置。

本主题稍后部分介绍了各步骤所需的特定权限。

应该在配置异地复制的过程中进行用户访问权限方面的准备，以便用户能够访问异地复制辅助数据库。 只要原始服务器处于联机状态（例如，在进行 DR 钻取时），就可以进行用户访问权限方面的准备，使用户能够访问异地还原数据库。

> [!NOTE]
> 如果你在故障转移到或异地还原到某个服务器时，该服务器并没有进行适当的配置，则只能使用服务器管理员帐户通过登录方式对其进行访问。
> 
> 

在目标服务器上设置登录名涉及三个步骤，概述如下：

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1.确定有权访问主数据库的登录名：
该过程的第一个步骤就是确定必须在目标服务器上复制哪些登录名。 可以使用一对 SELECT 语句来完成此操作，其中一个语句用于源服务器上的逻辑 master 数据库，另一个语句用于主数据库本身。

只有服务器管理员或 **LoginManager** 服务器角色的成员，才能使用以下 SELECT 语句确定源服务器上的登录名。 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

只有 db_owner 数据库角色的成员、dbo 用户或服务器管理员，才能确定主数据库中的所有数据库用户主体。

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2.查找步骤 1 中确定的登录名的 SID：
通过将前一部分中所述的查询的输出进行比较以及对 SID 进行匹配，你可以将服务器登录名映射到数据库用户。 包含数据库用户以及匹配的 SID 的登录名有权以该数据库用户主体的身份访问该数据库。 

可以使用以下查询来查看所有用户主体及其在数据库中的 SID。 只有 db_owner 数据库角色的成员或服务器管理员才能运行此查询。

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> **INFORMATION_SCHEMA** 和 **sys** 用户具有 *NULL* SID，**guest** SID 为 **0x00**。 如果数据库创建者是服务器管理员而不是 **DbManager** 的成员，则 **dbo** SID 可能以 *0x01060000000001648000000000048454* 开头。
> 
> 

#### <a name="3-create-the-logins-on-the-target-server"></a>3.在目标服务器上创建登录名：
最后一个步骤是转到一个或多个目标服务器，并使用相应的 SID 生成登录名。 基本语法如下。

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> 如果你要授予用户对辅助数据库而不是主数据库的访问权限，你可以使用以下语法更改主服务器上的用户登录名来实现此目的。
> 
> ALTER LOGIN <login name> DISABLE
> 
> DISABLE 不会更改密码，因此你始终可以根据需要启用该登录名。
> 
> 

## <a name="next-steps"></a>后续步骤
* 若要深入了解如何管理数据库访问和登录，请参阅 [SQL 数据库安全：管理数据库访问和登录安全](sql-database-manage-logins.md)。
* 若要深入了解随附的数据库用户，请参阅[包含的数据库用户 - 使你的数据库可移植](https://msdn.microsoft.com/library/ff929188.aspx)。
* 若要了解如何使用和配置活动异地复制息，请参阅[活动异地复制](sql-database-geo-replication-overview.md)
* 若要了解如何使用异地还原，请参阅[异地还原](sql-database-recovery-using-backups.md#geo-restore)


