---
title: Dostosowywanie reguły zapory aplikacji sieci web w brama usługi aplikacji Azure — 2.0 interfejsu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat sposobu dostosowywania reguł zapory aplikacji sieci web w aplikacji bramy 2.0 interfejsu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: b0bd79bb7ce584a9abaffbb6c30d6fbfe64f87c2
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204211"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli-20"></a>Dostosowywanie reguły zapory aplikacji sieci web za pośrednictwem 2.0 interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [Program PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure 2.0](application-gateway-customize-waf-rules-cli.md)

Brama aplikacji w usłudze Azure zapory aplikacji sieci web (WAF) zapewnia ochronę dla aplikacji sieci web. Te zabezpieczenia są dostarczane przez Otwórz sieci Web aplikacji zabezpieczeń projektu (OWASP) podstawowe reguły Ustaw (CRS). Niektóre zasady mogą spowodować fałszywych alarmów i zablokowanie ruchu prawdziwe. Z tego powodu bramy aplikacji oferuje możliwość dostosowywania grup reguł i zasad. Aby uzyskać więcej informacji na określonej reguły grup i reguł, zobacz [listy grup reguł CRS zapory aplikacji sieci web i reguł](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Widok grup reguł i zasad

W poniższych przykładach kodu przedstawiają sposób wyświetlania reguły i zasady grupy, które można skonfigurować.

### <a name="view-rule-groups"></a>Grupy reguł widoku

Poniższy przykład przedstawia sposób wyświetlania grup reguł:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Następujące dane wyjściowe jest skróconą odpowiedzi z poprzedniego przykładu:

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Wyświetl reguły grupy reguły

Poniższy przykład przedstawia sposób wyświetlania reguły do grupy określonej reguły:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Następujące dane wyjściowe jest skróconą odpowiedzi z poprzedniego przykładu:

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Wyłącz reguły

Poniższy przykład powoduje wyłączenie reguł `910018` i `910017` na bramę aplikacji:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu reguł wyłączonych można poznać sposoby wyświetlania dzienników zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki bramy aplikacji](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
