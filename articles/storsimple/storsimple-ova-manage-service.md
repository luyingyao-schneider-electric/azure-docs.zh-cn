---
title: "为 StorSimple Virtual Array 部署 StorSimple Manager 服务 | Microsoft Docs"
description: "介绍了如何在 Azure 经典门户中创建和删除 StorSimple Manager 服务，并介绍了如何管理服务注册密钥。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 40084881-ad8d-440f-a365-68259beee616
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/19/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5c2a46dcf43287db2a2960af7993ae83558faadf


---
# <a name="deploy-the-storsimple-manager-service-for-storsimple-virtual-array"></a>为 StorSimple Virtual Array 部署 StorSimple Manager 服务
## <a name="overview"></a>概述
StorSimple Manager 服务在 Microsoft Azure 中运行并连接到多台 StorSimple 设备。 在创建该服务后，你可以使用它从在浏览器中运行的 Microsoft Azure 经典门户中管理设备。 这使得你可以从单个中央位置监视连接到 StorSimple Manager 服务的所有设备，因此最大限度地降低了管理负担。

StorSimple Manager 登陆页会列出可以用来管理 StorSimple 存储设备的所有 StorSimple Manager 服务。 对于每项 StorSimple Manager 服务，StorSimple Manager 页上提供了以下信息：

* **名称** – 在创建 StorSimple Manager 服务时为其分配的名称。 在创建服务后无法更改服务名称。
* **状态** – 服务的状态，可以是**活动**、**正在创建**或**联机**。
* **位置** – 将在其中部署 StorSimple 设备的地理位置。
* **订阅** – 与服务关联的计费订阅。

可以通过 StorSimple Manager 页执行的常见任务包括：

* 创建服务
* 删除服务
* 获取服务注册密钥
* 重新生成服务注册密钥

本教程介绍了如何执行其中每个任务。 本文中包含的信息仅适用于 StorSimple Virtual Array。 有关 StorSimple 8000 系列的详细信息，请转到[部署 StorSimple Manager 服务](storsimple-manage-service.md)。

## <a name="create-a-service"></a>创建服务
如果要部署 StorSimple 设备，请使用“快速创建”选项创建 StorSimple Manager 服务。 若要创建服务，需要具有以下项：

* 一个具有企业协议的订阅
* 一个活动的 Microsoft Azure 存储帐户
* 用于访问管理的帐单信息

在创建服务时，还可以选择生成默认存储帐户。

单个服务可以管理多台设备。 但是，一台设备不能跨越多个服务。 大型企业可以具有多个服务实例，以便使用不同的订阅、组织，甚至是部署位置。  

> [!NOTE]
> 你需要隔离 StorSimple Manager 服务的实例，管理 StorSimple 8000 系列设备和 StorSimple Virtual Array。
> 
> 

执行以下步骤，创建服务。

[!INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

## <a name="delete-a-service"></a>删除服务
删除服务之前，请确保没有连接的设备正在使用它。 如果服务正在使用中，停用连接的设备。 停用操作将断开设备与服务之间的连接，但将设备数据保留在云中。 

> [!IMPORTANT]
> 删除服务后，该操作不可逆。 
> 
> 

执行以下步骤，删除服务。

### <a name="to-delete-a-service"></a>删除服务
1. 在“StorSimple Manager服务”页上，选择要删除的服务。
2. 单击页面底部的“删除”。
3. 在确认通知中单击“是”。 可能需要花费几分钟时间才能删除服务。

## <a name="get-the-service-registration-key"></a>获取服务注册密钥
成功创建服务后，你需要为 StorSimple 设备注册该服务。 若要注册第一台 StorSimple 设备，需要使用服务注册密钥。 若要为其他设备注册现有 StorSimple 服务，需要使用注册密钥和服务数据加密密钥（以在注册期间在第一台设备上生成的密钥为准）。 有关服务数据加密密钥的详细信息，请参阅[从本地 Web UI 获取服务数据加密密钥](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)。 

执行以下步骤，获取服务注册密钥。

[!INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

将服务注册密钥保存在安全位置。 若要为其他设备注册此服务，你需要使用此密钥以及服务数据加密密钥。 获取服务注册密钥后，需要通过 StorSimple 接口的 Windows PowerShell 配置你的设备。

## <a name="regenerate-the-service-registration-key"></a>重新生成服务注册密钥
如果系统要求你执行密钥轮换或者如果服务管理员列表发生更改，你需要重新生成服务注册密钥。 重新生成密钥时，新密钥仅用于注册后续设备。 已注册的设备不受此过程影响。

执行以下步骤，重新生成服务注册密钥。

### <a name="to-regenerate-the-service-registration-key"></a>重新生成服务注册密钥
1. 在“StorSimple Manager 服务”页面上，单击“注册密钥”。
2. 在“服务注册密钥”对话框中，单击“重新生成”。
3. 将显示确认消息。 单击“确定”，继续重新生成操作。
4. 将显示新的服务注册密钥。
5. 复制此密钥，并保存密钥以供为任何新设备注册此服务。
6. 单击选中图标  ![选中图标](./media/storsimple-ova-manage-service/image7.png) 关闭此对话框。

## <a name="next-steps"></a>后续步骤
* 了解 StorSimple Virtual Array [入门](storsimple-ova-deploy1-portal-prep.md)。
* 了解如何[管理你的 StorSimple 设备](storsimple-ova-web-ui-admin.md)。




<!--HONumber=Nov16_HO3-->


