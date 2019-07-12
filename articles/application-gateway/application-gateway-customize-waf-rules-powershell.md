---
title: Dostosowywanie reguł zapory aplikacji sieci web w usłudze Azure Application Gateway — PowerShell
description: Ten artykuł zawiera informacje na temat sposobu Dostosowywanie reguł zapory aplikacji sieci web w usłudze Application Gateway przy użyciu programu PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 35114783f8f438a0890d59c05452f4f0b0dbb41b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618580"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Dostosowywanie reguł zapory aplikacji sieci web za pomocą programu PowerShell

Zapora aplikacji sieci web usługi Azure Application Gateway (WAF) zapewnia ochronę aplikacji sieci web. Te zabezpieczenia stosowane są dostarczane przez Otwórz sieci Web aplikacji Security Project (OWASP) podstawowych reguł Ustaw (CRS). Niektóre reguły może spowodować, że wyniki fałszywie dodatnie i blokować ruch rzeczywistych. Z tego powodu Application Gateway oferuje możliwości dostosowywania grup reguł i reguł. Aby uzyskać więcej informacji na temat grup określonych reguł i reguł, zobacz [listę grup reguł CRS zapory aplikacji sieci web i reguły](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Widok grup reguł i reguł

W poniższych przykładach kodu pokazano, jak wyświetlić reguły i grup reguł, które można skonfigurować na bramie aplikacji z obsługą zapory aplikacji sieci Web.

### <a name="view-rule-groups"></a>Wyświetl reguły grupy

Poniższy przykład pokazuje sposób wyświetlania grup reguł:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

Poniższe dane wyjściowe są obcięte odpowiedzi z poprzedniego przykładu:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Wyłączanie reguł

Poniższy przykład powoduje wyłączenie reguł `911011` i `911012` na bramę aplikacji:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Obowiązujące reguły

Poniższa lista zawiera warunki powodujące zapory aplikacji sieci Web zablokować żądania w trybie zapobiegania (w trybie wykrywania, są rejestrowane jako wyjątki). Nie są skonfigurowane lub wyłączone:

* Nie można przeanalizować treść żądania wyników w żądaniu blokowane, chyba że jednostka inspekcji jest wyłączona (XML, JSON, dane formularza)
* Długość danych treści (nie plikami) żądania jest większy niż skonfigurowany limit
* Żądanie jest większy niż limit treści (w tym pliki)
* Wystąpił błąd wewnętrzny związany w aparacie zapory aplikacji sieci Web

CRS 3.x określonych:

* Liczba przychodzących anomalii wynik przekroczył próg

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu reguł wyłączonych, możesz dowiedzieć się, jak wyświetlić dzienniki zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki bramy aplikacji](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
