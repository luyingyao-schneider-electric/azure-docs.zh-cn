---
title: "详细了解：Azure Active Directory 密码管理 | Microsoft 文档"
description: "有关 Azure AD 密码管理的高级主题，内容包括密码写回的工作原理、密码写回安全性、密码重置门户的工作原理，以及密码重置使用的数据。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: d3be2912-76c8-40a0-9507-b7b1a7ce2f8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: a07051ea0be58cafcf1a7d7ae800b44e7abd05cd
ms.lasthandoff: 04/13/2017


---
# <a name="learn-more-about-password-management"></a>了解有关密码管理的详细信息
> [!IMPORTANT]
> **你是否因登录时遇到问题而浏览至此？** 如果是这样， [可按以下方式更改和重置你的密码](active-directory-passwords-update-your-own-password.md#reset-my-password)。
>
>

如果你已部署了密码管理，或只是想要在部署之前深入了解密码管理工作原理的技术细节，则本部分可让你大致了解该服务背后的技术概念。 本部分将介绍以下内容：

* [**密码重置门户的工作原理**](#how-does-the-password-reset-portal-work)
* [**密码写回概述**](#password-writeback-overview)
 * [密码写回的工作原理](#how-password-writeback-works)
* [**密码写回支持的方案**](#scenarios-supported-for-password-writeback)
 * [支持的 Azure AD Connect、Azure AD Sync 和 DirSync 客户端](#supported-clients)
 * [密码写回所需的许可证](#licenses-required-for-password-writeback)
 * [密码写回支持的本地身份验证模式](#on-premises-authentication-modes-supported-for-password-writeback)
 * [密码写回支持的用户和管理员操作](#user-and-admin-operations-supported-for-password-writeback)
 * [密码写回不支持的用户和管理员操作](#user-and-admin-operations-not-supported-for-password-writeback)
* [**密码写回安全模型**](#password-writeback-security-model)
 * [密码写回加密详细信息](#password-writeback-encryption-details)
 * [密码写回带宽用量](#password-writeback-bandwidth-usage)
* [**部署、管理和访问用户的密码重置数据**](#deploying-managing-and-accessing-password-reset-data-for-your-users)
 * [密码重置使用哪些数据？](#what-data-is-used-by-password-reset)
 * [在无需最终用户注册的情况下部署密码重置](#deploying-password-reset-without-requiring-end-user-registration)
 * [用户注册密码重置时会发生什么情况？](#what-happens-when-a-user-registers)
 * [如何访问用户的密码重置数据](#how-to-access-password-reset-data-for-your-users)
 * [使用 PowerShell 设置密码重置数据](#setting-password-reset-data-with-powershell)
 * [使用 PowerShell 读取密码重置数据](#reading-password-reset-data-with-powershell)
* [**B2B 用户如何使用密码重置？**](#how-does-password-reset-work-for-b2b-users)

## <a name="how-does-the-password-reset-portal-work"></a>密码重置门户的工作原理
当某个用户导航到密码重置门户时，工作流将启动以确定此用户帐户是否有效、此用户所属的组织、此用户密码的管理位置以及用户是否已获许可使用该功能。  阅读以下步骤，了解有关密码重置页面背后的逻辑。

1. 用户单击“无法访问你的帐户”链接或直接转到 [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com)。
2. 用户输入用户 ID 并传递验证码。
3. Azure AD 通过执行以下操作来验证用户是否能够使用此功能：
   * 检查用户是否启用了此功能并分配有 Azure AD 许可证。
     * 如果用户未启用此功能或未分配有许可证，则要求用户联系其管理员重置其密码。
   * 检查用户是否具有针对其帐户定义且符合管理员策略的正确质询数据。
     * 如果策略仅要求一个质询，则确保用户具有针对由管理员策略启用的至少一个质询定义的适当数据。
       * 如果未配置用户，则建议用户联系其管理员重置其密码。
     * 如果策略要求两个质询，则确保用户具有针对由管理员策略启用的至少两个质询定义的适当数据。
       * 如果未配置用户，则我们建议用户联系其管理员重置其密码。
   * 检查是否在本地管理用户密码（联合或密码哈希同步）。
     * 如果已部署写回且在本地管理用户密码，则允许用户继续进行身份验证并重置其密码。
     * 如果未部署写回且在本地管理用户密码，则要求用户联系其管理员重置其密码。
4. 如果确定用户能够成功重置其密码，则将指导用户完成重置过程。

有关如何部署密码写回的详细信息，请参阅[入门：Azure AD 密码管理](active-directory-passwords-getting-started.md)。

## <a name="password-writeback-overview"></a>密码写回概述
密码写回是一个 [Azure Active Directory Connect](connect/active-directory-aadconnect.md) 组件，Azure Active Directory Premium 的当前订户可以启用和使用该组件。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。

使用此功能，你可将云租户配置为将密码写回本地 Active Directory。  有了此功能，你无需再设置和管理复杂的本地自助服务密码重置解决方案。此功能提供了一种基于云的便利方法，让你的用户能够在任何位置重置本地密码。  有关密码写回的一些主要功能，请继续阅读以下内容：

* **零延迟反馈。**  密码写回是一项同步操作。  如果用户的密码不符合策略或因某种原因而无法重置或更改，用户会立即收到通知。
* **支持使用 AD FS 或其他联合技术的用户重置密码。**  使用密码写回功能，只要联合用户帐户同步到你的 Azure AD 租户，用户就能够从云中管理他们的本地 AD 密码。
* **支持使用密码哈希同步的用户重置密码。** 当密码重置服务检测到同步用户帐户已启用密码哈希同步时，我们会同时重置此帐户的本地密码和云密码。
* **支持从访问面板和 Office 365 更改密码。**  当联合或密码同步用户更改其过期或未过期的密码时，我们将这些密码写回到你的本地 AD 环境。
* **支持管理员从** [**Azure 管理门户**](https://manage.windowsazure.com)重置密码时写回密码。  无论何时管理员在 [Azure 管理门户](https://manage.windowsazure.com)中重置用户的密码，只要该用户已联合或密码已同步，我们便还会设置管理员在本地 AD 上选择的密码。  Office 管理门户当前不支持此功能。
* **实施本地 AD 密码策略。**  当用户重置其密码时，在将重置提交到该目录之前，我们要确保它符合你的本地 AD 策略。  这包括历史记录、复杂性、年龄、密码筛选器以及你在本地 AD 中定义的所有其他密码限制。
* **不需要任何入站防火墙规则。**  密码写回功能使用 Azure 服务总线中继作为底层通信通道，这意味着你无需打开防火墙上的任何入站端口即可使用此功能（仅限 443 出站）。
* **本地 Active Directory 的受保护组中存在的用户帐户不支持。** 有关受保护组的详细信息，请参阅 [Active Directory 中的受保护帐户和组](https://technet.microsoft.com/library/dn535499.aspx)。

### <a name="how-password-writeback-works"></a>密码写回的工作原理
密码写回有三个主要组件：

* 密码重置云服务（也集成到 Azure AD 的密码更改页面中）
* 租户特定的 Azure 服务总线中继
* 本地密码重置终结点

这三个组件按下图所示配合工作：

  ![][001]

当联合或密码哈希同步用户在云中重置或更改其密码时，将发生以下情况：

1. 我们检查用户具有何种类型的密码。  如果我们看到密码在本地管理，则会确保写回服务启动且正在运行。  如果是，我们让用户继续操作；如果不是，我们告知用户不能在此处重置其密码。
2. 接下来，用户通过适当的身份验证界面，到达重置密码屏幕。
3. 用户选择一个新密码并进行确认。
4. 单击提交时，我们使用写回设置过程中创建的公钥来加密纯文本密码。
5. 加密密码后，我们将其包括在一个负载中，该负载通过 HTTPS 通道发送到租户特定的服务总线中继（我们在写回设置过程中也为你设置了此项）。  此中继受随机生成的密码保护，只有你的本地安装知道该密码。
6. 一旦消息到达服务总线，密码重置终结点便自动唤醒并看到有重置请求挂起。
7. 然后，该服务使用云定位点属性查找相关用户。  要使查找成功，该用户对象必须存在于 AD 连接器空间中、必须链接到相应的 MV 对象，而且必须链接到相应的 AAD 连接器对象。 最后，为使同步查找此用户帐户，AD 连接器对象到 MV 的链接必须具有同步规则 `Microsoft.InfromADUserAccountEnabled.xxx`。  如此要求的原因是：从云中进行调用时，同步引擎使用 cloudAnchor 属性查找 AAD 连接器空间对象，然后遵循该链接依次回到 MV 对象和 AD 对象。 由于同一用户可能有多个 AD 对象（多林），因此，同步引擎将依赖 `Microsoft.InfromADUserAccountEnabled.xxx` 链接选取正确的对象。 请注意，由于此逻辑，必须将 Azure AD Connect 连接到主域控制器才可使用密码写回功能。  若需执行此操作，可右键单击 Active Directory 同步连接器的“属性”，然后选择“配置目录分区”将 Azure AD Connect 配置为使用主域控制器仿真器。 在此处查找“域控制器连接设置”部分，并勾选标题为“仅使用首选的域控制器”的框。 请注意：如果首选的 DC 不是 PDC 仿真器，Azure AD Connect 仍将访问 PDC 进行密码写回。
8. 一旦找到用户帐户，我们将尝试直接在相应的 AD 林中重置密码。
9. 如果密码设置操作成功，我们将告诉用户其密码已被修改，他们可以继续操作。 使用密码同步将用户密码同步到 Azure AD 时，可能本地密码策略比云密码策略要弱。 在这种情况下，我们仍然强制执行本地策略，并允许密码哈希同步对该密码的哈希进行同步。 这样无论使用密码同步或联合身份验证来提供单一登录，都可以确保本地策略在云中强制执行。
10. 如果密码设置操作失败，我们会将错误返回给用户，让他们再试一次。  操作失败的可能原因是服务已关闭、用户选择的密码不符合组织策略、我们在本地 AD 中找不到该用户等。  我们对于许多这类情况都有一个特定消息，并告知用户他们可以执行哪些操作来解决问题。

## <a name="scenarios-supported-for-password-writeback"></a>密码写回支持的方案
以下部分介绍同步功能的各个版本支持哪些方案。  一般而言，如果你要使用密码写回，我们始终建议使用 Azure AD Connect 的自动更新功能，或安装最新版本的 [Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect)。

* [**支持的 Azure AD Connect、Azure AD Sync 和 DirSync 客户端**](#supported-clients)
* [**密码写回所需的许可证**](#licenses-required-for-password-writeback)
* [**密码写回支持的本地身份验证模式**](#on-premises-authentication-modes-supported-for-password-writeback)
* [**密码写回支持的用户和管理员操作**](#user-and-admin-operations-supported-for-password-writeback)
* [**密码写回不支持的用户和管理员操作**](#user-and-admin-operations-not-supported-for-password-writeback)

### <a name="supported-clients"></a>支持的客户端
如果你要使用密码写回，我们始终建议使用 Azure AD Connect 的自动更新功能，或安装最新版本的 [Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect)。

* **DirSync（任何高于 1.0.6862 的版本）** - _不支持_ - 仅支持基本写回功能，不再受产品组的支持
* **Azure AD Sync** - _已弃用_ - 仅支持基本写回功能，缺少 Azure AD Connect 中提供的帐户解锁功能、丰富日志记录和可靠性方面的改进。 因此我们**强烈**建议升级。
* **Azure AD Connect** - _完全支持_ - 支持所有写回功能 - 请升级到最新版本，尽可能获得新增功能和最高的稳定性/可靠性

### <a name="licenses-required-for-password-writeback"></a>密码写回所需的许可证
若要使用密码写回，必须在租户中分配以下许可证之一。

* **Azure AD Premium P1** - 对密码写回的使用不实施限制
* **Azure AD Premium P2** - 对密码写回的使用不实施限制
* **Enterprise Moblity Suite** - 对密码写回的使用不实施限制
* **Enterprise Cloud Suite** - 对密码写回的使用不实施限制

无法配合任何 Office 365 许可计划（不管是试用版还是付费版）使用密码写回。 必须升级到上述计划之一才能使用此功能。

我们尚未计划为任何 Office 365 SKU 启用密码写回。

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>密码写回支持的本地身份验证模式
密码写回适用于以下用户密码类型：

* **仅限云的用户**：密码写回不适用于这种情况，因为没有任何本地密码
* **密码同步的用户**：受密码写回的支持
* **联合用户**：受密码写回的支持
* **直通身份验证用户**：受密码写回的支持

### <a name="user-and-admin-operations-supported-for-password-writeback"></a>密码写回支持的用户和管理员操作
在以下所有情况下，都会写回密码：

* **支持的最终用户操作**
 * 最终用户以自助形式执行的任何自愿更改密码操作
 * 最终用户以自助形式执行的任何强制更改密码操作（例如，在密码过期时）
 * 最终用户通过[密码重置门户](https://passwordreset.microsoftonline.com)以自助形式执行的任何密码重置操作
* **支持的管理员操作**
 * 管理员以自助形式执行的任何自愿更改密码操作
 * 管理员以自助形式执行的任何强制更改密码操作（例如，在密码过期时）
 * 管理员通过[密码重置门户](https://passwordreset.microsoftonline.com)以自助形式执行的任何密码重置操作
 * 管理员通过[经典 Azure 管理门户](https://manage.windowsazure.com)发起的任何最终用户密码重置操作
 * 管理员通过 [Azure 门户](https://portal.azure.com)发起的任何最终用户密码重置操作

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>密码写回不支持的用户和管理员操作
在以下任意情况下，都不会写回密码：

* **不支持的最终用户操作**
 * 任何最终用户使用 PowerShell v1、v2 或 Azure AD 图形 API 重置其自己的密码
* **不支持的管理员操作**
 * 管理员通过 [Office 管理门户](https://portal.office.com)发起的任何最终用户密码重置操作
 * 管理员通过 PowerShell v1、v2 或 Azure AD 图形 API 发起的任何最终用户密码重置操作

尽管我们正在努力消除这些限制，但对于何时能够分享解决方法，我们还无法提供具体的时间表。

## <a name="password-writeback-security-model"></a>密码写回安全模型
密码写回是高度安全、极其可靠的服务。  为确保你的信息受保护，我们启用了一个 4 层安全模型，如下所述。

* **租户特定的服务总线中继** - 如果用户设置该服务，我们将建立租户特定的总线服务中继，该中继受随机生成的强密码保护，而 Microsoft 绝不会接触此密码。
* **锁定的加密强密码加密密钥** - 创建服务总线中继后，我们创建一个强大的非对称密钥对，用于在经过线路时加密密码。  此密钥仅驻留在公司在云中的密钥存储内，就像目录中的任何密码一样，将被牢牢锁住并接受审核。
* **行业标准 TLS** - 云中发生密码重置或更改操作时，我们将采用纯文本密码并用公钥对其进行加密。  然后，我们将其置于 HTTPS 消息中，该消息使用 Microsoft 的 SSL 证书通过加密通道发送到服务总线中继。  此消息到达服务总线后，你的本地代理将唤醒、使用先前生成的强密码对服务总线进行身份验证、选取加密消息、使用我们生成的私钥对消息进行解密，然后尝试通过 AD DS SetPassword API 设置密码。  通过此步骤，我们可以在云中强制实施你的 AD 本地密码策略（复杂性、年龄、历史记录、筛选器等）。
* **消息过期策略** - 最后，如果由于某种原因而使消息位于服务总线中（因为本地服务关闭），消息会超时并在几分钟后删除，以便进一步提高安全性。

### <a name="password-writeback-encryption-details"></a>密码写回加密详细信息
下面介绍了在用户提交密码重置请求之后、但该请求到达本地环境之前，为了确保最高的服务可靠性和安全性，该请求所要经历的加密步骤。

* **步骤 1 - 使用 2048 位 RSA 密钥进行密码加密** - 用户提交要写回本地的密码之后，首先会使用 2048 位 RSA 密钥来加密提交的密码本身。

* **步骤 2 - 使用 AES-GCM 进行包级别的加密** - 然后，使用 AES-GCM 加密整个包（密码 + 所需的元数据）。 这可以防止任何人通过查看/篡改内容来直接访问底层服务总线通道。

* **步骤 3 - 所有通信通过 TLS/SSL 发生** - 此外，与服务总线的所有通信都在 SSL/TLS 通道中发生。 这可以保护未经授权的第三方发送的内容。

* **步骤 4 - 每隔 6 个月自动滚动更新密钥** - 最后，将每隔 6 个月，或者每当在 Azure AD Connect 中禁用/重新启动密码写回时，自动滚动更新所有这些密钥，确保最高的服务安全性与可靠性。

### <a name="password-writeback-bandwidth-usage"></a>密码写回带宽用量

密码写回是一种带宽消耗量极低的服务，只在以下情况下，才将请求发回到本地代理：

1. 通过 Azure AD Connect 启用或禁用该功能时发送两条消息。
2. 在服务运行的持续时间内，如果服务有检测信号，则每隔 5 分钟发送一条消息。
3. 每次提交新密码时发送两条消息，一条消息是执行操作的请求，后续的另一条消息包含操作结果。 在以下情况下，将发送这些消息。
4. 每次在用户自助重置密码期间提交新密码时。
5. 每次在用户执行密码更改操作期间提交新密码时。
6. 每次在管理员发起的用户密码重置期间提交新密码时（仅限通过 Azure 管理门户）

#### <a name="message-size-and-bandwidth-considerations"></a>消息大小和带宽注意事项

上述每条消息的大小通常小于 1Kb，这意味着，即使是在承受极高负载的情况下，密码写回服务本身消耗的带宽每秒最多也就是几个 Kb。 由于每条消息是只在密码更新操作有需要时才实时发送的，并且消息很小，因此，写回功能的带宽用量确实微不足道，产生的影响可以忽略不计。

## <a name="deploying-managing-and-accessing-password-reset-data-for-your-users"></a>部署、管理和访问用户的密码重置数据
可以通过 Azure AD Connect、PowerShell、Graph 或注册体验来管理和访问用户的密码重置数据。  甚至可以利用下面所述的选项在整个组织中部署密码重置，而无需用户注册该功能。

  * [密码重置使用哪些数据？](#what-data-is-used-by-password-reset)
  * [在无需最终用户注册的情况下部署密码重置](#deploying-password-reset-without-requiring-end-user-registration)
  * [用户注册密码重置时会发生什么情况？](#what-happens-when-a-user-registers)
  * [如何访问用户的密码重置数据](#how-to-access-password-reset-data-for-your-users)
  * [使用 PowerShell 设置密码重置数据](#setting-password-reset-data-with-powershell)
  * [使用 PowerShell 读取密码重置数据](#reading-password-reset-data-with-powershell)

### <a name="what-data-is-used-by-password-reset"></a>密码重置使用哪些数据？
下表概述密码重置期间此数据使用的位置和方式，旨在帮助你决定哪些身份验证选项适合你的组织。 此表还显示当你代表用户从未验证此数据的输入路径提供数据时的格式要求。

> [!NOTE]
> 注册门户中不会显示办公电话，因为用户当前无法在目录中编辑此属性。 只有管理员可以设置此值。
>
>

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>联系方式名称</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Active Directory 数据元素</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Azure Active Directory 数据元素</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用/设置位置</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>格式要求</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>办公电话</p>
            </td>
            <td>
              <p>telephoneNumber</p>
              <p>可将此属性同步到 Azure Active Directory 中的 PhoneNumber 属性并立即用于密码重置，而无需用户首先注册。</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>例如 Set-MsolUser -UserPrincipalName JWarner@contoso.com -PhoneNumber "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>使用位置：</p>
              <p>密码重置门户</p>
              <p>设置位置：</p>
              <p>可从 PowerShell、DirSync、Azure 管理门户和 Office 管理门户设置 PhoneNumber</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz（例如 +1 1234567890）</p>
              <ul>
                <li class="unordered">
必须提供国家/地区代码<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
必须提供区号（如果适用）<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
必须在国家/地区代码前加上 +<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
必须在国家/地区代码与其余号码之间留一个空格<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
不支持分机号，如果你已指定分机号，我们将在调度电话呼叫前去掉分机号。<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>移动电话</p>
            </td>
            <td>
              <p>移动电话</p>
              <p>可将此属性同步到 Azure Active Directory 中的 MobilePhone 属性并立即用于密码重置，而无需用户首先注册。</p>
              <p>目前无法将此属性同步到 AuthenticationPhone。</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>或</p>
              <p>MobilePhone</p>
              <p>（如果存在数据，则使用身份验证电话，否则将退回到移动电话字段）。</p>
              <p>例如 Set-MsolUser -UserPrincipalName JWarner@contoso.com -MobilePhone "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>使用位置：</p>
              <p>密码重置门户</p>
              <p>注册门户</p>
              <p>设置位置： </p>
              <p>可从密码重置注册门户或 MFA 注册门户设置 AuthenticationPhone。</p>
              <p>可从 PowerShell、Azure AD Connect、Azure 管理门户和 Office 管理门户设置 MobilePhone</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz（例如 +1 1234567890）</p>
              <ul>
                <li class="unordered">
必须提供国家/地区代码。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
必须提供区号（如果适用）。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
必须在国家/地区代码前加上 +。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
必须在国家/地区代码与其余号码之间留一个空格。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
不支持分机号，如果你已指定分机号，我们将在调度电话呼叫前去掉分机号。<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>备用电子邮件</p>
            </td>
            <td>
              <p>不可用</p>
              <p>目前无法将 Active Directory 中的值同步到 AuthenticationEmail 或 AlternateEmailAddresses[0] 属性。 </p>
              <p>可以使用 PowerShell 设置 AlternateEmailAddresses[0]。 紧接在此表格后面的部分中提供了相关说明。</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>或者</p>
              <p>AlternateEmailAddresses[0] </p>
              <p>（如果存在数据，则使用身份验证电子邮件，否则将退回到“备用电子邮件”字段）。</p>
              <p>注意：在目录中备用电子邮件字段被指定为字符串数组。  我们使用此数组中的第一个条目。</p>
              <p>例如 Set-MsolUser -UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses "email@live.com"</p>
            </td>
            <td>
              <p>使用位置：</p>
              <p>密码重置门户</p>
              <p>注册门户</p>
              <p>设置位置： </p>
              <p>可从密码重置注册门户或 MFA 注册门户设置 AuthenticationEmail。</p>
              <p>可从 PowerShell、Azure 管理门户和 Office 管理门户设置 AlternateEmail</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a> or 甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
电子邮件应遵循标准格式。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
支持 Unicode 电子邮件。<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>安全问答</p>
            </td>
            <td>
              <p>不可用</p>
              <p>目前无法同步 Active Directory 中的安全问答。</p>
            </td>
            <td>
              <p>不能直接在目录中修改。</p>
              <p>只能在最终用户密码重置注册过程中设置。</p>
            </td>
            <td>
              <p>使用位置：</p>
              <p>密码重置门户</p>
              <p>注册门户 </p>
              <p>设置位置： </p>
              <p>只能通过 Azure 管理门户设置安全问题。</p>
              <p>只能通过注册门户设置给定用户的安全问题答案。</p>
            </td>
            <td>
              <p>安全问题最多有 200 个字符，最少有 3 个字符</p>
              <p>答案最多有 40 个字符，最少有 3 个字符</p>
            </td>
          </tr>
        </tbody></table>


### <a name="deploying-password-reset-without-requiring-end-user-registration"></a>在无需最终用户注册的情况下部署密码重置
如果想要部署密码重置且无需用户注册该功能，可以使用以下两个选项之一轻松实现此目的。 这可能是解除阻止大量用户使用 SSPR，同时允许他们在整个注册过程中验证此信息的有效方式。

我们的许多大客户目前都在使用此功能，在极短的时间内实现密码重置。

#### <a name="synchronize-phone-numbers-with-azure-ad-connect"></a>使用 Azure AD Connect 同步电话号码
如果将数据同步到以下一个或两个字段，该数据可立即用于密码重置，而无需用户首先注册：

* 移动电话
* 办公电话

若要了解需要在本地更新哪些属性，请转到[密码重置使用哪些数据？](#what-data-is-used-by-password-reset)部分，然后查找上面提到的字段。  

请确保任何电话号码采用“+1 1234567890”格式，使其能够在我们的系统中正常工作。

#### <a name="set-phone-numbers-or-emails-with-powershell"></a>使用 PowerShell 设置电话号码或电子邮件
如果设置了其中的一个或多个字段，这些字段可立即用于密码重置，而无需用户首先注册：

* 移动电话
* 办公电话
* 备用电子邮件

若要了解如何使用 PowerShell 设置这些属性，请转到[使用 PowerShell 设置密码重置数据](#setting-password-reset-data-with-powershell)部分。

请确保任何电话号码采用“+1 1234567890”格式，使其能够在我们的系统中正常工作。

### <a name="what-happens-when-a-user-registers"></a>当用户注册时会发生什么情况？
当用户注册时，注册页面**始终**设置以下字段：

* 身份验证电话
* 身份验证电子邮件
* 安全问答

如果你提供了“移动电话”或“备用电子邮件”的值，用户可以立即使用这些信息重置密码，即使他们尚未注册该服务。  此外，用户在首次注册时将看到这些值，并可随意进行修改。  但是，成功注册之后，这些值将分别保存在“身份验证电话”和“身份验证电子邮件”字段中。

### <a name="how-to-access-password-reset-data-for-your-users"></a>如何访问用户的密码重置数据
#### <a name="data-settable-via-synchronization"></a>可通过同步设置的数据
可以从本地同步以下字段：

* 移动电话
* 办公电话

#### <a name="data-settable-with-azure-ad-powershell--azure-ad-graph"></a>可使用 Azure AD PowerShell 和 Azure AD Graph 设置的数据
可以使用 Azure AD PowerShell 和 Azure AD 图形 API 设置以下字段：

* 备用电子邮件
* 移动电话
* 办公电话

#### <a name="data-settable-with-registration-ui-only"></a>只能使用注册 UI 设置的数据
只能通过 SSPR 注册 UI (https://aka.ms/ssprsetup) 访问以下字段：

* 安全问答

#### <a name="data-readable-with-azure-ad-powershell--azure-ad-graph"></a>可使用 Azure AD PowerShell 和 Azure AD Graph 读取的数据
可以使用 Azure AD PowerShell 和 Azure AD 图形 API 访问以下字段：

* 备用电子邮件
* 移动电话
* 办公电话
* 身份验证电话
* 身份验证电子邮件

### <a name="setting-password-reset-data-with-powershell"></a>使用 PowerShell 设置密码重置数据
可以使用 Azure AD PowerShell 设置以下字段的值。

* 备用电子邮件
* 移动电话
* 办公电话

**_PowerShell V1_**

若要开始，首先需要[下载并安装 Azure AD PowerShell 模块](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)。  安装后，你可以遵照以下步骤配置每个字段。

**_PowerShell V2_**

若要开始，首先需要[下载并安装 Azure AD V2 PowerShell 模块](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md)。 安装后，你可以遵照以下步骤配置每个字段。

若要通过支持 Install-Module 的最新版本的 PowerShell 快速安装，请运行以下命令（第一行只是检查是否已安装该模块）：

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="alternate-email---how-to-set-alternate-email-with-powershell"></a>备用电子邮件 - 如何使用 PowerShell 设置备用电子邮件
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
```

#### <a name="mobile-phone---how-to-set-mobile-phone-with-powershell"></a>移动电话 - 如何使用 PowerShell 设置移动电话
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 1234567890"
```

#### <a name="office-phone---how-to-set-office-phone-with-powershell"></a>办公电话 - 如何使用 PowerShell 设置办公电话
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"
```

### <a name="reading-password-reset-data-with-powershell"></a>使用 PowerShell 读取密码重置数据
可以使用 Azure AD PowerShell 读取以下字段的值。

* 备用电子邮件
* 移动电话
* 办公电话
* 身份验证电话
* 身份验证电子邮件

#### <a name="alternate-email---how-to-read-alternate-email-with-powershell"></a>备用电子邮件 - 如何使用 PowerShell 读取备用电子邮件
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select otherMails
```

#### <a name="mobile-phone---how-to-read-mobile-phone-with-powershell"></a>移动电话 - 如何使用 PowerShell 读取移动电话
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

**_PowerShell v2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select Mobile
```

#### <a name="office-phone---how-to-read-office-phone-with-powershell"></a>办公电话 - 如何使用 PowerShell 读取办公电话
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber
```

#### <a name="authentication-phone---how-to-read-authentication-phone-with-powershell"></a>身份验证电话 - 如何使用 PowerShell 读取身份验证电话
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

**_PowerShell V2_**

```
Not possible in PowerShell V2
```

#### <a name="authentication-email---how-to-read-authentication-email-with-powershell"></a>身份验证电子邮件 - 如何使用 PowerShell 读取身份验证电子邮件
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

**_PowerShell V2_**

```
Not possible in PowerShell V2
```
## <a name="how-does-password-reset-work-for-b2b-users"></a>B2B 用户如何使用密码重置？
所有 B2B 配置完全支持密码重置和更改。  请在下面阅读密码重置支持的 3 个具体的 B2B 案例。

1. **已有 Azure AD 租户的合作伙伴组织中的用户** - 如果与你合作的组织已有 Azure AD 租户，我们将**遵守该租户中已启用的任何密码重置策略**。 要使密码重置正常工作，合作伙伴组织只需确保启用 Azure AD SSPR（这不会给 O365 客户造成额外的费用）。可以遵照[密码管理入门](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)指南中的步骤启用 SSPR。
2. **已注册使用[自助注册](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup)的用户** - 如果与你合作的组织使用[自助注册](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup)功能来访问租户，我们将允许他们使用已注册的电子邮件来直接重置密码。
3. **B2B 用户** - 使用新的 [Azure AD B2B 功能](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)创建的任何新 B2B 用户也可以使用他们在邀请过程中注册的电子邮件来直接重置其密码。

若要测试上述任一功能，只需让上述合作伙伴用户之一转到 http://passwordreset.microsoftonline.com。  只要他们定义了备用电子邮件或身份验证电子邮件，密码重置就能按预期方式工作。  可在[密码重置使用哪些数据](https://azure.microsoft.com/en-us/documentation/articles/active-directory-passwords-learn-more/#what-data-is-used-by-password-reset)概述中找到有关此处所述 SSPR 使用的数据的信息。

## <a name="next-steps"></a>后续步骤
以下是所有 Azure AD 密码重置文档页面的链接：

* **你是否因登录时遇到问题而浏览至此？** 如果是这样， [可按以下方式更改和重置你的密码](active-directory-passwords-update-your-own-password.md#reset-my-password)。
* [**工作原理**](active-directory-passwords-how-it-works.md) - 了解六个不同的服务组件及其功能
* [**入门**](active-directory-passwords-getting-started.md) - 了解如何让用户重置和更改云密码或本地密码
* [**自定义**](active-directory-passwords-customize.md) - 了解如何根据组织的需求自定义服务的外观和行为
* [**最佳做法**](active-directory-passwords-best-practices.md) - 了解如何快速部署且有效管理组织的密码
* [**深入分析**](active-directory-passwords-get-insights.md) - 了解集成式报告功能
* [**常见问题**](active-directory-passwords-faq.md) - 获取常见问题的解答
* [**故障排除**](active-directory-passwords-troubleshoot.md) - 了解如何快速排查服务的问题

[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"

