---
title: Hostowanie wielu witryn w usłudze Azure Application Gateway
description: Ten artykuł zawiera omówienie obsługi wielolokacjowej platformy Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/7/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: ac9dd31e01b1915642951aeddb10d3eae118d943
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523785"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hostowanie wielu witryn usługi Application Gateway

Obsługa wielu witryn umożliwia skonfigurowanie więcej niż jednej aplikacji sieci Web na tym samym porcie bramy aplikacji. Ta funkcja umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie maksymalnie 100 witryn internetowych do jednej bramy aplikacji. Każdą witrynę sieci Web można skierować do jej puli zaplecza. W poniższym przykładzie usługa Application Gateway obsługuje ruch dla domen contoso.com i fabrikam.com z dwóch pul serwerów zaplecza o nazwach ContosoServerPool i FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Reguły są przetwarzane w kolejności, w której są wyświetlane w portalu dla jednostki SKU w wersji 1. W przypadku jednostki SKU v2 dokładne dopasowania mają wyższy priorytet. Zdecydowanie zaleca się skonfigurowanie odbiorników obejmujących wiele lokacji przed skonfigurowaniem podstawowego odbiornika.  Zapewni to skierowanie ruchu do odpowiedniego zaplecza. Jeśli podstawowy odbiornik znajduje się na początku listy i jest zgodny z żądaniem przychodzącym, jest ono przetwarzane przez ten odbiornik.

Żądania dotyczące adresu `http://contoso.com` są kierowane do puli ContosoServerPool, a żądania dotyczące adresu `http://fabrikam.com` — do puli FabrikamServerPool.

Podobnie dwie domeny podrzędne tej samej domeny nadrzędnej mogą być hostowane na tym samym wdrożeniu usługi Application Gateway. Przykłady użycia domen podrzędnych mogą obejmować domeny `http://blog.contoso.com` i `http://app.contoso.com` hostowane w jednym wdrożeniu bramy Application Gateway.

## <a name="host-headers-and-server-name-indication-sni"></a>Nagłówki hosta i oznaczanie nazwy serwera (SNI, Server Name Indication)

Istnieją trzy popularne mechanizmy włączania hostingu wielu witryn w tej samej infrastrukturze.

1. Hostowanie wielu aplikacji internetowych — każda z nich na unikatowym adresie IP.
2. Użycie nazwy hosta do hostowania wielu aplikacji internetowych na tym samym adresie IP.
3. Użycie różnych portów do hostowania wielu aplikacji internetowych na tym samym adresie IP.

Obecnie usługa Application Gateway pobiera jeden publiczny adres IP, na którym nasłuchuje ruchu. Z tego względu obsługiwanie wielu aplikacji z oddzielnym adresem IP dla każdej z nich nie jest obecnie obsługiwane. Usługa Application Gateway obsługuje hostowanie wielu aplikacji, z których każda nasłuchuje na innym porcie, ale ten scenariusz wymaga, aby aplikacje akceptowały ruch na portach niestandardowych, co często nie jest pożądaną konfiguracją. Usługa Application Gateway bazuje na nagłówkach hosta HTTP 1.1 w celu hostowania więcej niż jednej witryny sieci Web na tym samym publicznym adresie IP i porcie. Witryny hostowane w usłudze Application Gateway mogą także obsługiwać odciążanie protokołu SSL za pomocą rozszerzenia TLS oznaczania nazwy serwera. Ten scenariusz oznacza, że przeglądarka i farma sieci Web zaplecza klienta muszą obsługiwać protokół HTTP/1.1 i rozszerzenie TLS zgodnie ze standardem RFC 6066.

## <a name="listener-configuration-element"></a>Element konfiguracji odbiornika

Istniejący element konfiguracji HTTPListener został ulepszony na potrzeby obsługi elementów oznaczania nazwy hosta i nazwy serwera, co jest używane przez usługę Application Gateway w celu kierowania ruchu do odpowiedniej puli zaplecza. Poniższy przykład kodu jest fragmentem elementu HttpListeners z pliku szablonu.

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

Możesz odwiedzić stronę [Resource Manager template using multiple site hosting](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) (Szablon usługi Resource Manager z zastosowaniem hostowania wielu witryn), aby zapoznać się z kompleksowym wdrożeniem opartym na szablonie.

## <a name="routing-rule"></a>Reguła routingu

Reguła routingu nie wymaga żadnej zmiany. Nadal należy wybierać podstawową regułę routingu „Basic” w celu powiązania odpowiedniego odbiornika witryny z właściwą pulą adresów zaplecza.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z informacjami o hostowaniu wielu witryn przejdź do [tworzenia bramy aplikacji przy użyciu hostowania wielu witryn](tutorial-multiple-sites-powershell.md), aby utworzyć bramę aplikacji z możliwością obsługi więcej niż jednej aplikacji internetowej.

