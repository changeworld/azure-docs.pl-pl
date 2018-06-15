---
title: Obsługa protokołu WebSocket w bramy aplikacji Azure | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie obsługi protokołu WebSocket bramy aplikacji.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 8968dac1-e9bc-4fa1-8415-96decacab83f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: amsriva
ms.openlocfilehash: 75b06ddd02da231b7813c609c848c75e42116da5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836798"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Omówienie obsługi protokołu WebSocket w bramy aplikacji

Brama aplikacji w zapewnia macierzystą obsługę protokołu WebSocket we wszystkich rozmiarów bramy. Brak ma użytkownika można skonfigurować ustawienia selektywnie włączać lub wyłączać obsługę protokołu WebSocket. 

Protokół WebSocket standaryzowane w [RFC6455](https://tools.ietf.org/html/rfc6455) umożliwia pełnego dupleksu komunikacji między serwerem klientem za pośrednictwem długo działające połączenia TCP. Ta funkcja służy do większej liczby interaktywnych komunikacji między serwerem sieci web i klienta, który może być dwukierunkowe, bez konieczności sondowania jako wymagane w implementacji oparte na protokole HTTP. Protokół WebSocket niski ma narzut w odróżnieniu od protokołu HTTP i można ponownie użyć tego samego połączenia TCP dla wielu żądań/odpowiedzi spowodować efektywniejsze wykorzystanie zasobów. Protokoły WebSocket są przeznaczone do pracy za pośrednictwem tradycyjnych HTTP portów 80 i 443.

Aby kontynuować, przy użyciu standardowych odbiornika HTTP na porcie 80 i 443 do odbierania ruchu protokołu WebSocket. Ruch protokołu WebSocket jest następnie przekierowywane do serwera zaplecza włączone protokołu WebSocket przy użyciu puli zaplecza odpowiednie określonego w zasadach bramy aplikacji. Serwer wewnętrznej bazy danych musi odpowiadać na sond bramy aplikacji, które zostały opisane w [omówienie sondy kondycji](application-gateway-probe-overview.md) sekcji. Sondy kondycji bramy aplikacji są tylko HTTP/HTTPS. Każdy serwer wewnętrznej bazy danych musi odpowiadać na badania HTTP bramy aplikacji przekierowujący ruch protokołu WebSocket do serwera.

## <a name="listener-configuration-element"></a>Element konfiguracji odbiornika

Istniejący odbiornik HTTP może służyć do obsługi ruchu sieciowego protokołu WebSocket. Poniżej przedstawiono fragment element httpListeners z przykładowego pliku szablonu. Będzie potrzebny odbiorników HTTP i HTTPS do obsługi protokołu WebSocket i zabezpieczania ruchu protokołu WebSocket. Podobnie można użyć [portal](application-gateway-create-gateway-portal.md) lub [PowerShell](application-gateway-create-gateway-arm.md) Aby utworzyć bramę aplikacji za pomocą obiektów nasłuchujących na porcie 80/443 do obsługi ruchu sieciowego protokołu WebSocket.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool, BackendHttpSetting, konfiguracja usługi Routing i zasady

BackendAddressPool służy do definiowania puli zaplecza przy użyciu protokołu WebSocket włączone serwerów. BackendHttpSetting jest zdefiniowana z portem 80 i 443 wewnętrznej bazy danych. Właściwości koligacji na podstawie plików cookie i requestTimeouts nie mają znaczenia dla ruchu protokołu WebSocket. Nie ma żadnej zmiany wymagane w regule routingu, "Basic" służy do powiązanie odpowiednie odbiornika do odpowiedniej puli adresów zaplecza. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Włączony protokół WebSocket wewnętrznej bazy danych

Z wewnętrzną bazą danych musi być serwerem sieci web HTTP/HTTPS na skonfigurowanego portu dla protokołu WebSocket do pracy (zazwyczaj 80/443). To wymaganie dotyczy, ponieważ protokół WebSocket wymaga początkowego uzgadniania się wraz z uaktualnieniem protokołu WebSocket jako pole nagłówka HTTP. Poniżej przedstawiono przykład nagłówka:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Inną przyczyną tego jest sondy kondycji tej aplikacji bramy wewnętrznej bazy danych obsługuje tylko protokoły HTTP i HTTPS. Jeśli serwer wewnętrznej bazy danych nie odpowiada na HTTP lub HTTPS, sond, pochodzi z puli zaplecza.

## <a name="next-steps"></a>Następne kroki

Po szkoleniowe dotyczące obsługi protokołu WebSocket, przejdź do [Utwórz bramę aplikacji](application-gateway-create-gateway.md) aby zacząć korzystać z protokołu WebSocket aplikacji sieci web z obsługą.

