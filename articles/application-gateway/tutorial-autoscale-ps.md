---
title: 'Samouczek: Tworzenie automatycznie skalowanej, strefowo nadmiarowej bramy aplikacji z zastrzeżonym adresem IP — Azure PowerShell'
description: W tym samouczku dowiesz się, jak utworzyć automatycznie skalowaną, strefowo nadmiarową bramę aplikacji z zastrzeżonym adresem IP przy użyciu programu Azure PowerShell.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: dd6cc65fca98bc435a8cfea575ba10e3cff376be
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424680"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Samouczek: Tworzenie bramy aplikacji, która usprawnia dostęp do aplikacji internetowych

Jeśli jesteś administratorem IT i chcesz poprawić dostęp do aplikacji internetowych, możesz zoptymalizować bramę aplikacji, aby była skalowana na podstawie potrzeb klientów i obejmowała wiele stref dostępności. Ten samouczek pomoże Ci skonfigurować funkcje usługi Azure App Gateway pod kątem skalowania automatycznego, nadmiarowości stref i zastrzeżonych wirtualnych adresów IP (statycznych adresów IP). Aby rozwiązać problem, użyjesz poleceń cmdlet programu Azure PowerShell i modelu wdrażania usługi Azure Resource Manager.

> [!IMPORTANT] 
> Jednostka SKU autoskalowanej i strefowo nadmiarowej bramy aplikacji jest aktualnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej skalowania automatycznego
> * Tworzenie zastrzeżonego publicznego adresu IP
> * Konfigurowanie infrastruktury bramy aplikacji
> * Określanie skalowania automatycznego
> * Tworzenie bramy aplikacji
> * Testowanie bramy aplikacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga, aby program Azure PowerShell działał lokalnie. Moduł Azure PowerShell musi być zainstalowany w wersji 6.9.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Po zweryfikowaniu wersji programu PowerShell uruchom polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów w jednej z dostępnych lokalizacji.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzureRmResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną z jedną dedykowaną podsiecią dla bramy aplikacji ze skalowaniem automatycznym. Aktualnie w każdej dedykowanej podsieci można wdrożyć tylko jedną bramę aplikacji ze skalowaniem automatycznym.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Tworzenie zastrzeżonego publicznego adresu IP

Określ metodę alokacji publicznego adresu IP (PublicIPAddress) jako **statyczną**. Adres VIP bramy aplikacji ze skalowaniem automatycznym można być tylko statyczny. Dynamiczne adresy IP nie są obsługiwane. Obsługiwana jest tylko standardowa jednostka SKU PublicIpAddress.

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Pobieranie szczegółów

Pobierz szczegóły grupy zasobów, podsieci i adresu IP lokalnego obiektu w celu utworzenia szczegółów konfiguracji adresu IP dla bramy aplikacji.

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Konfigurowanie infrastruktury

Ustaw konfigurację adresu IP, konfigurację adresu IP frontonu, pulę zaplecza, ustawienia protokołu HTTP, certyfikat, port, odbiornik i regułę w formacie identycznym, jaki obowiązuje w istniejącej usłudze Application Gateway w warstwie Standardowa. W nowej jednostce SKU obowiązuje taki sam model obiektów jak w jednostce SKU w warstwie Standardowa.

```azurepowershell
$ipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzureRmApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzureRmApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "scrap" -AsPlainText -Force
$sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "D:\Networking\ApplicationGateway\scrap.pfx"
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Określanie skalowania automatycznego

Teraz możesz określić konfigurację skalowania automatycznego dla bramy aplikacji. Obsługiwane są dwa typy konfiguracji skalowania automatycznego:

* **Tryb stałej pojemności**. W tym trybie brama aplikacji nie jest skalowana automatycznie i działa ze stałą pojemnością jednostki skalowania.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Tryb skalowania automatycznego**. W tym trybie brama aplikacji jest skalowana automatycznie w oparciu o wzorzec ruchu aplikacji.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Utwórz bramę aplikacji i dodaj strefy nadmiarowości oraz konfigurację skalowania automatycznego.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Aby uzyskać publiczny adres IP bramy aplikacji, użyj polecenia Get-AzureRmPublicIPAddress. Skopiuj publiczny adres IP (lub nazwę DNS), a następnie wklej go na pasku adresu przeglądarki.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najpierw zapoznaj się z zasobami utworzonymi przy użyciu bramy aplikacji. Następnie, gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, możesz je usunąć za pomocą polecenia `Remove-AzureRmResourceGroup`.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie bramy aplikacji za pomocą reguł routingu bazujących na ścieżce adresu URL](./tutorial-url-route-powershell.md)
