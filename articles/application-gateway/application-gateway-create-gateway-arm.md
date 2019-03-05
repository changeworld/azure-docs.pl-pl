---
title: Tworzenie bramy aplikacji — program Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji przy użyciu programu Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurepowershell
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: 412ede9895e6684d039dcad4df4189c8b3774d76
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313824"
---
# <a name="create-an-application-gateway-using-azure-powershell"></a>Tworzenie bramy aplikacji przy użyciu programu Azure PowerShell

Za pomocą programu Azure PowerShell do utworzenia lub zarządzanie nimi bramy application Gateway z wiersza polecenia lub skryptów. Ten przewodnik Szybki Start dowiesz się, jak utworzyć zasoby sieciowe, serwerów wewnętrznej bazy danych i bramy aplikacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli zdecydujesz się zainstalować i korzystać z programu PowerShell lokalnie, ten samouczek wymaga programu Azure PowerShell w wersji modułu 1.0.0 lub nowszym. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych 

Utwórz konfigurację podsieci przy użyciu [New AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Utwórz sieć wirtualną za pomocą [New AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) za pomocą konfiguracji podsieci. A na koniec Utwórz publiczny adres IP, korzystając [New AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Te zasoby służą do zapewniania łączności sieciowej z bramą aplikacji i skojarzonymi z nią zasobami.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```
## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie utworzysz dwie maszyny wirtualne, które będą używane jako serwery zaplecza dla bramy aplikacji. Zainstaluj również usługi IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.

### <a name="create-two-virtual-machines"></a>Tworzenie dwóch maszyn wirtualnych

Utwórz interfejs sieciowy z [New AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Utwórz konfigurację maszyny wirtualnej za pomocą [New AzVMConfig](/powershell/module/az.compute/new-azvmconfig). W przypadku uruchamiania poniższych poleceń będą wyświetlane monity o poświadczenia. Wprowadź *azureuser* jako nazwę użytkownika i *Azure123456!* jako hasło. Utwórz maszynę wirtualną za pomocą polecenia [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $vnet.Subnets[1].Id
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2
  $vm = Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  $vm = Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  $vm = Set-AzVMBootDiagnostics `
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

## <a name="create-an-application-gateway"></a>Tworzenie bramy aplikacji

### <a name="create-the-ip-configurations-and-frontend-port"></a>Tworzenie konfiguracji adresów IP i portu frontonu

Użyj [New AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) umożliwia utworzenie konfiguracji, które odpowiadają za skojarzenie podsieci wcześniej utworzony z usługą application gateway. Użyj [New AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) umożliwia utworzenie konfiguracji, który przypisuje także wcześniej utworzony publiczny adres IP bramy aplikacji. Użyj [New AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) można przypisać portu 80, które ma być używany do dostępu do bramy aplikacji.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$pip = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$subnet=$vnet.Subnets[0]
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

Użyj [New AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) do utworzenia puli wewnętrznej bazy danych w usłudze application gateway. Skonfiguruj ustawienia dla puli zaplecza za pomocą [New AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Tworzenie odbiornika i dodawanie reguły

Odbiornik jest wymagany, aby brama aplikacji mogła właściwie kierować ruch do puli zaplecza. Utwórz odbiornik za pomocą [New AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) z konfiguracji frontonu i port frontonu, która została wcześniej utworzona. Reguła jest wymagana, aby odbiornik wiedział, której puli zaplecza używać dla ruchu przychodzącego. Użyj [New AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) można utworzyć regułę o nazwie *rule1*.

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

Teraz, gdy utworzono niezbędne zasoby pomocnicze, użyj [New AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) Określ parametry w usłudze application gateway, a następnie użyć [New AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) do jego utworzenia.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
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

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Użyj [Get AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) na publiczny adres IP bramy aplikacji. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Testowanie bramy aplikacji](./media/application-gateway-create-gateway-arm/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie będą już potrzebne, możesz użyć [AzResourceGroup Usuń](/powershell/module/az.resources/remove-azresourcegroup) polecenia, aby usunąć grupę zasobów, usługa application gateway i wszystkie pokrewne zasoby.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start utworzono grupę zasobów, zasoby sieciowe i serwery zaplecza. Te zasoby są następnie używane do utworzenia bramy aplikacji. Aby dowiedzieć się więcej na temat bramy aplikacji i skojarzonych z nimi zasobów, przejdź do artykuły z poradami.

