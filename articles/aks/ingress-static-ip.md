---
title: Tworzenie kontrolera transferu danych przychodzących HTTP ze statycznym adresem IP w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak zainstalować i skonfigurować kontroler transferu danych przychodzących NGINX ze statycznym publicznym adresem IP w klastrze usługi Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 10422595b85c71020225df694778e6b8ae7e0185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191354"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Tworzenie kontrolera transferu danych przychodzących ze statycznym publicznym adresem IP w usłudze Azure Kubernetes Service (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć [kontroler transferu danych przychodzących NGINX][nginx-ingress] w klastrze usługi Azure Kubernetes (AKS). Kontroler transferu danych przychodzących jest skonfigurowany ze statycznym publicznym adresem IP. Projekt [cert-manager][cert-manager] służy do automatycznego generowania i konfigurowania certyfikatów [Let's Encrypt.][lets-encrypt] Na koniec dwie aplikacje są uruchamiane w klastrze AKS, z których każda jest dostępna za pośrednictwem jednego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera transferu danych przychodzących z zewnętrzną łącznością sieciową][aks-ingress-basic]
- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera transferu danych przychodzących, który używa własnych certyfikatów TLS][aks-ingress-own-tls]
- [Tworzenie kontrolera transferu danych przychodzących, który używa szyfrowania Let's do automatycznego generowania certyfikatów TLS z dynamicznym publicznym adresem IP][aks-ingress-tls]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

W tym artykule używa helm zainstalować kontroler transferu danych przychodzących NGINX, cert-manager i przykładowej aplikacji sieci web. Upewnij się, że używasz najnowszej wersji helm. Aby uzyskać instrukcje uaktualniania, zobacz [dokumenty instalacji helm][helm-install]. Aby uzyskać więcej informacji na temat konfigurowania i używania helmu, zobacz [Instalowanie aplikacji z helmem w usłudze Azure Kubernetes Service (AKS)][use-helm].

W tym artykule wymaga również, że są uruchomione interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera transferu danych przychodzących

Domyślnie kontroler przystawek NGINX jest tworzony z nowym przypisaniem publicznego adresu IP. Ten publiczny adres IP jest tylko statyczny dla trwałości kontrolera transferu danych przychodzących i jest tracony, jeśli kontroler zostanie usunięty i utworzony ponownie. Typowym wymaganiem konfiguracji jest zapewnienie kontrolerowi transferu danych przychodzących NGINX istniejącego statycznego publicznego adresu IP. Statyczny publiczny adres IP pozostaje po usunięciu kontrolera transferu danych przychodzących. Takie podejście umożliwia korzystanie z istniejących rekordów DNS i konfiguracji sieci w spójny sposób w całym cyklu życia aplikacji.

Jeśli chcesz utworzyć statyczny publiczny adres IP, najpierw uzyskaj nazwę grupy zasobów klastra AKS za pomocą polecenia [az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Następnie utwórz publiczny adres IP z metodą alokacji *statycznej* przy użyciu polecenia [az network public-ip create.][az-network-public-ip-create] Poniższy przykład tworzy publiczny adres IP o nazwie *myAKSPublicIP* w grupie zasobów klastra AKS uzyskany w poprzednim kroku:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Teraz wdrożyć *wykres nginx-ingress* z Helm. Dodaj `--set controller.service.loadBalancerIP` parametr i określ własny publiczny adres IP utworzony w poprzednim kroku. W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchamiania replik kontrolera transferu danych przychodzących, upewnij się, że w klastrze usługi AKS znajduje się więcej niż jeden węzeł.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. Węzły systemu Windows Server (obecnie w wersji zapoznawczej w usłudze AKS) nie powinny uruchamiać kontrolera transferu danych przychodzących. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy obszar nazw Kubernetes dla zasobów przychodzących o nazwie *ingress-basic*. W razie potrzeby określ obszar nazw dla własnego środowiska. Jeśli klaster AKS nie jest włączony `--set rbac.create=false` RBAC, dodaj do poleceń Helm.

> [!TIP]
> Jeśli chcesz włączyć [zachowanie adresu IP źródła klienta][client-source-ip] dla żądań do kontenerów w klastrze, dodaj `--set controller.service.externalTrafficPolicy=Local` do polecenia instalacji helm. Adres IP źródła klienta jest przechowywany w nagłówku żądania w obszarze *X-Forwarded-For*. W przypadku korzystania z kontrolera transferu danych przychodzących z włączoną obsługą ochrony adresów IP źródła klienta przekazywanie protokołu SSL nie będzie działać.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
```

Po utworzeniu usługi modułu równoważenia obciążenia Kubernetes dla kontrolera transferu danych przychodzących NGINX przypisany jest statyczny adres IP, jak pokazano w poniższym przykładzie danych wyjściowych:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
nginx-ingress-controller                    LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
nginx-ingress-default-backend               ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Nie utworzono jeszcze żadnych reguł ruchu przychodzącego, więc domyślna strona kontrolera 404 kontrolera transferu danych przychodzących NGINX jest wyświetlana po przejmuje się do publicznego adresu IP. Reguły transferu danych przychodzących są konfigurowane w następujących krokach.

## <a name="configure-a-dns-name"></a>Konfigurowanie nazwy DNS

Aby certyfikaty HTTPS działały poprawnie, skonfiguruj numer FQDN dla adresu IP kontrolera transferu danych przychodzących. Zaktualizuj następujący skrypt za pomocą adresu IP kontrolera transferu danych przychodzących i unikatowej nazwy, której chcesz użyć w sieci FQDN:

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="40.121.63.72"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Kontroler transferu danych przychodzących jest teraz dostępny za pośrednictwem sieci FQDN.

## <a name="install-cert-manager"></a>Instalowanie narzędzia cert-manager

Kontroler ruchu przychodzącego NGINX obsługuje kończenie żądań protokołu TLS. Istnieje kilka sposobów pobierania i konfigurowania certyfikatów protokołu HTTPS. W tym artykule przedstawiono przy użyciu [cert-manager][cert-manager], który zapewnia automatyczne [umożliwia szyfrowanie][lets-encrypt] certyfikatów generowania i zarządzania funkcjami.

> [!NOTE]
> W tym artykule użyto `staging` środowiska dla Let's Encrypt. W wdrożeniach produkcyjnych `letsencrypt-prod` `https://acme-v02.api.letsencrypt.org/directory` użyj i w definicjach zasobów i podczas instalowania wykresu Helm.

Aby zainstalować kontroler certyfikat-manager w klastrze obsługującym funkcję `helm install` RBAC, należy użyć następującego polecenia:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.13.0 \
  jetstack/cert-manager
```

Aby uzyskać więcej informacji na temat konfiguracji programu cert-manager, zobacz [projekt cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Tworzenie wystawcy klastra urzędu certyfikacji

Przed wystawieniem certyfikatów, cert-manager wymaga [emitenta][cert-manager-issuer] lub [zasobu ClusterIssuer.][cert-manager-cluster-issuer] Te zasoby kubernetes są identyczne `Issuer` w funkcjonalności, jednak `ClusterIssuer` działa w jednej przestrzeni nazw i działa we wszystkich obszarach nazw. Aby uzyskać więcej informacji, zobacz dokumentację [wystawcy programu cert-manager.][cert-manager-issuer]

Utwórz wystawcę `cluster-issuer.yaml`klastra, taką jak , używając następującego przykładowego manifestu. Zaktualizuj adres e-mail o prawidłowy adres organizacji:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
```

Aby utworzyć wystawcę, użyj `kubectl apply -f cluster-issuer.yaml` polecenia.

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Uruchamianie aplikacji demonstracyjnych

Skonfigurowano kontroler transferu danych przychodzących i rozwiązanie do zarządzania certyfikatami. Teraz uruchommy dwie aplikacje demonstracyjne w klastrze AKS. W tym przykładzie Helm jest używany do wdrażania dwóch wystąpień prostej aplikacji "Hello world".

Przed zainstalowaniem przykładowych wykresów Helm należy dodać repozytorium przykładów platformy Azure do środowiska Helm w następujący sposób:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Utwórz pierwszą aplikację demonstracyjną z wykresu Helm za pomocą następującego polecenia:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Teraz zainstaluj drugie wystąpienie aplikacji demonstracyjnej. W drugim wystąpieniu należy określić nowy tytuł, tak aby dwie aplikacje były wizualnie różne. Można również określić unikatową nazwę usługi:

```console
helm install aks-helloworld-2 azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Tworzenie trasy transferu ruchu przychodzącego

Obie aplikacje są teraz uruchomione w klastrze kubernetes, jednak `ClusterIP`są one skonfigurowane z usługą typu . W związku z tym aplikacje nie są dostępne z Internetu. Aby udostępnić je publicznie, należy utworzyć zasób transferu danych przychodzących Kubernetes. Zasób transferu danych przychodzących konfiguruje reguły, które kierują ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch `https://demo-aks-ingress.eastus.cloudapp.azure.com/` na adres jest `aks-helloworld`kierowany do usługi o nazwie . Ruch na `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` adres jest kierowany `ingress-demo` do usługi. Zaktualizuj *hosty* i *hosta* do nazwy DNS utworzonej w poprzednim kroku.

Utwórz plik `hello-world-ingress.yaml` o nazwie i skopiuj w poniższym przykładzie YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Utwórz zasób transferu `kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic` ruchu przychodzącego za pomocą polecenia.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Tworzenie obiektu certyfikatu

Następnie należy utworzyć zasób certyfikatu. Zasób certyfikatu definiuje żądany certyfikat X.509. Aby uzyskać więcej informacji, zobacz [certyfikaty menedżera certyfikatów][cert-manager-certificates].

Cert-manager prawdopodobnie automatycznie utworzył obiekt certyfikatu dla Ciebie przy użyciu ingress-shim, który jest automatycznie wdrażany z cert-manager od wersji 0.2.2. Aby uzyskać więcej informacji, zobacz [dokumentację ingress-shim][ingress-shim].

Aby sprawdzić, czy certyfikat został `kubectl describe certificate tls-secret --namespace ingress-basic` pomyślnie utworzony, użyj polecenia.

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

Jeśli chcesz utworzyć dodatkowy zasób certyfikatu, możesz to zrobić za pomocą następującego przykładowego manifestu. Zaktualizuj *dnsNames* i *domeny* do nazwy DNS utworzonej w poprzednim kroku. Jeśli używasz kontrolera danych przychodzących tylko wewnętrznego, określ wewnętrzną nazwę DNS usługi.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
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

Aby utworzyć zasób `kubectl apply -f certificates.yaml` certyfikatu, użyj tego polecenia.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Testowanie konfiguracji transferu danych przychodzących

Otwórz przeglądarkę internetową w sieci FQDN kontrolera transferu danych *https://demo-aks-ingress.eastus.cloudapp.azure.com*przychodzących kubernetes, na przykład .

W miarę `letsencrypt-staging`używania tych przykładów wystawiony certyfikat SSL nie jest zaufany przez przeglądarkę. Zaakceptuj monit ostrzegawczy, aby kontynuować aplikację. Informacje o certyfikacie pokazują, że ten fałszywy certyfikat *LE Intermediate X1* jest wydawany przez Let's Encrypt. Ten fałszywy certyfikat `cert-manager` wskazuje, że przetworzył żądanie poprawnie i otrzymał certyfikat od dostawcy:

![Szyfrujmy certyfikat przemieszczania](media/ingress/staging-certificate.png)

Po zmianie Let's Encrypt `prod` do `staging`użycia, a nie, używany jest zaufany certyfikat wystawiony przez Let's Encrypt, jak pokazano w poniższym przykładzie:

![Zaszyfrujmy certyfikat](media/ingress/certificate.png)

Aplikacja demonstracyjne jest wyświetlana w przeglądarce internetowej:

![Przykład aplikacji jeden](media/ingress/app-one.png)

Teraz dodaj ścieżkę */hello-world-two* do FQDN, taką jak *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two*. Druga aplikacja demo z tytułem niestandardowym jest pokazana:

![Przykład aplikacji dwa](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym artykule użyto helm do zainstalowania składników transferu przychodzącego, certyfikatów i przykładowych aplikacji. Podczas wdrażania wykresu Helm tworzone są wiele zasobów Kubernetes. Zasoby te obejmują zasoby, wdrożenia i usługi. Aby wyczyścić te zasoby, można usunąć cały przykładowy obszar nazw lub poszczególne zasoby.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Usuwanie przykładowego obszaru nazw i wszystkich zasobów

Aby usunąć całą przykładową przestrzeń `kubectl delete` nazw, użyj polecenia i określ nazwę obszaru nazw. Wszystkie zasoby w obszarze nazw są usuwane.

```console
kubectl delete namespace ingress-basic
```

Następnie usuń repozytorium Helm dla aplikacji AKS hello world:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Usuwanie zasobów pojedynczo

Alternatywnie bardziej szczegółowe podejście jest usunięcie poszczególnych zasobów utworzonych. Najpierw usuń zasoby certyfikatu:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Teraz wyświetl komunikat Helm `helm list` z poleceniem. Poszukaj wykresów o nazwach *nginx-ingress*, *cert-manager*i *aks-helloworld*, jak pokazano w poniższym przykładzie danych wyjściowych:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-11 15:02:21.51172346   deployed        aks-helloworld-0.1.0
aks-helloworld-2        ingress-basic   1               2020-01-11 15:03:10.533465598  deployed        aks-helloworld-0.1.0
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic    1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Usuń zwolnienia za `helm uninstall` pomocą polecenia. Poniższy przykład usuwa wdrożenia transferu ruchu przychodzącego NGINX, menedżera certyfikatów i dwóch przykładowych aplikacji AKS hello world.

```
$ helm uninstall aks-helloworld aks-helloworld-2 nginx-ingress cert-manager -n ingress-basic

release "aks-helloworld" deleted
release "aks-helloworld-2" deleted
release "nginx-ingress" deleted
release "cert-manager" deleted
```

Następnie usuń repozytorium Helm dla aplikacji AKS hello world:

```console
helm repo remove azure-samples
```

Usuń sam obszar nazw. Użyj `kubectl delete` polecenia i określ nazwę obszaru nazw:

```console
kubectl delete namespace ingress-basic
```

Na koniec usuń statyczny publiczny adres IP utworzony dla kontrolera transferu danych przychodzących. Podaj nazwę grupy zasobów *klastra MC_* uzyskaną w pierwszym kroku tego artykułu, na przykład *MC_myResourceGroup_myAKSCluster_eastus:*

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawierał niektóre zewnętrzne składniki usługi AKS. Aby dowiedzieć się więcej o tych składnikach, zobacz następujące strony projektu:

- [Helm CLI][helm-cli]
- [Kontroler transferu danych przychodzących NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera transferu danych przychodzących z zewnętrzną łącznością sieciową][aks-ingress-basic]
- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- [Tworzenie kontrolera transferu danych przychodzących, który używa własnych certyfikatów TLS][aks-ingress-own-tls]
- [Tworzenie kontrolera transferu danych przychodzących z dynamicznym publicznym adresem IP i konfigurowanie szyfrowania let's do automatycznego generowania certyfikatów TLS][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
