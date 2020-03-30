---
title: Adnotacje kontrolera transferu danych przychodzących bramy aplikacji
description: Ten artykuł zawiera dokumentację dotyczącą adnotacji specyficznych dla kontrolera transferu danych przychodzących bramy aplikacji.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335820"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Adnotacje dla kontrolera transferu danych przychodzących bramy aplikacji 

## <a name="introductions"></a>Wprowadzenia

Zasób transferu przychodzącego Kubernetes można opisać za pomocą dowolnych par kluczy/wartości. AGIC polega na adnotacje do programowania funkcji bramy aplikacji, które nie są konfigurowalne za pośrednictwem pliku YAML przychodzących. Adnotacje przychodzących są stosowane do wszystkich ustawień HTTP, pul zaplecza i odbiorników pochodzących z zasobu transferu danych przychodzących.

## <a name="list-of-supported-annotations"></a>Lista obsługiwanych adnotacji

Aby zasób przynikanie był obserwowany przez AGIC, `kubernetes.io/ingress.class: azure/application-gateway`musi być oznaczony **adnotacją** . Tylko wtedy AGIC będzie współpracować z danym zasobem Ingress.

| Klucz adnotacji | Typ wartości | Wartość domyślna | Dozwolone wartości
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`(sekundy) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`(sekundy) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Prefiks ścieżki wewnętrznej bazy danych

Ta adnotacja umożliwia ścieżkę wewnętrznej bazy danych określoną w zasobie transferu danych przychodzących, która ma zostać przepisana z prefiksem określonym w tej adnotacji. Dzięki temu użytkownicy mogą udostępniać usługi, których punkty końcowe są inne niż nazwy punktów końcowych używane do udostępnienia usługi w zasobie transferu przychodzącego.

### <a name="usage"></a>Sposób użycia

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Przykład

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
W powyższym przykładzie zdefiniowaliśmy zasób `go-server-ingress-bkprefix` transferu danych `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` przychodzących o nazwie z adnotacją . Adnotacja nakazuje bramie aplikacji utworzenie ustawienia HTTP, które będzie miało zastąpienie `/hello` `/test/`prefiksu ścieżki dla ścieżki do .

> [!NOTE] 
> W powyższym przykładzie mamy tylko jedną regułę zdefiniowaną. Jednak adnotacje mają zastosowanie do całego zasobu transferu danych przychodzących, więc jeśli użytkownik zdefiniował wiele reguł, prefiks ścieżki wewnętrznej bazy danych zostanie skonfigurowany dla każdej z określonych ścieżek. W związku z tym jeśli użytkownik chce różnych reguł z różnych prefiksów ścieżki (nawet dla tej samej usługi) będą musieli zdefiniować różne zasoby transferu danych przychodzących.

## <a name="tls-redirect"></a>Przekierowanie TLS

Bramę aplikacji [można skonfigurować tak,](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) aby automatycznie przekierowywała adresy URL HTTP do swoich odpowiedników https. Gdy ta adnotacja jest obecna, a TLS jest poprawnie skonfigurowany, kontroler transferu danych przychodzących Kubernetes utworzy [regułę routingu z konfiguracją przekierowania](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) i zastosuje zmiany do bramy aplikacji. Utworzonym przekierowaniem `301 Moved Permanently`będzie HTTP .

### <a name="usage"></a>Sposób użycia

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Przykład

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Opróżnianie połączenia

`connection-draining`: Ta adnotacja umożliwia użytkownikom określenie, czy włączyć opróżnianie połączenia.
`connection-draining-timeout`: Ta adnotacja umożliwia użytkownikom określenie limitu czasu, po którym brama aplikacji zakończy żądania do punktu końcowego wysysania wewnętrznej bazy danych.

### <a name="usage"></a>Sposób użycia

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Przykład

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Koligacja oparta na plikach cookie

Ta adnotacja pozwala określić, czy włączyć koligacji opartej na plikach cookie.

### <a name="usage"></a>Sposób użycia

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Przykład

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Limit czasu żądania

Ta adnotacja umożliwia określenie limitu czasu żądania w sekundach, po którym brama aplikacji zakończy się niepowodzeniem żądania, jeśli odpowiedź nie zostanie odebrana.

### <a name="usage"></a>Sposób użycia

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Przykład

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Korzystanie z prywatnego adresu IP

Ta adnotacja pozwala nam określić, czy udostępnić ten punkt końcowy na prywatnej własności IP bramy aplikacji.

> [!NOTE]
> * Brama aplikacji nie obsługuje wielu adresów IP na tym samym porcie (przykład: 80/443). Przychodzących z adnotacji `appgw.ingress.kubernetes.io/use-private-ip: "false"` i `appgw.ingress.kubernetes.io/use-private-ip: "true"` `HTTP` inny z on spowoduje AGIC nie po awarii w aktualizacji bramy aplikacji.
> * W przypadku bramy aplikacji, która nie ma prywatnego `appgw.ingress.kubernetes.io/use-private-ip: "true"` adresu IP, przychodzące dane z zostaną zignorowane. Zostanie to odzwierciedlone w dziennikach kontrolera i `NoPrivateIP` zdarzeniach transferu danych przychodzących dla osób przychodzących z ostrzeżeniem.


### <a name="usage"></a>Sposób użycia
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Przykład
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Protokół wewnętrznej bazy danych

Ta adnotacja pozwala nam określić protokół, który brama aplikacji powinna używać podczas rozmowy z zasobnikami. Obsługiwane protokoły: `http`,`https`

> [!NOTE]
> * Certyfikaty z podpisem własnym są obecnie obsługiwane przez bramę aplikacji, obecnie usługa AGIC obsługuje tylko wtedy, `https` gdy zasobniki używają certyfikatu podpisanego przez dobrze znany urząd certyfikacji.
> * Upewnij się, że nie używasz portu 80 z https i portu 443 z HTTP na zasobnikach.

### <a name="usage"></a>Sposób użycia
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Przykład
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```