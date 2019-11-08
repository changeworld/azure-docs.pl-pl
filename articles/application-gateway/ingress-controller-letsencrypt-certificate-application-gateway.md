---
title: Używanie certyfikatów LetsEncrypt.org z Application Gateway
description: Ten artykuł zawiera informacje na temat uzyskiwania certyfikatu z usługi LetsEncrypt.org i używania go na Application Gateway klastrach AKS.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2e91a888d0dc98a4f94b956e15336d75291f733e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795916"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Używanie certyfikatów z LetsEncrypt.org na Application Gateway dla klastrów AKS

Ta sekcja służy do konfigurowania AKS do korzystania z [LetsEncrypt.org](https://letsencrypt.org/) i automatycznego uzyskiwania certyfikatu TLS/SSL dla domeny. Certyfikat zostanie zainstalowany na Application Gateway, co spowoduje zakończenie protokołu SSL/TLS dla klastra AKS. W opisanej tutaj konfiguracji jest używany dodatek Kubernetes [Menedżera certyfikatów](https://github.com/jetstack/cert-manager) , który automatyzuje tworzenie certyfikatów i zarządzanie nimi.

Wykonaj poniższe kroki, aby zainstalować [Menedżera certyfikatów](https://docs.cert-manager.io) w istniejącym klastrze AKS.

1. Wykres Helm

    Uruchom następujący skrypt, aby zainstalować `cert-manager` wykres Helm. Spowoduje to:

    - Utwórz nową przestrzeń nazw `cert-manager` w AKS
    - Utwórz następujący CRDs: Certificate, Challenge, ClusterIssuer, Issuer, Order
    - Instalowanie wykresu Menedżera certyfikatów (z [docs.CERT-Manager.IO)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

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

2. Zasób ClusterIssuer

    Utwórz zasób `ClusterIssuer`. Jest ona wymagana przez `cert-manager` do reprezentowania `Lets Encrypt` urząd certyfikacji, w którym zostaną uzyskane podpisane certyfikaty.

    Przy użyciu zasobu `ClusterIssuer` niezwiązanego z przestrzenią nazw Menedżer certyfikatów będzie wystawiał certyfikaty, które mogą być używane z wielu przestrzeni nazw. `Let’s Encrypt` korzysta z protokołu ACME do sprawdzenia, czy podaną nazwę domeny i wystawiasz certyfikat. Więcej szczegółowych informacji na temat konfigurowania [`ClusterIssuer` właściwości.](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html) `ClusterIssuer` poinstruuje `cert-manager`, aby wystawiać certyfikaty przy użyciu środowiska przejściowego `Lets Encrypt` używanego do testowania (certyfikat główny nieobecny w sklepach/magazynów zaufania klienta).

    Domyślny typ wyzwania w YAML poniżej jest `http01`. Inne wyzwania są udokumentowane na [letsencrypt.org — typy wyzwania](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Zaktualizuj `<YOUR.EMAIL@ADDRESS>` w YAML poniżej

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

3. Wdróż aplikację

    Utwórz zasób transferu danych przychodzących, aby udostępnić aplikację `guestbook` przy użyciu Application Gateway z opcją szyfrowania certyfikatu.

    Upewnij się, że Application Gateway ma publiczną konfigurację adresu IP frontonu z nazwą DNS (przy użyciu domyślnej domeny `azure.com` lub zainicjuj usługę `Azure DNS Zone` i przypisz własną domenę niestandardową).
    Zanotuj `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`adnotacji, która informuje Menedżera certyfikatów, aby przetworzyć oznakowany zasób transferu danych przychodzących.

    > [!IMPORTANT] 
    > Zaktualizuj `<PLACEHOLDERS.COM>` w YAML poniżej z własną domeną (lub Application Gateway, na przykład "kh-aks-ingress.westeurope.cloudapp.azure.com")

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

    Po kilku sekundach można uzyskać dostęp do usługi `guestbook` za pośrednictwem adresu URL HTTPS Application Gateway przy użyciu automatycznie wystawionego certyfikatu `Lets Encrypt` **przemieszczania** .
    Przeglądarka może ostrzec użytkownika o nieprawidłowym urzędzie certyfikacji. Certyfikat przemieszczania jest wystawiony przez `CN=Fake LE Intermediate X1`. Wskazuje to, że system działał zgodnie z oczekiwaniami i jest gotowy do certyfikatu produkcyjnego.

4. Certyfikat produkcyjny po pomyślnym skonfigurowaniu certyfikatu przemieszczania można przełączyć się na produkcyjny serwer xyz:
    1. Zastąp adnotację przemieszczania w zasobie transferu danych przychodzących przy użyciu: `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Usuń istniejące `ClusterIssuer` przemieszczania utworzone w poprzednim kroku i Utwórz nowe, zastępując serwer XYZ z ClusterIssuer YAML powyżej z `https://acme-v02.api.letsencrypt.org/directory`

5. Wygaśnięcie i odnowienie certyfikatu przed wygaśnięciem `Lets Encrypt` certyfikat `cert-manager` automatycznie zaktualizuje certyfikat w magazynie Kubernetes Secret. W tym momencie Application Gateway kontroler transferu danych przychodzących będzie stosował zaktualizowany klucz tajny, do którego odwołuje się zasób transferu danych przychodzących, za pomocą którego można skonfigurować Application Gateway.
