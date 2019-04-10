---
title: Limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń w usłudze Azure Application Gateway — witryna Azure portal
description: Ten artykuł zawiera informacje dotyczące limitów rozmiarów żądania zapory aplikacji sieci web i listy wykluczeń w konfiguracji w usłudze Application Gateway przy użyciu witryny Azure portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.workload: infrastructure-services
ms.date: 1/29/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a814fc6e9a72ba92d915821bd1e1694366844555
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277424"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń

Zapora aplikacji sieci web usługi Azure Application Gateway (WAF) zapewnia ochronę aplikacji sieci web. W tym artykule opisano limity rozmiaru żądanie zapory aplikacji sieci Web i listy wykluczeń w konfiguracji.

## <a name="waf-request-size-limits"></a>Limity rozmiaru żądanie zapory aplikacji sieci Web

![Limity rozmiaru żądania](media/application-gateway-waf-configuration/waf-requestsizelimit.png)

Zapora aplikacji sieci Web pozwala skonfigurować limity rozmiaru żądania w ramach dolną i górną granicę. Dostępne są następujące konfiguracje limitów rozmiaru dwa:

- Pole o rozmiarze treść żądania maksymalna jest określona w artykułów bazy wiedzy i kontrolek, które przekazuje ogólną limit rozmiaru żądania, z wyłączeniem żadnych plików. To pole można dostosować w zakresie od co najmniej 1 KB do wartości maksymalnej 128 KB. Wartością domyślną dla rozmiaru treść żądania jest 128 KB.
- Pola limit przekazywania plików jest wyrażona w Megabajtach i określa maksymalny dozwolony rozmiar przekazywanych plików. To pole może mieć minimalną wartość 1 MB i maksymalnie 500 MB dla jednostki SKU dużych wystąpień, gdy średnie jednostki SKU może zawierać maksymalnie 100 MB. Wartość domyślna dla pliku przekazywania limit wynosi 100 MB.

Zapora aplikacji sieci Web oferuje także można skonfigurować pokrętła, aby włączyć inspekcję treści żądania lub wyłączyć. Domyślnie włączona jest inspekcja treści żądania. Jeśli inspekcja treść żądania jest wyłączona, zapory aplikacji sieci Web nie może oszacować zawartość treści komunikatu HTTP. W takich przypadkach zapory aplikacji sieci Web w dalszym ciągu wymuszania reguł zapory aplikacji sieci Web na nagłówki plików cookie i identyfikatora URI. Jeśli inspekcja treść żądania jest wyłączona, pole o rozmiarze treść żądania maksymalna nie ma zastosowania i nie można ustawić. Umożliwia wyłączenie inspekcji treść żądania dla wiadomości większych niż 128 KB do wysłania do zapory aplikacji sieci Web, ale treść komunikatu nie jest kontrolowane w zakresie luki w zabezpieczeniach.

## <a name="waf-exclusion-lists"></a>Listy wykluczeń zapory aplikacji sieci Web

![waf-exclusion.png](media/application-gateway-waf-configuration/waf-exclusion.png)

Listy wykluczeń zapory aplikacji sieci Web pozwala na pominięcie niektórych atrybutów żądania oceny zapory aplikacji sieci Web. Typowym przykładem jest, że usługi Active Directory włożony tokenów, które są używane do uwierzytelniania lub pola hasła. Takie atrybuty są podatne na zawierać znaków specjalnych, co może powodować wyzwalanie wynik fałszywie dodatni z reguł zapory aplikacji sieci Web. Gdy atrybut zostanie dodany do listy wykluczeń zapory aplikacji sieci Web, nie jest uznawane za przez żadną regułę zapory aplikacji sieci Web skonfigurowanych i aktywne. Listy wykluczeń są globalne w zakresie.

Następujące atrybuty mogą być dodawane do listy wykluczeń:

* Nagłówki żądania
* Pliki cookie żądania
* Treść żądania

   * Wieloczęściowych danych formularza
   * XML
   * JSON

Określ nagłówek żądania dokładnie, treści, pliku cookie lub dopasowanie atrybut ciągu zapytania.  Alternatywnie można opcjonalnie określić częściowego dopasowania. Wykluczenie jest zawsze włączona pole nagłówka, nigdy na jego wartość. Reguły wykluczania mają zakres globalny i mają zastosowanie do wszystkich stron i wszystkie reguły.

Operatory kryteria dopasowania obsługiwane są następujące:

- **Równa się**:  Ten operator jest używany dla dokładnego dopasowania. Przykład wybierania nagłówka o nazwie **bearerToken**, operatorem równa się za pomocą selektora ustawiony jako **bearerToken**.
- **Rozpoczyna się od**: Ten operator pasuje do wszystkich pól, rozpoczynających się od wartości określonej selektora.
- **Kończy się**:  Ten operator pasuje do wszystkich pól żądania, które kończy się wartość określony selektor.
- **Zawiera**: Ten operator pasuje do wszystkich pól żądania, które zawierają wartość określony selektor.
- **Równe wszystkim**: Ten operator pasuje do wszystkich pól żądania. * będzie wartości selektora.

We wszystkich przypadkach dopasowania jest uwzględniana wielkość liter i wyrażenie regularne nie są dozwolone jako selektorów.

### <a name="examples"></a>Przykłady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższy fragment kodu programu Azure PowerShell pokazuje użycie wykluczeń:

```azurepowershell
// exclusion 1: exclude request head start with xyz
// exclusion 2: exclude request args equals a

$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestHeaderNames" -SelectorMatchOperator "StartsWith" -Selector "xyz"

$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig -MatchVariable "RequestArgNames" -SelectorMatchOperator "Equals" -Selector "a"

// add exclusion lists to the firewall config

$firewallConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention -RuleSetType "OWASP" -RuleSetVersion "2.2.9" -DisabledRuleGroups $disabledRuleGroup1,$disabledRuleGroup2 -RequestBodyCheck $true -MaxRequestBodySizeInKb 80 -FileUploadLimitInMb 70 -Exclusions $exclusion1,$exclusion2
```

Poniższy fragment kodu json demonstruje użycie wykluczeń:

```json
"webApplicationFirewallConfiguration": {
          "enabled": "[parameters('wafEnabled')]",
          "firewallMode": "[parameters('wafMode')]",
          "ruleSetType": "[parameters('wafRuleSetType')]",
          "ruleSetVersion": "[parameters('wafRuleSetVersion')]",
          "disabledRuleGroups": [],
          "exclusions": [
            {
                "matchVariable": "RequestArgNames",
                "selectorMatchOperator": "StartsWith",
                "selector": "a^bc"
            }
```

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu ustawień zapory aplikacji sieci Web, możesz dowiedzieć się, jak wyświetlić dzienniki zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki usługi Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).
