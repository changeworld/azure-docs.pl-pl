---
title: Tworzenie bramy aplikacji za pośrednictwem protokołu HTTP do przekierowania protokołu HTTPS - programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć bramę aplikacji przy użyciu przekierowanego ruchu z protokołu HTTP do HTTPS przy użyciu programu Azure PowerShell.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 7/13/2018
ms.author: victorh
ms.openlocfilehash: 0028f183276eb93b5c71330ec650eaf178523bdd
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428924"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-azure-powershell"></a>Tworzenie bramy aplikacji za pośrednictwem protokołu HTTP do przekierowania protokołu HTTPS przy użyciu programu Azure PowerShell

Można użyć programu Azure PowerShell, aby utworzyć [bramy application gateway](overview.md) przy użyciu certyfikatu dla kończenia żądań SSL. Reguła routingu jest używany do przekierowywania ruchu HTTP do portu HTTPS w Twojej bramy application gateway. W tym przykładzie tworzymy [zestawu skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) dla puli zaplecza usługi application gateway, która zawiera dwa wystąpienia maszyn wirtualnych. 

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji z certyfikatem
> * Dodaj regułę odbiornik i Przekierowanie
> * Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu domyślnej puli zaplecza

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Dla tego samouczka jest wymagany moduł Azure PowerShell w wersji 3.6 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Aby uruchamiać polecenia w tym samouczku, trzeba będzie również uruchomić `Login-AzureRmAccount` do utworzenia połączenia z platformą Azure.

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Do użycia w środowisku produkcyjnym należy zaimportować prawidłowy certyfikat podpisany przez zaufanego dostawcę. W tym samouczku utworzysz certyfikat z podpisem własnym przy użyciu polecenia [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). Korzystając z polecenia [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) i zwróconego odcisku palca, możesz wyeksportować plik pfx z certyfikatu.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Wynik powinien wyglądać podobnie do następującego:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Użyj odcisku palca w celu utworzenia pliku pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów platformy Azure o nazwie *myResourceGroupAG* przy użyciu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

```powershell
New-AzureRmResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Tworzenie zasobów sieciowych

Utwórz konfiguracje podsieci *myBackendSubnet* i *myAGSubnet*, korzystając z polecenia [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Utwórz sieć wirtualną o nazwie *myVNet*, używając polecenia [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) z konfiguracjami podsieci. Na koniec utwórz publiczny adres IP o nazwie *myAGPublicIPAddress*, korzystając z polecenia [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). Te zasoby służą do zapewniania łączności sieciowej z bramą aplikacji i skojarzonymi z nią zasobami.

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

### <a name="create-the-ip-configurations-and-frontend-port"></a>Tworzenie konfiguracji adresów IP i portu frontonu

Skojarz wcześniej utworzoną podsieć *myAGSubnet* z bramą aplikacji za pomocą polecenia [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration). Przypisz adres *myAGPublicIPAddress* do bramy aplikacji przy użyciu polecenia [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig). A następnie utwórz przy użyciu portu HTTPS [New AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport).

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
$frontendPort = New-AzureRmApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>Tworzenie puli zaplecza i ustawień

Utwórz pulę zaplecza o nazwie *appGatewayBackendPool* dla bramy aplikacji przy użyciu polecenia [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool). Skonfiguruj ustawienia dla puli zaplecza przy użyciu polecenia [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings).

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

### <a name="create-the-default-listener-and-rule"></a>Tworzenie odbiornika domyślnego i reguły domyślnej

Odbiornik jest wymagany, aby brama aplikacji mogła właściwie kierować ruch do puli zaplecza. W tym przykładzie utworzysz podstawowy odbiornik, który nasłuchuje ruchu HTTPS pod głównym adresem URL. 

Tworzenie obiektu certyfikatu za pomocą [New AzureRmApplicationGatewaySslCertificate](/powershell/module/azurerm.network/new-azurermapplicationgatewaysslcertificate) , a następnie utwórz odbiornik o nazwie *appGatewayHttpListener* przy użyciu [ Nowe AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) z konfiguracji frontonu, portu frontonu i certyfikatów, która została wcześniej utworzona. Reguła jest wymagana, aby odbiornik wiedział, której puli zaplecza używać dla ruchu przychodzącego. Utwórz regułę podstawową o nazwie *rule1* przy użyciu polecenia [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule).

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzureRmApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultListener = New-AzureRmApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -SslCertificate $cert
$frontendRule = New-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Teraz, po utworzeniu niezbędnych zasobów pomocniczych, określ parametry bramy aplikacji o nazwie *myAppGateway* przy użyciu polecenia [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku), a następnie utwórz tę bramę aplikacji za pomocą polecenia [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) z certyfikatem.

```powershell
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
  -FrontendPorts $frontendPort `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="add-a-listener-and-redirection-rule"></a>Dodaj regułę odbiornik i Przekierowanie

### <a name="add-the-http-port"></a>Dodaj HTTP port

Dodaj HTTP port do bramy aplikacji przy użyciu [AzureRmApplicationGatewayFrontendPort Dodaj](/powershell/module/azurerm.network/add-azurermapplicationgatewayfrontendport).

```powershell
$appgw = Get-AzureRmApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG
Add-AzureRmApplicationGatewayFrontendPort `
  -Name httpPort  `
  -Port 80 `
  -ApplicationGateway $appgw
```

### <a name="add-the-http-listener"></a>Dodawanie odbiornika HTTP

Dodawanie odbiornika HTTP o nazwie *myListener* do bramy aplikacji przy użyciu [AzureRmApplicationGatewayHttpListener Dodaj](/powershell/module/azurerm.network/add-azurermapplicationgatewayhttplistener).

```powershell
$fipconfig = Get-AzureRmApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
$fp = Get-AzureRmApplicationGatewayFrontendPort `
  -Name httpPort `
  -ApplicationGateway $appgw
Add-AzureRmApplicationGatewayHttpListener `
  -Name myListener `
  -Protocol Http `
  -FrontendPort $fp `
  -FrontendIPConfiguration $fipconfig `
  -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>Dodaj konfigurację przekierowania

Dodaj HTTP do HTTPS konfiguracji przekierowania do bramy aplikacji przy użyciu [polecenie Add-AzureRmApplicationGatewayRedirectConfiguration](/powershell/module/azurerm.network/add-azurermapplicationgatewayredirectconfiguration).

```powershell
$defaultListener = Get-AzureRmApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -ApplicationGateway $appgw
Add-AzureRmApplicationGatewayRedirectConfiguration -Name httpToHttps `
  -RedirectType Permanent `
  -TargetListener $defaultListener `
  -IncludePath $true `
  -IncludeQueryString $true `
  -ApplicationGateway $appgw
```

### <a name="add-the-routing-rule"></a>Dodaj regułę routingu

Dodaj regułę routingu za pomocą konfiguracji przekierowania do bramy aplikacji przy użyciu [AzureRmApplicationGatewayRequestRoutingRule Dodaj](/powershell/module/azurerm.network/add-azurermapplicationgatewayrequestroutingrule).

```powershell
$myListener = Get-AzureRmApplicationGatewayHttpListener `
  -Name myListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzureRmApplicationGatewayRedirectConfiguration `
  -Name httpToHttps `
  -ApplicationGateway $appgw
Add-AzureRmApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $myListener `
  -RedirectConfiguration $redirectConfig `
  -ApplicationGateway $appgw
Set-AzureRmApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

W tym przykładzie utworzysz zestaw skalowania maszyn wirtualnych, aby zapewnić serwery dla puli zaplecza w bramie aplikacji. Zestaw skalowania przypisuje się do puli zaplecza podczas konfigurowania ustawień adresu IP.

```powershell
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
  -OsDiskCreateOption FromImage
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

```powershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
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

Aby uzyskać publiczny adres IP bramy aplikacji, możesz użyć polecenia [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. Na przykład: http://52.170.203.149

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Ostrzeżenie o zabezpieczeniach](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

Aby zaakceptować ostrzeżenie o zabezpieczeniach, jeśli używasz certyfikatu z podpisem własnym, wybierz pozycję **Szczegóły**, a następnie pozycję **Przejdź do strony internetowej**. Zostanie wyświetlona zabezpieczona witryna internetowa usług IIS, tak jak w poniższym przykładzie:

![Testowanie podstawowego adresu URL w bramie aplikacji](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie certyfikatu z podpisem własnym
> * Konfigurowanie sieci
> * Tworzenie bramy aplikacji z certyfikatem
> * Dodaj regułę odbiornik i Przekierowanie
> * Tworzenie zestawu skalowania maszyn wirtualnych przy użyciu domyślnej puli zaplecza
