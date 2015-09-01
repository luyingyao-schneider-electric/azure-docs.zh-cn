<properties
   pageTitle="管理和审核对资源的访问权限"
   description="使用基于角色的访问控制 (RBAC) 来管理用户对 Azure 中部署的资源的权限。"
   services="azure-portal"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-portal"
   ms.date="04/28/2015"
   wacn.date=""/>

# 管理和审核对资源的访问权限

使用 Azure 资源管理器可以确保组织中的用户拥有用于管理或访问资源的适当权限。资源管理器利用基于角色的访问控制 (RBAC)，因此，你可以轻松地向各个资源或资源组应用安全策略。例如，可以授予用户访问订阅中特定虚拟机的权限，允许用户管理订阅中的所有网站，但但不允许管理其他资源。

## 概念

对于基于角色的访问控制，你需要了解几个重要概念：

1. 主体 - 被授予权限的实体，例如用户、安全组或应用程序。
2. 角色 - 允许的操作集
3. 范围 - 角色应用到的级别，例如订阅、资源组或资源。
3. 角色分配 - 将主体添加到角色并设置范围的过程。

## 角色示例：
为了理解 RBAC 的概念，让我们探讨常见角色定义的一些示例：

| 角色 | 允许的操作 |
| ------- | ----------------- |
| 读取器 | */read（读取任何内容） |
| 所有者 | *       （读取/写入任何内容）|

若要将“读取者”角色分配给名为 **ExampleGroup** 的资源组的“用户 A”，并将“所有者”角色分配给整个订阅的“用户 B”，请进行以下分配：

| 角色 | 主体 | 范围 |
| --------|-------------|---------- |
| 读取器 | 用户 A | /subscriptions/{subscriptionId}/resourceGroups/examplegroup |
| 所有者 | 用户 B | /subscriptions/{subscriptionId} |

## 示例方案

在本主题中，你将了解如何通过 Azure PowerShell、适用于 Mac、Linux 和 Windows 的 Azure CLI 以及 REST API 执行以下常见方案。

1. 查看订阅中的可用角色，以及允许这些角色执行的操作。
2. 向整个订阅中某个组的成员授予“读取者”权限。
3. 向某个应用程序授予“参与者”权限，以允许该应用程序管理资源组中的资源。
4. 向单个用户授予对特定网站的“所有者”权限。
5. 列出资源组的审核日志。


## 如何使用 PowerShell 管理访问权限
如果你尚未安装最新版本的 Azure PowerShell，请参阅[安装和配置 Azure PowerShell](powershell-install-configure)。打开 Azure PowerShell 控制台。

1. 使用你的凭据登录到 Azure 帐户。该命令将返回有关你的帐户的信息。

        PS C:\> Add-AzureAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

2. 如果你有多个订阅，请提供要用于部署的订阅 ID。

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. 切换到 Azure 资源管理器模块。

        PS C:\> Switch-AzureMode AzureResourceManager

### 查看可用角色
若要查看订阅的所有可用角色，请运行 **Get-AzureRoleDefinition** 命令。

    PS C:\> Get-AzureRoleDefinition

    Name                          Id                            Actions                  NotActions
    ----                          --                            -------                  ----------
    API Management Service Con... /subscriptions/####... {Microsoft.ApiManagement/S...   {}
    Application Insights Compo... /subscriptions/####... {Microsoft.Insights/compon...   {}
    ...

### 向订阅的某个组授予“读取者”权限。
1. 在运行 **Get-AzureRoleDefinition** 命令时提供角色名称，以查看相应的“读取者”角色定义。检查允许的操作是否为你想要分配的操作。

        PS C:\> Get-AzureRoleDefinition Reader

        Name            Id                            Actions           NotActions
        ----            --                            -------           ----------
        Reader          /subscriptions/####...        {*/read}          {}

2. 通过运行 **Get-AzureADGroup** 命令获取所需的安全组。提供该组在订阅中的实际名称。下面显示了 ExampleAuditorGroup。

        PS C:\> $group = Get-AzureAdGroup -SearchString ExampleAuditorGroup

3. 为审核员安全组创建角色分配。当命令完成时，将返回新的角色分配。

        PS C:\> New-AzureRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader

        Mail               :
        RoleAssignmentId   : /subscriptions/####/providers/Microsoft.Authorization/roleAssignments/####
        DisplayName        : Auditors
        RoleDefinitionName : Reader
        Actions            : {*/read}
        NotActions         : {}
        Scope              : /subscriptions/####
        ObjectId           : ####

###向资源组的应用程序授予“参与者”权限。
1. 在运行 **Get-AzureRoleDefinition** 命令时提供角色名称，以查看相应的“参与者”角色定义。检查允许的操作是否为你想要分配的操作。

        PS C:\> Get-AzureRoleDefinition Contributor

2. 运行 **Get-AzureADServicePrincipal** 命令并提供订阅中应用程序的名称，以获取服务主体对象 ID。下面显示了 ExampleApplication。

        PS C:\> $service = Get-AzureADServicePrincipal -ServicePrincipalName ExampleApplicationName

3. 通过运行 **New-AzureRoleAssignment** 命令为服务主体创建角色分配。

        PS C:\> New-AzureRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

有关设置 Azure Active Directory 应用程序和服务主体的更全面说明，请参阅[使用 Azure 资源管理器对服务主体进行身份验证](resource-group-authenticate-service-principal)。

###向资源的用户授予“所有者”权限。
1. 在运行 **Get-AzureRoleDefinition** 命令时提供角色名称，以查看相应的“所有者”角色定义。检查允许的操作是否为你想要分配的操作。

        PS C:\> Get-AzureRoleDefinition Owner

2. 为用户创建角色分配。

        PS C:\> New-AzureRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###列出资源组的审核日志。
若要获取资源组的审核日志，请运行 **Get-AzureResourceGroupLog** 命令。

      PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleGroupName

## 如何使用适用于 Mac、Linux 和 Windows 的 Azure CLI

如果你未安装适用于 Mac、Linux 和 Windows 的 Azure CLI，或者没有配置要与 Azure CLI 配合使用的组织帐户，请参阅[安装和配置 Azure CLI](xplat-cli-install)。

1. 使用你的凭据登录到 Azure 帐户。该命令将返回登录的结果。

        azure login

        ...
        info:    login command OK

2. 如果你有多个订阅，请提供要用于部署的订阅 ID。

        azure account set <YourSubscriptionNameOrId>

3. 切换到 Azure 资源管理器模块。你将收到新模式确认。

        azure config mode arm

        info:     New mode is arm

### 查看可用角色
查看订阅的所有可用角色。将返回角色定义的列表。

    azure role list

### 向订阅的某个组授予“读取者”权限。
1. 获取“读取者”角色的角色定义。检查允许的操作是否为你想要分配的操作。

        azure role show Reader

        info:    Executing command role show
        + Getting role definitions
        data:    Name    Actions  NotActions
        data:    ------  -------  ----------
        data:    Reader  */read
        info:    role show command OK

2. 通过根据名称搜索组，获取所需的安全组及其 objectId。以下示例显示了 ExampleAuditorGroup。

        azure ad group show --search ExampleAuditorGroup

        info:    Executing command ad group show
        + Getting group list
        data:    Display Name:      ExampleAuditorGroup
        data:    ObjectId:          1c272299-9729-462a-8d52-7efe5ece0c5c
        data:    Security Enabled:  true
        data:    Mail Enabled:
        data:
        info:    ad group show command OK

3. 为安全组创建角色分配。

        azure role assignment create --objectId {group-object-id} -o Reader -c /subscriptions/{subscriptionId}/

        info:    Executing command role assignment create
        + Getting role definition id
        + Creating role assignment
        info:    role assignment create command OK


### 向资源组的应用程序授予“参与者”权限。
1. 获取“参与者”角色的角色定义。检查允许的操作是否为你想要分配的操作。

        azure role show Contributor

2. 获取应用程序的 ObjectId。提供要检索的应用程序的名称。

        azure ad sp show --search ExampleApplicationName

2. 为应用程序创建角色分配。

        azure role assignment create --ObjectId {service-principal-object-id} -o Contributor -c /subscriptions/{subscriptionId}/


###向特定网站的用户授予“所有者”权限。
1. 获取“所有者”角色的角色定义。检查允许的操作是否为你想要分配的操作。

        azure role show Owner

2. 为用户创建角色分配。

        azure role assignment create --mail "someone@example.com" -o Owner -c /subscriptions/{subscriptionId}/resourceGroups/{groupName}/providers/Microsoft.Web/sites/{mySiteName}


###列出资源组的审核日志。
若要获取资源组的审核日志，请运行 **azure group log show** 命令，并提供所需资源组的名称。

         azure group log show ExampleGroupName


## 如何使用 REST API
若要通过 Azure 资源管理器 REST API 管理基于角色的访问控制，必须在发送请求时设置常见标头和参数（包括身份验证令牌）。有关信息，请参阅[常见参数和标头](https://msdn.microsoft.com/zh-cn/library/azure/dn906885.aspx)。

若要发现支持的 api 版本，请运行：

      GET https://management.azure.com/providers/Microsoft.Authorization?api-version=2015-01-01

对于本主题，可以使用版本 `2014-10-01-preview`。

###创建角色分配
获取可用的角色定义。

    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-10-01-preview

创建角色分配。

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview
    {
      "properties": {
          "roleDefinitionId": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
          "principalId": "{principal-object-id}",
          "scope": "/subscriptions/{subscription-id}"
       }
    }


###删除角色分配

      Delete  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview


## 后续步骤

- [Microsoft Azure 门户中基于角色的访问控制](role-based-access-control-configure)
- [使用 Azure 经典门户创建新的 Azure 服务主体](resource-group-create-service-principal-portal)
- [通过 Azure 资源管理器对服务主体进行身份验证](resource-group-authenticate-service-principal)

<!---HONumber=61-->