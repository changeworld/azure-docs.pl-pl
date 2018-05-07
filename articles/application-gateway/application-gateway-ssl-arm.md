---
title: Utwórz bramę aplikacji z kończenia żądań SSL - programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji i Dodaj certyfikat dla zakończenia połączenia SSL przy użyciu programu Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: victorh
ms.openlocfilehash: ccecef08afe207cb33d28a0434436f1b31c60c6b
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="create-an-application-gateway-with-ssl-termination-using-azure-powershell"></a>Utwórz bramę aplikacji z kończenia żądań SSL przy użyciu programu Azure PowerShell

Można użyć programu Azure PowerShell do tworzenia [brama aplikacji w](application-gateway-introduction.md) przy użyciu certyfikatu dla [kończenia żądań SSL](application-gateway-backend-ssl.md) używającą [zestaw skali maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) dla serwerów wewnętrznej bazy danych. W tym przykładzie zestaw skalowania zawiera dwa wystąpienia maszyny wirtualnej, które są dodawane do domyślnej puli zaplecza bramy aplikacji. 

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Utwórz certyfikat z podpisem własnym
> * Konfigurowanie sieci
> * Utwórz bramę aplikacji przy użyciu certyfikatu
> * Utwórz zestaw z domyślnej puli zaplecza skali maszyny wirtualnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Dla tego samouczka jest wymagany moduł Azure PowerShell w wersji 3.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-self-signed-certificate"></a>Utwórz certyfikat z podpisem własnym

W środowisku produkcyjnym należy zaimportować certyfikat podpisane przez zaufanego dostawcę. W tym samouczku utworzysz przy użyciu certyfikatu z podpisem własnym [SelfSignedCertificate nowy](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). Można użyć [PfxCertificate eksportu](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) z odciskiem palca, który został zwrócony wyeksportować plik pfx z certyfikatu.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Powinien zostać wyświetlony ekran podobny do tego wyniku:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Za pomocą odcisku palca w celu utworzenia pliku pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Tworzenie grupy zasobów platformy Azure o nazwie *myResourceGroupAG* z [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

```powershell
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Konfigurowanie podsieci o nazwie *myBackendSubnet* i *myAGSubnet* przy użyciu [AzureRmVirtualNetworkSubnetConfig nowy](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Utwórz sieć wirtualną o nazwie *myVNet* przy użyciu [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) z konfiguracjami podsieci. I na koniec Utwórz publiczny adres IP o nazwie *myAGPublicIPAddress* przy użyciu [AzureRmPublicIpAddress nowy](/powershell/module/azurerm.network/new-azurermpublicipaddress). Te zasoby są używane do zapewnienia możliwości połączenia sieci z bramy aplikacji i jej skojarzonych zasobów.

```powershell
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

```powershell
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendport = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>Tworzenie puli wewnętrznej bazy danych i ustawień

Tworzenie puli wewnętrznej bazy danych o nazwie *appGatewayBackendPool* dla bramy aplikacji przy użyciu [AzureRmApplicationGatewayBackendAddressPool nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Skonfiguruj ustawienia dla puli wewnętrznej bazy danych za pomocą [AzureRmApplicationGatewayBackendHttpSettings nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

```powershell
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

Odbiornik wymaganego do włączenia usługi Brama aplikacji w celu kierowania ruchu odpowiednio do puli wewnętrznej bazy danych. W tym przykładzie utworzysz odbiornika podstawowa, która nasłuchuje ruchu HTTPS pod adresem URL katalogu głównego. 

Tworzenie obiektu certyfikatu przy użyciu [AzureRmApplicationGatewaySslCertificate nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewaysslcertificate) , a następnie utworzyć odbiornik o nazwie *mydefaultListener* przy użyciu [ Nowy AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) z konfiguracji serwera sieci Web, portów frontonu i wcześniej utworzony certyfikat. Reguła jest wymagana dla odbiornika wiedzieć, który puli wewnętrznej bazy danych mają być używane dla ruchu przychodzącego. Utwórz podstawowe reguły o nazwie *rule1* przy użyciu [AzureRmApplicationGatewayRequestRoutingRule nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzureRmApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultlistener = New-AzureRmApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport `
  -SslCertificate $cert
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-certificate"></a>Utwórz bramę aplikacji przy użyciu certyfikatu

Teraz, gdy utworzono niezbędne dodatkowe zasoby, należy określić parametry dla bramy aplikacji o nazwie *myAppGateway* przy użyciu [AzureRmApplicationGatewaySku nowy](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku), a następnie utwórz ją za pomocą [ Nowy AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) z certyfikatem.

### <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

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
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

W tym przykładzie utworzysz skonfigurowany w celu zapewnienia serwery w puli zaplecza w brama aplikacji w skali maszyny wirtualnej. Można przypisać zestaw do puli wewnętrznej bazy danych podczas konfigurowania ustawień IP skalowania.

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
$ipConfig = New-AzureRmVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
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
  -ComputerNamePrefix myvmss
Add-AzureRmVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Instalowanie usług IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/vhorne/samplescripts/master/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzureRmVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Można użyć [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) uzyskać publiczny adres IP bramy aplikacji. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Ostrzeżenie bezpieczne](./media/application-gateway-ssl-arm/application-gateway-secure.png)

Aby zaakceptować zabezpieczeń ostrzeżenie, jeśli używasz certyfikatu z podpisem własnym, wybierz **szczegóły** , a następnie **przejdź do strony sieci Web**. Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

![Podstawowy adres URL testu bramy aplikacji](./media/application-gateway-ssl-arm/application-gateway-iistest.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz certyfikat z podpisem własnym
> * Konfigurowanie sieci
> * Utwórz bramę aplikacji przy użyciu certyfikatu
> * Utwórz zestaw z domyślnej puli zaplecza skali maszyny wirtualnej

Aby dowiedzieć się więcej na temat bram aplikacji i ich skojarzonych zasobów, nadal artykuły.
