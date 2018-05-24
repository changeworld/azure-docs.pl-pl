---
title: Kierowanie ruchu internetowego na podstawie adresu URL — Azure PowerShell
description: Dowiedz się, jak kierować ruch internetowy do określonych skalowalnych pul serwerów na podstawie adresu URL, korzystając z programu Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 3/20/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 63533319d555e79c86d4fe3cdae0b168115e7ec5
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="route-web-traffic-based-on-the-url-using-azure-powershell"></a>Kierowanie ruchu internetowego na podstawie adresu URL za pomocą programu Azure PowerShell

Korzystając z programu Azure PowerShell, możesz skonfigurować kierowanie ruchu internetowego do określonych skalowalnych pul serwerów na podstawie adresu URL użytego do uzyskania dostępu do aplikacji. Podczas pracy z tym samouczkiem utworzysz [bramę aplikacji usługi Azure Application Gateway](application-gateway-introduction.md) z trzema pulami zaplecza, korzystając z [zestawów skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Każda z pul zaplecza ma określony cel, na przykład obsługę typowych danych, obrazów i wideo.  Kierowanie ruchu do oddzielnych pul gwarantuje, że klienci otrzymają potrzebne informacje we właściwym czasie.

Aby włączyć kierowanie ruchu, należy utworzyć [reguły routingu](application-gateway-url-route-overview.md) przypisane do odbiorników nasłuchujących na określonych portach w celu zapewnienia, że ruch internetowy będzie kierowany do odpowiednich serwerów w pulach.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie odbiorników, mapy ścieżek URL i reguł
> * Tworzenie skalowalnych pul zaplecza

![Przykład routingu adresów URL](./media/tutorial-url-route-powershell/scenario.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 3.6 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie ` Get-Module -ListAvailable AzureRM`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

Ze względu na czas potrzebny na utworzenie zasobów, ukończenie tego samouczka może zająć do 90 minut.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Należy utworzyć grupę zasobów zawierającą wszystkie zasoby aplikacji. 

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).  

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Niezależnie od tego, czy masz istniejącą sieć wirtualną, czy tworzysz nową, musisz upewnić się, że zawiera ona podsieć używaną wyłącznie na potrzeby bram aplikacji. W ramach pracy z tym samouczkiem utworzysz podsieci dla bramy aplikacji i dla zestawów skalowania. Utworzysz publiczny adres IP, aby umożliwić dostęp do zasobów przy użyciu bramy aplikacji.

Utwórz konfiguracje podsieci o nazwie *myAGSubnet* i *myBackendSubnet* za pomocą polecenia [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Utwórz sieć wirtualną o nazwie *myVNet*, używając polecenia [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) z konfiguracjami podsieci. Na koniec utwórz publiczny adres IP o nazwie *myAGPublicIPAddress*, korzystając z polecenia [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Te zasoby służą do zapewniania łączności sieciowej z bramą aplikacji i skojarzonymi z nią zasobami.

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

W tej części utworzysz zasoby, które obsługują bramę aplikacji, a na koniec utworzysz bramę aplikacji. Zasoby, które utworzysz, obejmują:

- *Konfiguracje adresów IP i port frontonu* — umożliwiają skojarzenie wcześniej utworzonej podsieci z bramą aplikacji i przypisanie portu, który ma być używany do uzyskiwania do niej dostępu.
- *Domyślna pula* — wszystkie bramy aplikacji muszą mieć co najmniej jedną pulę serwerów zaplecza.
- *Odbiornik domyślny i reguła domyślna* — odbiornik domyślny nasłuchuje ruchu na porcie, który został przypisany, a reguła domyślna wysyła ruch do puli domyślnej.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Tworzenie konfiguracji adresów IP i portu frontonu

Skojarz wcześniej utworzoną podsieć *myAGSubnet* z bramą aplikacji za pomocą polecenia [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Przypisz adres *myAGPublicIPAddress* do bramy aplikacji przy użyciu polecenia [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig).

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

### <a name="create-the-default-pool-and-settings"></a>Tworzenie puli domyślnej i ustawień

Utwórz domyślną pulę zaplecza o nazwie *appGatewayBackendPool* dla bramy aplikacji przy użyciu polecenia [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Skonfiguruj ustawienia dla puli zaplecza przy użyciu polecenia [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

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

### <a name="create-the-default-listener-and-rule"></a>Tworzenie odbiornika domyślnego i reguły domyślnej

Odbiornik jest wymagany, aby brama aplikacji mogła właściwie kierować ruch do puli zaplecza. W tym samouczku utworzysz dwa odbiorniki. Pierwszy, podstawowy odbiornik, który utworzysz, nasłuchuje ruchu pod głównym adresem URL. Drugi odbiornik, który utworzysz, nasłuchuje ruchu pod innymi określonymi adresami URL.

Utwórz odbiornik domyślny o nazwie *mydefaultListener* przy użyciu polecenia [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener), stosując wcześniej utworzone konfiguracje i port frontonu. 

Reguła jest wymagana, aby odbiornik wiedział, której puli zaplecza używać dla ruchu przychodzącego. Utwórz regułę podstawową o nazwie *rule1* przy użyciu polecenia [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

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

Teraz, po utworzeniu niezbędnych zasobów pomocniczych, określ parametry bramy aplikacji o nazwie *myAppGateway* przy użyciu polecenia [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku), a następnie utwórz tę bramę aplikacji za pomocą polecenia [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway).

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

Tworzenie bramy aplikacji może potrwać do 30 minut. Zanim przejdziesz do następnej sekcji, zaczekaj na pomyślne zakończenie wdrażania. Na tym etapie masz bramę aplikacji nasłuchującą ruchu na porcie 80 i wysyłającą ten ruch do domyślnej puli serwerów.

### <a name="add-image-and-video-backend-pools-and-port"></a>Dodawanie pul zaplecza i portu na potrzeby obsługi obrazów i wideo

Dodaj pule zaplecza o nazwach *imagesBackendPool* i *videoBackendPool* do bramy aplikacji, używając polecenia [Add-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/add-azurermapplicationgatewaybackendaddresspool). Dodaj port frontonu dla tych pul, używając polecenia [Add-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/add-azurermapplicationgatewayfrontendport). Prześlij zmiany do bramy aplikacji za pomocą polecenia [Set-AzureRmApplicationGateway](/powershell/module/azurerm.network/set-azurermapplicationgateway).

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

Aktualizowanie bramy aplikacji również może potrwać do 20 minut.

### <a name="add-backend-listener"></a>Dodawanie odbiornika zaplecza

Dodaj odbiornik zaplecza o nazwie *backendListener*, wymagany do kierowania ruchu, używając polecenia [Add-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/add-azurermapplicationgatewayhttplistener).

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

### <a name="add-url-path-map"></a>Dodawanie mapy ścieżek URL

Mapy ścieżek URL zapewniają kierowanie adresów URL wysyłanych do aplikacji do określonych pul zaplecza. Utwórz mapy ścieżek URL o nazwach *imagePathRule* i *videoPathRule* za pomocą poleceń [New-AzureRmApplicationGatewayPathRuleConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewaypathruleconfig) i [Add-AzureRmApplicationGatewayUrlPathMapConfig](/powershell/module/azurerm.network/add-azurermapplicationgatewayurlpathmapconfig).

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

### <a name="add-routing-rule"></a>Dodawanie reguły routingu

Reguła routingu kojarzy mapę adresów URL z utworzonym odbiornikiem. Dodaj regułę o nazwie *rule2* przy użyciu polecenia [Add-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/add-azurermapplicationgatewayrequestroutingrule).

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

## <a name="create-virtual-machine-scale-sets"></a>Tworzenie zestawów skalowania maszyn wirtualnych

W tym przykładzie utworzysz trzy zestawy skalowania maszyn wirtualnych do obsługi trzech utworzonych pul zaplecza. Utworzone zestawy skalowania będą miały nazwy *myvmss1*, *myvmss2* i *myvmss3*. Zestaw skalowania przypisuje się do puli zaplecza podczas konfigurowania ustawień adresu IP.

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

Każdy zestaw skalowania zawiera dwa wystąpienia maszyny wirtualnej, na których instaluje się usługi IIS umożliwiające uruchomienie strony testowej w celu przetestowania działania bramy aplikacji.

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

Aby uzyskać publiczny adres IP bramy aplikacji, użyj polecenia [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Na przykład *http://52.168.55.24*, *http://52.168.55.24:8080/images/test.htm* lub *http://52.168.55.24:8080/video/test.htm*.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testowanie podstawowego adresu URL w bramie aplikacji](./media/tutorial-url-route-powershell/application-gateway-iistest.png)

Zmień adres URL na http://&lt;adres-ip&gt;:8080/images/test.htm, zamieniając ciąg &lt;adres-ip&gt; na odpowiedni adres IP. Wynik powinien przypominać następujący przykład:

![Testowanie adresu URL obrazów w bramie aplikacji](./media/tutorial-url-route-powershell/application-gateway-iistest-images.png)

Zmień adres URL na http://&lt;adres-ip&gt;:8080/video/test.htm, zamieniając ciąg &lt;adres-ip&gt; na odpowiedni adres IP. Wynik powinien przypominać następujący przykład:

![Testowanie adresu URL wideo w bramie aplikacji](./media/tutorial-url-route-powershell/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie odbiorników, mapy ścieżek URL i reguł
> * Tworzenie skalowalnych pul zaplecza

> [!div class="nextstepaction"]
> [Przekierowywanie ruchu internetowego na podstawie adresu URL](./tutorial-url-redirect-powershell.md)
