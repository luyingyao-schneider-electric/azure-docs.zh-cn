---
title: "在服务器之间或订阅之间移动数据库，以及将数据库移入和移出 Azure。"
description: "在 Azure SQL 数据库中复制、移动和迁移数据与数据库的快速步骤。"
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
ms.assetid: dea01868-5757-41e2-826c-ec26361a339c
ms.service: sql-database
ms.custom: migrate and move
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bc88bb49c601f525c62c4dac4716a3f77a30172e


---
# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>在服务器之间或订阅之间移动数据库，以及将数据库移入和移出 Azure
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>将数据库移到同一订阅中的不同服务器
* 在 [Azure 门户](https://portal.azure.com)中，单击“**SQL 数据库**”，从列表中选择数据库，然后单击“**复制**”。 有关详细信息，请参阅[复制 Azure SQL 数据库](sql-database-copy.md)。

## <a name="to-move-a-database-between-subscriptions"></a>在订阅之间移动数据库
* 在 [Azure 门户](https://portal.azure.com)中，单击“**SQL Server**”，然后从列表中选择托管数据库的服务器。 单击“**移动**”，然后选择要移动的资源以及要移动到其中的订阅。

## <a name="to-migrate-a-sql-database-into-azure"></a>将 SQL 数据库迁移到 Azure
* 确定数据库兼容性，然后根据需要选择正确的迁移方法。 根据[迁移 SQL Server 数据库](sql-database-cloud-migrate.md)中的指导和选项进行操作。

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>创建数据库副本以便在 Azure 外部使用
* [导出 BACPAC 文件。](sql-database-export.md)




<!--HONumber=Nov16_HO3-->


