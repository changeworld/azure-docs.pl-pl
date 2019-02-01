---
title: Tworzenie ruchu przychodzącego protokołu HTTPS za pomocą klastra Azure Kubernetes Service (AKS)
description: Informacje o sposobie instalowania i konfigurowania kontrolera ruch przychodzący serwera NGINX, który używa teraz szyfrowania do automatycznego generowania certyfikatu SSL w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: cfc99074c0f8347611d805ce18a656a7a22a5f5e
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55512027"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Tworzenie kontrolera danych przychodzących HTTPS w usłudze Azure Kubernetes Service (AKS)

Kontroler danych przychodzących jest to oprogramowanie, które zapewnia zwrotny serwer proxy, routing ruchu można konfigurować i zakończenie protokołu TLS dla usługi Kubernetes. Zasoby danych przychodzących rozwiązania Kubernetes są używane do konfigurowania reguł ruchu przychodzącego i trasy dla poszczególnych usług Kubernetes. Korzystanie z reguł ruchu przychodzącego i kontroler danych przychodzących, pojedynczy adres IP może służyć do kierowania ruchu do wielu usług w klastrze Kubernetes.

W tym artykule przedstawiono sposób wdrażania [kontrolera danych przychodzących NGINX] [ nginx-ingress] w klastrze usługi Azure Kubernetes Service (AKS). [Menedżera certyfikatów] [ cert-manager] projekt jest używana do automatycznego generowania i skonfigurować [umożliwia szyfrowanie] [ lets-encrypt] certyfikatów. Na koniec dwie aplikacje są uruchamiane w klastrze AKS, z których każdy jest dostępny za pośrednictwem pojedynczego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie kontrolera podstawowego transferu danych przychodzących za pomocą połączenia z siecią zewnętrzną][aks-ingress-basic]
- [Włączyć dodatek routing aplikacji protokołu HTTP][aks-http-app-routing]
- [Tworzenie kontrolera danych przychodzących, korzystającą z sieci prywatne, wewnętrzne i adres IP][aks-ingress-internal]
- [Tworzenie kontrolera transferu danych przychodzących, która korzysta z certyfikatów protokołu TLS][aks-ingress-own-tls]
- [Tworzenie kontrolera danych przychodzących, używającej umożliwia szyfrowanie można automatycznie wygenerować certyfikaty protokołu TLS z statyczny publiczny adres IP][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule używa narzędzia Helm do zainstalowania serwera NGINX kontrolera danych przychodzących, Menedżer certyfikatów i przykładową aplikację sieci web. Musisz mieć narzędzia Helm inicjowane w obrębie klastra usługi AKS i przy użyciu konta usługi dla Tiller. Upewnij się, że używasz najnowszej wersji narzędzia Helm. Aby uzyskać instrukcje uaktualniania, zobacz [Helm zainstalować docs][helm-install]. Aby uzyskać więcej informacji na temat konfigurowania i używania narzędzia Helm, zobacz [instalowanie aplikacji za pomocą narzędzia Helm w usłudze Azure Kubernetes Service (AKS)][use-helm].

W tym artykule wymaga również, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.41 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera danych przychodzących

Aby utworzyć kontroler danych przychodzących, użyj `Helm` zainstalował *ruch przychodzący serwera nginx*. Dodano nadmiarowość dwóch replik kontrolerów ruch przychodzący serwera NGINX są wdrażane przy użyciu `--set controller.replicaCount` parametru. Aby w pełni korzystać z systemem replik kontrolera danych przychodzących, upewnij się, że istnieje więcej niż jeden węzeł w klastrze AKS.

> [!TIP]
> W poniższym przykładzie instalowana kontroler danych przychodzących w `kube-system` przestrzeni nazw. Jeśli to konieczne, można określić innej przestrzeni nazw dla Twojego środowiska. Jeśli klaster AKS nie jest włączone RBAC, Dodaj `--set rbac.create=false` poleceń.

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

Podczas instalacji Azure publiczny adres IP jest tworzona dla kontrolera danych przychodzących. Ten publiczny adres IP to statyczny dla żywotności kontrolera danych przychodzących. Jeśli usuniesz kontrolera danych przychodzących, przypisanie publicznego adresu IP zostanie utracony. Jeśli następnie utworzysz kontroler dodatkowy ruch przychodzący, nowy publiczny adres IP zostanie przypisany. Jeśli chcesz zachować użycie publicznego adresu IP, możesz zamiast tego [utworzyć kontroler danych przychodzących z statyczny publiczny adres IP][aks-ingress-static-tls].

Aby uzyskać publiczny adres IP, użyj `kubectl get service` polecenia. Trwa kilka minut, aż adres IP, który można przypisać do usługi.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Utworzono jeszcze żadnych reguł ruchu przychodzącego. Po przejściu do publicznego adresu IP, zostanie wyświetlona strona 404 domyślna serwera NGINX kontrolera danych przychodzących.

## <a name="configure-a-dns-name"></a>Konfigurowanie nazwy DNS

W przypadku certyfikaty HTTPS działała prawidłowo należy skonfigurować nazwy FQDN dla adresu IP kontrolera danych przychodzących. Zaktualizuj poniższy skrypt adres IP kontrolera danych przychodzących i unikatową nazwę, której chcesz użyć dla nazwy FQDN:

```console
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Kontroler danych przychodzących jest teraz dostępna za pośrednictwem nazwy FQDN.

## <a name="install-cert-manager"></a>Zainstaluj Menedżera certyfikatów

Kontroler danych przychodzących NGINX obsługuje kończenie żądań protokołu TLS. Istnieje kilka sposobów, aby pobrać i skonfigurować certyfikaty dla protokołu HTTPS. W tym artykule pokazano sposób użycia [Menedżera certyfikatów][cert-manager], która umożliwia automatyczne [umożliwia szyfrowanie] [ lets-encrypt] Generowanie certyfikatu i Funkcje zarządzania.

> [!NOTE]
> W tym artykule wykorzystano `staging` środowiska umożliwia szyfrowanie. W przypadku wdrożeń produkcyjnych, użyj `letsencrypt-prod` i `https://acme-v02.api.letsencrypt.org/directory` w definicjach zasobów, a podczas instalowania wykresu Helm.

Aby zainstalować kontroler Menedżera certyfikatów w klastrze z włączoną funkcją RBAC, należy użyć następującego `helm install` polecenia:

```console
helm install stable/cert-manager \
    --namespace kube-system \
    --set ingressShim.defaultIssuerName=letsencrypt-staging \
    --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Jeśli klaster nie jest włączone RBAC, zamiast tego użyj następującego polecenia:

```console
helm install stable/cert-manager \
    --namespace kube-system \
    --set ingressShim.defaultIssuerName=letsencrypt-staging \
    --set ingressShim.defaultIssuerKind=ClusterIssuer \
    --set rbac.create=false \
    --set serviceAccount.create=false
```

Aby uzyskać więcej informacji na temat konfiguracji Menedżera certyfikatów, zobacz [projektu Menedżera certyfikatów][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Utwórz wystawcę klastra urzędu certyfikacji

Zanim certyfikaty mogą być wystawiane, Menedżer certyfikatów wymaga [wystawcy] [ cert-manager-issuer] lub [ClusterIssuer] [ cert-manager-cluster-issuer] zasobów. Te zasoby platformy Kubernetes są identyczne w działaniu, jednak `Issuer` działa w jednej przestrzeni nazw i `ClusterIssuer` działa we wszystkich przestrzeni nazw. Aby uzyskać więcej informacji, zobacz [wystawcy certyfikatu Menedżera] [ cert-manager-issuer] dokumentacji.

Tworzenie klastra wystawcy, takich jak `cluster-issuer.yaml`, za pomocą następujących manifest przykładu. Przy użyciu prawidłowego adresu z Twojej organizacji, należy zaktualizować adres e-mail:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Aby utworzyć wystawcy, użyj `kubectl apply -f cluster-issuer.yaml` polecenia.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Uruchamianie aplikacji demonstracyjnych

Skonfigurowano kontroler danych przychodzących i rozwiązania do zarządzania certyfikatu. Teraz sklonujemy wykonywania dwóch pokaz aplikacji w klastrze AKS. W tym przykładzie Helm służy do wdrażania dwa wystąpienia aplikacji proste "Hello world".

Przed zainstalowaniem wykresów rozwiązania Helm przykładowe Dodaj repozytorium przykładów dla platformy Azure w środowisku Helm w następujący sposób:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Tworzenie pierwszej aplikacji demonstracyjnej z planu narzędzia Helm, za pomocą następującego polecenia:

```console
helm install azure-samples/aks-helloworld
```

Teraz zainstalować drugie wystąpienie aplikacji pokazowej. Dla drugiego wystąpienia należy określić nowy tytuł, aby wizualnie różniące się od dwóch aplikacji. Możesz również określić unikatową nazwę usługi:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Utwórz trasę w protokole transferu danych przychodzących

Obie aplikacje są uruchomione w klastrze Kubernetes, ale są one konfigurowane przy użyciu usługi typu `ClusterIP`. Jako takie aplikacje nie są dostępne z Internetu. Aby udostępnić je publicznie, utwórz zasób transferu danych przychodzących rozwiązania Kubernetes. Zasób ruch przychodzący konfiguruje zasady, które kierują ruch do jednego z dwóch aplikacji.

W poniższym przykładzie ruch do adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/` jest kierowany do usługi o nazwie `aks-helloworld`. Ruch do adresu `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` jest kierowany do `ingress-demo` usługi. Aktualizacja *hosty* i *hosta* na nazwę DNS utworzonego w poprzednim kroku.

Utwórz plik o nazwie `hello-world-ingress.yaml` i skopiuj poniższy przykład kodu YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
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

Tworzenie przy użyciu zasobów ruch przychodzący `kubectl apply -f hello-world-ingress.yaml` polecenia.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Utwórz obiekt certyfikatu

Następnie można utworzyć zasobu certyfikatu. Zasób certyfikatu definiuje żądany certyfikat X.509. Aby uzyskać więcej informacji, zobacz [certyfikaty Menedżera certyfikatów][cert-manager-certificates].

Menedżer certyfikatów prawdopodobnie automatycznie utworzył obiekt certyfikatu przy użyciu transferu danych przychodzących — podkładki, który jest automatycznie wdrażane za pomocą Menedżera certyfikatów od v0.2.2. Aby uzyskać więcej informacji, zobacz [dokumentacji podkładki ruch przychodzący][ingress-shim].

Aby sprawdzić, czy certyfikat został pomyślnie utworzony, użyj `kubectl describe certificate tls-secret` polecenia.

Jeśli certyfikat został wystawiony, zobaczysz dane wyjściowe podobne do następujących:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Jeśli musisz utworzyć zasób dodatkowy certyfikat, możesz to zrobić z manifestem poniższy przykład. Aktualizacja *dnsNames* i *domen* na nazwę DNS utworzonego w poprzednim kroku. Jeśli używasz kontrolera danych przychodzących wyłącznie wewnętrznym, należy określić wewnętrzna nazwa DNS usługi.

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
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Aby utworzyć zasób certyfikatu, użyj `kubectl apply -f certificates.yaml` polecenia.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testowanie konfiguracji transferu danych przychodzących

Otwórz przeglądarkę internetową nazwę FQDN kontrolera danych przychodzących rozwiązania Kubernetes, takich jak *https://demo-aks-ingress.eastus.cloudapp.azure.com*.

Do używania w tych przykładach `letsencrypt-staging`, wystawionego certyfikatu SSL nie jest zaufany przez przeglądarkę. Zaakceptuj monit ostrzegawczy, aby przejść do aplikacji. Przedstawia informacje o certyfikacie, to *sfałszowana LE pośrednich X1* certyfikat wystawiony przez umożliwia szyfrowanie. Ten certyfikat fałszywych wskazuje `cert-manager` poprawnie przetwarzał żądanie i otrzymała certyfikat od dostawcy:

![Umożliwia szyfrowanie przemieszczania certyfikatu](media/ingress/staging-certificate.png)

Po zmianie umożliwia szyfrowanie do użycia `prod` zamiast `staging`, zaufany certyfikat wystawiony przez umożliwia szyfrowanie jest używany, jak pokazano w poniższym przykładzie:

![Umożliwia szyfrowanie certyfikatu](media/ingress/certificate.png)

Aplikacji demonstracyjnej jest wyświetlany w przeglądarce sieci web:

![Przykładowa aplikacja, jeden](media/ingress/app-one.png)

Teraz Dodaj */hello-world-two* ścieżkę do pełni kwalifikowaną nazwę domeny, takich jak *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. Pokazano drugiej aplikacji demonstracyjnej z tytułem niestandardowe:

![Przykładowa aplikacja dwóch](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule używane narzędzia Helm do zainstalowania składników transferu danych przychodzących, certyfikaty i przykładowe aplikacje. Podczas wdrażania wykresu Helm tworzonych wiele zasobów Kubernetes. Te zasoby obejmują zasobników, wdrożenia i usług. Aby wyczyścić, należy najpierw usunąć zasoby certyfikatu:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Teraz wyświetlić listę wersji narzędzia Helm przy użyciu `helm list` polecenia. Wyszukaj wykresy o nazwie *ruch przychodzący serwera nginx*, *Menedżera certyfikatów*, i *aks-helloworld*, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
billowing-kitten        1           Tue Oct 16 17:24:05 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
loitering-waterbuffalo  1           Tue Oct 16 17:26:16 2018    DEPLOYED    cert-manager-v0.3.4     v0.3.2      kube-system
flabby-deer             1           Tue Oct 16 17:27:06 2018    DEPLOYED    aks-helloworld-0.1.0                default
linting-echidna         1           Tue Oct 16 17:27:02 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Usuń wersjach z `helm delete` polecenia. Poniższy przykład usuwa wdrożenia ruch przychodzący serwera NGINX, Menedżer certyfikatów i dwie przykładowe AKS Witaj świecie aplikacje.

```
$ helm delete billowing-kitten loitering-waterbuffalo flabby-deer linting-echidna

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Następnie usuń repozytorium narzędzia Helm dla usługi AKS hello world aplikacji:

```console
helm repo remove azure-samples
```

Na koniec usunąć trasę ruchu przychodzącego, który kierowany ruch do aplikacji przykładowej:

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule uwzględnione niektóre składniki zewnętrzne w usłudze AKS. Aby dowiedzieć się więcej na temat tych składników, zobacz następujące strony projektu:

- [Interfejs wiersza polecenia narzędzia Helm][helm-cli]
- [Kontroler danych przychodzących serwera NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Możesz również wykonać następujące czynności:

- [Tworzenie kontrolera podstawowego transferu danych przychodzących za pomocą połączenia z siecią zewnętrzną][aks-ingress-basic]
- [Włączyć dodatek routing aplikacji protokołu HTTP][aks-http-app-routing]
- [Tworzenie kontrolera danych przychodzących, korzystającą z sieci prywatne, wewnętrzne i adres IP][aks-ingress-internal]
- [Tworzenie kontrolera transferu danych przychodzących, która korzysta z certyfikatów protokołu TLS][aks-ingress-own-tls]
- [Tworzenie kontrolera danych przychodzących, używającej umożliwia szyfrowanie można automatycznie wygenerować certyfikaty protokołu TLS z statyczny publiczny adres IP][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: http://docs.cert-manager.io/en/latest/reference/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
