---
title: Kierowanie ruchu internetowego na podstawie adresu URL — Azure PowerShell
description: Dowiedz się, jak kierować ruch internetowy do określonych skalowalnych pul serwerów na podstawie adresu URL, korzystając z programu Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/31/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8ca5c411db5644182f7e87f1ee1c63f3cbc4d2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73200403"
---
# <a name="route-web-traffic-based-on-the-url-using-azure-powershell"></a>Kierowanie ruchu internetowego na podstawie adresu URL za pomocą programu Azure PowerShell

Korzystając z programu Azure PowerShell, możesz skonfigurować kierowanie ruchu internetowego do określonych skalowalnych pul serwerów na podstawie adresu URL użytego do uzyskania dostępu do aplikacji. W tym artykule utworzysz [bramę aplikacji platformy Azure](application-gateway-introduction.md) z trzema pulami wewnętrznej bazy danych przy użyciu [zestawów skalowania maszyny wirtualnej.](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) Każda z pul zaplecza ma określony cel, na przykład obsługę typowych danych, obrazów i wideo.  Kierowanie ruchu do oddzielnych pul gwarantuje, że klienci otrzymają potrzebne informacje we właściwym czasie.

Aby włączyć kierowanie ruchu, należy utworzyć [reguły routingu](application-gateway-url-route-overview.md) przypisane do odbiorników nasłuchujących na określonych portach w celu zapewnienia, że ruch internetowy będzie kierowany do odpowiednich serwerów w pulach.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Konfigurowanie sieci
> * Tworzenie odbiorników, mapy ścieżek URL i reguł
> * Tworzenie skalowalnych pul zaplecza

![Przykład routingu adresów URL](./media/tutorial-url-route-powershell/scenario.png)

Jeśli wolisz, możesz wykonać tę procedurę przy użyciu [interfejsu wiersza polecenia platformy Azure](tutorial-url-route-cli.md) lub [witryny Azure portal](create-url-route-portal.md).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać programu PowerShell lokalnie, ten artykuł wymaga modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

Ze względu na czas potrzebny do utworzenia zasobów może upłynąć do 90 minut, aby zakończyć tę procedurę.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów zawierającą wszystkie zasoby aplikacji. 

Utwórz grupę zasobów platformy Azure przy użyciu [programu New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Niezależnie od tego, czy masz istniejącą sieć wirtualną, czy tworzysz nową, musisz upewnić się, że zawiera ona podsieć używaną wyłącznie na potrzeby bram aplikacji. W tym artykule utworzysz podsieć bramy aplikacji i podsieci dla zestawów skalowania. Utworzysz publiczny adres IP, aby umożliwić dostęp do zasobów przy użyciu bramy aplikacji.

Tworzenie konfiguracji podsieci *myAGSubnet* i *myBackendSubnet* przy użyciu [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Utwórz sieć wirtualną o nazwie *myVNet* przy użyciu [sieci New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) z konfiguracjami podsieci. I wreszcie, utworzyć publiczny adres IP o nazwie *myAGPublicIPAddress* za pomocą [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Te zasoby służą do zapewniania łączności sieciowej z bramą aplikacji i skojarzonymi z nią zasobami.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

W tej części utworzysz zasoby, które obsługują bramę aplikacji, a na koniec utworzysz bramę aplikacji. Utworzone zasoby obejmują:

- *Konfiguracje ip i port frontend* — kojarzy podsieć utworzoną wcześniej do bramy aplikacji i przypisuje port do użycia w celu uzyskania do niej dostępu.
- *Domyślna pula* — wszystkie bramy aplikacji muszą mieć co najmniej jedną pulę serwerów zaplecza.
- *Odbiornik domyślny i reguła domyślna* — odbiornik domyślny nasłuchuje ruchu na porcie, który został przypisany, a reguła domyślna wysyła ruch do puli domyślnej.

### <a name="create-the-ip-configurations-and-frontend-port"></a>Tworzenie konfiguracji adresów IP i portu frontonu

Skojarz *myAGSubnet* utworzoną wcześniej z bramą aplikacji przy użyciu [funkcji New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Przypisz *myAGPublicIPAddress* do bramy aplikacji za pomocą [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$pip = Get-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-default-pool-and-settings"></a>Tworzenie puli domyślnej i ustawień

Utwórz domyślną pulę wewnętrznej bazy danych o nazwie *appGatewayBackendPool* dla bramy aplikacji przy użyciu [new-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Skonfiguruj ustawienia puli wewnętrznej bazy danych przy użyciu [funkcji New-AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Tworzenie odbiornika domyślnego i reguły domyślnej

Odbiornik jest wymagany, aby brama aplikacji mogła właściwie kierować ruch do puli zaplecza. W tym artykule utworzysz dwóch odbiorników. Pierwszy, podstawowy odbiornik, który utworzysz, nasłuchuje ruchu pod głównym adresem URL. Drugi odbiornik, który utworzysz, nasłuchuje ruchu pod innymi określonymi adresami URL.

Utwórz domyślny odbiornik o nazwie *myDefaultListener* przy użyciu [new-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) z konfiguracją frontendu i portem frontendu, który został wcześniej utworzony. 

Reguła jest wymagana, aby odbiornik wiedział, której puli zaplecza używać dla ruchu przychodzącego. Utwórz podstawową regułę o nazwie *rule1* przy użyciu [funkcji New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myDefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Teraz, gdy utworzono niezbędne zasoby pomocnicze, określ parametry bramy aplikacji o nazwie *myAppGateway* za pomocą [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku), a następnie utwórz ją za pomocą [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2

$appgw = New-AzApplicationGateway `
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

Utworzenie bramy aplikacji może potrwać do 30 minut. Zaczekaj na pomyślne zakończenie wdrożenia, zanim przejdziesz do kolejnej sekcji. 

W tym momencie masz bramę aplikacji, która nasłuchuje ruchu na porcie 80 i wysyła ten ruch do domyślnej puli serwerów.

### <a name="add-image-and-video-backend-pools-and-port"></a>Dodawanie pul zaplecza i portu na potrzeby obsługi obrazów i wideo

Dodaj pule zaplecza o nazwie *imagesParkusowanie* i *videoBackendPool* do bramy aplikacji[Add-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool). Dodaj port frontend dla basenów za pomocą [Add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport). Prześlij zmiany do bramy aplikacji za pomocą [Set-AzApplicationGateway](/powershell/module/az.network/set-azapplicationgateway).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080

Set-AzApplicationGateway -ApplicationGateway $appgw
```

Aktualizowanie bramy aplikacji również może potrwać do 20 minut.

### <a name="add-backend-listener"></a>Dodawanie odbiornika zaplecza

Dodaj odbiornik wewnętrznej bazy danych o nazwie *backendListener,* który jest potrzebny do kierowania ruchu za pomocą [Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>Dodawanie mapy ścieżek URL

Mapy ścieżek URL zapewniają kierowanie adresów URL wysyłanych do aplikacji do określonych pul zaplecza. Tworzenie map ścieżki adresu URL o nazwie *imagePathRule* i *videoPathRule* przy użyciu [New-AzApplicationGatewayPathConfig](/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) i [Add-AzApplicationGatewayUrlPathMapConfig](/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSetting `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$imagePool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$imagePathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings

$videoPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
    -Paths "/video/*" `
    -BackendAddressPool $videoPool `
    -BackendHttpSettings $poolSettings

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rule"></a>Dodawanie reguły routingu

Reguła routingu kojarzy mapę adresów URL z utworzonym odbiornikiem. Dodaj regułę o nazwie *rule2* za pomocą [add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Tworzenie zestawów skalowania maszyn wirtualnych

W tym przykładzie utworzysz trzy zestawy skalowania maszyn wirtualnych do obsługi trzech utworzonych pul zaplecza. Utworzone zestawy skalowania będą miały nazwy *myvmss1*, *myvmss2* i *myvmss3*. Zestaw skalowania przypisuje się do puli zaplecza podczas konfigurowania ustawień adresu IP.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$imagesPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
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

  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest `
    -OsDiskCreateOption FromImage

  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>Instalowanie usług IIS

Każdy zestaw skalowania zawiera dwa wystąpienia maszyny wirtualnej, na których instaluje się usługi IIS.  Przykładowa strona jest tworzona w celu sprawdzenia, czy brama aplikacji działa.

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i
  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Użyj [Get-AzPublicIPAddress,](/powershell/module/az.network/get-azpublicipaddress) aby uzyskać publiczny adres IP bramy aplikacji. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Takie jak `http://52.168.55.24` `http://52.168.55.24:8080/images/test.htm`, `http://52.168.55.24:8080/video/test.htm`, lub .

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testowanie podstawowego adresu URL w bramie aplikacji](./media/tutorial-url-route-powershell/application-gateway-iistest.png)

Zmień adres URL na&lt;http:// adres&gt;IP:8080/images/test.htm, zastępując swój adres &lt;IP&gt;dla adresu IP, a powinieneś zobaczyć coś takiego jak na przykład:

![Testowanie adresu URL obrazów w bramie aplikacji](./media/tutorial-url-route-powershell/application-gateway-iistest-images.png)

Zmień adres URL na&lt;http:// adres&gt;IP:8080/video/test.htm, zastępując swój adres &lt;IP&gt;dla adresu IP, a powinieneś zobaczyć coś takiego jak na przykład:

![Testowanie adresu URL wideo w bramie aplikacji](./media/tutorial-url-route-powershell/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebna, usuń grupę zasobów, bramę aplikacji i wszystkie powiązane zasoby za pomocą [programu Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Następne kroki

[Przekierowywanie ruchu internetowego na podstawie adresu URL](./tutorial-url-redirect-powershell.md)
