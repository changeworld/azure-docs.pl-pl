---
title: Udostępnianie usługi AKS za pośrednictwem protokołu HTTP lub HTTPS przy użyciu bramy aplikacji
description: Ten artykuł zawiera informacje dotyczące sposobu udostępnienia usługi AKS za pośrednictwem protokołu HTTP lub HTTPS przy użyciu bramy aplikacji.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c664141a8c89ccbdf37bd3f9a19cfa659982a47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795571"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Udostępnianie usługi AKS za pośrednictwem protokołu HTTP lub HTTPS przy użyciu bramy aplikacji 

Te samouczki pomagają zilustrować użycie [zasobów transferu danych przychodzących usługi Kubernetes w](https://kubernetes.io/docs/concepts/services-networking/ingress/) celu udostępnienia przykładowej usługi Kubernetes za pośrednictwem bramy aplikacji platformy [Azure](https://azure.microsoft.com/services/application-gateway/) za pośrednictwem protokołu HTTP lub HTTPS.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstalowany `ingress-azure` wykres sterów.
  - [**Wdrażanie greenfield:**](ingress-controller-install-new.md)Jeśli zaczynasz od zera, zapoznaj się z tymi instrukcjami instalacji, w których opisano kroki wdrażania klastra AKS z bramą aplikacji i instalowania kontrolera transferu danych przychodzących bramy aplikacji w klastrze AKS.
  - [**Wdrożenie brownfield:**](ingress-controller-install-existing.md)Jeśli masz istniejący klaster AKS i bramę aplikacji, zapoznaj się z tymi instrukcjami, aby zainstalować kontroler transferu danych przychodzących bramy aplikacji w klastrze AKS.
- Jeśli chcesz używać protokołu HTTPS w tej aplikacji, potrzebujesz certyfikatu x509 i jego klucza prywatnego.

## <a name="deploy-guestbook-application"></a>Wdrażanie `guestbook` aplikacji

Aplikacja księgi gości jest kanoniczna aplikacja Kubernetes, która składa się z wewnętrznej bazy danych interfejsu użytkownika sieci Web, wewnętrznej bazy danych i redis. Domyślnie `guestbook` udostępnia swoją aplikację za pośrednictwem `frontend` usługi `80`o nazwie na porcie . Bez zasobu przychodzącego Usługi Kubernetes usługa nie jest dostępna spoza klastra AKS. Będziemy używać aplikacji i konfiguracji zasobów przychodzących, aby uzyskać dostęp do aplikacji za pośrednictwem protokołu HTTP i HTTPS.

Postępuj zgodnie z poniższymi instrukcjami, aby wdrożyć aplikację księgi gości.

1. Pobierz `guestbook-all-in-one.yaml` [stąd](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)
1. Wdrażanie `guestbook-all-in-one.yaml` w klastrze AKS przez uruchomienie

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

Teraz `guestbook` aplikacja została wdrożona.

## <a name="expose-services-over-http"></a>Udostępnianie usług za pośrednictwem protokołu HTTP

Aby udostępnić aplikację księgi gości, będziemy używać następującego zasobu transferu przychodzącego:

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

Ten ruch przychodzący `frontend` udostępni usługę `guestbook-all-in-one` wdrożenia jako domyślną wywiązywęść wewnętrznej bramy aplikacji.

Zapisz powyższy zasób `ing-guestbook.yaml`przychodzących jako .

1. Wdrażanie `ing-guestbook.yaml` przez uruchomienie:

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. Sprawdź dziennik kontrolera transferu danych przychodzących pod kątem stanu wdrożenia.

Teraz `guestbook` aplikacja powinna być dostępna. Można to sprawdzić, odwiedzając adres publiczny bramy aplikacji.

## <a name="expose-services-over-https"></a>Udostępnianie usług za pośrednictwem protokołu HTTPS

### <a name="without-specified-hostname"></a>Bez określonej nazwy hosta

Bez określania nazwy hosta usługa księgi gości będzie dostępna na wszystkich nazwach hosta wskazujących bramę aplikacji.

1. Przed wdrożeniem transferu przychodzącego należy utworzyć klucz tajny kubernetes do obsługi certyfikatu i klucza prywatnego. Klucz tajny kubernetes można utworzyć, uruchamiając

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. Zdefiniuj następujący ruch przychodzący. W przystawce określ nazwę klucza `secretName` tajnego w sekcji.

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
    > Zastąp `<guestbook-secret-name>` w powyższym zasobie transferu przychodzącego nazwę klucza tajnego. Zasób przychodzących przechowuj powyższy `ing-guestbook-tls.yaml`przystawkę w nazwie pliku .

1. Wdrażanie ing-guestbook-tls.yaml przez uruchomienie

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. Sprawdź dziennik kontrolera transferu danych przychodzących pod kątem stanu wdrożenia.

Teraz `guestbook` aplikacja będzie dostępna zarówno na HTTP, jak i HTTPS.

### <a name="with-specified-hostname"></a>Z określoną nazwa hosta

Można również określić nazwę hosta na przychodzących w celu multipleksu konfiguracji I usług TLS.
Określając nazwę hosta, usługa księgi gości będzie dostępna tylko na określonym hoście.

1. Zdefiniuj następujący ruch przychodzący.
    W przystawce określ nazwę klucza `secretName` tajnego w sekcji i `hosts` odpowiednio zastąp nazwę hosta w sekcji.

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

1. Wdrażanie `ing-guestbook-tls-sni.yaml` przez uruchomienie

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. Sprawdź dziennik kontrolera transferu danych przychodzących pod kątem stanu wdrożenia.

Teraz `guestbook` aplikacja będzie dostępna zarówno na HTTP, jak i`<guestbook.contoso.com>` HTTPS tylko na określonym hoście (w tym przykładzie).

## <a name="integrate-with-other-services"></a>Integracja z innymi usługami

Następujący ruch przychodzący umożliwia dodawanie dodatkowych ścieżek do tego transferu przychodzącego i przekierowywanie tych ścieżek do innych usług:

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
