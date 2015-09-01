<properties pageTitle="为 Expressroute 配置虚拟网络" description="本教程将指导你为 ExpressRoute 设置虚拟网络 (VNet)" documentationCenter="na" services="expressroute" authors="cherylmc" manager="adinah" editor="tysonn"/>

<tags ms.service="expressroute" ms.date="04/29/2015" wacn.date=""/>

#  为 ExpressRoute 配置虚拟网络和 VNet 网关

1. 登录到**管理门户**。
2. 在屏幕左下角，单击“新建”。在导航窗格中，单击“网络服务”，然后单击“虚拟网络”。单击“自定义创建”以启动配置向导。
3. 在“虚拟网络详细信息”页上，输入以下信息。有关详细信息页上的设置的详细信息，请参阅[“虚拟网络详细信息”页](https://msdn.microsoft.com/zh-cn/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNetDetails)。

	- **名称** - 为虚拟网络命名。在部署你的 VM 和 PaaS 实例时，将使用此虚拟网络名称，因此最好不要让此名称太复杂。
	- **位置** - 位置直接与你想让资源 (VM) 驻留在的物理位置（区域）有关。例如，如果你希望部署到此虚拟网络的 VM 的物理位置位于中国东部，请选择该位置。创建虚拟网络后，将无法更改与虚拟网络关联的区域。

4. 在“DNS 服务器和 VPN 连接”页上，输入以下信息，然后单击右下角的“下一步”箭头。有关此页上的设置的详细信息，请参阅[“DNS 服务器和 VPN 连接”页](https://msdn.microsoft.com/zh-cn/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETDNS)

	- **DNS 服务器** - 输入 DNS 服务器名称和 IP 地址，或从下拉列表中选择一个以前注册的 DNS 服务器。此设置不创建 DNS 服务器，但可以指定要用于对此虚拟网络进行名称解析的 DNS 服务器。
	- **配置站点到站点 VPN** - 选中“配置站点到站点 VPN”复选框。
	- **选择 ExpressRoute** - 选中“使用 ExpressRoute”复选框。仅当你在上一步中选中了“配置站点到站点 VPN”，才会显示此选项。
	- **本地网络** - 本地网络代表你的物理本地位置。你可以选择以前创建的本地网络，也可以创建一个新的本地网络。

	如果选择现有的本地网络，请跳过步骤 5（下文）。

5. 如果你要创建新的本地网络，则会看到“站点到站点连接”页。如果你选择了以前创建的本地网络，则不会在向导中显示此页，你可以转到下一部分。若要配置本地网络，请输入以下信息，然后单击“下一步”箭头。有关此页上的设置的详细信息，请参阅[“站点到站点配置”页](https://msdn.microsoft.com/zh-cn/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETSITE)。

	- **名称** - 要称呼本地（内部）网络站点的名称。
	- **VPN 设备 IP 地址** - 此设置与 ExpressRoute 不相关。你可以在此空间中输入任何虚构 IP 地址。
	- **地址空间** - 包括“起始 IP”和 CIDR（地址计数）。在此可以指定要通过虚拟网络网关发送到你本地内部位置的地址范围。
	- **添加地址空间** - 如果你提供多个要通过虚拟网络网关发送的地址范围，则可在此指定每个额外的地址范围。可以稍后在“本地网络”页中添加或删除范围。

6. 在“虚拟网络地址空间”页上，输入以下信息，然后单击右下角的复选框以配置网络。有相当多的规则与虚拟网络地址空间有关，因此你可能需要参阅[“虚拟网络地址空间”页](https://msdn.microsoft.com/zh-cn/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNET_ADDRESS)以了解详细信息。

	- **地址空间** - 包括起始 IP 和地址计数。请验证你指定的地址空间不与本地网络的任一个地址空间相重叠。
	- **添加子网** - 包括起始 IP 和地址计数。附加的子网不是必需的，但你可能需要为具有动态 IP 地址 (DIP) 的 VM 创建一个单独的子网。或者你可能需要在子网中拥有与 PaaS 实例分开的 VM。
	- 添加网关子网 - 单击此项可添加网关子网。网关子网仅用于此虚拟网络网关，并且是此配置必需的。

	***重要说明***
	ExpressRoute 的网关子网必须是 /28。


7. 单击页面底部的复选标记，此时将开始创建虚拟网络。创建完成时，将在“管理门户”的“网络”页上看到“状态”下列出的“已创建”。

8. 在“网络”页上，单击刚创建的虚拟网络，然后单击“仪表板”。
9. 在“仪表板”页的底部，单击“创建网关”，然后单击“是”。
10. 开始创建网关时，你将看到一条消息，告知网关已经开始创建。创建网关最多可能需要 15 分钟。
11. **将你的网络连接到线路。** 只有在你已确认线路已转为以下状态后，才继续按下面的说明进行操作： 

	- ServiceProviderState：已设置
	- 状态：已启用

	请确认你至少拥有一个已创建网关的 Azure 虚拟网络。网关子网必须是 /28 才能使用 ExpressRoute 连接，并且必须已启动且正在运行。

			PS C:> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
			Provisioned

<!---HONumber=60-->