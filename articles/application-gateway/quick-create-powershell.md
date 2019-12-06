---
title: 'Szybki Start: bezpośredni ruch internetowy przy użyciu programu PowerShell'
titleSuffix: Azure Application Gateway
description: Dowiedz się, jak za pomocą Azure PowerShell utworzyć Application Gateway platformy Azure, która kieruje ruch internetowy do maszyn wirtualnych w puli zaplecza.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 9c3fac7aecaf37b5822ad6e8c655867f6f2c683c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872710"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Szybki Start: bezpośredni ruch internetowy za pomocą usługi Azure Application Gateway przy użyciu Azure PowerShell

W tym przewodniku szybki start pokazano, jak za pomocą Azure PowerShell szybko utworzyć bramę aplikacji.  Po utworzeniu bramy aplikacji przetestuj ją, aby upewnić się, że działa prawidłowo. Dzięki usłudze Azure Application Gateway kierowanie ruchu sieci Web aplikacji do określonych zasobów przez przypisanie odbiorników do portów, tworzenie reguł i Dodawanie zasobów do puli zaplecza. Dla uproszczenia w tym artykule użyto prostej konfiguracji z publicznym adresem IP frontonu, podstawowego odbiornika do hostowania pojedynczej lokacji w tej bramie aplikacji, dwóch maszyn wirtualnych używanych dla puli zaplecza i podstawowej reguły routingu żądań.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-powershell-module"></a>Moduł programu Azure PowerShell

Jeśli zdecydujesz się zainstalować program Azure PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej.

1. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). 
2. Aby utworzyć połączenie z platformą Azure, uruchom polecenie `Login-AzAccount`.

### <a name="resource-group"></a>Grupa zasobów

Na platformie Azure możesz przydzielić powiązane zasoby do grupy zasobów. Możesz użyć istniejącej grupy zasobów lub utworzyć nową. W tym przykładzie utworzysz nową grupę zasobów za pomocą polecenia cmdlet [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) w następujący sposób: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

### <a name="required-network-resources"></a>Wymagane zasoby sieciowe

Do komunikacji między tworzonymi zasobami platforma Azure potrzebuje sieci wirtualnej.  Podsieć bramy aplikacji może zawierać tylko bramy aplikacji. Inne zasoby nie są dozwolone.  Można utworzyć nową podsieć dla Application Gateway lub użyć istniejącej. W tym przykładzie utworzysz dwie podsieci w tym przykładzie: jeden dla bramy aplikacji, a drugi dla serwerów wewnętrznej bazy danych. Można skonfigurować adres IP frontonu Application Gateway publiczny lub prywatny zgodnie z Twoim przypadkiem użycia. W tym przykładzie wybrano publiczny adres IP frontonu.

1. Utwórz konfiguracje podsieci, wywołując metodę [New-AzVirtualNetworkSubnetConfig](/powershell/module/Az.network/new-Azvirtualnetworksubnetconfig).
2. Utwórz sieć wirtualną z konfiguracjami podsieci, wywołując metodę [New-AzVirtualNetwork](/powershell/module/Az.network/new-Azvirtualnetwork). 
3. Utwórz publiczny adres IP, wywołując metodę [New-AzPublicIpAddress](/powershell/module/Az.network/new-Azpublicipaddress). 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>Tworzenie bramy Application Gateway

### <a name="create-the-ip-configurations-and-frontend-port"></a>Tworzenie konfiguracji adresów IP i portu frontonu

1. Użyj [New-AzApplicationGatewayIPConfiguration](/powershell/module/Az.network/new-Azapplicationgatewayipconfiguration) , aby utworzyć konfigurację, która kojarzy utworzoną podsieć z bramą aplikacji. 
2. Użyj [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/Az.network/new-Azapplicationgatewayfrontendipconfig) , aby utworzyć konfigurację, która przypisuje publiczny adres IP, który został wcześniej utworzony do bramy aplikacji. 
3. Użyj [New-AzApplicationGatewayFrontendPort](/powershell/module/Az.network/new-Azapplicationgatewayfrontendport) , aby przypisać port 80 w celu uzyskania dostępu do bramy aplikacji.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

### <a name="create-the-backend-pool"></a>Tworzenie puli zaplecza

1. Użyj [New-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/new-Azapplicationgatewaybackendaddresspool) , aby utworzyć pulę zaplecza dla bramy aplikacji. Pula zaplecza będzie teraz pusta i podczas tworzenia kart sieciowych serwera wewnętrznej bazy danych w następnej sekcji zostaną dodane do puli zaplecza.
2. Skonfiguruj ustawienia puli zaplecza przy użyciu elementu [New-AzApplicationGatewayBackendHttpSetting](/powershell/module/Az.network/new-Azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Tworzenie odbiornika i dodawanie reguły

Platforma Azure wymaga odbiornika, aby brama aplikacji mogła właściwie kierować ruch do puli zaplecza. Platforma Azure wymaga również reguły, aby odbiornik wiedział, której puli zaplecza używać dla ruchu przychodzącego. 

1. Utwórz odbiornik za pomocą polecenia [New-AzApplicationGatewayHttpListener](/powershell/module/Az.network/new-Azapplicationgatewayhttplistener) z konfiguracją frontonu i wcześniej utworzonym portem frontonu. 
2. Użyj [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/Az.network/new-Azapplicationgatewayrequestroutingrule) , aby utworzyć regułę o nazwie *RULE1*. 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Teraz po utworzeniu niezbędnych zasobów pomocniczych utwórz bramę aplikacji:

1. Użyj polecenie [New-AzApplicationGatewaySku](/powershell/module/Az.network/new-Azapplicationgatewaysku) , aby określić parametry dla bramy aplikacji.
2. Użyj [New-AzApplicationGateway](/powershell/module/Az.network/new-Azapplicationgateway) , aby utworzyć bramę aplikacji.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="backend-servers"></a>Serwery zaplecza

Teraz, gdy utworzono Application Gateway, Utwórz maszyny wirtualne zaplecza, które będą hostować witryny sieci Web. Zaplecze może składać się z kart sieciowych, zestawów skalowania maszyn wirtualnych, publicznych adresów IP, wewnętrznych adresów IP, w pełni kwalifikowanych nazw domen (FQDN) i wielodostępnych zaplecza, takich jak Azure App Service. W tym przykładzie utworzysz dwie maszyny wirtualne platformy Azure, które będą używane jako serwery zaplecza dla bramy aplikacji. Zainstalujesz również usługi IIS na maszynach wirtualnych, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji.

#### <a name="create-two-virtual-machines"></a>Tworzenie dwóch maszyn wirtualnych

1. Pobierz ostatnio utworzoną konfigurację puli zaplecza Application Gateway za pomocą [Get-AzApplicationGatewayBackendAddressPool](/powershell/module/Az.network/get-Azapplicationgatewaybackendaddresspool)
2. Utwórz interfejs sieciowy przy użyciu elementu [New-AzNetworkInterface](/powershell/module/Az.network/new-Aznetworkinterface). 
3. Utwórz konfigurację maszyny wirtualnej za pomocą elementu [New-AzVMConfig](/powershell/module/Az.compute/new-Azvmconfig).
4. Utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/Az.compute/new-Azvm).

Po uruchomieniu następującego przykładowego kodu w celu utworzenia maszyn wirtualnych na platformie Azure zostanie wyświetlony monit o podanie poświadczeń. Wprowadź *azureuser* jako nazwę użytkownika i *Azure123456!* jako hasło:
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Mimo że zainstalowanie usług IIS nie jest wymagane do utworzenia bramy aplikacji, zainstalowano je w ramach tego przewodnika Szybki start, aby sprawdzić, czy platforma Azure pomyślnie utworzyła bramę aplikacji. Użyj usług do przetestowania bramy aplikacji:

1. Uruchom [Get-AzPublicIPAddress](/powershell/module/Az.network/get-Azpublicipaddress) , aby uzyskać publiczny adres IP bramy aplikacji. 
2. Skopiuj i wklej publiczny adres IP na pasku adresu przeglądarki. Po odświeżeniu przeglądarki powinna zostać wyświetlona nazwa maszyny wirtualnej. Prawidłowa odpowiedź weryfikuje, czy Brama aplikacji została pomyślnie utworzona i może pomyślnie nawiązać połączenie z zapleczem.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testowanie bramy aplikacji](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą bramy aplikacji, usuń grupę zasobów. Usuwając grupę zasobów, usuwasz również bramę aplikacji i wszystkie powiązane z nią zasoby. 

Aby usunąć grupę zasobów, wywołaj polecenie cmdlet [Remove-AzResourceGroup](/powershell/module/Az.resources/remove-Azresourcegroup) w następujący sposób:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie ruchem internetowym przy użyciu bramy aplikacji za pomocą programu Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

