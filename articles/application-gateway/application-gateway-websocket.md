---
title: Obsługa protokołu WebSocket w usłudze Azure Application Gateway | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: cc6e2480ea117a288ae94c9cd66be6a354d8230f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993324"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Omówienie obsługi protokołu WebSocket w usłudze Application Gateway

Usługa Application Gateway zapewnia Natywna obsługa protokołu WebSocket we wszystkich rozmiarów bramy. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket. 

Protokół WebSocket ujednoliceniu [RFC6455](https://tools.ietf.org/html/rfc6455) umożliwia komunikację pełnodupleksową między serwerem klientem za pośrednictwem połączenia protokołu TCP długotrwałe. Ta funkcja umożliwia bardziej interaktywny komunikacji między serwerem sieci web i klienta, który można dwukierunkową, bez konieczności sondowania jako wymagane w implementacji oparty na protokole HTTP. WebSocket niski ma obciążenie w odróżnieniu od protokołu HTTP i można ponownie użyć tego samego połączenia protokołu TCP dla wielu żądań/odpowiedzi skutkuje bardziej wydajne wykorzystanie zasobów. Protokoły WebSocket zostały zaprojektowane do pracy za pośrednictwem tradycyjnych portów HTTP 80 i 443.

Aby kontynuować, przy użyciu standardowych odbiornik HTTP na porcie 80 i 443 do odbierania ruchu protokołu WebSocket. Ruch protokołu WebSocket jest następnie przekierowywane do serwera zaplecza włączone WebSocket określonych w regułach bramy aplikacji przy użyciu odpowiedniej puli zaplecza. Serwer wewnętrznej bazy danych musi odpowiadać na sondy bramy aplikacji, które są opisane w [Przegląd sondy kondycji](application-gateway-probe-overview.md) sekcji. Sondy kondycji bramy aplikacji, są tylko HTTP/HTTPS. Każdy serwer zaplecza musi odpowiadać na sondy HTTP na potrzeby bramy aplikacji do kierowania ruchu protokołu WebSocket z serwerem.

## <a name="listener-configuration-element"></a>Element konfiguracji odbiornika

Istniejący odbiornik HTTP może służyć do obsługi ruchu sieciowego protokołu WebSocket. Poniżej przedstawiono fragment elementu httpListeners z pliku szablonu próbki. Będziesz potrzebować odbiorników HTTP i HTTPS do obsługi protokołu WebSocket i bezpieczny ruch protokołu WebSocket. Podobnie można użyć [portal](application-gateway-create-gateway-portal.md) lub [PowerShell](application-gateway-create-gateway-arm.md) do utworzenia bramy aplikacji przy użyciu obiektów nasłuchujących na porcie 80/443 do obsługi ruchu sieciowego protokołu WebSocket.

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

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Konfiguracja reguły BackendAddressPool, parametr BackendHttpSetting i routingu

BackendAddressPool jest używane do definiowania puli zaplecza przy użyciu protokołu WebSocket włączone serwerów. Parametr backendHttpSetting jest zdefiniowana za pomocą portu zaplecza, 80 i 443. Właściwości koligacji na podstawie plików cookie i requestTimeouts nie są istotne dla ruchu protokołu WebSocket. Nie ma żadnej zmiany wymagane w regule routingu, "Podstawowe" umożliwia powiązanie odpowiednie odbiornika do odpowiedniej puli adresów zaplecza. 

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

## <a name="websocket-enabled-backend"></a>Włączone WebSocket wewnętrznej bazy danych

Wewnętrzną bazą danych musi być uruchomione na skonfigurowanego serwera sieci web HTTP/HTTPS portu (zwykle 80/443) protokołu WebSocket do pracy. To wymaganie jest, ponieważ protokół WebSocket wymaga początkowej uzgadnianie się przy uaktualnianiu protokołu WebSocket jako pole nagłówka HTTP. Oto przykład nagłówka:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Inną przyczyną tego jest tej sondy kondycji zaplecza bramy aplikacji obsługuje tylko protokoły HTTP i HTTPS. Jeśli serwer wewnętrznej bazy danych nie odpowiada sond protokołu HTTP lub HTTPS, pochodzi z puli zaplecza.

## <a name="next-steps"></a>Kolejne kroki

Po zapoznaniu się obsługa protokołu WebSocket, przejdź do [utworzyć bramę aplikacji](application-gateway-create-gateway.md) wprowadzenie WebSocket aplikacji sieci web z obsługą.

