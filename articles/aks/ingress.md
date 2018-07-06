---
title: Konfigurowanie transferu danych przychodzących za pomocą klastra Azure Kubernetes Service (AKS)
description: Instalowanie i konfigurowanie serwera NGINX kontrolera danych przychodzących w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/25/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bd223e9eebac495d7336c618b831528505c30959
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857399"
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>Ruch przychodzący protokołu HTTPS, w usłudze Azure Kubernetes Service (AKS)

Kontroler danych przychodzących jest to oprogramowanie, które zapewnia zwrotny serwer proxy, routing ruchu można konfigurować i zakończenie protokołu TLS dla usługi Kubernetes. Zasoby danych przychodzących rozwiązania Kubernetes są używane do konfigurowania reguł ruchu przychodzącego i trasy dla poszczególnych usług Kubernetes. Korzystanie z reguł ruchu przychodzącego i kontroler danych przychodzących, pojedynczy adres zewnętrzny może służyć do kierowania ruchu do wielu usług w klastrze Kubernetes.

W tym dokumencie przedstawiono przykładowe wdrożenie z [kontrolera danych przychodzących NGINX] [ nginx-ingress] w klastrze usługi Azure Kubernetes Service (AKS). Ponadto [Menedżera certyfikatów] [ cert-manager] projekt jest używana do automatycznego generowania i skonfigurować [umożliwia szyfrowanie] [ lets-encrypt] certyfikatów. Na koniec kilka aplikacji są uruchamiane w klastrze AKS, z których każdy jest dostępny jeden adres.

## <a name="install-an-ingress-controller"></a>Zainstaluj kontroler danych przychodzących

Użyj narzędzia Helm, aby zainstalować kontroler danych przychodzących NGINX. Zobacz kontrolera danych przychodzących NGINX [dokumentacji] [ nginx-ingress] wdrożenia są szczegółowo informacji.

W tym przykładzie instaluje kontrolera w `kube-system` przestrzeni nazw, to ustawienie można zmodyfikować do wybranej przestrzeni nazw. Jeśli klaster AKS nie jest włączone RBAC, Dodaj `--set rbac.create=false` do polecenia. Aby uzyskać więcej informacji, zobacz [wykresu ruch przychodzący serwera nginx][nginx-ingress].

```bash
helm install stable/nginx-ingress --namespace kube-system
```

Podczas instalacji Azure publiczny adres IP jest tworzona dla kontrolera danych przychodzących. Aby uzyskać publiczny adres IP, użyj polecenia kubectl get usługi. Może upłynąć trochę czasu, adres IP, który można przypisać do usługi.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Ponieważ zostały utworzone żadne reguły ruchu przychodzącego, po przejściu do publicznego adresu IP, są kierowane do transferu danych przychodzących kontrolerów domyślną stronę serwera NGINX 404.

![Domyślna serwera NGINX wewnętrznej bazy danych](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Konfigurowanie nazwy DNS

Ponieważ są używane certyfikaty protokołu HTTPS, należy skonfigurować nazwy FQDN dla adresu IP kontrolerów transferu danych przychodzących. Na przykład nazwy FQDN platformy Azure jest tworzony przy użyciu wiersza polecenia platformy Azure. Zaktualizuj skrypt przy użyciu adresu IP kontrolera danych przychodzących i nazwy, którego chcesz użyć w pełni kwalifikowaną nazwę domeny.

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

Powinno być teraz dostępne za pośrednictwem nazwa FQDN kontrolera danych przychodzących.

## <a name="install-cert-manager"></a>Zainstaluj Menedżera certyfikatów

Kontroler danych przychodzących NGINX obsługuje kończenie żądań protokołu TLS. Gdy istnieje kilka sposobów, aby pobrać i skonfigurować certyfikaty dla protokołu HTTPS, w tym dokumencie przedstawiono przy użyciu [Menedżera certyfikatów][cert-manager], która umożliwia automatyczne [umożliwia szyfrowanie] [ lets-encrypt] certyfikatu generacji oraz funkcji zarządzania.

Aby zainstalować kontroler Menedżera certyfikatów, użyj następującego polecenia instalacji narzędzia Helm.

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

Aby uzyskać więcej informacji na temat konfiguracji Menedżera certyfikatów, zobacz [projektu Menedżera certyfikatów][cert-manager].

## <a name="create-ca-cluster-issuer"></a>Tworzenie urzędu certyfikacji, wystawca klastra

Zanim certyfikaty mogą być wystawiane, Menedżer certyfikatów wymaga [wystawcy] [ cert-manager-issuer] lub [ClusterIssuer] [ cert-manager-cluster-issuer] zasobów. Zasoby są identyczne w działaniu, jednak `Issuer` działa w jednej przestrzeni nazw gdzie `ClusterIssuer` działa we wszystkich przestrzeni nazw. Aby uzyskać więcej informacji, zobacz [wystawcy certyfikatu Menedżera] [ cert-manager-issuer] dokumentacji.

Utwórz wystawcę klastra, przy użyciu następujących manifestu. Przy użyciu prawidłowego adresu z Twojej organizacji, należy zaktualizować adres e-mail.

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

Następnie można utworzyć zasobu certyfikatu. Zasób certyfikatu definiuje żądany certyfikat X.509. Aby uzyskać więcej informacji znajduje się pozycja [certyfikaty Menedżera certyfikatów][cert-manager-certificates].

Utwórz zasób certyfikatu za pomocą następujących manifestu.

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

W tym momencie kontroler danych przychodzących i rozwiązania do zarządzania certyfikatu skonfigurowano. Teraz można uruchomić kilka aplikacji w klastrze AKS.

W tym przykładzie Helm służy do uruchamiania wielu wystąpień aplikacji proste hello world.

Przed uruchomieniem aplikacji, Dodaj repozytorium narzędzia Helm przykładów dla platformy Azure w systemie deweloperskim.

```bash
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Uruchamianie usługi AKS hello world wykresu za pomocą następującego polecenia:

```bash
helm install azure-samples/aks-helloworld
```

Teraz zainstalować drugie wystąpienie aplikacja hello world.

Drugie wystąpienie należy określić nowy tytuł, aby wizualnie różniące się od dwóch aplikacji. Należy również określić unikatową nazwę usługi. Te konfiguracje są widoczne w następującym poleceniu.

```bash
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Utwórz trasę ruchu przychodzącego

Obie aplikacje są teraz uruchomione w klastrze Kubernetes, jednak skonfigurowano usługi typu `ClusterIP`. Jako takie aplikacje nie są dostępne z Internetu. Aby stały się dostępne, utwórz zasób transferu danych przychodzących rozwiązania Kubernetes. Zasób ruch przychodzący konfiguruje zasady, które kierują ruch do jednego z dwóch aplikacji.

Utwórz plik o nazwie `hello-world-ingress.yaml` i skopiuj do poniższego kodu YAML.

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

Utwórz zasób ruch przychodzący z `kubectl apply` polecenia.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>Testowanie konfiguracji transferu danych przychodzących

Przeglądaj, aby nazwa FQDN kontrolera danych przychodzących rozwiązania Kubernetes, powinna zostać wyświetlona aplikacja hello world.

![Przykładowa aplikacja, jeden](media/ingress/app-one.png)

Teraz przejdź do nazwy FQDN kontrolera danych przychodzących z `/hello-world-two` ścieżki, powinna zostać wyświetlona aplikacja hello world z tytułem niestandardowych.

![Przykładowa aplikacja dwóch](media/ingress/app-two.png)

Także zauważyć, że połączenie jest szyfrowane, oraz że jest używany certyfikat wystawiony przez umożliwia szyfrowanie.

![Umożliwia szyfrowanie certyfikatu](media/ingress/certificate.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o oprogramowaniu, przedstawione w tym dokumencie.

- [Interfejs wiersza polecenia narzędzia Helm][helm-cli]
- [Kontroler danych przychodzących serwera NGINX][nginx-ingress]
- [Menedżer certyfikatów][cert-manager]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
