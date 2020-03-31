---
title: Używanie certyfikatów LetsEncrypt.org z bramą aplikacji
description: Ten artykuł zawiera informacje dotyczące uzyskiwania certyfikatu od LetsEncrypt.org i używania go w bramie aplikacji dla klastrów AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73957986"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Używanie certyfikatów z LetsEncrypt.org w bramie aplikacji dla klastrów AKS

W tej sekcji skonfigurowano system AKS w celu wykorzystania [LetsEncrypt.org](https://letsencrypt.org/) i automatycznego uzyskiwania certyfikatu TLS/SSL dla domeny. Certyfikat zostanie zainstalowany w bramie aplikacji, która będzie wykonywać zakończenie protokołu SSL/TLS dla klastra AKS. Konfiguracja opisana w tym miejscu używa [dodatku](https://github.com/jetstack/cert-manager) Kubernetes menedżera certyfikatu, który automatyzuje tworzenie certyfikatów i zarządzanie nimi.

Wykonaj poniższe czynności, aby zainstalować [menedżera certyfikatów](https://docs.cert-manager.io) w istniejącym klastrze AKS.

1. Wykres steru

    Uruchom następujący skrypt, `cert-manager` aby zainstalować wykres helm. Będzie to:

    - tworzenie nowej `cert-manager` przestrzeni nazw w programie AKS
    - tworzenie następujących CRD: Certyfikat, Wyzwanie, ClusterIssuer, Wystawca, Zamówienie
    - zainstalować wykres menedżera certyfikatu (od [docs.cert-manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. Zasób klastraIsuer

    Tworzenie `ClusterIssuer` zasobu. Jest to wymagane `cert-manager` przez `Lets Encrypt` do reprezentowania urzędu certyfikacji, gdzie podpisane certyfikaty zostaną uzyskane.

    Za pomocą zasobu niepodleganego, `ClusterIssuer` cert-manager wystawi certyfikaty, które mogą być używane z wielu obszarów nazw. `Let’s Encrypt`używa protokołu ACME do sprawdzenia, czy użytkownik kontroluje daną nazwę domeny i wystawia certyfikat. Więcej szczegółów na `ClusterIssuer` temat konfigurowania właściwości [tutaj](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer`zainstruuje `cert-manager` wystawianie `Lets Encrypt` certyfikatów przy użyciu środowiska przejściowego używanego do testowania (certyfikat główny nie jest obecny w magazynach zaufania przeglądarki/klienta).

    Domyślnym typem wyzwania w `http01`poniższym pliku YAML jest . Inne wyzwania są dokumentowane na [letsencrypt.org - Typy wyzwań](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Aktualizacja `<YOUR.EMAIL@ADDRESS>` w YAML poniżej

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. Wdrażanie aplikacji

    Utwórz zasób transferu `guestbook` danych przychodzących, aby udostępnić aplikację przy użyciu bramy aplikacji z certyfikatem Lets Encrypt.

    Upewnij się, że brama aplikacji ma publiczną konfigurację ip `azure.com` frontu z `Azure DNS Zone` nazwą DNS (przy użyciu domeny domyślnej lub aprowizowania usługi i przypisać własną domenę niestandardową).
    Zanotuj `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`adnotację , która nakazuje cert-managerowi przetworzenie oznakowanego zasobu transferu danych przychodzących.

    > [!IMPORTANT] 
    > Aktualizacja `<PLACEHOLDERS.COM>` w YAML poniżej za pomocą własnej domeny (lub bramy aplikacji, na przykład "kh-aks-ingress.westeurope.cloudapp.azure.com")

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Po kilku sekundach można `guestbook` uzyskać dostęp do usługi za pośrednictwem adresu URL HTTPS bramy aplikacji przy użyciu automatycznie wystawionego certyfikatu **przemieszczania.** `Lets Encrypt`
    Twoja przeglądarka może ostrzec cię o nieprawidłowym urzędzie cert. Certyfikat przemieszczania jest `CN=Fake LE Intermediate X1`wystawiany przez program . Jest to wskazanie, że system działał zgodnie z oczekiwaniami i jesteś gotowy do certyfikatu produkcyjnego.

4. Certyfikat produkcji

    Po pomyślnym skonfigurowaniu certyfikatu przemieszczania można przełączyć się na produkcyjny serwer ACME:
    1. Zastąp adnotację przemieszczania zasobu transferu danych przychodzących:`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Usuń istniejącą `ClusterIssuer` tymczasowość utworzoną w poprzednim kroku i utwórz nową, zastępując serwer ACME z powyższego pliku YAML clusterissuera`https://acme-v02.api.letsencrypt.org/directory`

5. Wygaśnięcie i odnawianie certyfikatu

    Przed `Lets Encrypt` wygaśnięciem certyfikatu `cert-manager` zostanie automatycznie zaktualizowany certyfikat w magazynie tajnym usługi Kubernetes. W tym momencie kontroler transferu danych przychodzących bramy aplikacji zastosuje zaktualizowany klucz tajny, do którego odwołuje się zasoby transferu danych przychodzących, których używa do konfigurowania bramy aplikacji.
