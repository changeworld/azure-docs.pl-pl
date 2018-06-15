---
title: Dostosowywanie reguły zapory aplikacji sieci web w brama usługi aplikacji Azure — PowerShell | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat sposobu dostosowywania reguł zapory aplikacji sieci web w aplikacji bramy przy użyciu programu PowerShell.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: victorh
ms.openlocfilehash: f992fbf9ab223e18c24c27ce0577b1af2017281a
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33201372"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Dostosowywanie reguły zapory aplikacji sieci web za pomocą programu PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [Program PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](application-gateway-customize-waf-rules-cli.md)

Brama aplikacji w usłudze Azure zapory aplikacji sieci web (WAF) zapewnia ochronę dla aplikacji sieci web. Te zabezpieczenia są dostarczane przez Otwórz sieci Web aplikacji zabezpieczeń projektu (OWASP) podstawowe reguły Ustaw (CRS). Niektóre zasady mogą spowodować fałszywych alarmów i zablokowanie ruchu prawdziwe. Z tego powodu bramy aplikacji oferuje możliwość dostosowywania grup reguł i zasad. Aby uzyskać więcej informacji na określonej reguły grup i reguł, zobacz [listy grup CRS reguły zapory aplikacji sieci web i reguł](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Widok grup reguł i zasad

W poniższych przykładach kodu przedstawiają sposób wyświetlania reguły i zasady grupy, które są konfigurowane na bramie aplikacji z obsługą zapory aplikacji sieci Web.

### <a name="view-rule-groups"></a>Grupy reguł widoku

Poniższy przykład przedstawia sposób wyświetlania grup reguł:

```powershell
Get-AzureRmApplicationGatewayAvailableWafRuleSets
```

Następujące dane wyjściowe jest skróconą odpowiedzi z poprzedniego przykładu:

```
OWASP (Ver. 3.0):

    REQUEST-910-IP-REPUTATION:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            910011     Rule 910011
            910012     Rule 910012
            ...        ...

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            ...        ...

OWASP (Ver. 2.2.9):

    crs_20_protocol_violations:
        Description:
            
        Rules:
            RuleId     Description
            ------     -----------
            960911     Invalid HTTP Request Line
            981227     Apache Error: Invalid URI in Request.
            960000     Attempted multipart/form-data bypass
            ...        ...
```

## <a name="disable-rules"></a>Wyłącz reguły

Poniższy przykład powoduje wyłączenie reguł `910018` i `910017` na bramę aplikacji:

```powershell
$disabledrules=New-AzureRmApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-910-IP-REPUTATION -Rules 910018,910017
Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
```

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu reguł wyłączonych można poznać sposoby wyświetlania dzienników zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki bramy aplikacji](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
