---
title: Utwórz bramę aplikacji z adresu URL przekierowania na podstawie ścieżki - programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji z adresu URL na podstawie ścieżki przekierowywania ruchu przy użyciu programu Azure PowerShell.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: davidmu
ms.openlocfilehash: 85a684d4d988bdc01acdb3af3ddce028270cf105
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-application-gateway-with-url-path-based-redirection-using-azure-powershell"></a>Utwórz bramę aplikacji z adresu URL przekierowania na podstawie ścieżki przy użyciu programu Azure PowerShell

Służy do konfigurowania programu Azure PowerShell [reguł routingu na podstawie adresu URL](application-gateway-url-route-overview.md) podczas tworzenia [brama aplikacji w](application-gateway-introduction.md). W tym samouczku, tworzenia pul zaplecza przy użyciu [zestawy skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Następnie można utworzyć reguły routingu adres URL, które upewnij się, że ruch w sieci web jest przekierowywany do puli zaplecza odpowiednie.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji
> * Dodaj odbiorników i reguły routingu
> * Utwórz zestawy skalowania maszyny wirtualnej dla pul zaplecza

W poniższym przykładzie przedstawiono lokacji ruchu przychodzącego z portów 8080 i 8081 i być kierowany do tej samej puli wewnętrznej bazy danych:

![Przykład routingu adresów URL](./media/tutorial-url-redirect-powershell/scenario.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 3.6 lub nowszej. Aby znaleźć wersję, uruchom ` Get-Module -ListAvailable AzureRM` . Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Tworzenie grupy zasobów platformy Azure przy użyciu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Tworzenie konfiguracji podsieci dla *myBackendSubnet* i *myAGSubnet* przy użyciu [AzureRmVirtualNetworkSubnetConfig nowy](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Utwórz sieć wirtualną o nazwie *myVNet* przy użyciu [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) z konfiguracjami podsieci. I na koniec Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu [AzureRmPublicIpAddress nowy](/powershell/module/azurerm.network/new-azurermpublicipaddress). Te zasoby są używane do zapewnienia możliwości połączenia sieci z bramy aplikacji i jej skojarzonych zasobów.

```azurepowershell-interactive
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

### <a name="create-the-ip-configurations-and-frontend-port"></a>Tworzenie konfiguracji adresów IP i port serwera sieci Web

Skojarz *myAGSubnet* wcześniej utworzony przy użyciu bramy aplikacji [AzureRmApplicationGatewayIPConfiguration nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Przypisz *myAGPublicIPAddress* do bramy aplikacji przy użyciu [AzureRmApplicationGatewayFrontendIPConfig nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig). A następnie można utworzyć przy użyciu portu HTTP [AzureRmApplicationGatewayFrontendPort nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport).

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

Odbiornik wymaganego do włączenia usługi Brama aplikacji w celu kierowania ruchu odpowiednio do puli wewnętrznej bazy danych. W tym samouczku możesz utworzyć wiele odbiorników. Pierwszy podstawowe odbiornika oczekuje ruchu pod adresem URL katalogu głównego. Inne odbiorników oczekują ruchu na określone adresy URL, takie jak *http://52.168.55.24:8080/images/* lub *http://52.168.55.24:8081/video/*.

Utwórz odbiornik o nazwie *defaultListener* przy użyciu [AzureRmApplicationGatewayHttpListener nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) z konfiguracji serwera sieci Web i portu frontonu, która została wcześniej utworzona. Reguła jest wymagana dla odbiornika wiedzieć, który puli wewnętrznej bazy danych mają być używane dla ruchu przychodzącego. Utwórz podstawowe reguły o nazwie *rule1* przy użyciu [AzureRmApplicationGatewayRequestRoutingRule nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name defaultListener `
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
New-AzureRmApplicationGateway `
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

### <a name="add-backend-pools-and-ports"></a>Dodawanie pul zaplecza i porty

Można dodać pul zaplecza do bramy aplikacji przy użyciu [AzureRmApplicationGatewayBackendAddressPool Dodaj](/powershell/module/azurerm.network/add-azurermapplicationgatewaybackendaddresspool). W tym przykładzie *imagesBackendPool* i *videoBackendPool* są tworzone. Dodawanie portu frontonu dla pul przy użyciu [AzureRmApplicationGatewayFrontendPort Dodaj](/powershell/module/azurerm.network/add-azurermapplicationgatewayfrontendport). Następnie przesłać zmian bramy aplikacji przy użyciu [AzureRmApplicationGateway zestawu](/powershell/module/azurerm.network/set-azurermapplicationgateway).

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
Add-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name rport `
  -Port 8081
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

## <a name="add-listeners-and-rules"></a>Dodaj odbiorników i reguł

### <a name="add-listeners"></a>Dodawanie obiektów nasłuchujących

Dodaj odbiorników o nazwie *backendListener* i *redirectedListener* niezbędnych do kierowania ruchu przy użyciu [AzureRmApplicationGatewayHttpListener Dodaj](/powershell/module/azurerm.network/add-azurermapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPort = Get-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport
$redirectPort = Get-AzureRmApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name rport
$fipconfig = Get-AzureRmApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw
Add-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort
Add-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name redirectedListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $redirectPort  
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-default-url-path-map"></a>Dodaj mapę ścieżki adresu URL domyślnego

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

### <a name="add-redirection-configuration"></a>Dodawanie konfiguracji przekierowania

Można skonfigurować przekierowanie dla odbiornika za pomocą [AzureRmApplicationGatewayRedirectConfiguration Dodaj](/powershell/module/azurerm.network/add-azurermapplicationgatewayredirectconfiguration).

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendListener = Get-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener 
$redirectConfig = Add-AzureRmApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectConfig `
  -RedirectType Found `
  -TargetListener $backendListener `
  -IncludePath $true `
  -IncludeQueryString $true
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-redirection-url-path-map"></a>Dodaj mapę ścieżki adresu URL przekierowania

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$poolSettings = Get-AzureRmApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings
$defaultPool = Get-AzureRmApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool
$redirectConfig = Get-AzureRmApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectConfig
$redirectPathRule = New-AzureRmApplicationGatewayPathRuleConfig `
  -Name redirectPathRule `
  -Paths "/images/*" `
  -RedirectConfiguration $redirectConfig
Add-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name redirectpathmap `
  -PathRules $redirectPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rules"></a>Dodaj zasady routingu

Reguły routingu skojarzyć mapy adres URL z odbiorników, które zostały utworzone. Można dodawać reguły o nazwie *defaultRule* i *redirectedRule* przy użyciu [AzureRmApplicationGatewayRequestRoutingRule Dodaj](/powershell/module/azurerm.network/add-azurermapplicationgatewayrequestroutingrule).


```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendlistener = Get-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener
$redirectlistener = Get-AzureRmApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name redirectedListener
$urlPathMap = Get-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap
$redirectPathMap = Get-AzureRmApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name redirectpathmap
Add-AzureRmApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name defaultRule `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap
Add-AzureRmApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name redirectedRule `
  -RuleType PathBasedRouting `
  -HttpListener $redirectlistener `
  -UrlPathMap $redirectPathMap
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
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1"); 
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

Można użyć [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) uzyskać publiczny adres IP bramy aplikacji. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Takie jak *http://52.168.55.24*, *http://52.168.55.24:8080/images/test.htm*, *http://52.168.55.24:8080/video/test.htm*, lub *http://52.168.55.24:8081/images/test.htm*.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Podstawowy adres URL testu bramy aplikacji](./media/tutorial-url-redirect-powershell/application-gateway-iistest.png)

Zmień adres URL do http://&lt;adres ip&gt;: adres 8080/video/test.htm, zastępując IP &lt;adres ip&gt;, i powinien zostać wyświetlony ekran podobny do następującego:

![Adres URL obrazów testu w bramy aplikacji](./media/tutorial-url-redirect-powershell/application-gateway-iistest-images.png)

Zmień adres URL do http://&lt;adres ip&gt;: adres 8080/video/test.htm, zastępując IP &lt;adres ip&gt;, i powinien zostać wyświetlony ekran podobny do następującego:

![Testuj adres URL wideo w bramy aplikacji](./media/tutorial-url-redirect-powershell/application-gateway-iistest-video.png)

Teraz, Zmień adres URL do http://&lt;adres ip&gt;: adres 8081/images/test.htm, zastępując IP &lt;adres ip&gt;, i powinna zostać wyświetlona przekierowany z powrotem do puli zaplecza obrazów na http://ruchu&lt;adres ip&gt;: 8080/obrazów.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji
> * Dodaj odbiorników i reguły routingu
> * Utwórz zestawy skalowania maszyny wirtualnej dla pul zaplecza

> [!div class="nextstepaction"]
> [Dowiedz się więcej o co można zrobić z bramy aplikacji](application-gateway-introduction.md)
