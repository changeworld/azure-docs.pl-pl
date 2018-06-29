---
title: Skonfiguruj transfer danych przychodzących z klastrem usługi Kubernetes Azure (AKS)
description: Instalowanie i konfigurowanie kontrolera wejściowych NGINX w klastrze usługi Kubernetes Azure (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9bee80ebbaf0568706428d673d584819b1daa143
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096992"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>Ruch przychodzący HTTPS z usługi Azure Kubernetes (AKS)

Kontroler transfer danych przychodzących jest oprogramowaniem udostępniający zwrotnego serwera proxy, routingu ruchu można konfigurować i zakończenie TLS Kubernetes usług. Kubernetes wejściowych zasobów są używane do konfigurowania tras dla poszczególnych usług Kubernetes i transfer danych przychodzących reguł. Przy użyciu kontrolera wejściowych i transfer danych przychodzących reguł, pojedynczy adres zewnętrzny można kierować ruchem do wielu usług w klastrze Kubernetes.

Ten dokument przeprowadzi Cię przez wdrożenie próbki [kontrolera wejściowych NGINX] [ nginx-ingress] w klastrze usługi Kubernetes Azure (AKS). Ponadto [Menedżera certyfikatów] [ cert-manager] projektu służy do automatycznego generowania i skonfigurować [umożliwia szyfrowanie] [ lets-encrypt] certyfikatów. Na koniec kilka aplikacji są uruchamiane w klastrze AKS, z których każdy jest dostępny za pośrednictwem jednego adresu.

## <a name="install-an-ingress-controller"></a>Instalowanie kontrolera wejściowych

Należy zainstalować kontroler wejściowych NGINX Helm. Zobacz kontrolera wejściowych NGINX [dokumentacji] [ nginx-ingress] wdrożenia szczegółowych informacji.

Instalacja kontrolera w `kube-system` przestrzeni nazw, to ustawienie można zmodyfikować do przestrzeni nazw wybranych przez użytkownika. Jeśli klaster AKS nie jest włączone RBAC, Dodaj `--set rbac.create=false` do polecenia. Aby uzyskać więcej informacji, zobacz [wykresu wejściowych nginx][nginx-ingress].

```bash
helm install stable/nginx-ingress --namespace kube-system
```

Podczas instalacji Azure publiczny adres IP jest tworzona dla kontrolera wejściowych. Aby uzyskać publicznego adresu IP, użyj polecenia kubectl get usługi. Może upłynąć trochę czasu, adres IP ma być przypisany do usługi.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Ponieważ żadne reguły wejściowych utworzono po przejściu do publicznego adresu IP, są kierowane do strony NGINX wejściowych kontrolerów domyślne 404.

![Domyślne NGINX wewnętrznej bazy danych](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Konfigurowanie nazwy DNS

Ponieważ są używane certyfikaty protokołu HTTPS, należy skonfigurować nazwy FQDN dla adresu IP kontrolerów wejściowych. Na przykład nazwa FQDN Azure jest tworzony z wiersza polecenia platformy Azure. Skrypt aktualizacji o adresie IP kontrolera wejściowych i nazwy, który chcesz użyć w nazwie FQDN.

```bash
#!/bin/bash

# Public IP address
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with dns name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Kontroler wejściowych powinno być teraz dostępne za pośrednictwem nazwę FQDN.

## <a name="install-cert-manager"></a>Zainstaluj Menedżera certyfikatów

Kontroler wejściowych NGINX obsługuje zakończenia TLS. Gdy istnieje kilka sposobów, aby pobrać i skonfigurować certyfikaty dla protokołu HTTPS, w tym dokumencie przedstawiono przy użyciu [Menedżera certyfikatów][cert-manager], która umożliwia automatyczne [umożliwia szyfrowanie] [ lets-encrypt] certyfikatu generacji oraz funkcji zarządzania.

Aby zainstalować kontroler Menedżera certyfikatów, następujące polecenie Helm instalacji.

```bash
helm install stable/cert-manager --set ingressShim.defaultIssuerName=letsencrypt-prod --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Jeśli klaster nie jest włączone RBAC, użyj tego polecenia.

```bash
helm install stable/cert-manager \
  --set ingressShim.defaultIssuerName=letsencrypt-prod \
  --set ingressShim.defaultIssuerKind=ClusterIssuer \
  --set rbac.create=false \
  --set serviceAccount.create=false
```

Aby uzyskać więcej informacji o konfiguracji Menedżera certyfikatów, zobacz [projektu Menedżera certyfikatów][cert-manager].

## <a name="create-ca-cluster-issuer"></a>Utwórz wystawcy klastra urzędu certyfikacji

Przed certyfikaty mogą być wystawiane, Menedżer certyfikatów wymaga [wystawcy] [ cert-manager-issuer] lub [ClusterIssuer] [ cert-manager-cluster-issuer] zasobów. Zasoby są takie same jak w funkcji jednak `Issuer` działa w jednej przestrzeni nazw gdzie `ClusterIssuer` działa we wszystkich obszarów nazw. Aby uzyskać więcej informacji, zobacz [wystawcy certyfikatu Menedżera] [ cert-manager-issuer] dokumentacji.

Utwórz wystawcy klastra przy użyciu następujących manifestu. Zaktualizuj adres e-mail z prawidłowym adresem z Twojej organizacji.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-prod
    http01: {}
```

## <a name="create-certificate-object"></a>Tworzenie obiektu certyfikatu

Następnie można utworzyć zasobu certyfikatu. Zasób certyfikatu definiuje żądany certyfikat X.509. Aby uzyskać więcej informacji zobacz, [certyfikaty Menedżera certyfikatów][cert-manager-certificates].

Utwórz zasób certyfikatu z następujących manifestu.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
```

## <a name="run-application"></a>Uruchamianie aplikacji

W tym momencie kontroler wejściowych i rozwiązania do zarządzania certyfikatu skonfigurowano. Teraz uruchom kilka aplikacji w klastrze AKS.

Na przykład Helm służy do uruchamiania wielu wystąpień aplikacji world hello proste.

Przed uruchomieniem aplikacji, należy dodać repozytorium Helm przykładów dla platformy Azure w systemie deweloperskim.

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Uruchom AKS hello world wykresu za pomocą następującego polecenia:

```bash
helm install azure-samples/aks-helloworld
```

Teraz można zainstalować drugie wystąpienie aplikacji hello world.

Drugie wystąpienie określ nowy tytuł tak, aby aplikacje były różne wizualne. Należy również określić unikatową nazwę usługi. Te konfiguracje są widoczne w poniższym poleceniu.

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Utwórz trasę wejściowych

Obie aplikacje są obecnie uruchomione w klastrze Kubernetes jednak skonfigurowano usługi typu `ClusterIP`. Tak aplikacje nie są dostępne z Internetu. Aby udostępnić, utwórz zasób Kubernetes wejściowych. Zasobów wejściowych konfiguruje reguły, które kierować ruchem do jednej z dwóch aplikacji.

Utwórz nazwę pliku `hello-world-ingress.yaml` i skopiuj następujące yaml programu.

Zwróć uwagę, że ruch do adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/` jest kierowany do usługi o nazwie `aks-helloworld`. Ruch do adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` jest kierowany do `ingress-demo` usługi.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Utwórz zasób transfer danych przychodzących z `kubectl apply` polecenia.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>Przetestuj konfigurację wejściowych

Przeglądaj, aby nazwa FQDN kontrolera wejściowych Kubernetes, powinna zostać wyświetlona aplikacja hello world.

![Przykładowa aplikacja, jeden](media/ingress/app-one.png)

Teraz przejdź do nazwy FQDN kontroler transfer danych przychodzących z `/hello-world-two` ścieżki, powinny pojawić się aplikacji hello world z tytułem niestandardowych.

![Przykładowa aplikacja dwa](media/ingress/app-two.png)

Także zauważyć, że połączenie jest szyfrowane, oraz że jest używany certyfikat wystawiony przez umożliwia szyfrowanie.

![Umożliwia szyfrowanie certyfikatu](media/ingress/certificate.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat oprogramowania zostało to pokazane w tym dokumencie.

- [Helm interfejsu wiersza polecenia][helm-cli]
- [NGINX wejściowych kontrolera][nginx-ingress]
- [Menedżer certyfikatów][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
