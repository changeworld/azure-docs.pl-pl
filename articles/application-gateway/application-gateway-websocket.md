---
title: Obsługa websocket w usłudze Azure Application Gateway
description: Usługa Application Gateway zapewnia natywną obsługę protokołu WebSocket w bramach każdego rozmiaru. Nie ma ustawień konfigurowanych przez użytkownika.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: baa02c4d946a121f26f421af99835ae2bea18847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130333"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Omówienie obsługi websocket w bramie aplikacji

Usługa Application Gateway zapewnia natywną obsługę protokołu WebSocket w bramach każdego rozmiaru. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket. 

Protokół WebSocket znormalizowany w [specyfikacji RFC6455](https://tools.ietf.org/html/rfc6455) umożliwia pełną komunikację dwustronną między serwerem a klientem za pośrednictwem długotrwałego połączenia TCP. Ta funkcja umożliwia bardziej interaktywną komunikację między serwerem sieci web a klientem, która może być dwukierunkowa bez konieczności sondowania zgodnie z wymaganiami implementacji opartych na protokreliach HTTP. WebSocket ma niskie obciążenie w przeciwieństwie do PROTOKOŁU HTTP i może ponownie użyć tego samego połączenia TCP dla wielu żądań/odpowiedzi, co powoduje bardziej efektywne wykorzystanie zasobów. Protokoły WebSocket są przeznaczone do pracy nad tradycyjnymi portami HTTP 80 i 443.

Możesz nadal używać standardowego odbiornika HTTP na porcie 80 lub 443, aby odbierać ruch WebSocket. Ruch WebSocket jest następnie kierowany do serwera wewnętrznej bazy danych z włączoną funkcją WebSocket przy użyciu odpowiedniej puli wewnętrznej bazy danych określonej w regułach bramy aplikacji. Serwer wewnętrznej bazy danych musi odpowiadać na sondy bramy aplikacji, które są opisane w sekcji [przegląd sondy kondycji.](application-gateway-probe-overview.md) Sondy kondycji bramy aplikacji są tylko HTTP/HTTPS. Każdy serwer wewnętrznej bazy danych musi odpowiadać na sondy HTTP dla bramy aplikacji, aby kierować ruch WebSocket do serwera.

Jest używany w aplikacjach, które korzystają z szybkiej komunikacji w czasie rzeczywistym, takich jak czat, pulpit nawigacyjny i aplikacje do gier.

## <a name="how-does-websocket-work"></a>Jak działa WebSocket

Aby ustanowić połączenie WebSocket, między klientem a serwerem wymieniane jest określone uzgadnianie oparte na protokonasie HTTP. W przypadku powodzenia protokół warstwy aplikacji jest "uaktualniany" z protokołu HTTP do websockets przy użyciu wcześniej ustanowionego połączenia TCP. Gdy to nastąpi, HTTP jest całkowicie poza obrazem; dane mogą być wysyłane lub odbierane za pomocą protokołu WebSocket przez oba punkty końcowe, dopóki połączenie WebSocket nie zostanie zamknięte. 

![Websocket](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Element konfiguracji odbiornika

Istniejący odbiornik HTTP może służyć do obsługi ruchu WebSocket. Poniżej znajduje się fragment elementu httpListeners z przykładowego pliku szablonu. Do obsługi websocket i bezpiecznego ruchu WebSocket potrzebne są zarówno odbiorniki HTTP, jak i HTTPS. Podobnie można użyć portalu lub programu Azure PowerShell do utworzenia bramy aplikacji z odbiornikami na porcie 80/443 do obsługi ruchu WebSocket.

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

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Konfiguracja zaplecza Usługi BackendAddressPool, BackendHttpSetting i Konfiguracja reguł routingu

BackendAddressPool służy do definiowania puli wewnętrznej bazy danych z serwerami obsługującymi websocket. Wewnętrznej bazy danychHttpSetting jest zdefiniowany za pomocą portu wewnętrznej bazy danych 80 i 443. Wartość limitu czasu żądania w ustawieniach HTTP dotyczy również sesji WebSocket. Nie ma żadnych zmian wymaganych w reguły routingu, który jest używany do powiązania odpowiedniego odbiornika do odpowiedniej puli adresów wewnętrznej bazy danych. 

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

## <a name="websocket-enabled-backend"></a>WebSocket włączone zaplecze

Zaplecze musi mieć serwer sieci Web HTTP/HTTPS uruchomiony na skonfigurowanym porcie (zwykle 80/443), aby websocket działał. To wymaganie jest, ponieważ protokół WebSocket wymaga początkowego uzgadniania być HTTP z uaktualnieniem do protokołu WebSocket jako pole nagłówka. Oto przykład nagłówka:

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

Innym powodem jest to, że sonda kondycji wewnętrznej bazy danych bramy aplikacji obsługuje tylko protokoły HTTP i HTTPS. Jeśli serwer wewnętrznej bazy danych nie odpowiada na sondy HTTP lub HTTPS, zostanie on wyjęty z puli wewnętrznej bazy danych.

## <a name="next-steps"></a>Następne kroki

Po zapoznaniu się z obsługą WebSocket, przejdź do [tworzenia bramy aplikacji,](quick-create-powershell.md) aby rozpocząć pracę z aplikacją sieci web z włączoną funkcją WebSocket.