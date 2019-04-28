---
title: Omówienie routingu zawartości opartego na adresach URL w usłudze Azure Application Gateway
description: Ta strona zawiera omówienie routingu zawartości opartego na adres URL bramy aplikacji platformy Azure, konfiguracji UrlPathMap i reguły pathbasedrouting w usłudze.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/8/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 2f0bd0a20b866d342802014f1034a7ed9d939d53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831374"
---
# <a name="url-path-based-routing-overview"></a>Routing oparty na ścieżkach URL — omówienie

Routing oparty na ścieżkach URL umożliwia kierowanie ruchu do pul serwerów zaplecza na podstawie ścieżek URL żądania. 

Jeden ze scenariuszy polega na kierowaniu żądań dla różnych typów zawartości do różnych pól serwerów zaplecza.

W poniższym przykładzie Usługa Application Gateway obsługuje ruch dla domeny contoso.com z trzech pul serwerów zaplecza na przykład: Puli VideoServerPool, ImageServerPool i DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Żądania http\://contoso.com/video/* są kierowane do puli VideoServerPool, a http\://contoso.com/images/* są kierowane do puli ImageServerPool. Pula DefaultServerPool jest wybierana, jeśli żaden z wzorców ścieżki nie pasuje.

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

### <a name="pathpattern"></a>PathPattern

PathPattern znajduje się lista wzorców ścieżki do dopasowania. Każdy wzorzec musi rozpoczynać się od znaku „/”, a znak gwiazdki „*” jest dozwolony jedynie na końcu po znaku „/”. Ciąg przekazywany do dopasowywania ścieżki nie zawiera żadnego tekstu po pierwszym? lub # i te znaki są niedozwolone w tym miejscu. W przeciwnym razie znaków w adresie URL są dozwolone w PathPattern.

Obsługiwane wzorce zależą od tego, czy wdrażanie bramy Application Gateway v1 lub v2:

#### <a name="v1"></a>v1

Reguły ścieżki jest rozróżniana wielkość liter.

|wzorzec ścieżki V1  |Są obsługiwane?  |
|---------|---------|
|`/images/*`     |tak|
|`/images*`     |nie|
|`/images/*.jpg`     |nie|
|`/*.jpg`     |nie|
|`/Repos/*/Comments/*`     |nie|
|`/CurrentUser/Comments/*`     |tak|

#### <a name="v2"></a>v2

Reguły ścieżki jest uwzględniana wielkość liter.

|wzorzec ścieżki v2  |Są obsługiwane?  |
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

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się z informacjami na temat routingu zawartości opartego na adresach URL skorzystaj z informacji dotyczących [tworzenia bramy aplikacji przy użyciu routingu opartego na adresach URL](application-gateway-create-url-route-portal.md), aby utworzyć bramę aplikacji za pomocą reguł routingu adresów URL.
