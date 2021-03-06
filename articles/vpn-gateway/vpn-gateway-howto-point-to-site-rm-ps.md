---
title: "使用点到站点连接将计算机连接到 Azure 虚拟网络：PowerShell | Microsoft 文档"
description: "通过创建点到站点 VPN 网关连接，安全地将计算机连接到 Azure 虚拟网络。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: b0ccde30b93214b161558daf8e2b4e37e58711da
ms.lasthandoff: 04/12/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>使用 PowerShell 配置与 VNet 的点到站点连接
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 门户](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [经典 - Azure 门户](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

通过点到站点 (P2S) 配置，可以使单台客户端计算机与虚拟网络建立安全的连接。 P2S 是基于 SSTP（安全套接字隧道协议）的 VPN 连接。 如果要从远程位置（例如，从家里或会议室）连接到 VNet，或者只有少数几台客户端计算机需要连接到虚拟网络，点到站点连接将非常有用。 P2S 连接不需要 VPN 设备或面向公众的 IP 地址。 可从客户端计算机建立 VPN 连接。

本文介绍如何在 Resource Manager 部署模型中使用 PowerShell 通过点到站点连接来创建 VNet。 有关点到站点连接的详细信息，请参阅本文末尾的[点到站点常见问题解答](#faq)。

### <a name="deployment-models-and-methods-for-p2s-connections"></a>P2S 连接的部署模型和方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

下表显示 P2S 配置的两个部署模型和可用部署方法。 当有配置步骤相关的文章发布时，我们会直接从此表格链接到该文章。

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>基本工作流
![将计算机连接到 Azure VNet - 点到站点连接示意图](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

在此方案中，使用点到站点连接创建虚拟网络。 还可根据这些说明生成此配置所需的证书。 P2S 连接由以下项组成：具有 VPN 网关的 VNet、根证书 .cer 文件（公钥）、客户端证书和客户端上的 VPN 配置。 

针对此配置使用以下值。 在本文的第 [1](#declare) 部分中设置了变量。 可以将这些步骤用作演练并使用这些值而不更改它们，也可以更改这些值以反映自己的环境。 

### <a name="example"></a>示例值
* **名称：VNet1**
* **地址空间：192.168.0.0/16** 和 **10.254.0.0/16**<br>本示例中使用了多个地址空间，说明此配置可与多个地址空间一起使用。 但是，对于此配置，多个地址空间并不必要。
* **子网名称：FrontEnd**
  * **子网地址范围：192.168.1.0/24**
* **子网名称：BackEnd**
  * **子网地址范围：10.254.1.0/24**
* **子网名称：GatewaySubnet**<br>要使 VPN 网关正常工作，必须使用子网名称 GatewaySubnet。
  * **GatewaySubnet 地址范围：192.168.200.0/24** 
* **VPN 客户端地址池：172.16.201.0/24**<br>使用此点到站点连接连接到 VNet 的 VPN 客户端接收来自 VPN 客户端地址池的 IP 地址。
* **订阅：** 如果有多个订阅，请验证是否正在使用正确的订阅。
* **资源组：TestRG**
* **位置：美国东部**
* **DNS 服务器**：要用于名称解析的 DNS 服务器的 IP 地址。
* **GW 名称：Vnet1GW**
* **公共 IP 名称：VNet1GWPIP**
* **VpnType：RouteBased**

## <a name="before-beginning"></a>开始之前
* 确保你拥有 Azure 订阅。 如果你还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。
* 安装最新版本的 Azure Resource Manager PowerShell cmdlet。 有关安装 PowerShell cmdlet 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。 

## <a name="declare"></a>第 1 部分 - 登录并设置变量
在本部分中，将登录并声明用于此配置的值。 声明的值将在示例脚本中使用。 更改这些值以反映自己的环境。 也可以使用声明的值完成这些步骤作为练习。

1. 使用提升的权限打开 PowerShell 控制台，然后登录到 Azure 帐户。 该 cmdlet 将提示你提供登录凭据。 登录后它会下载你的帐户设置，以便这些信息可供 Azure PowerShell 使用。

  ```powershell
  Login-AzureRmAccount
  ```
2. 获取 Azure 订阅的列表。

  ```powershell  
  Get-AzureRmSubscription
  ```
3. 指定要使用的订阅。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
4. 声明要使用的值。 使用以下示例，在必要时将值替换为自己的值。

  ```powershell
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $DNS = "8.8.8.8"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="ConfigureVNet"></a>第 2 部分 - 配置 VNet
1. 创建资源组。

  ```powershell
  New-AzureRmResourceGroup -Name $RG -Location $Location
  ```
2. 为虚拟网络创建子网配置，并将其命名为 FrontEnd、BackEnd 和 GatewaySubnet。 这些前缀必须是已声明的 VNet 地址空间的一部分。

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
  ```
3. 创建虚拟网络。 指定的 DNS 服务器应该是可以解析所连接的资源名称的 DNS 服务器。 在此示例中，使用公共 IP 地址。 请务必使用自己的值。

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
  ```
4. 指定所创建的虚拟网络的变量。

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  ```
5. 请求动态分配的公共 IP 地址。 网关需要此 IP 地址才能正常运行。 稍后要将网关连接到网关 IP 配置。

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```


## <a name="Certificates"></a>第 3 部分 - 证书

Azure 使用证书对点到站点 VPN 的 VPN 客户端进行身份验证。 创建根证书以后，请将公共证书数据（不是密钥）作为 Base-64 编码的 X.509 .cer 文件导出。 然后，将公共证书数据从根证书上载到 Azure。

在使用点到站点连接连接到 VNet 的每台客户端计算机上，必须安装客户端证书。 客户端证书从根证书生成，安装在每个客户端计算机上。 如果未安装有效的客户端证书，而客户端尝试连接到 VNet，则身份验证会失败。

### <a name="cer"></a>步骤 1 - 获取根证书的 .cer 文件

#### <a name="enterprise-certificate"></a>企业证书
 
如果要使用企业级解决方案，可以使用现有的证书链。 获取要使用的根证书的 .cer 文件。

#### <a name="self-signed-root-certificate"></a>自签名根证书

如果使用的不是企业证书解决方案，则需要创建自签名根证书。 若要创建包含进行 P2S 身份验证所需的字段的自签名根证书，可以使用 PowerShell。 [使用 PowerShell 为点到站点连接创建自签名的根证书](vpn-gateway-certificates-point-to-site.md)将引导用户完成相关步骤，以便创建自签名的根证书。

> [!NOTE]
> 以前，为点到站点连接创建自签名根证书和生成客户端证书的建议方法是使用 makecert。 现在，也可以使用 PowerShell 来创建这些证书。 使用 PowerShell 的优势之一是能够创建 SHA-2 证书。 请参阅[使用 PowerShell 为点到站点连接创建自签名根证书](vpn-gateway-certificates-point-to-site.md)，了解所需的值。
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>导出自签名根证书的公钥

点到站点连接要求将公钥 (.cer) 上载到 Azure。 以下步骤帮助你导出自签名根证书的 .cer 文件。

1. 若要获取证书 .cer 文件，请打开“管理用户证书”。
2. 找到“Certificates - Current User\Personal\Certificates”中的“P2SRootCert”自签名根证书，然后右键单击。 单击“所有任务”，然后单击“导出”打开“证书导出向导”。
3. 在向导中，单击“下一步”。 选择“否，不导出私钥”，然后单击“下一步”。
4. 在“导出文件格式”页上，选择“Base-64 编码的 X.509 (.CER)”，然后单击“下一步”。 
5. 在“要导出的文件”页上，浏览到“C:”，然后创建名为“cert”的子目录并将其选中。 将证书文件命名为“P2SRootCert.cer”，然后单击“保存”。 
6. 单击“下一步”，然后单击“完成”导出证书。 此时会显示“导出成功”。 单击“确定”关闭向导。

### <a name="generate"></a>步骤 2 - 生成客户端证书
可以为每个客户端生成唯一证书，也可以在多个客户端上使用同一证书。 生成唯一客户端证书的优势是能够吊销单个证书。 否则，如果每个人都使用相同的客户端证书，则在需要撤销它时，必须为所有使用该证书进行身份验证的客户端生成并安装新证书。

#### <a name="enterprise-certificate"></a>企业证书
- 如果使用的是企业证书解决方案，请使用通用名称值格式“name@yourdomain.com”生成客户端证书，而不要使用“域名\用户名”格式。
- 请确保客户端证书基于“用户”证书模板，该模板使用“客户端身份验证”（而不是“智能卡登录”等）作为使用列表中的第一项。可以通过双击客户端证书，然后查看“详细信息”>“增强型密钥用法”来检查证书。

#### <a name="self-signed-root-certificate"></a>自签名根证书 
如果使用的是自签名根证书，请参阅[使用 PowerShell 生成客户端证书](vpn-gateway-certificates-point-to-site.md#clientcert)，了解生成与点到站点连接兼容的客户端证书的步骤。


### <a name="exportclientcert"></a>步骤 3 - 导出客户端证书

如果你根据 [PowerShell](vpn-gateway-certificates-point-to-site.md#clientcert) 说明从自签名根证书生成了客户端证书，该证书会自动安装在用于生成该证书的计算机上。 如果想要在另一台客户端计算机上安装客户端证书，则需导出该证书。
 
1. 若要导出客户端证书，请打开“管理用户证书”。 右键单击要导出的客户端证书，单击“所有任务”，然后单击“导出”打开“证书导出向导”。
2. 在向导中，单击“**下一步**”，选择“**是，导出私钥**”，然后单击“**下一步**”。
3. 在“导出文件格式”页上，保留选择默认值。 请确保选中“包括证书路径中的所有证书(如果可能)”，以便将所需根证书信息也导出。 然后单击“下一步”。
4. 在“**安全性**”页上，必须保护私钥。 如果选择使用密码，请务必记下或牢记为此证书设置的密码。 然后单击“下一步”。
5. 在“要导出的文件”中，“浏览”到要将证书导出的目标位置。 在“文件名”中，为证书文件命名。 然后单击“下一步”。
6. 单击“完成”导出证书。

### <a name="upload"></a>步骤 4 - 上载根证书 .cer 文件

创建网关后，可以将受信任根证书的 .cer 文件上载到 Azure。 最多可以上载 20 个根证书的文件。 请勿将根证书私钥上传到 Azure。 上载 .Cer 文件后，Azure 将使用它来对连接到虚拟网络的客户端进行身份验证。

1. 为证书名称声明变量，将值替换为自己的值：

  ```powershell
  $P2SRootCertName = "Mycertificatename.cer"
  ```
2. 将文件路径替换为自己的路径，然后运行 cmdlet。

  ```powershell
  $filePathForCert = "C:\cert\Mycertificatename.cer"
  $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
  $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
  $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
  ```


## <a name="creategateway"></a>第 4 部分 - 创建 VPN 网关
为 VNet 配置和创建虚拟网络网关。 -GatewayType 必须是 **Vpn**，且 -VpnType 必须是 **RouteBased**。 VPN 网关可能最多需要 45 分钟才能完成。

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
-VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert
```


## <a name="clientconfig"></a>第 5 部分 - 下载 VPN 客户端配置包
若要使用点到站点 VPN 连接到 VNet，每个客户端都必须安装 VPN 客户端配置包。 该包不安装 VPN 客户端。 它使用连接到虚拟网络所需的设置配置本机 Windows VPN 客户端。 有关支持的客户端操作系统列表，请参阅本文末尾的[点到站点连接常见问题解答](#faq)。

1. 创建网关后，可生成和下载客户端配置包。 此示例下载 64 位客户端包。 若要下载 32 位客户端，将“Amd64”替换为“x86”。 还可以使用 Azure 门户下载 VPN 客户端。

  ```powershell
  Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
  -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
  ```
2. 复制并粘贴返回到 Web 浏览器的链接以下载包，注意删除链接的 """。 
3. 下载该包并将其安装在客户端计算机上。 如果显示 SmartScreen 弹出窗口，请单击“更多信息”，然后单击“仍要运行”。 也可将要安装的包保存在其他客户端计算机上。
4. 在客户端计算机上，导航到“网络设置”，然后单击“VPN”。 VPN 连接显示所连接到的虚拟网络的名称。



## <a name="clientcertificate"></a>第 6 部分 - 安装已导出的客户端证书

如果想要从另一台客户端计算机（而不是用于生成客户端证书的计算机）创建 P2S 连接，需要安装客户端证书。 安装客户端证书时，需要使用导出客户端证书时创建的密码。

1. 找到 *.pfx* 文件并将其复制到客户端计算机。 在客户端计算机上，双击 *.pfx* 文件以进行安装。 将“存储位置”保留为“当前用户”，然后单击“下一步”。
2. 在“要导入的**文件**”页上，不要进行任何更改。 单击“资源组名称” 的 Azure 数据工厂。
3. 在“私钥保护”页上，输入证书的密码，或验证安全主体是否正确，然后单击“下一步”。
4. 在“**证书存储**”页上，保留默认位置，然后单击“**下一步**”。
5. 单击“**完成**”。 在证书安装的“**安全警告**”上，单击“**是**”。 可随时单击“是”，因为证书已生成。 现已成功导入证书。

## <a name="connect"></a>第 7 部分：连接到 Azure
1. 要连接到 VNet，请在客户端计算机上，导航到 VPN 连接，找到创建的 VPN 连接。 其名称与虚拟网络的名称相同。 单击“连接”。 可能会出现与使用证书相关的弹出消息。 单击“继续”使用提升的权限。 
2. 在“连接”状态页上，单击“连接”以启动连接。 如果看到“选择证书”屏幕，请确保所显示的客户端证书是要用来连接的证书。 如果不是，请使用下拉箭头选择正确的证书，然后单击“确定”。
   
    ![VPN 客户端连接到 Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. 连接已建立。
   
    ![已建立连接](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

如果连接时遇到问题，请检查以下项：

- 打开“管理用户证书”，然后导航到“受信任的根证书颁发机构\证书”。 验证根证书是否已列出。 若要进行身份验证，根证书必须存在。 使用默认值“包括证书路径中的所有证书(如果可能)”导出客户端证书 .pfx 时，根证书信息也会导出。 安装客户端证书时，根证书也会安装在客户端计算机上。 

- 如果使用的是通过企业 CA 解决方案颁发的证书，并且无法进行身份验证，请检查客户端证书上的身份验证顺序。 可以通过双击客户端证书，并转到“详细信息”>“增强型密钥用法”来检查身份验证列表顺序。 请确保此列表显示的第一项是“客户端身份验证”。 如果不是，则需要基于将“客户端身份验证”作为列表中第一项的用户模板颁发客户端证书。  


## <a name="verify"></a>第 8 部分 - 验证连接
1. 若要验证你的 VPN 连接是否处于活动状态，请打开提升的命令提示符，然后运行 *ipconfig/all*。
2. 查看结果。 请注意，你收到的 IP 地址是在配置中指定的点到站点 VPN 客户端地址池中的地址之一。 结果与以下示例类似：
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled


## <a name="connectVM"></a>连接到虚拟机

1. 连接到 VNet 后，即可通过 P2S 连接连接到 VM。 若要连接到 VM，需要虚拟机的专用 IP 地址。 以下示例演示了如何通过 [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkinterface?view=azurermps-3.7.0) 获取专用 IP 地址。 结果返回一个列表，其中包含所有资源组中的 VM 和相应的专用 IP 地址。 

  ```powershell   
  $vms = get-azurermvm
  $nics = get-azurermnetworkinterface | where VirtualMachine -NE $null #skip Nics with no VM
  
  foreach($nic in $nics)
  {
    $vm = $vms | where-object -Property Id -EQ $nic.VirtualMachine.id
    $prv =  $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAddress
    $alloc =  $nic.IpConfigurations | select-object -ExpandProperty PrivateIpAllocationMethod
    Write-Output "$($vm.Name) : $prv , $alloc"
  }
  ```
2. 使用以下命令创建与虚拟机的远程桌面会话。 将该 IP 地址替换为专用 IP 地址，后者与要连接到的 VM 相关联。 出现提示时，输入创建虚拟机时使用的凭据。 

  ```powershell   
  mstsc /v:192.168.1.4
  ```

如果你在通过 P2S 连接到虚拟机时出现问题，请使用“ipconfig”检查分配给以太网适配器的 IPv4 地址，该适配器所在的计算机正是你要从其进行连接的计算机。 如果该 IP 地址位于你要连接到的 VNet 的地址范围内，或者位于 VPNClientAddressPool 的地址范围内，则称为地址空间重叠。 当地址空间以这种方式重叠时，网络流量不会抵达 Azure，而是呆在本地网络中。 如果网络地址空间不重叠，但你仍然无法连接到 VM，请参阅[排查通过远程桌面连接到 VM 时的问题](../virtual-machines/windows/troubleshoot-rdp-connection.md)。

## <a name="addremovecert"></a>添加或删除受信任的根证书

可以在 Azure 中添加和删除受信任的根证书。 删除受信任的根证书后，从该根证书生成的客户端证书将无法通过点到站点配置连接到 Azure。 如果希望客户端进行连接，则需安装新客户端证书，该证书是从 Azure 中受信任的证书生成的。

### <a name="to-add-a-trusted-root-certificate"></a>添加受信任的根证书
最多可以将 20 个受信任的根证书 .cer 文件添加到 Azure。 以下步骤用于添加根证书：

1. 创建并准备要添加到 Azure 的新根证书。 将公钥导出为 Base-64 编码 X.509 (.CER) 文件并使用文本编辑器打开它。 复制值，如以下示例所示：
   
  ![证书](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

    > [!NOTE]
    > 复制证书数据时，请确保将文本复制为一个无回车符或换行符的连续行。 可能需要在文本编辑器中将视图修改为“显示符号/显示所有字符”以查看回车符和换行符。
  >
  >

2. 将证书名称和密钥信息指定为变量。 将该信息替换为自己的信息，如以下示例所示：

  ```powershell
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
3. 添加新的根证书。 一次只能添加一个证书。

  ```powershell
  Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
4. 可以使用以下示例来验证是否已正确添加新证书。

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```


### <a name="to-remove-a-trusted-root-certificate"></a>删除受信任的根证书


1. 声明变量。

  ```powershell
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  $P2SRootCertName2 = "ARMP2SRootCert2.cer"
  $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
  ```
2. 删除证书。

  ```powershell
  Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
  ```
3. 使用以下示例来验证是否已成功删除证书。

  ```powershell
  Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
  -VirtualNetworkGatewayName "VNet1GW"
  ```

## <a name="revoke"></a>吊销客户端证书
你可以吊销客户端证书。 证书吊销列表可让你选择性地拒绝基于单个客户端证书的点到站点连接。 这不同于删除受信任的根证书。 如果从 Azure 中删除受信任的根证书 .cer，它会吊销由吊销的根证书生成/签名的所有客户端证书的访问权限。 如果吊销客户端证书而非根证书，则可继续使用从根证书生成的其他证书进行身份验证。

常见的做法是使用根证书管理团队或组织级别的访问权限，然后使用吊销的客户端证书针对单个用户进行精细的访问控制。

### <a name="to-revoke-a-client-certificate"></a>吊销客户端证书

1. 检索客户端证书指纹。 有关详细信息，请参阅[如何检索证书的指纹](https://msdn.microsoft.com/library/ms734695.aspx)。
2. 将信息复制到一个文本编辑器，删除所有空格，使之成为一个连续的字符串。 该字符串在下一步声明为变量。
3. 声明变量。 确保声明在前面的步骤中检索的指纹。

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
4. 将指纹添加到已吊销证书的列表。 添加指纹后，会显示“成功”。

  ```powershell
  Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
  -Thumbprint $RevokedThumbprint1
  ```
5. 确认指纹已添加到证书吊销列表。

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```
6. 添加指纹后，不再可以使用证书来连接。 客户端在尝试使用此证书进行连接时，会收到一条消息，指出证书不再有效。

### <a name="to-reinstate-a-client-certificate"></a>恢复客户端证书
可以通过从吊销的客户端证书列表中删除指纹来恢复客户端证书。

1. 声明变量。 确保为需要恢复的证书声明正确的指纹。

  ```powershell
  $RevokedClientCert1 = "NameofCertificate"
  $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
  $GWName = "Name_of_virtual_network_gateway"
  $RG = "Name_of_resource_group"
  ```
2. 从证书吊销列表中删除证书指纹。

  ```powershell
  Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
  -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
  ```
3. 检查指纹是否已从吊销列表中删除。

  ```powershell
  Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
  ```

## <a name="faq"></a>点到站点常见问题解答

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>后续步骤
连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。 若要详细了解网络和虚拟机，请参阅 [Azure 和 Linux VM 网络概述](../virtual-machines/linux/azure-vm-network-overview.md)。
