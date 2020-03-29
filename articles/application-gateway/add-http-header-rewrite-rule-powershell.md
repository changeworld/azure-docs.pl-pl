---
title: Przepisywanie nagłówków HTTP w usłudze Azure Application Gateway
description: Ten artykuł zawiera informacje dotyczące przepisywania nagłówków HTTP w usłudze Azure Application Gateway przy użyciu programu Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64947203"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Przepisywanie nagłówków żądań i odpowiedzi HTTP za pomocą bramy aplikacji platformy Azure — Azure PowerShell

W tym artykule opisano sposób używania programu Azure PowerShell do konfigurowania wystąpienia [jednostki SKU bramy aplikacji](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) w wersji 2 w celu ponownego ustawienia nagłówków HTTP w żądaniach i odpowiedziach.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Musisz uruchomić program Azure PowerShell lokalnie, aby wykonać kroki opisane w tym artykule. Musisz również mieć zainstalowany moduł Az w wersji 1.0.0 lub nowszej. Uruchom, `Import-Module Az` `Get-Module Az` a następnie, aby określić zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Po zweryfikowaniu wersji programu PowerShell uruchom polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Musisz mieć wystąpienie jednostki SKU bramy aplikacji w wersji 2. Przepisywanie nagłówków nie jest obsługiwane w jednostce SKU w wersji 1. Jeśli nie masz jednostki SKU w wersji 2, przed rozpoczęciem należy utworzyć wystąpienie [jednostki SKU bramy aplikacji w wersji 2.](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)

## <a name="create-required-objects"></a>Tworzenie wymaganych obiektów

Aby skonfigurować przepisywanie nagłówka HTTP, należy wykonać te kroki.

1. Utwórz obiekty, które są wymagane do ponownego zapisu nagłówka HTTP:

   - **RequestHeaderConfiguration**: Służy do określania pól nagłówka żądania, które mają zostać przepisane i nowej wartości nagłówków.

   - **ResponseHeaderConfiguration**: Służy do określania pól nagłówka odpowiedzi, które mają zostać przepisane i nowej wartości nagłówków.

   - **ActionSet**: Zawiera konfiguracje nagłówków żądań i odpowiedzi określone wcześniej.

   - **Warunek:** Konfiguracja opcjonalna. Przepisz warunki oceny zawartości żądań i odpowiedzi HTTP(S). Akcja ponownego zapisu nastąpi, jeśli żądanie HTTP(S) lub odpowiedź jest zgodna z warunkiem ponownego zapisu.

     Jeśli skojarzysz więcej niż jeden warunek z akcją, akcja występuje tylko wtedy, gdy spełnione są wszystkie warunki. Innymi słowy operacja jest logiczną operacją AND.

   - **RewriteRule**: Zawiera wiele przepisać akcję / przepisać kombinacje warunków.

   - **RuleSequence**: Opcjonalna konfiguracja, która pomaga określić kolejność wykonywania reguł. Ta konfiguracja jest przydatna, gdy masz wiele reguł przepisywania w zestawie przepisywania. Reguła ponownego zapisu, która ma niższą wartość sekwencji reguł, jest uruchamiana jako pierwsza. Jeśli przypisać tę samą wartość sekwencji reguły do dwóch reguł ponownego zapisu, kolejność wykonywania jest niedeterministyczne.

     Jeśli nie określisz ruleSequence jawnie, domyślna wartość 100 jest ustawiona.

   - **RewriteRuleSet**: Zawiera wiele reguł przepisywania, które będą skojarzone z regułą routingu żądań.

2. Dołącz RewriteRuleSet do reguły routingu. Konfiguracja ponownego zapisu jest dołączona do odbiornika źródłowego za pośrednictwem reguły routingu. W przypadku korzystania z podstawowej reguły routingu konfiguracja ponownego zapisu nagłówka jest skojarzona z odbiornikiem źródłowym i jest regułą na nowo mówiącą o nagłówku globalnym. W przypadku korzystania z reguły routingu opartej na ścieżce konfiguracja przepisywania nagłówka jest definiowana na mapie ścieżki adresu URL. W takim przypadku ma ona zastosowanie tylko do określonego obszaru ścieżki witryny.

Można utworzyć wiele zestawów ponownego zapisu nagłówka HTTP i zastosować każdy zestaw ponownego zapisu do wielu odbiorników. Ale można zastosować tylko jeden zestaw przepisać do określonego odbiornika.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Określanie konfiguracji reguły przepisywania nagłówka HTTP

W tym przykładzie zmodyfikujemy adres URL przekierowania, przepisując nagłówek lokalizacji w odpowiedzi HTTP za każdym razem, gdy nagłówek lokalizacji zawiera odwołanie do azurewebsites.net. Aby to zrobić, dodamy warunek, aby ocenić, czy nagłówek lokalizacji w odpowiedzi zawiera azurewebsites.net. Użyjemy wzoru `(https?):\/\/.*azurewebsites\.net(.*)$`. I użyjemy `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` jako wartości nagłówka. Ta wartość zastąpi *azurewebsites.net* *contoso.com* w nagłówku lokalizacji.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Pobieranie konfiguracji bramy aplikacji

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Pobieranie konfiguracji reguły routingu żądań

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Aktualizowanie bramy aplikacji o konfigurację przepisywania nagłówków HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Usuwanie reguły przepisywania

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o konfigurowaniu niektórych typowych przypadków użycia, zobacz [typowe scenariusze przepisywania nagłówka](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).