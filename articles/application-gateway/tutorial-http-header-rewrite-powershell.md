---
title: Tworzenie bramy aplikacji platformy Azure & przepisywanie nagłówków HTTP
description: Ten artykuł zawiera informacje dotyczące tworzenia bramy aplikacji platformy Azure i przepisywania nagłówków HTTP przy użyciu programu Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: 2663c049245a7025b5948a64fc5008bb9e7dee90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173713"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Tworzenie bramy aplikacji i przepisywanie nagłówków HTTP

Za pomocą programu Azure PowerShell można skonfigurować [reguły do przepisywania nagłówków żądań i odpowiedzi HTTP](rewrite-http-headers.md) podczas tworzenia nowej [jednostki SKU bramy aplikacji automatycznego i nadmiarowego strefy](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
>
> * Tworzenie sieci wirtualnej skalowania automatycznego
> * Tworzenie zastrzeżonego publicznego adresu IP
> * Konfigurowanie infrastruktury bramy aplikacji
> * Określanie konfiguracji reguły przepisywania nagłówka http
> * Określanie skalowania automatycznego
> * Tworzenie bramy aplikacji
> * Testowanie bramy aplikacji

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł wymaga lokalnego uruchomienia programu Azure PowerShell. Musisz mieć zainstalowany moduł Az w wersji 1.0.0 lub nowszej. Uruchom, `Import-Module Az` `Get-Module Az` a następnie, aby znaleźć wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Po zweryfikowaniu wersji programu PowerShell uruchom polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów w jednej z dostępnych lokalizacji.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną z jedną dedykowaną podsiecią dla bramy aplikacji ze skalowaniem automatycznym. Aktualnie w każdej dedykowanej podsieci można wdrożyć tylko jedną bramę aplikacji ze skalowaniem automatycznym.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Tworzenie zastrzeżonego publicznego adresu IP

Określ metodę alokacji publicznego adresu IP (PublicIPAddress) jako **statyczną**. Adres VIP bramy aplikacji ze skalowaniem automatycznym można być tylko statyczny. Dynamiczne adresy IP nie są obsługiwane. Obsługiwana jest tylko standardowa jednostka SKU PublicIpAddress.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Pobieranie szczegółów

Pobierz szczegóły grupy zasobów, podsieci i adresu IP lokalnego obiektu w celu utworzenia szczegółów konfiguracji adresu IP dla bramy aplikacji.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Konfigurowanie infrastruktury

Skonfiguruj konfigurację IP, konfigurację IP front-end, pulę zaplecza, ustawienia HTTP, certyfikat, port i odbiornik w formacie identycznym z istniejącą bramą aplikacji Standard. W nowej jednostce SKU obowiązuje taki sam model obiektów jak w jednostce SKU w warstwie Standardowa.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Określanie konfiguracji reguły przepisywania nagłówka HTTP

Skonfiguruj nowe obiekty wymagane do ponownego zapisu nagłówków http:

- **RequestHeaderConfiguration**: ten obiekt jest używany do określania pól nagłówka żądania, które mają zostać przepisane i nowej wartości, do której oryginalne nagłówki muszą zostać przepisane.
- **ResponseHeaderConfiguration**: ten obiekt jest używany do określania pól nagłówka odpowiedzi, które mają zostać przepisane i nowej wartości, do której oryginalne nagłówki muszą zostać przepisane.
- **ActionSet**: ten obiekt zawiera konfiguracje nagłówków żądania i odpowiedzi określone powyżej. 
- **RewriteRule**: ten obiekt zawiera wszystkie *actionSets* określone powyżej. 
- **RewriteRuleSet**— ten obiekt zawiera wszystkie *przepisaćRules* i będzie musiał być dołączony do reguły routingu żądania - podstawowe lub oparte na ścieżce.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Określanie reguły marszruty

Utwórz regułę routingu żądań. Po utworzeniu ta konfiguracja ponownego zapisu jest dołączona do odbiornika źródłowego za pomocą reguły routingu. Podczas korzystania z podstawowej reguły routingu konfiguracja ponownego zapisu nagłówka jest skojarzona z odbiornikiem źródłowym i jest regułą na nowo mówiącą o nagłówku globalnym. Gdy używana jest reguła routingu oparta na ścieżce, konfiguracja przepisywania nagłówka jest definiowana na mapie ścieżki adresu URL. Tak więc dotyczy to tylko określonego obszaru ścieżki witryny. Poniżej zostanie utworzona podstawowa reguła routingu i dołączony jest zestaw reguł ponownego zapisu.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Określanie skalowania automatycznego

Teraz możesz określić konfigurację skalowania automatycznego dla bramy aplikacji. Obsługiwane są dwa typy konfiguracji skalowania automatycznego:

* **Tryb stałej pojemności**. W tym trybie brama aplikacji nie jest skalowana automatycznie i działa ze stałą pojemnością jednostki skalowania.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Tryb skalowania automatycznego**. W tym trybie brama aplikacji jest skalowana automatycznie w oparciu o wzorzec ruchu aplikacji.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Tworzenie bramy aplikacji

Utwórz bramę aplikacji i dodaj strefy nadmiarowości oraz konfigurację skalowania automatycznego.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Testowanie bramy aplikacji

Użyj Get-AzPublicIPAddress, aby uzyskać publiczny adres IP bramy aplikacji. Skopiuj publiczny adres IP (lub nazwę DNS), a następnie wklej go na pasku adresu przeglądarki.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najpierw zapoznaj się z zasobami utworzonymi przy użyciu bramy aplikacji. Następnie, gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, możesz je usunąć za pomocą polecenia `Remove-AzResourceGroup`.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Następne kroki

- [Tworzenie bramy aplikacji za pomocą reguł routingu bazujących na ścieżce adresu URL](./tutorial-url-route-powershell.md)
