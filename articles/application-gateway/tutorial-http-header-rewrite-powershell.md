---
title: Ponownie zapisuje nagłówki HTTP w usłudze Azure Application Gateway
description: Ten artykuł zawiera informacje na temat sposobu tworzenia usługi Azure Application Gateway i ponownie zapisuje nagłówki HTTP przy użyciu programu Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 4747d824dcf531ed883d476a0daad182ea081c39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715100"
---
# <a name="tutorial-create-an-application-gateway-and-rewrite-http-headers"></a>Samouczek: Tworzenie bramy aplikacji i ponownie zapisuje nagłówki HTTP

Można użyć programu Azure PowerShell, aby skonfigurować [reguły ponownego zapisywania nagłówki żądania i odpowiedzi HTTP](rewrite-http-headers.md) podczas tworzenia nowego [skalowania automatycznego i jednostki SKU bramy aplikacji strefowo nadmiarowy](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

> [!IMPORTANT] 
> Jednostka SKU autoskalowanej i strefowo nadmiarowej bramy aplikacji jest aktualnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie sieci wirtualnej skalowania automatycznego
> * Tworzenie zastrzeżonego publicznego adresu IP
> * Konfigurowanie infrastruktury bramy aplikacji
> * Określ konfigurację reguły ponownego zapisywania adresów nagłówek http
> * Określanie skalowania automatycznego
> * Tworzenie bramy aplikacji
> * Testowanie bramy aplikacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga, aby program Azure PowerShell działał lokalnie. Musi mieć Az modułu Wersja 1.0.0 lub nowszy. Uruchom `Import-Module Az` i następnie`Get-Module Az` Aby znaleźć wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Po zweryfikowaniu wersji programu PowerShell uruchom polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

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

Skonfigurować IP konfiguracji, frontonu konfiguracji adresu IP, puli zaplecza, HTTP ustawienia, certyfikat, port i odbiornika w formie identyczne do istniejącej bramy standardowej aplikacji. W nowej jednostce SKU obowiązuje taki sam model obiektów jak w jednostce SKU w warstwie Standardowa.

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

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Określ konfigurację reguły ponownego zapisywania adresów nagłówek HTTP

Skonfiguruj nowe obiekty, które są wymagane do przepisania nagłówków http:

- **RequestHeaderConfiguration**: ten obiekt jest używany do określenia pola nagłówka żądania, które planujesz ponownego zapisywania i nową wartość, która oryginalnego nagłówki muszą zostać przepisane, aby.
- **ResponseHeaderConfiguration**: ten obiekt jest używany do określania, które zamierzasz zmodyfikować pola nagłówka odpowiedzi i nową wartość, która oryginalnego nagłówki muszą zostać przepisane, aby.
- **ActionSet**: ten obiekt zawiera konfiguracje nagłówki żądania i odpowiedzi określonym powyżej. 
- **RewriteRule**: ten obiekt zawiera wszystkie *actionSets* określonej powyżej. 
- **RewriteRuleSet**— ten obiekt zawiera wszystkie *rewriteRules* i musi być dołączony do żądania regułę routingu — basic lub opartego na ścieżkach.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Określ reguły routingu

Utwórz regułę routingu żądania. Po utworzeniu tej konfiguracji ponownego zapisywania jest dołączony do odbiornika źródła za pomocą reguły routingu. Korzystając z podstawową regułę routingu, konfiguracja ponownego napisania nagłówka jest skojarzony z odbiornika źródła i nadpisania globalnego nagłówka. W przypadku regułę routingu opartego na ścieżkach Konfiguracja ponownego napisania nagłówka został zdefiniowany w Mapa ścieżki adresu URL. Tak mają zastosowanie tylko do określonej ścieżki obszaru lokacji. Poniżej utworzono podstawową regułę routingu i zestaw reguł ponownego zapisywania jest dołączony.

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

Użyj Get AzPublicIPAddress, aby pobrać publiczny adres IP bramy aplikacji. Skopiuj publiczny adres IP (lub nazwę DNS), a następnie wklej go na pasku adresu przeglądarki.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Najpierw zapoznaj się z zasobami utworzonymi przy użyciu bramy aplikacji. Następnie, gdy grupa zasobów, brama aplikacji i wszystkie pokrewne zasoby nie będą już potrzebne, możesz je usunąć za pomocą polecenia `Remove-AzResourceGroup`.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie bramy aplikacji za pomocą reguł routingu bazujących na ścieżce adresu URL](./tutorial-url-route-powershell.md)
