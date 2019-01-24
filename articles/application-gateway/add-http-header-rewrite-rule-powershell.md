---
title: Ponownie zapisuje nagłówki HTTP w istniejącej bramie aplikacji platformy Azure
description: Ten artykuł zawiera informacje na temat sposobu ponownego zapisywania nagłówków HTTP w istniejącej bramie aplikacji platformy Azure przy użyciu programu Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: cb3af5dc8368dc7e598bd0b05653b8ae921a5097
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452313"
---
# <a name="rewrite-http-headers-in-an-existing-application-gateway"></a>Ponownie zapisuje nagłówki HTTP w istniejącej bramie aplikacji

Można użyć programu Azure PowerShell, aby skonfigurować [reguły ponownego zapisywania nagłówki żądania i odpowiedzi HTTP](rewrite-http-headers.md) w istniejącym [skalowania automatycznego i jednostki SKU bramy aplikacji strefowo nadmiarowy](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

> [!IMPORTANT] 
> Jednostka SKU autoskalowanej i strefowo nadmiarowej bramy aplikacji jest aktualnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Pobieranie konfiguracji istniejącej bramie aplikacji
> * Określ konfigurację reguły ponownego zapisywania adresów nagłówek http
> * Aktualizacja bramy aplikacji przy użyciu powyższej konfiguracji do poprawiania nagłówki http

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga, aby program Azure PowerShell działał lokalnie. Musi mieć Az modułu Wersja 1.0.0 lub nowszy. Uruchom `Import-Module Az` i następnie`Get-Module Az` Aby znaleźć wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Po zweryfikowaniu wersji programu PowerShell uruchom polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Określ konfigurację reguły ponownego zapisywania adresów nagłówek http**

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

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Pobieranie konfiguracji istniejącej bramy application gateway

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Pobieranie konfiguracji istniejącą regułę routingu żądania

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name Rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aktualizacja bramy aplikacji przy użyciu konfiguracji do poprawiania nagłówki http

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name rewriteRuleSet1 -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Usuwanie reguły ponownego zapisywania

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "rewriteRuleSet1" -ApplicationGateway $appgw 
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw 
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie bramy aplikacji za pomocą reguł routingu bazujących na ścieżce adresu URL](./tutorial-url-route-powershell.md)
