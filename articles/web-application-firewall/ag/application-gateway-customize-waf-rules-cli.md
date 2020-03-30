---
title: Dostosowywanie reguł przy użyciu interfejsu wiersza polecenia — Zapora aplikacji sieci Web platformy Azure
description: Ten artykuł zawiera informacje dotyczące dostosowywania reguł zapory aplikacji sieci Web w bramie aplikacji za pomocą interfejsu wiersza polecenia platformy Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048531"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Dostosowywanie reguł zapory aplikacji sieci Web przy użyciu interfejsu wiersza polecenia platformy Azure

Zapora aplikacji bramy aplikacji azure (WAF) zapewnia ochronę aplikacji sieci web. Zabezpieczenia te są dostarczane przez podstawowy zestaw reguł projektu OWASP (Open Web Application Security Project). Niektóre reguły mogą powodować fałszywe alarmy i blokować rzeczywisty ruch. Z tego powodu brama aplikacji umożliwia dostosowanie grup reguł i reguł. Aby uzyskać więcej informacji na temat określonych grup i reguł reguł, zobacz [Lista grup reguł i reguł crs zapory aplikacji sieci Web](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Wyświetlanie grup reguł i reguł

Poniższe przykłady kodu pokazują, jak wyświetlić reguły i grupy reguł, które można skonfigurować.

### <a name="view-rule-groups"></a>Wyświetlanie grup reguł

W poniższym przykładzie pokazano, jak wyświetlić grupy reguł:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Następujące dane wyjściowe są obciętą odpowiedzią z poprzedniego przykładu:

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

### <a name="view-rules-in-a-rule-group"></a>Wyświetlanie reguł w grupie reguł

W poniższym przykładzie pokazano, jak wyświetlić reguły w określonej grupie reguł:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Następujące dane wyjściowe są obciętą odpowiedzią z poprzedniego przykładu:

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

## <a name="disable-rules"></a>Wyłącz reguły

Poniższy przykład wyłącza reguły `910018` i `910017` na bramie aplikacji:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
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
