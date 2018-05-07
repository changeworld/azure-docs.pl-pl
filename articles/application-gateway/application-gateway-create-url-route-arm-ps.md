---
title: Utwórz bramę aplikacji z adresu URL na podstawie ścieżki reguły routingu - programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć adres URL na podstawie ścieżki reguły routingu dla aplikacji bramy i maszyny wirtualnej zestaw skalowania przy użyciu programu Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 749d958840d2733537c2fc2bb025ab0becb800c1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="create-an-application-gateway-with-url-path-based-routing-rules-using-azure-powershell"></a>Utwórz bramę aplikacji z adresu URL na podstawie ścieżki reguły routingu przy użyciu programu Azure PowerShell

Służy do konfigurowania programu Azure PowerShell [reguł routingu na podstawie ścieżki adresu URL](application-gateway-url-route-overview.md) podczas tworzenia [brama aplikacji w](application-gateway-introduction.md). W tym samouczku, tworzenia pul zaplecza przy użyciu [zestaw skali maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Następnie można utworzyć reguły routingu, które upewnij się, że ruch w sieci web dociera do odpowiednich serwerów w pulach.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Utwórz bramę aplikacji z mapą adresu URL
> * Utwórz zestawy skalowania maszyny wirtualnej z pul zaplecza

![Przykład routingu adresów URL](./media/application-gateway-create-url-route-arm-ps/scenario.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 3.6 lub nowszej. Aby znaleźć wersję, uruchom ` Get-Module -ListAvailable AzureRM` . Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Tworzenie grupy zasobów platformy Azure przy użyciu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Tworzenie konfiguracji podsieci *myAGSubnet* i *myBackendSubnet* przy użyciu [AzureRmVirtualNetworkSubnetConfig nowy](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Utwórz sieć wirtualną o nazwie *myVNet* przy użyciu [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) z konfiguracjami podsieci. I na koniec Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu [AzureRmPublicIpAddress nowy](/powershell/module/azurerm.network/new-azurermpublicipaddress). Te zasoby są używane do zapewnienia możliwości połączenia sieci z bramy aplikacji i jej skojarzonych zasobów.

```azurepowershell-interactive
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

### <a name="create-the-ip-configurations-and-frontend-port"></a>Tworzenie konfiguracji adresów IP i port serwera sieci Web

Skojarz *myAGSubnet* wcześniej utworzony przy użyciu bramy aplikacji [AzureRmApplicationGatewayIPConfiguration nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Przypisz *myAGPublicIPAddress* do bramy aplikacji przy użyciu [AzureRmApplicationGatewayFrontendIPConfig nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$pip = Get-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-default-pool-and-settings"></a>Utwórz pulę domyślną i ustawienia

Tworzenie domyślnej puli wewnętrznej bazy danych o nazwie *appGatewayBackendPool* dla bramy aplikacji przy użyciu [AzureRmApplicationGatewayBackendAddressPool nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Skonfiguruj ustawienia dla puli wewnętrznej bazy danych za pomocą [AzureRmApplicationGatewayBackendHttpSettings nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$defaultPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Tworzenie odbiornika domyślne i reguły

Odbiornik wymaganego do włączenia usługi Brama aplikacji w celu kierowania ruchu odpowiednio do puli wewnętrznej bazy danych. W tym samouczku utworzysz dwa odbiorniki. Pierwszy odbiornika podstawowe utworzonego nasłuchuje ruchu pod adresem URL katalogu głównego. Drugi odbiornik, który można utworzyć nasłuchuje ruchu na określone adresy URL.

Utworzenie obiektu nasłuchującego domyślny o nazwie *myDefaultListener* przy użyciu [AzureRmApplicationGatewayHttpListener nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) z konfiguracji serwera sieci Web i portu frontonu, która została wcześniej utworzona. Reguła jest wymagana dla odbiornika wiedzieć, który puli wewnętrznej bazy danych mają być używane dla ruchu przychodzącego. Utwórz podstawowe reguły o nazwie *rule1* przy użyciu [AzureRmApplicationGatewayRequestRoutingRule nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name myDefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Teraz, gdy utworzono niezbędne dodatkowe zasoby, należy określić parametry dla bramy aplikacji o nazwie *myAppGateway* przy użyciu [AzureRmApplicationGatewaySku nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku), a następnie utwórz ją za pomocą [ Nowy AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway).

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="add-image-and-video-backend-pools-and-port"></a>Dodawanie obrazu i wideo pul zaplecza i portu

Można dodać puli wewnętrznej bazy danych o nazwie *imagesBackendPool* i *videoBackendPool* z bramą aplikacji przy użyciu [Add-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/add-azurermapplicationgatewaybackendaddresspool). Dodawanie portu frontonu dla pul przy użyciu [AzureRmApplicationGatewayFrontendPort Dodaj](/powershell/module/azurerm.network/add-azurermapplicationgatewayfrontendport). Następnie przesłać zmian bramy aplikacji przy użyciu [AzureRmApplicationGateway zestawu](/powershell/module/azurerm.network/set-azurermapplicationgateway).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
Add-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool 
Add-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool
Add-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-backend-listener"></a>Dodaj odbiornika wewnętrznej bazy danych

Dodaj odbiornika wewnętrznej bazy danych o nazwie *backendListener* który jest potrzebne do kierowania ruchu przy użyciu [AzureRmApplicationGatewayHttpListener Dodaj](/powershell/module/azurerm.network/add-azurermapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPort = Get-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport
$fipconfig = Get-AzureRmApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw
Add-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>Dodaj mapę ścieżki adresu URL

Adres URL ścieżki mapy upewnij się, że określone adresy URL są kierowane do pul zaplecza określone. Można utworzyć mapowania ścieżki adresu URL o nazwie *imagePathRule* i *videoPathRule* przy użyciu [AzureRmApplicationGatewayPathRuleConfig nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewaypathruleconfig) i [ Dodaj AzureRmApplicationGatewayUrlPathMapConfig](/powershell/module/azurerm.network/add-azurermapplicationgatewayurlpathmapconfig).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$poolSettings = Get-AzureRmApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings
$imagePool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool
$videoPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool
$defaultPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings
$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
    -Paths "/video/*" `
    -BackendAddressPool $videoPool `
    -BackendHttpSettings $poolSettings
Add-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rule"></a>Dodaj regułę routingu

Reguły routingu kojarzy mapy adresu URL przy użyciu odbiornika, który został utworzony. Można dodać reguły o nazwie **rule2* przy użyciu [AzureRmApplicationGatewayRequestRoutingRule Dodaj](/powershell/module/azurerm.network/add-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendlistener = Get-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener
$urlPathMap = Get-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap
Add-AzureRmApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Utwórz zestawy skalowania maszyny wirtualnej

W tym przykładzie utworzysz trzy zestawy skalowania maszyn wirtualnych, które obsługują trzy pule zaplecza, które zostały utworzone. Zestawy skalowania, które możesz utworzyć są nazywane *myvmss1*, *myvmss2*, i *myvmss3*. Każdy zestaw skalowania zawiera dwa wystąpienia maszyny wirtualnej, na których można zainstalować usługi IIS. Można przypisać zestaw do puli wewnętrznej bazy danych podczas konfigurowania ustawień IP skalowania.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw
$imagesPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw
$videoPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw
for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }
  $ipConfig = New-AzureRmVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId
  $vmssConfig = New-AzureRmVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic
  Set-AzureRmVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest
  Set-AzureRmVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i
  Add-AzureRmVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig
  New-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Instalowanie usług IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/vhorne/samplescripts/master/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i
  Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzureRmVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Można użyć [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) uzyskać publiczny adres IP bramy aplikacji. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Takie jak *http://52.168.55.24*, *http://52.168.55.24:8080/images/test.htm*, lub *http://52.168.55.24:8080/video/test.htm*.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Podstawowy adres URL testu bramy aplikacji](./media/application-gateway-create-url-route-arm-ps/application-gateway-iistest.png)

Zmień adres URL do http://<ip-address>:8080/video/test.htm, zastępując adresu IP < adres ip >, a powinien zostać wyświetlony ekran podobny do następującego:

![Adres URL obrazów testu w bramy aplikacji](./media/application-gateway-create-url-route-arm-ps/application-gateway-iistest-images.png)

Zmień adres URL do http://<ip-address>:8080/video/test.htm i powinny zostać wyświetlone informacje, jak w następującym przykładzie:

![Testuj adres URL wideo w bramy aplikacji](./media/application-gateway-create-url-route-arm-ps/application-gateway-iistest-video.png)

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Utwórz bramę aplikacji z mapą adresu URL
> * Utwórz zestawy skalowania maszyny wirtualnej z pul zaplecza

Aby dowiedzieć się więcej na temat bram aplikacji i ich skojarzonych zasobów, nadal artykuły.
