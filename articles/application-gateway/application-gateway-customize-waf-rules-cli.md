---
title: Dostosowywanie reguł zapory aplikacji sieci web w usłudze Azure Application Gateway — interfejs wiersza polecenia platformy Azure
description: Ten artykuł zawiera informacje na temat sposobu Dostosowywanie reguł zapory aplikacji sieci web w usłudze Application Gateway przy użyciu wiersza polecenia platformy Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 70377e6414b41669b6d3e991d24136a3cc4270be
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618654"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli"></a>Dostosowywanie reguł zapory aplikacji sieci web za pomocą wiersza polecenia platformy Azure

Zapora aplikacji sieci web usługi Azure Application Gateway (WAF) zapewnia ochronę aplikacji sieci web. Te zabezpieczenia stosowane są dostarczane przez Otwórz sieci Web aplikacji Security Project (OWASP) podstawowych reguł Ustaw (CRS). Niektóre reguły może spowodować, że wyniki fałszywie dodatnie i blokować ruch rzeczywistych. Z tego powodu Application Gateway oferuje możliwości dostosowywania grup reguł i reguł. Aby uzyskać więcej informacji na temat grup określonych reguł i reguł, zobacz [listę grup reguł CRS zapory aplikacji sieci web i reguły](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Widok grup reguł i reguł

W poniższych przykładach kodu pokazano, jak wyświetlić reguły i grup reguł, które można skonfigurować.

### <a name="view-rule-groups"></a>Wyświetl reguły grupy

Poniższy przykład pokazuje sposób wyświetlania grup reguł:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Poniższe dane wyjściowe są obcięte odpowiedzi z poprzedniego przykładu:

```json
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

```json
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

## <a name="mandatory-rules"></a>Obowiązujące reguły

Poniższa lista zawiera warunki powodujące zapory aplikacji sieci Web zablokować żądania w trybie zapobiegania (w trybie wykrywania, są rejestrowane jako wyjątki). Nie są skonfigurowane lub wyłączone:

* Nie można przeanalizować treść żądania wyników w żądaniu blokowane, chyba że jednostka inspekcji jest wyłączona (XML, JSON, dane formularza)
* Długość danych treści (nie plikami) żądania jest większy niż skonfigurowany limit
* Żądanie jest większy niż limit treści (w tym pliki)
* Wystąpił błąd wewnętrzny związany w aparacie zapory aplikacji sieci Web

CRS 3.x określonych:

* Liczba przychodzących anomalii wynik przekroczył próg

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu reguł wyłączonych, możesz dowiedzieć się, jak wyświetlić dzienniki zapory aplikacji sieci Web. Aby uzyskać więcej informacji, zobacz [diagnostyki usługi Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
