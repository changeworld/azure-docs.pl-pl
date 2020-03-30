---
title: Dostosowywanie reguł przy użyciu programu PowerShell
titleSuffix: Azure Web Application Firewall
description: Ten artykuł zawiera informacje dotyczące dostosowywania reguł zapory aplikacji sieci Web w bramie aplikacji za pomocą programu PowerShell.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048504"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>Dostosowywanie reguł zapory aplikacji sieci Web przy użyciu programu PowerShell

Zapora aplikacji bramy aplikacji azure (WAF) zapewnia ochronę aplikacji sieci web. Zabezpieczenia te są dostarczane przez podstawowy zestaw reguł projektu OWASP (Open Web Application Security Project). Niektóre reguły mogą powodować fałszywe alarmy i blokować rzeczywisty ruch. Z tego powodu brama aplikacji umożliwia dostosowanie grup reguł i reguł. Aby uzyskać więcej informacji na temat określonych grup i reguł reguł, zobacz [Lista grup i reguł reguł zapory zapory aplikacji sieci Web](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Wyświetlanie grup reguł i reguł

Poniższe przykłady kodu pokazują, jak wyświetlić reguły i grupy reguł, które można konfigurować na bramie aplikacji z włączoną funkcją WAF.

### <a name="view-rule-groups"></a>Wyświetlanie grup reguł

W poniższym przykładzie pokazano, jak wyświetlić grupy reguł:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

Następujące dane wyjściowe są obciętą odpowiedzią z poprzedniego przykładu:

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

## <a name="disable-rules"></a>Wyłącz reguły

Poniższy przykład wyłącza reguły `911011` i `911012` na bramie aplikacji:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Przepisy obowiązkowe

Poniższa lista zawiera warunki, które powodują, że WAF zablokować żądanie w trybie zapobiegania (w trybie wykrywania są rejestrowane jako wyjątki). Tych nie można skonfigurować ani wyłączyć:

* Niesprzejęcie analizy treści żądania powoduje zablokowanie żądania, chyba że inspekcja ciała jest wyłączona (XML, JSON, dane formularza)
* Długość danych treści żądania (bez plików) jest większa niż skonfigurowany limit
* Treść żądania (łącznie z plikami) jest większa niż limit
* Wystąpił błąd wewnętrzny w silniku WAF

CRS 3.x specyficzne:

* Wynik anomalii ruchu przychodzącego przekroczył próg

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu wyłączonych reguł możesz dowiedzieć się, jak wyświetlać dzienniki WAF. Aby uzyskać więcej informacji, zobacz [Diagnostyka bramy aplikacji](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
