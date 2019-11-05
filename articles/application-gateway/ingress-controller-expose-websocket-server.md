---
title: Uwidacznianie serwera WebSocket Application Gateway
description: Ten artykuł zawiera informacje na temat udostępniania serwerowi WebSocket Application Gateway z kontrolerem transferu danych przychodzących dla klastrów AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2019
ms.author: caya
ms.openlocfilehash: 08a12a2de0c4912913058291206772bbcb2c4a44
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513434"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>Uwidacznianie serwera WebSocket Application Gateway

Zgodnie z opisem w dokumentacji Application Gateway v2 — [zapewnia natywną obsługę protokołów WebSocket i http/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic). Należy pamiętać, że dla obu Application Gateway i Kubernetesal — nie istnieje konfigurowalne ustawienie użytkownika umożliwiające wybiórcze Włączanie lub wyłączanie obsługi protokołu WebSocket.

W poniższym rozmieszczeniu Kubernetes YAML przedstawiono konfigurację minimalną używaną do wdrożenia serwera WebSocket, która jest taka sama jak wdrażanie zwykłego serwera sieci Web:
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

Uwzględniając, że wszystkie wymagania wstępne są spełnione i masz Application Gateway kontrolowane przez ruch przychodzący Kubernetes w AKS, wdrożenie powyżej spowoduje, że serwer WebSockets zostanie uwidoczniony na porcie 80 publicznego adresu IP Application Gateway i `ws.contoso.com` domeny.

Następujące polecenie zwinięcie spowoduje przetestowanie wdrożenia serwera WebSocket:
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>Sondy kondycji protokołu WebSocket

Jeśli w danym wdrożeniu nie zdefiniowano jawnie sond kondycji, Application Gateway podejmie próbę pobrania HTTP w punkcie końcowym serwera WebSocket.
W zależności od implementacji serwera (w[tym miejscu](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)) mogą być wymagane nagłówki specyficzne dla protokołu WebSocket (`Sec-Websocket-Version` dla wystąpienia).
Ponieważ Application Gateway nie dodaje nagłówków protokołu WebSocket, prawdopodobnie zostanie `400 Bad Request`odpowiedź z sondy kondycji Application Gateway z serwera WebSocket.
W efekcie Application Gateway oznacza to, że Twoje wartości są oznaczone jako w złej kondycji, co w efekcie spowoduje `502 Bad Gateway` dla użytkowników serwera WebSocket.
Aby tego uniknąć, może być konieczne dodanie procedury obsługi HTTP GET na potrzeby kontroli kondycji na serwerze (`/health` dla wystąpienia, które zwraca `200 OK`).
