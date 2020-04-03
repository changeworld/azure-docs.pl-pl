---
title: Tworzenie transferu przychodzącego HTTPS za pomocą klastra usługi Azure Kubernetes (AKS)
description: Dowiedz się, jak zainstalować i skonfigurować kontroler transferu danych przychodzących NGINX, który używa szyfrowania Let's do automatycznego generowania certyfikatów TLS w klastrze usługi Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 4d0edcac86bebb77495907ec43debf077448b8ac
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617225"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Tworzenie kontrolera transferu danych przychodzących HTTPS w usłudze Azure Kubernetes (AKS)

Kontroler ruchu przychodzącego to element oprogramowania dostarczający odwrotny serwer proxy, konfigurowalne trasowanie ruchu oraz zakończenie protokołu TLS dla usług Kubernetes. Zasoby ruchu przychodzącego usług Kubernetes są używane do skonfigurowania zasad ruchu przychodzącego oraz tras dla poszczególnych usług Kubernetes. Dzięki korzystaniu z kontrolera ruchu przychodzącego oraz zasad ruchu przychodzącego można użyć jednego adresu IP do trasowania ruchu w wielu usługach w klastrze Kubernetes.

W tym artykule pokazano, jak wdrożyć [kontroler transferu danych przychodzących NGINX][nginx-ingress] w klastrze usługi Azure Kubernetes (AKS). Projekt [cert-manager][cert-manager] służy do automatycznego generowania i konfigurowania certyfikatów [Let's Encrypt.][lets-encrypt] Na koniec dwie aplikacje są uruchamiane w klastrze AKS, z których każda jest dostępna za pośrednictwem jednego adresu IP.

Możesz również wykonać następujące czynności:

- [Tworzenie podstawowego kontrolera transferu danych przychodzących z zewnętrzną łącznością sieciową][aks-ingress-basic]
- [Włączanie dodatku routingu aplikacji HTTP][aks-http-app-routing]
- [Tworzenie kontrolera transferu danych przychodzących, który używa wewnętrznej, prywatnej sieci i adresu IP][aks-ingress-internal]
- [Tworzenie kontrolera transferu danych przychodzących, który używa własnych certyfikatów TLS][aks-ingress-own-tls]
- [Tworzenie kontrolera transferu danych przychodzących, który używa szyfrowania Let's do automatycznego generowania certyfikatów TLS ze statycznym publicznym adresem IP][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

W tym artykule przyjęto również założenie, że masz [domenę niestandardową][custom-domain] ze [strefą DNS][dns-zone] w tej samej grupie zasobów co klaster AKS.

W tym artykule używa helm zainstalować kontroler transferu danych przychodzących NGINX, cert-manager i przykładowej aplikacji sieci web. Upewnij się, że używasz najnowszej wersji helm. Aby uzyskać instrukcje uaktualniania, zobacz [dokumenty instalacji helm][helm-install]. Aby uzyskać więcej informacji na temat konfigurowania i używania helmu, zobacz [Instalowanie aplikacji z helmem w usłudze Azure Kubernetes Service (AKS)][use-helm].

W tym artykule wymaga również, że są uruchomione interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Tworzenie kontrolera transferu danych przychodzących

Aby utworzyć kontroler ruchu przychodzącego, `helm` użyj polecenia, aby zainstalować *nginx-ingress*. W celu zwiększenia nadmiarowości za pomocą parametru `--set controller.replicaCount` wdrażane są dwie repliki kontrolerów wejściowych NGINX. Aby w pełni korzystać z uruchamiania replik kontrolera transferu danych przychodzących, upewnij się, że w klastrze usługi AKS znajduje się więcej niż jeden węzeł.

Kontroler wejściowy należy również zaplanować w węźle z systemem Linux. Węzły systemu Windows Server (obecnie w wersji zapoznawczej w usłudze AKS) nie powinny uruchamiać kontrolera transferu danych przychodzących. Za pomocą parametru `--set nodeSelector` podaje się selektor węzła, który nakazuje harmonogramowi usługi Kubernetes uruchomienie kontrolera wejściowego NGINX w węźle opartym na systemie Linux.

> [!TIP]
> Poniższy przykład tworzy obszar nazw Kubernetes dla zasobów przychodzących o nazwie *ingress-basic*. W razie potrzeby określ obszar nazw dla własnego środowiska.

> [!TIP]
> Jeśli chcesz włączyć [zachowanie adresu IP źródła klienta][client-source-ip] dla żądań do kontenerów w klastrze, dodaj `--set controller.service.externalTrafficPolicy=Local` do polecenia instalacji helm. Adres IP źródła klienta jest przechowywany w nagłówku żądania w obszarze *X-Forwarded-For*. W przypadku korzystania z kontrolera transferu danych przychodzących z włączoną obsługą ochrony adresów IP źródła klienta przekazywanie protokołu SSL nie będzie działać.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repo
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Podczas instalacji publiczny adres IP platformy Azure jest tworzony dla kontrolera transferu danych przychodzących. Ten publiczny adres IP jest statyczny dla trwałości kontrolera transferu danych przychodzących. Jeśli usuniesz kontroler transferu danych przychodzących, przypisanie publicznego adresu IP zostanie utracone. Jeśli następnie utworzysz dodatkowy kontroler transferu danych przychodzących, zostanie przypisany nowy publiczny adres IP. Jeśli chcesz zachować użycie publicznego adresu IP, możesz zamiast [tego utworzyć kontroler transferu danych przychodzących ze statycznym publicznym adresem IP][aks-ingress-static-tls].

Aby uzyskać publiczny adres IP, użyj `kubectl get service` polecenia. Trwa kilka minut, aby adres IP został przypisany do usługi.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
nginx-ingress-controller                         LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
nginx-ingress-default-backend                    ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Nie utworzono jeszcze żadnych reguł ruchu przychodzącego. Jeśli przejdziesz do publicznego adresu IP, zostanie wyświetlona domyślna strona 404 kontrolera ruchu przychodzącego NGINX.

## <a name="add-an-a-record-to-your-dns-zone"></a>Dodawanie rekordu A do strefy DNS

Dodaj rekord *A* do strefy DNS z zewnętrznym adresem IP usługi NGINX przy użyciu [rekordu az network dns record-set add-record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name '*' \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Opcjonalnie można skonfigurować nazwę FQDN dla adresu IP kontrolera transferu danych przychodzących zamiast domeny niestandardowej. Należy zauważyć, że ten przykład jest dla powłoki Bash.
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Instalowanie narzędzia cert-manager

Kontroler ruchu przychodzącego NGINX obsługuje kończenie żądań protokołu TLS. Istnieje kilka sposobów pobierania i konfigurowania certyfikatów protokołu HTTPS. W tym artykule przedstawiono przy użyciu [cert-manager][cert-manager], który zapewnia automatyczne [umożliwia szyfrowanie][lets-encrypt] certyfikatów generowania i zarządzania funkcjami.

Aby zainstalować kontroler certyfikat-manager:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the ingress-basic namespace to disable resource validation
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
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
```

Aby utworzyć wystawcę, użyj `kubectl apply` polecenia.

```console
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

## <a name="run-demo-applications"></a>Uruchamianie aplikacji demonstracyjnych

Skonfigurowano kontroler transferu danych przychodzących i rozwiązanie do zarządzania certyfikatami. Teraz uruchommy dwie aplikacje demonstracyjne w klastrze AKS. W tym przykładzie Helm jest używany do wdrażania dwóch wystąpień prostej aplikacji *Hello world.*

Przed zainstalowaniem przykładowych wykresów Helm, dodaj repozytorium przykładów platformy Azure do środowiska Helm.

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Utwórz aplikację demons o nazwie *aks-helloworld* przy użyciu *wykresu helm azure-samples/aks-helloworld.*

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Utwórz drugie wystąpienie aplikacji demonstracyjnej o nazwie *aks-helloworld-two*. Określ nowy tytuł i unikatową nazwę usługi, tak aby dwie aplikacje były wizualnie różne za pomocą *--set*.

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Tworzenie trasy transferu ruchu przychodzącego

Obie aplikacje są teraz uruchomione w klastrze kubernetes. Jednak są one skonfigurowane z `ClusterIP` usługą typu i nie są dostępne z Internetu. Aby udostępnić je publicznie, należy utworzyć zasób transferu danych przychodzących Kubernetes. Zasób transferu danych przychodzących konfiguruje reguły, które kierują ruch do jednej z dwóch aplikacji.

W poniższym przykładzie ruch do adresu *hello-world-ingress. MY_CUSTOM_DOMAIN* jest kierowany do usługi *aks-helloworld.* Ruch do adresu *hello-world-ingress. MY_CUSTOM_DOMAIN/hello-world-two* jest kierowany do usługi *aks-helloworld-two.* Ruch do *hello-world-ingress. MY_CUSTOM_DOMAIN/static* jest kierowane do usługi o nazwie *aks-helloworld* dla zasobów statycznych.

Utwórz plik `hello-world-ingress.yaml` o nazwie przy użyciu poniższego przykładu YAML. Zaktualizuj *hosty* i *hosta* do nazwy DNS utworzonej w poprzednim kroku.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Utwórz zasób transferu `kubectl apply` ruchu przychodzącego za pomocą polecenia.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Sprawdzanie, czy obiekt certyfikatu został utworzony

Następnie należy utworzyć zasób certyfikatu. Zasób certyfikatu definiuje żądany certyfikat X.509. Aby uzyskać więcej informacji, zobacz [certyfikaty menedżera certyfikatów][cert-manager-certificates]. Cert-manager automatycznie utworzył obiekt certyfikatu za pomocą ingress-shim, który jest automatycznie wdrażany z cert-manager od wersji 0.2.2. Aby uzyskać więcej informacji, zobacz [dokumentację ingress-shim][ingress-shim].

Aby sprawdzić, czy certyfikat został `kubectl get certificate --namespace ingress-basic` pomyślnie utworzony, użyj polecenia i *sprawdź,* czy funkcja READY jest *prawdziwa,* co może potrwać kilka minut.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Testowanie konfiguracji transferu danych przychodzących

Otwórz przeglądarkę internetową, aby *powitać świat-wnika. MY_CUSTOM_DOMAIN* kontrolera transferu danych przychodzących w u użytkownika Kubernetes. Zwróć uwagę, że jesteś przekierowany do korzystania z protokołu HTTPS i certyfikat jest zaufany, a aplikacja demonstracyjne jest wyświetlana w przeglądarce internetowej. Dodaj *ścieżkę /hello-world-two* i zwróć uwagę na drugą aplikację demonstracyjną z tytułem niestandardowym.

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

Alternatywnie bardziej szczegółowe podejście jest usunięcie poszczególnych zasobów utworzonych. Najpierw usuń zasoby wystawcy klastra:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Lista komunikatów helm `helm list` z poleceniem. Poszukaj wykresów o nazwach *nginx-ingress* i *aks-helloworld*, jak pokazano w poniższym przykładzie danych wyjściowych:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-15 10:24:32.054871 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-15 10:24:37.671667 -0600 CST    deployed        aks-helloworld-0.1.0               
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Usuń zwolnienia za `helm delete` pomocą polecenia. Poniższy przykład usuwa wdrożenia transferu ruchu przychodzącego NGINX i dwóch przykładowych aplikacji AKS hello world.

```
$ helm uninstall aks-helloworld aks-helloworld-two cert-manager nginx --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "cert-manager" uninstalled
release "nginx" uninstalled
```

Następnie usuń repozytorium Helm dla aplikacji AKS hello world:

```console
helm repo remove azure-samples
```

Usuń trasę transferu danych przychodzących, która kierowała ruch do przykładowych aplikacji:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Na koniec można usunąć sam obszar nazw. Użyj `kubectl delete` polecenia i określ nazwę obszaru nazw:

```console
kubectl delete namespace ingress-basic
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
- [Tworzenie kontrolera transferu danych przychodzących, który używa szyfrowania Let's do automatycznego generowania certyfikatów TLS ze statycznym publicznym adresem IP][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
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
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
