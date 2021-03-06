---
title: "Azure Active Directory 身份验证库 | Microsoft 文档"
description: "通过 Azure AD 身份验证库 (ADAL)，客户端应用程序开发人员能够轻松利用云或本地 Active Directory (AD) 对用户进行身份验证，然后获取访问令牌，以进行安全的 API 调用。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: bryanla
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 2ad287d383f65ea66382d1f449a76db596963f46
ms.lasthandoff: 03/29/2017


---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory 身份验证库
通过 Azure AD 身份验证库 (ADAL)，客户端应用程序开发人员能够轻松利用云或本地 Active Directory (AD) 对用户进行身份验证，然后获取访问令牌，以进行安全的 API 调用。 ADAL 提供许多可以方便开发人员进行身份验证的功能，例如，异步支持、用于存储访问令牌和刷新令牌的可配置令牌缓存、访问令牌过期时和提供刷新令牌时自动刷新令牌，等等。 ADAL 可以应对大部分复杂情况，因而可以帮助开发人员集中处理其应用程序中的业务逻辑，并可轻松保护资源而不必成为安全方面的专家。

可在各种平台上使用 ADAL。

### <a name="client-libraries"></a>客户端库

| 平台 | 库 | 下载 | 源代码 | 示例 | 引用
| --- | --- | --- | --- | --- | --- |
| .NET 客户端、Windows 应用商店、UWP、Xamarin iOS 和 Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [桌面应用](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-dotnet) |[参考](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) | 
| .NET 客户端、Windows 应用商店、Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2) | [桌面应用](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) |[参考](https://docs.microsoft.com/en-us/active-directory/adal//v2/microsoft.identitymodel.clients.activedirectory) | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[单页应用](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS、macOS |ADAL |[CocoaPods](http://cocoadocs.org/docsets/ADAL/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS 应用](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-ios) | [参考](http://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[中央存储库](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android 应用](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java Web 应用](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-java) | |

### <a name="server-libraries"></a>服务器库 

| 平台 | 库 | 下载 | 源代码 | 示例 | 引用
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN for AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC 应用](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN for OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Web 应用](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web API](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |适用于 WS 联合身份验证的 OWIN |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC Web 应用](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |适用于 .NET 4.5 的标识协议扩展 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |适用于 .NET 4.5 的 JWT 处理程序 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |



## <a name="scenarios"></a>方案
以下是可以使用 ADAL 进行身份验证的三种常见方案。  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>使用远程资源对客户端应用程序的用户进行身份验证
在此方案中，开发人员有一个客户端（如 WPF 应用程序）需要访问由 Azure AD 保护的远程资源（如 Web API）。 他有一个 Azure 订阅，知道如何调用下游 Web API，还知道 Web API 使用哪个 Azure AD 租户。 因此，他可以通过将身份验证体验完全委托给 ADAL 或通过显式处理用户凭据，来使用 ADAL 实现通过 Azure AD 进行身份验证。 使用 ADAL 可以轻松地对用户进行身份验证，从 Azure AD 获取访问令牌并刷新该令牌，然后使用访问令牌向 Web API 发出请求。

有关使用 Azure AD 身份验证演示此方案的代码示例，请参阅[本机客户端 WPF 应用程序到 Web API](https://github.com/azureadsamples/nativeclient-dotnet)。

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>使用远程资源对服务器应用程序进行身份验证
在此方案中，开发人员在服务器上有一个正在运行的应用程序需要访问由 Azure AD 保护的远程资源（如 Web API）。 他有一个 Azure 订阅，知道如何调用下游服务，还知道 Web API 使用哪个 Azure AD 租户。 因此，他可以通过显式处理应用程序的凭据，来使用 ADAL 实现通过 Azure AD 进行身份验证。 使用 ADAL 可以轻松地使用应用程序的客户端凭据从 Azure AD 中检索令牌，然后使用该令牌向 Web API 发出请求。 ADAL 还通过缓存访问令牌并在必要时续订它，来处理对访问令牌生存期的管理。 有关演示此方案的代码示例，请参阅[控制台应用程序到 Web API](https://github.com/AzureADSamples/Daemon-DotNet)。

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>对代表用户访问远程资源的服务器应用程序进行身份验证
在此方案中，开发人员在服务器上有一个正在运行的应用程序需要访问由 Azure AD 保护的远程资源（如 Web API）。 请求也需要代表 Azure AD 中的用户发出。 他有一个 Azure 订阅，知道如何调用下游 Web API，还知道服务使用哪个 Azure AD 租户。 用户通过 Web 应用程序的身份验证后，应用程序可以从 Azure AD 获取该用户的授权代码。 然后，Web 应用程序可以使用该授权代码以及与应用程序关联的客户端凭据，代表用户通过 ADAL 从 Azure AD 中获取访问令牌和刷新令牌。 Web 应用程序拥有访问令牌后，就可以调用 Web API，直到该令牌过期。 令牌过期后，Web 应用程序可以使用前面收到的刷新令牌，通过 ADAL 获取新的访问令牌。

## <a name="see-also"></a>另请参阅
[Azure Active Directory 开发人员指南](active-directory-developers-guide.md)

[Azure Active directory 的身份验证方案](active-directory-authentication-scenarios.md)

[Azure Active Directory 代码示例](active-directory-code-samples.md)

