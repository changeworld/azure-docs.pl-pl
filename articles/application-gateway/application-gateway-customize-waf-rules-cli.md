---
title: Dostosowywanie reguł zapory aplikacji sieci web w usłudze Azure Application Gateway — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat sposobu Dostosowywanie reguł zapory aplikacji sieci web w usłudze Application Gateway przy użyciu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: c02e4edabdcb73bc14c64b42788cddc98d78498c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964125"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli"></a>Dostosowywanie reguł zapory aplikacji sieci web za pomocą wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [Program PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](application-gateway-customize-waf-rules-cli.md)

Zapora aplikacji sieci web usługi Azure Application Gateway (WAF) zapewnia ochronę aplikacji sieci web. Te zabezpieczenia stosowane są dostarczane przez Otwórz sieci Web aplikacji Security Project (OWASP) podstawowych reguł Ustaw (CRS). Niektóre reguły może spowodować, że wyniki fałszywie dodatnie i blokować ruch rzeczywistych. Z tego powodu Application Gateway oferuje możliwości dostosowywania grup reguł i reguł. Aby uzyskać więcej informacji na temat grup określonych reguł i reguł, zobacz [listę grup reguł CRS zapory aplikacji sieci web i reguły](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Widok grup reguł i reguł

W poniższych przykładach kodu pokazano, jak wyświetlić reguły i grup reguł, które można skonfigurować.

### <a name="view-rule-groups"></a>Wyświetl reguły grupy

Poniższy przykład pokazuje sposób wyświetlania grup reguł:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Poniższe dane wyjściowe są obcięte odpowiedzi z poprzedniego przykładu:

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

Poniższy przykład pokazuje, jak wyświetlić zasady w grupie określoną regułą:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Poniższe dane wyjściowe są obcięte odpowiedzi z poprzedniego przykładu:

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

## <a name="disable-rules"></a>Wyłączanie reguł

Poniższy przykład powoduje wyłączenie reguł `910018` i `910017` na bramę aplikacji:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu reguł wyłączonych, możesz dowiedzieć się, jak wyświetlić dzienniki zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki usługi Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
