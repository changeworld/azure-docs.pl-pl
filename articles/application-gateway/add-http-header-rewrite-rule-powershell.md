---
title: Ponownie zapisuje nagłówki HTTP w usłudze Azure Application Gateway
description: Ten artykuł zawiera informacje na temat sposobu ponownego zapisywania nagłówków HTTP w usłudze Azure Application Gateway przy użyciu programu Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 405bc9aed4605e9728e112595f33c879bf55ec7f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005625"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Ponownie zapisuje nagłówki żądania i odpowiedzi HTTP przy użyciu usługi Azure Application Gateway — PowerShell platformy Azure

W tym artykule pokazano, jak skonfigurować za pomocą programu Azure PowerShell [jednostek SKU v2 Application Gateway](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) do przepisania nagłówków HTTP żądania i odpowiedzi.

> [!IMPORTANT]
> Jednostka SKU autoskalowanej i strefowo nadmiarowej bramy aplikacji jest aktualnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Ten samouczek wymaga, aby program Azure PowerShell działał lokalnie. Musi mieć Az modułu Wersja 1.0.0 lub nowszy. Uruchom `Import-Module Az` i następnie`Get-Module Az` Aby znaleźć wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Po zweryfikowaniu wersji programu PowerShell uruchom polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Musisz mieć usługi Application Gateway w wersji 2 jednostki SKU, ponieważ możliwość ponownego zapisywania nagłówka nie jest obsługiwana dla jednostek SKU v1. Jeśli nie masz jednostki SKU w wersji 2, Utwórz [jednostek SKU v2 Application Gateway](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) przed przystąpieniem do wykonywania.

## <a name="what-is-required-to-rewrite-a-header"></a>Co jest wymagane do przepisania nagłówka

Aby skonfigurować ponownego napisania nagłówka HTTP, konieczne będzie:

1. Utwórz nowe obiekty, które są wymagane do przepisania nagłówków http:

   - **RequestHeaderConfiguration**: ten obiekt jest używany do określenia pola nagłówka żądania, które planujesz ponownego zapisywania i nową wartość, która oryginalnego nagłówki muszą zostać przepisane, aby.

   - **ResponseHeaderConfiguration**: ten obiekt jest używany do określania, które zamierzasz zmodyfikować pola nagłówka odpowiedzi i nową wartość, która oryginalnego nagłówki muszą zostać przepisane, aby.

   - **ActionSet**: ten obiekt zawiera konfiguracje nagłówki żądania i odpowiedzi określonym powyżej.

   - **Warunek**: Jest opcjonalna konfiguracja. warunek ponownego zapisywania jest dodawany, oceni treści żądania HTTP (S) i odpowiedzi. Decyzja do wykonania akcji ponownego napisania skojarzone z warunkiem ponownego napisania będzie zależeć czy dopasowywane żądania HTTP (S) lub odpowiedzi z warunkiem ponownego zapisywania. 

     Jeśli więcej niż jeden warunki są skojarzone z akcji, a następnie akcję będą wykonywane tylko wtedy, gdy wszystkie warunki są spełnione, czyli, logiczne i będzie można wykonać operacji.

   - **RewriteRule**: zawiera wiele akcję ponownego napisania — kombinacje warunek ponowne zapisywanie adresów.

   - **RuleSequence**: Jest to opcjonalna konfiguracja. Pomaga określić kolejność, w którym są wykonywane przez reguły ponownego zapisywania różnych. Jest to przydatne, gdy wiele reguł ponownego zapisywania znajduje się w zestawie ponownego zapisywania. Reguły ponownego pisania o niższej wartości kolejności reguł pobiera wykonywany jako pierwszy. Możesz podać tę samą sekwencję regułę na dwie reguły ponownego zapisywania kolejność wykonywania zostaną deterministyczna.

     Jeśli nie określisz jawnie RuleSequence, będzie można ustawić wartość domyślną równą 100.

   - **RewriteRuleSet**: ten obiekt zawiera wiele reguł ponownego zapisywania, które zostaną skojarzone w regule routingu żądań.

2. Trzeba będzie można dołączyć rewriteRuleSet przy użyciu reguły routingu. Jest to spowodowane konfiguracji ponownego zapisywania jest dołączony do odbiornika źródła za pomocą reguły routingu. Korzystając z podstawową regułę routingu, konfiguracja ponownego napisania nagłówka jest skojarzony z odbiornika źródła i nadpisania globalnego nagłówka. W przypadku regułę routingu opartego na ścieżkach Konfiguracja ponownego napisania nagłówka został zdefiniowany w Mapa ścieżki adresu URL. Tak mają zastosowanie tylko do określonej ścieżki obszaru lokacji.

Można utworzyć wiele zestawów ponownego napisania nagłówka http, a każdy zestaw ponownego napisania można zastosować do wielu odbiorników. Jednak można zastosować tylko do jednego ponownego zapisywania zestawu do określonego odbiornika.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>**Określ konfigurację reguły ponownego zapisywania adresów nagłówek http**

W tym przykładzie zostanie zmodyfikujemy adresu URL przekierowania, zapisując je ponownie w nagłówku location odpowiedzi http, zawsze wtedy, gdy nagłówek location zawiera odwołanie do "azurewebsites.net". Aby to zrobić, dodamy warunek do oceny, czy nagłówek lokalizacji, w odpowiedzi zawiera azurewebsites.net za pomocą wzorca `(https?):\/\/.*azurewebsites\.net(.*)$`. Firma Microsoft użyje `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako wartość nagłówka. Spowoduje to zamianę *azurewebsites.net* z *contoso.com* w nagłówku location.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-configuration-of-your-existing-application-gateway"></a>Pobieranie konfiguracji istniejącej bramy application gateway

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-configuration-of-your-existing-request-routing-rule"></a>Pobieranie konfiguracji istniejącą regułę routingu żądania

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aktualizacja bramy aplikacji przy użyciu konfiguracji do poprawiania nagłówki http

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

Aby dowiedzieć się więcej na temat konfiguracji wymagane do wykonania, niektóre typowe przypadki użycia, zobacz [typowego nagłówka Nadpisz scenariuszy](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).