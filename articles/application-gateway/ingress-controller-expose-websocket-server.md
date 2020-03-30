---
title: Uwidacznianie serwera WebSocket na bramę aplikacji
description: Ten artykuł zawiera informacje dotyczące sposobu udostępnienia serwera WebSocket do bramy aplikacji z kontrolerem transferu danych przychodzących dla klastrów AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297836"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Uwidacznianie serwera WebSocket na bramę aplikacji

Jak opisano w dokumentacji bramy aplikacji w wersji 2 — [zapewnia natywną obsługę protokołów WebSocket i HTTP/2.](features.md#websocket-and-http2-traffic) Należy pamiętać, że zarówno dla bramy aplikacji, jak i usługi Kubernetes Ingress — nie ma ustawienia konfigurowanego przez użytkownika, aby selektywnie włączać lub wyłączać obsługę WebSocket.

Yaml wdrożenia kubernetes poniżej pokazuje minimalną konfigurację używaną do wdrażania serwera WebSocket, który jest taki sam jak wdrażanie zwykłego serwera sieci web:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

Biorąc pod uwagę, że wszystkie wymagania wstępne są spełnione i masz bramę aplikacji kontrolowaną przez usługi Przychodzące Kubernetes w systemie AKS, powyższe wdrożenie spowodowałoby serwer WebSockets narażone na porcie 80 publicznego adresu IP bramy aplikacji i `ws.contoso.com` domeny.

Następujące polecenie cURL przetestuje wdrożenie serwera WebSocket:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Sondy kondycji WebSocket

Jeśli wdrożenie nie jawnie zdefiniować sondy kondycji, brama aplikacji podejmie próbę HTTP GET w punkcie końcowym serwera WebSocket.
W zależności od implementacji serwera[(oto jeden kochamy)](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)WebSocket`Sec-Websocket-Version` konkretne nagłówki mogą być wymagane (na przykład).
Ponieważ brama aplikacji nie dodaje nagłówków WebSocket, odpowiedź sondy kondycji bramy aplikacji z `400 Bad Request`serwera WebSocket będzie najprawdopodobniej .
W rezultacie brama aplikacji oznaczy zasobników jako złej kondycji, co ostatecznie spowoduje `502 Bad Gateway` dla konsumentów serwera WebSocket.
Aby tego uniknąć, może być konieczne dodanie do serwera programu`/health` obsługi HTTP `200 OK`GET w celu sprawdzenia kondycji ( na przykład zwraca ).
