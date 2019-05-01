---
title: Ponownie zapisuje nagłówki HTTP w usłudze Azure Application Gateway
description: Ten artykuł zawiera informacje na temat sposobu ponownego zapisywania nagłówków HTTP w usłudze Azure Application Gateway przy użyciu programu Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947203"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Ponownie zapisuje nagłówki żądania i odpowiedzi HTTP przy użyciu usługi Azure Application Gateway — PowerShell platformy Azure

W tym artykule opisano sposób konfigurowania przy użyciu programu Azure PowerShell [jednostek SKU v2 Application Gateway](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) wystąpienia do przepisania nagłówków HTTP żądania i odpowiedzi.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Musisz uruchomić program PowerShell platformy Azure lokalnie, aby wykonać kroki opisane w tym artykule. Możesz również potrzebować takich praw Az modułu Wersja 1.0.0 lub nowszej. Uruchom `Import-Module Az` i następnie `Get-Module Az` można określić wersji, która została zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Po zweryfikowaniu wersji programu PowerShell uruchom polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Musisz mieć wystąpienia bramy Application Gateway jednostkę SKU v2. Ponowne napisanie nagłówków nie jest obsługiwana w ramach jednostki SKU v1. Jeśli nie masz jednostki SKU w wersji 2, Utwórz [jednostek SKU v2 Application Gateway](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) wystąpienia przed przystąpieniem do wykonywania.

## <a name="create-required-objects"></a>Utwórz wymagany obiekt

Aby skonfigurować ponownego napisania nagłówka HTTP, należy wykonać następujące czynności.

1. Tworzenie obiektów, które są wymagane do ponownego napisania nagłówka HTTP:

   - **RequestHeaderConfiguration**: Służy do określania pola nagłówka żądania, które planujesz ponownego zapisywania i nową wartość dla nagłówków.

   - **ResponseHeaderConfiguration**: Służy do określania, które zamierzasz zmodyfikować pola nagłówka odpowiedzi i nową wartość dla nagłówków.

   - **ActionSet**: Zawiera konfiguracje nagłówki żądania i odpowiedzi, określony wcześniej.

   - **Warunek**: Konfiguracja opcjonalna. Ponowne zapisywanie adresów warunki zostaną obliczone treści żądania HTTP (S) i odpowiedzi. Akcja ponownego napisania wystąpi, jeśli żądania HTTP (S) lub odpowiedzi dopasowuje warunek ponownego zapisywania.

     Jeśli więcej niż jeden warunek jest skojarzona z akcją, akcja jest wykonywana tylko wtedy, gdy są spełnione wszystkie warunki. Innymi słowy operacja jest operacją i logicznych.

   - **RewriteRule**: Zawiera wiele akcji ponownego napisania / ponownego zapisywania kombinacje warunku.

   - **RuleSequence**: Wykonaj opcjonalnym, który ułatwia określenie kolejności, w której reguły ponownego zapisywania. Ta konfiguracja jest przydatne, jeśli masz wiele reguł ponownego zapisywania w zestawie ponownego zapisywania. Reguły ponownego pisania, który ma mniejszą wartość kolejności reguły jest uruchamiany w pierwszy. Jeśli przypiszesz taką samą wartość w sekwencji regułę na dwie reguły ponownego zapisywania kolejność wykonywania jest niedeterministyczny.

     Jeśli nie określisz jawnie RuleSequence, ustawiono wartość domyślną równą 100.

   - **RewriteRuleSet**: Zawiera wiele reguł ponownego zapisywania, które zostaną skojarzone w regule routingu żądań.

2. Dołącz RewriteRuleSet do reguły routingu. Konfiguracja ponownego zapisywania jest dołączony do odbiornika źródła za pomocą reguły routingu. Korzystając z podstawową regułę routingu, konfiguracja ponownego napisania nagłówka jest skojarzony z odbiornika źródła i nadpisania globalnego nagłówka. Gdy używasz regułę routingu opartego na ścieżkach, konfiguracja ponownego napisania nagłówka został zdefiniowany w Mapa ścieżki adresu URL. W takim przypadku zastosowanie tylko do określonej ścieżki obszaru lokacji.

Można utworzyć wiele zestawów ponownego napisania nagłówka HTTP i stosować każdego Napisz ponownie ustawić do wielu odbiorników. Jednak można zastosować tylko do jednego ponownego zapisywania zestawu do określonego odbiornika.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Określ konfigurację reguły ponownego zapisywania adresów nagłówka HTTP

W tym przykładzie zmodyfikujemy adresem URL przekierowania, zapisując je ponownie w nagłówku location odpowiedzi HTTP, zawsze wtedy, gdy nagłówek location zawiera odwołanie do azurewebsites.net. Aby to zrobić, dodamy warunek do oceny, czy nagłówek lokalizacji, w odpowiedzi zawiera azurewebsites.net. Użyjemy wzorca `(https?):\/\/.*azurewebsites\.net(.*)$`. I użyjemy `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako wartość nagłówka. Ta wartość spowoduje zastąpienie *azurewebsites.net* z *contoso.com* w nagłówku location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Pobierz konfigurację bramy application gateway

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Pobieranie konfiguracji reguły routingu żądania

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aktualizacja bramy aplikacji przy użyciu konfiguracji do poprawiania nagłówki HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Usuwanie reguły ponownego zapisywania

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat sposobu konfigurowania niektórych typowych przypadków użycia, zobacz [typowego nagłówka Nadpisz scenariuszy](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).