---
title: Obsługa protokołu WebSocket w usłudze Azure Application Gateway | Dokumentacja firmy Microsoft
description: Ta strona zawiera omówienie obsługi protokołu WebSocket bramy aplikacji.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/18/2019
ms.openlocfilehash: 54c34690e678f07d6309a1877b0ca5d0a0b274f5
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59606909"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Omówienie obsługi protokołu WebSocket w usłudze Application Gateway

Usługa Application Gateway zapewnia Natywna obsługa protokołu WebSocket we wszystkich rozmiarów bramy. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket. 

Protokół WebSocket ujednoliceniu [RFC6455](https://tools.ietf.org/html/rfc6455) umożliwia komunikację pełnodupleksową między serwerem klientem za pośrednictwem połączenia protokołu TCP długotrwałe. Ta funkcja umożliwia bardziej interaktywny komunikacji między serwerem sieci web i klienta, który można dwukierunkową, bez konieczności sondowania jako wymagane w implementacji oparty na protokole HTTP. WebSocket niski ma obciążenie w odróżnieniu od protokołu HTTP i można ponownie użyć tego samego połączenia protokołu TCP dla wielu żądań/odpowiedzi skutkuje bardziej wydajne wykorzystanie zasobów. Protokoły WebSocket zostały zaprojektowane do pracy za pośrednictwem tradycyjnych portów HTTP 80 i 443.

Aby kontynuować, przy użyciu standardowych odbiornik HTTP na porcie 80 i 443 do odbierania ruchu protokołu WebSocket. Ruch protokołu WebSocket jest następnie przekierowywane do serwera zaplecza włączone WebSocket określonych w regułach bramy aplikacji przy użyciu odpowiedniej puli zaplecza. Serwer wewnętrznej bazy danych musi odpowiadać na sondy bramy aplikacji, które są opisane w [Przegląd sondy kondycji](application-gateway-probe-overview.md) sekcji. Sondy kondycji bramy aplikacji, są tylko HTTP/HTTPS. Każdy serwer zaplecza musi odpowiadać na sondy HTTP na potrzeby bramy aplikacji do kierowania ruchu protokołu WebSocket z serwerem.

Jest on używany w aplikacjach korzystających z komunikacji szybki, w czasie rzeczywistym, takich jak rozmowy, pulpit nawigacyjny i gier, aplikacji.

## <a name="how-does-websocket-work"></a>Jak działa WebSocket

Aby ustanowić połączenie WebSocket, określonych uzgadnianie oparty na protokole HTTP są wymieniane między klientem a serwerem. W przypadku powodzenia protokołu warstwy aplikacji jest "uaktualniony" z protokołu HTTP do funkcji WebSockets, przy użyciu uprzednio ustanowionym połączeniu TCP. Gdy ten problem wystąpi, HTTP nie jest zsynchronizowana obrazu; dane mogą być wysyłane lub odebranych przy użyciu protokołu WebSocket przez oba punkty końcowe, do czasu zamknięcia połączenia protokołu WebSocket. 

![addcert](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Element konfiguracji odbiornika

Istniejący odbiornik HTTP może służyć do obsługi ruchu sieciowego protokołu WebSocket. Poniżej przedstawiono fragment elementu httpListeners z pliku szablonu próbki. Będziesz potrzebować odbiorników HTTP i HTTPS do obsługi protokołu WebSocket i bezpieczny ruch protokołu WebSocket. Podobnie można użyć portalu lub programu Azure PowerShell do utworzenia bramy aplikacji przy użyciu obiektów nasłuchujących na porcie 80/443 do obsługi ruchu sieciowego protokołu WebSocket.

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

Po zapoznaniu się obsługa protokołu WebSocket, przejdź do [utworzyć bramę aplikacji](quick-create-powershell.md) wprowadzenie WebSocket aplikacji sieci web z obsługą.