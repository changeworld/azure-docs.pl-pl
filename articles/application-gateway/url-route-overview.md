---
title: Omówienie routingu zawartości opartego na adresach URL w usłudze Azure Application Gateway
description: Ten artykuł zawiera omówienie routingu zawartości opartej na adresach URL bramy aplikacji platformy Azure, konfiguracji UrlPathMap i reguły PathBasedRouting.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 09/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e20acb131b1a091fef858dab34705f4a8d3b4c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251842"
---
# <a name="url-path-based-routing-overview"></a>Routing oparty na ścieżkach URL — omówienie

Routing oparty na ścieżkach URL umożliwia kierowanie ruchu do pul serwerów zaplecza na podstawie ścieżek URL żądania. 

Jeden ze scenariuszy polega na kierowaniu żądań dla różnych typów zawartości do różnych pól serwerów zaplecza.

W poniższym przykładzie usługa Application Gateway obsługuje ruch dla domeny contoso.com z trzech pul serwerów zaplecza, na przykład: VideoServerPool, ImageServerPool i DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Żądania http\://contoso.com/video/* są kierowane do VideoServerPool,\:a http //contoso.com/images/* są kierowane do ImageServerPool. Pula DefaultServerPool jest wybierana, jeśli żaden z wzorców ścieżki nie pasuje.

> [!IMPORTANT]
> Dla jednostki SKU w wersji 1 reguły są przetwarzane w kolejności, w jakiej są wymienione w portalu. Jeśli podstawowy odbiornik znajduje się na początku listy i jest zgodny z żądaniem przychodzącym, jest ono przetwarzane przez ten odbiornik. Dla jednostki SKU v2 dokładne dopasowania mają wyższy priorytet. Jednak zaleca się skonfigurowanie detektorów wielu lokacji przed skonfigurowaniem odbiornika podstawowego. Zapewnia to skierowanie ruchu do odpowiedniego zaplecza.

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

### <a name="pathpattern"></a>ŚcieżkaPattern

PathPattern to lista wzorców ścieżek do dopasowania. Każdy wzorzec musi rozpoczynać się od znaku „/”, a znak gwiazdki „*” jest dozwolony jedynie na końcu po znaku „/”. Ciąg podawany do matcher ścieżki nie zawiera żadnego tekstu po pierwszym ? lub #, a te znaki nie są tutaj dozwolone. W przeciwnym razie wszystkie znaki dozwolone w adresie URL są dozwolone w PathPattern.

Obsługiwane wzorce zależą od tego, czy wdrażasz bramę aplikacji w wersji 1, czy w wersji 2:

#### <a name="v1"></a>w wersji 1

Reguły ścieżki są niewrażliwe na wielkości liter.

|Wzorzec ścieżki w wersji 1  |Czy jest obsługiwana?  |
|---------|---------|
|`/images/*`     |tak|
|`/images*`     |nie|
|`/images/*.jpg`     |nie|
|`/*.jpg`     |nie|
|`/Repos/*/Comments/*`     |nie|
|`/CurrentUser/Comments/*`     |tak|

#### <a name="v2"></a>v2

Reguły ścieżki są niewrażliwe na wielkości liter.

|Wzorzec ścieżki w wersji 2  |Czy jest obsługiwana?  |
|---------|---------|
|`/images/*`     |tak|
|`/images*`     |tak|
|`/images/*.jpg`     |nie|
|`/*.jpg`     |nie|
|`/Repos/*/Comments/*`     |nie|
|`/CurrentUser/Comments/*`     |tak|

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

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z informacjami na temat routingu zawartości opartego na adresach URL skorzystaj z informacji dotyczących [tworzenia bramy aplikacji przy użyciu routingu opartego na adresach URL](create-url-route-portal.md), aby utworzyć bramę aplikacji za pomocą reguł routingu adresów URL.
