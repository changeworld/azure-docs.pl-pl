---
title: Omówienie routingu zawartości opartego na adresach URL w usłudze Azure Application Gateway
description: Ten artykuł zawiera omówienie routingu zawartości opartego na adresach URL, konfiguracji UrlPathMap i reguły PathBasedRouting w usłudze Application Gateway.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 04/23/2018
ms.date: 04/17/2019
ms.author: v-junlch
ms.openlocfilehash: ee0267146140d095487b293331a7de493ba151c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61361968"
---
# <a name="azure-application-gateway-url-path-based-routing-overview"></a>Omówienie routingu opartego na ścieżkach URL w usłudze Azure Application Gateway

Routing oparty na ścieżkach URL umożliwia kierowanie ruchu do pul serwerów zaplecza na podstawie ścieżek URL żądania. 

Jeden ze scenariuszy polega na kierowaniu żądań dla różnych typów zawartości do różnych pól serwerów zaplecza.

W poniższym przykładzie Usługa Application Gateway obsługuje ruch dla domeny contoso.com z trzech pul serwerów zaplecza na przykład: Puli VideoServerPool, ImageServerPool i DefaultServerPool.

![imageURLroute](./media/url-route-overview/figure1.png)

Żądania dotyczące <http://contoso.com/video/*> są kierowane do puli VideoServerPool, a <http://contoso.com/images/*> do puli ImageServerPool. Pula DefaultServerPool jest wybierana, jeśli żaden z wzorców ścieżki nie pasuje.

> [!IMPORTANT]
> Reguły są przetwarzane w kolejności, w jakiej znajdują się na liście w portalu. Zdecydowanie zaleca się skonfigurowanie odbiorników obejmujących wiele lokacji przed skonfigurowaniem podstawowego odbiornika.  Zapewnia to skierowanie ruchu do odpowiedniego zaplecza. Jeśli podstawowy odbiornik znajduje się na początku listy i jest zgodny z żądaniem przychodzącym, jest ono przetwarzane przez ten odbiornik.

## <a name="urlpathmap-configuration-element"></a>Element konfiguracji UrlPathMap

Element urlPathMap jest używany do określania wzorców ścieżki na potrzeby mapowań pul serwerów zaplecza. Poniższy przykład kodu jest fragmentem elementu urlPathMap z pliku szablonu.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

> [!NOTE]
> PathPattern: To ustawienie znajduje się lista wzorców ścieżki do dopasowania. Każdy wzorzec musi rozpoczynać się od znaku „/”, a znak gwiazdki „*” jest dozwolony jedynie na końcu po znaku „/”. Ciąg przekazywany do narzędzia dopasowywania ścieżki nie zawiera żadnego tekstu po pierwszym znaku „?” lub „#” i te znaki nie są tu dozwolone.

Aby uzyskać więcej informacji, zobacz [Resource Manager template using URL-based routing](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) (Szablon usługi Resource Manager korzystający z routingu opartego na adresach URL).

## <a name="pathbasedrouting-rule"></a>Reguła PathBasedRouting

Reguła RequestRoutingRule typu PathBasedRouting jest używana do powiązania odbiornika z elementem urlPathMap. Wszystkie żądania otrzymane dla tego odbiornika są kierowane zgodnie z zasadami określonymi w elemencie urlPathMap.
Fragment reguły PathBasedRouting:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się z informacjami na temat routingu zawartości opartego na adresach URL skorzystaj z informacji dotyczących [tworzenia bramy aplikacji przy użyciu routingu opartego na adresach URL](tutorial-url-route-powershell.md), aby utworzyć bramę aplikacji za pomocą reguł routingu adresów URL.

<!-- Update_Description: update metedata properties -->