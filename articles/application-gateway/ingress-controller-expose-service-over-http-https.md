---
title: Uwidacznianie usługi AKS za pośrednictwem protokołu HTTP lub HTTPS przy użyciu Application Gateway
description: Ten artykuł zawiera informacje dotyczące sposobu uwidaczniania usługi AKS za pośrednictwem protokołu HTTP lub HTTPS przy użyciu Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795571"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Uwidacznianie usługi AKS za pośrednictwem protokołu HTTP lub HTTPS przy użyciu Application Gateway 

Te samouczki ułatwiają zilustrowanie użycia [zasobów Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) w celu udostępnienia przykładowej usługi Kubernetes za [Application Gateway](https://azure.microsoft.com/services/application-gateway/) pośrednictwem protokołu HTTP lub https.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstalowano `ingress-azure` wykresu Helm.
  - [**Wdrożenie Greenfield**](ingress-controller-install-new.md): Jeśli zaczynasz od podstaw, zapoznaj się z tymi instrukcjami dotyczącymi instalacji, które opisują kroki wdrażania klastra AKS z Application Gateway i zainstalowania w klastrze AKS kontrolera usługi Application.
  - [**Wdrożenie brownfield**](ingress-controller-install-existing.md): Jeśli masz istniejący klaster AKS i Application Gateway, zapoznaj się z tymi instrukcjami, aby zainstalować w klastrze AKS kontroler danych wejściowych bramy aplikacji.
- Jeśli chcesz używać protokołu HTTPS w tej aplikacji, będzie potrzebny certyfikat x509 i jego klucz prywatny.

## <a name="deploy-guestbook-application"></a>Wdróż aplikację `guestbook`

Aplikacja księgi Gościa to kanoniczna aplikacja Kubernetes, która składa się z frontonu interfejsu użytkownika sieci Web, zaplecza i bazy danych Redis. Domyślnie `guestbook` uwidacznia swoją aplikację za pomocą usługi z nazwą `frontend` na porcie `80`. Bez Kubernetes zasobów przychodzących usługa nie jest dostępna spoza klastra AKS. W celu uzyskania dostępu do aplikacji za pośrednictwem protokołów HTTP i HTTPS będziemy korzystać z aplikacji i skonfigurować zasoby transferu danych przychodzących.

Postępuj zgodnie z poniższymi instrukcjami, aby wdrożyć aplikację Księgi Gości.

1. Pobierz `guestbook-all-in-one.yaml` z tego [miejsca](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Wdróż `guestbook-all-in-one.yaml` w klastrze AKS, uruchamiając

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Teraz aplikacja `guestbook` została wdrożona.

## <a name="expose-services-over-http"></a>Uwidacznianie usług za pośrednictwem protokołu HTTP

W celu udostępnienia aplikacji księgi gościa będziemy używać następujących zasobów przychodzących:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

Ten ruch przychodzący spowoduje udostępnienie `frontend` usługi `guestbook-all-in-one` wdrożenia jako domyślnego zaplecze Application Gateway.

Zapisz powyżej zasób transferu danych przychodzących jako `ing-guestbook.yaml`.

1. Wdróż `ing-guestbook.yaml`, uruchamiając:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Sprawdź dziennik transferu danych przychodzących na potrzeby stanu wdrożenia.

Teraz aplikacja `guestbook` powinna być dostępna. Możesz to sprawdzić, odwiedzając adres publiczny Application Gateway.

## <a name="expose-services-over-https"></a>Uwidacznianie usług za pośrednictwem protokołu HTTPS

### <a name="without-specified-hostname"></a>Bez określonej nazwy hosta

Bez określenia nazwy hosta usługa księgi gościa będzie dostępna na wszystkich nazwach hostów wskazujących na bramę aplikacji.

1. Przed wdrożeniem transferu danych przychodzących należy utworzyć klucz tajny Kubernetes, aby hostować certyfikat i klucz prywatny. Wpis tajny Kubernetes można utworzyć, uruchamiając

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Zdefiniuj następujące przychodzące. W obszarze transfer danych przychodzących Określ nazwę wpisu tajnego w sekcji `secretName`.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > Zastąp `<guestbook-secret-name>` w powyższym zasobie transferu danych przychodzących nazwą klucza tajnego. Zapisz powyżej zasób transferu danych przychodzących w nazwie pliku `ing-guestbook-tls.yaml`.

1. Wdrożenie w usłudze YAML

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Sprawdź dziennik transferu danych przychodzących na potrzeby stanu wdrożenia.

Teraz aplikacja `guestbook` będzie dostępna zarówno dla protokołu HTTP, jak i HTTPS.

### <a name="with-specified-hostname"></a>Z określoną nazwą hosta

Można również określić nazwę hosta dla ruchu przychodzącego w celu ustawienia konfiguracji i usług TLS.
Określając nazwę hosta, usługa księgi gościa będzie dostępna tylko na określonym hoście.

1. Zdefiniuj następujące przychodzące.
    W obszarze ruch przychodzący Określ nazwę wpisu tajnego w sekcji `secretName` i odpowiednio Zamień nazwę hosta w sekcji `hosts`.

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. Wdróż `ing-guestbook-tls-sni.yaml`, uruchamiając

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Sprawdź dziennik transferu danych przychodzących na potrzeby stanu wdrożenia.

Teraz aplikacja `guestbook` będzie dostępna zarówno dla protokołu HTTP, jak i HTTPS na określonym hoście (`<guestbook.contoso.com>` w tym przykładzie).

## <a name="integrate-with-other-services"></a>Integracja z innymi usługami

Poniższe usługi wejściowe umożliwią dodanie dodatkowych ścieżek do tego ruchu przychodzącego i przekierowanie tych ścieżek do innych usług:

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      rules:
      - http:
          paths:
          - path: </other/*>
            backend:
              serviceName: <other-service>
              servicePort: 80
          - backend:
              serviceName: frontend
              servicePort: 80
    ```
