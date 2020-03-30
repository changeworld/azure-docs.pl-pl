---
title: Hostowanie wielu witryn w usłudze Azure Application Gateway
description: Ten artykuł zawiera omówienie obsługi obsługi wielu lokacji usługi Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257414"
---
# <a name="application-gateway-multiple-site-hosting"></a>Hostowanie wielu witryn usługi Application Gateway

Hosting wielu lokacji umożliwia skonfigurowanie więcej niż jednej aplikacji sieci web na tym samym porcie bramy aplikacji. Ta funkcja umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie maksymalnie 100 witryn internetowych do jednej bramy aplikacji. Każdą witrynę sieci Web można skierować do jej puli zaplecza. W poniższym przykładzie brama `contoso.com` `fabrikam.com` aplikacji obsługuje ruch dla i z dwóch pul serwerów zaplecza o nazwie ContosoServerPool i FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Reguły są przetwarzane w kolejności, w jakiej są wymienione w portalu dla jednostki SKU w wersji 1. Dla jednostki SKU v2 dokładne dopasowania mają wyższy priorytet. Zdecydowanie zaleca się skonfigurowanie odbiorników obejmujących wiele lokacji przed skonfigurowaniem podstawowego odbiornika.  Zapewni to skierowanie ruchu do odpowiedniego zaplecza. Jeśli podstawowy odbiornik znajduje się na początku listy i jest zgodny z żądaniem przychodzącym, jest ono przetwarzane przez ten odbiornik.

Żądania dotyczące adresu `http://contoso.com` są kierowane do puli ContosoServerPool, a żądania dotyczące adresu `http://fabrikam.com` — do puli FabrikamServerPool.

Podobnie można obsługiwać wiele poddomen tej samej domeny nadrzędnej w tym samym wdrożeniu bramy aplikacji. Na przykład można `http://blog.contoso.com` hostować i `http://app.contoso.com` na wdrożenie bramy pojedynczej aplikacji.

## <a name="host-headers-and-server-name-indication-sni"></a>Nagłówki hosta i oznaczanie nazwy serwera (SNI, Server Name Indication)

Istnieją trzy popularne mechanizmy włączania hostingu wielu witryn w tej samej infrastrukturze.

1. Hostowanie wielu aplikacji internetowych — każda z nich na unikatowym adresie IP.
2. Użycie nazwy hosta do hostowania wielu aplikacji internetowych na tym samym adresie IP.
3. Użycie różnych portów do hostowania wielu aplikacji internetowych na tym samym adresie IP.

Obecnie brama aplikacji obsługuje jeden publiczny adres IP, w którym nasłuchuje ruchu. Tak więc wiele aplikacji, każda z własnym adresem IP nie jest obecnie obsługiwana. 

Brama aplikacji obsługuje wiele aplikacji, z których każda nasłuchuje na różnych portach, ale w tym scenariuszu wymaga to, aby aplikacje akceptował ruch na niestandardowych portach. Często nie jest to konfiguracja, która ma być żądana.

Usługa Application Gateway bazuje na nagłówkach hosta HTTP 1.1 w celu hostowania więcej niż jednej witryny sieci Web na tym samym publicznym adresie IP i porcie. Witryny hostowane w bramie aplikacji mogą również obsługiwać odciążanie protokołu TLS z rozszerzeniem TLS wskazania nazwy serwera (SNI). Ten scenariusz oznacza, że przeglądarka i farma sieci Web zaplecza klienta muszą obsługiwać protokół HTTP/1.1 i rozszerzenie TLS zgodnie ze standardem RFC 6066.

## <a name="listener-configuration-element"></a>Element konfiguracji odbiornika

Istniejące elementy konfiguracji HTTPListener są rozszerzone do obsługi elementów wskazania nazwy hosta i nazwy serwera. Jest on używany przez bramę aplikacji do kierowania ruchu do odpowiedniej puli wewnętrznej bazy danych. 

Poniższy przykład kodu jest fragment elementu HttpListeners z pliku szablonu:

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

W regule routingu nie jest wymagana żadna zmiana. Nadal należy wybierać podstawową regułę routingu „Basic” w celu powiązania odpowiedniego odbiornika witryny z właściwą pulą adresów zaplecza.

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

