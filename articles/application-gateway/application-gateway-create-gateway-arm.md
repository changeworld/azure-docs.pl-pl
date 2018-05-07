---
title: Utwórz bramę aplikacji - programu Azure PowerShell | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 1ad0fcd135e664b2b97fd16ebe851c817f567d7f
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-using-azure-powershell"></a>Utwórz bramę aplikacji przy użyciu programu Azure PowerShell

Można użyć programu Azure PowerShell do tworzenia lub zarządzać bramy aplikacji z poziomu wiersza polecenia lub w skryptach. Ta opcja szybkiego startu przedstawia tworzenie zasobów sieciowych, serwerów wewnętrznej bazy danych oraz bramy aplikacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, ten samouczek wymaga modułu Azure PowerShell w wersji 3.6 lub nowszej. Aby znaleźć wersję, uruchom `Get-Module -ListAvailable AzureRM` . Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tworzenie grupy zasobów platformy Azure przy użyciu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych 

Tworzenie konfiguracji podsieci przy użyciu [AzureRmVirtualNetworkSubnetConfig nowy](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Tworzenie sieci wirtualnych za pomocą funkcji [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) z konfiguracjami podsieci. I na koniec Utwórz publiczny adres IP przy użyciu [AzureRmPublicIpAddress nowy](/powershell/module/azurerm.network/new-azurermpublicipaddress). Te zasoby są używane do zapewnienia możliwości połączenia sieci z bramy aplikacji i jej skojarzonych zasobów.

```azurepowershell-interactive
$backendSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
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
## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tym przykładzie utworzysz dwie maszyny wirtualne do użycia jako serwery zaplecza bramy aplikacji. Należy również zainstalować usług IIS na maszynach wirtualnych, aby sprawdzić, czy brama aplikacji została pomyślnie utworzona.

### <a name="create-two-virtual-machines"></a>Tworzenie dwóch maszyn wirtualnych

Tworzenie interfejsu sieciowego z [AzureRmNetworkInterface nowy](/powershell/module/azurerm.network/new-azurermnetworkinterface). Tworzenie konfiguracji maszyny wirtualnej z [AzureRmVMConfig nowy](/powershell/module/azurerm.compute/new-azurermvmconfig). Po uruchom następujące polecenia, zostanie wyświetlony monit o poświadczenia. Wprowadź *azureuser* dla nazwy użytkownika i *Azure123456!* hasła. Utwórz maszynę wirtualną za pomocą polecenia [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzureRmNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -SubnetId $vnet.Subnets[1].Id
  $vm = New-AzureRmVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2
  $vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  $vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Add-AzureRmVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  $vm = Set-AzureRmVMBootDiagnostics `
    -VM $vm `
    -Disable
  New-AzureRmVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzureRmVMExtension `
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

### <a name="create-the-ip-configurations-and-frontend-port"></a>Tworzenie konfiguracji adresów IP i port serwera sieci Web

Użyj [AzureRmApplicationGatewayIPConfiguration nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration) do utworzenia konfiguracji, które kojarzy podsieci, która wcześniej została utworzona z bramy aplikacji. Użyj [AzureRmApplicationGatewayFrontendIPConfig nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig) do utworzenia konfiguracji, który przypisuje publicznego adresu IP również wcześniej utworzony na bramie aplikacji. Użyj [AzureRmApplicationGatewayFrontendPort nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport) można przypisać portu 80, które ma być używany do uzyskania dostępu bramy aplikacji.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$pip = Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
$subnet=$vnet.Subnets[0]
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

### <a name="create-the-backend-pool"></a>Tworzenie puli wewnętrznej bazy danych

Użyj [AzureRmApplicationGatewayBackendAddressPool nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool) można utworzyć puli zaplecza bramy aplikacji. Skonfiguruj ustawienia dla puli wewnętrznej bazy danych za pomocą [AzureRmApplicationGatewayBackendHttpSettings nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```azurepowershell-interactive
$address1 = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzureRmApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool `
  -BackendIPAddresses $address1.ipconfigurations[0].privateipaddress, $address2.ipconfigurations[0].privateipaddress
$poolSettings = New-AzureRmApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-listener-and-add-a-rule"></a>Utworzenie obiektu nasłuchującego, a następnie dodaj regułę

Odbiornik wymaganego do włączenia usługi Brama aplikacji w celu kierowania ruchu odpowiednio do puli wewnętrznej bazy danych. Utwórz odbiornik przy użyciu [AzureRmApplicationGatewayHttpListener nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) z konfiguracji serwera sieci Web i portu frontonu, która została wcześniej utworzona. Reguła jest wymagana dla odbiornika wiedzieć, który puli wewnętrznej bazy danych mają być używane dla ruchu przychodzącego. Użyj [AzureRmApplicationGatewayRequestRoutingRule nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) do utworzenia reguły o nazwie *rule1*.

```azurepowershell-interactive
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Teraz, gdy utworzono niezbędne dodatkowe zasoby, używać [AzureRmApplicationGatewaySku nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) określić parametry dla bramy aplikacji, a następnie użyć [AzureRmApplicationGateway nowy](/powershell/module/azurerm.network/new-azurermapplicationgateway) do Czy chcesz go utworzyć.

```azurepowershell-interactive
$sku = New-AzureRmApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
New-AzureRmApplicationGateway `
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

Użyj [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) uzyskać publiczny adres IP bramy aplikacji. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Brama aplikacji w testu](./media/application-gateway-create-gateway-arm/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, można użyć [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) polecenia, aby usunąć grupę zasobów, bramy aplikacji i wszystkie powiązane zasoby.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start utworzono grupę zasobów, zasoby sieciowe i serwery zaplecza. Te zasoby są następnie używane do tworzenia bramy aplikacji. Aby dowiedzieć się więcej na temat bram aplikacji i ich skojarzonych zasobów, nadal artykuły.

