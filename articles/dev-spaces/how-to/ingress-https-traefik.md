---
title: Użyj niestandardowego kontrolera transferu danych przychodzących traefik i skonfiguruj protokół HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Dowiedz się, jak skonfigurować usługę Azure Dev Spaces do używania niestandardowego kontrolera transferu danych przychodzących traefik i konfigurowania protokołu HTTPS przy użyciu tego kontrolera transferu danych przychodzących
keywords: Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s
ms.openlocfilehash: fd11b3bbd3f90b75203084ff0753c1485d57a35b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155433"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Użyj niestandardowego kontrolera transferu danych przychodzących traefik i skonfiguruj protokół HTTPS

W tym artykule pokazano, jak skonfigurować usługi Azure Dev Spaces do używania niestandardowego kontrolera transferu danych przychodzących traefik. W tym artykule pokazano również, jak skonfigurować ten niestandardowy kontroler transferu danych przychodzących do używania protokołu HTTPS.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto][azure-account-create].
* [Zainstalowany interfejs wiersza polecenia platformy Azure][az-cli].
* [Klaster usługi Azure Kubernetes Service (AKS) z włączoną usługą Azure Dev Spaces.][qs-cli]
* [zainstalowany kubectl.][kubectl]
* [Helm 3 zainstalowany][helm-installed].
* [Domena niestandardowa][custom-domain] ze [strefą DNS][dns-zone]. W tym artykule założono, że domena niestandardowa i strefa DNS znajdują się w tej samej grupie zasobów co klaster AKS, ale możliwe jest użycie domeny niestandardowej i strefy DNS w innej grupie zasobów.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Konfigurowanie niestandardowego kontrolera transferu danych przychodzących traefik

Połącz się z klastrem za pomocą [kubectl][kubectl], klienta wiersza polecenia Kubernetes. Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Dodaj [oficjalne stabilne repozytorium Helm][helm-stable-repo], które zawiera wykres helm kontrolera traefik ingress.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Utwórz obszar nazw kubernetes dla kontrolera transferu danych `helm`przychodzących traefik i zainstaluj go przy użyciu programu .

> [!NOTE]
> Jeśli klaster AKS nie ma włączonego rbac, usuń parametr *--set rbac.enabled=true.*

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> Powyższy przykład tworzy publiczny punkt końcowy dla kontrolera transferu danych przychodzących. Jeśli chcesz użyć prywatnego punktu końcowego dla kontrolera transferu danych przychodzących, dodaj *--set service.annotations." usługa\\\\.beta\\.kubernetes .io/azure-load-balancer-internal"=true* parametr do polecenia *instalacji helm.*
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Ten prywatny punkt końcowy jest narażony w sieci wirtualnej, w której wdrożono klaster AKS.

Pobierz adres IP usługi kontrolera transferu danych przychodzących traefik za pomocą [kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

Przykładowe dane wyjściowe pokazuje adresy IP dla wszystkich usług w przestrzeni nazwy *traefik.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Dodaj rekord *A* do strefy DNS z zewnętrznym adresem IP usługi traefik przy użyciu [rekordu dns az network dns set a add-record][az-network-dns-record-set-a-add-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

Powyższy przykład dodaje rekord *A* do *MY_CUSTOM_DOMAIN* strefy DNS.

W tym artykule używasz [przykładowej aplikacji Azure Dev Spaces Bike Sharing,](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) aby zademonstrować użycie usługi Azure Dev Spaces. Klonowanie aplikacji z usługi GitHub i przechodzenie do jej katalogu:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Otwórz [plik values.yaml][values-yaml] i wykonuj następujące aktualizacje:
* Zastąp wszystkie wystąpienia *<REPLACE_ME_WITH_HOST_SUFFIX>* *traefikiem. MY_CUSTOM_DOMAIN* do *MY_CUSTOM_DOMAIN*MY_CUSTOM_DOMAIN przy użyciu domeny. 
* Zamień *kubernetes.io/ingress.class: traefik-azds # Dev Spaces-specific* z *kubernetes.io/ingress.class: traefik # Custom Ingress*. 

Poniżej znajduje się `values.yaml` przykład zaktualizowanego pliku:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Zapisz zmiany i zamknij plik.

Utwórz przestrzeń *deweloperów* za `azds space select`pomocą przykładowej aplikacji za pomocą programu .

```console
azds space select -n dev -y
```

Wdrażanie przykładowej `helm install`aplikacji przy użyciu programu .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

Powyższy przykład wdraża przykładową aplikację do obszaru nazw *deweloperów.*

Wyświetl adresy URL, aby `azds list-uris`uzyskać dostęp do przykładowej aplikacji za pomocą programu .

```console
azds list-uris
```

Poniższe dane wyjściowe pokazują `azds list-uris`przykładowe adresy URL z pliku .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Przejdź do usługi *bikesharingweb,* otwierając publiczny `azds list-uris` adres URL z polecenia. W powyższym przykładzie publiczny adres URL usługi *bikesharingweb* to `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

> [!NOTE]
> Jeśli zamiast usługi *bikesharingweb* zostanie wyświetlona strona błędu, sprawdź, czy zaktualizowano zarówno *adnotację kubernetes.io/ingress.class,* **jak** i hosta w pliku *values.yaml.*

Użyj `azds space select` polecenia, aby utworzyć przestrzeń podrzędną w obszarze *deweloperów* i wyświetlić listę adresów URL, aby uzyskać dostęp do przestrzeni deweloperskiej podrzędnej.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Poniższe dane wyjściowe pokazuje `azds list-uris` przykładowe adresy URL z dostępu do przykładowej aplikacji w *azureuser1* przestrzeni dewelopera podrzędnego.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Przejdź do usługi *bikesharingweb* w przestrzeni deweloperskiej podrzędnej *azureuser1,* otwierając publiczny adres URL z `azds list-uris` polecenia. W powyższym przykładzie publiczny adres URL usługi *bikesharingweb* w przestrzeni `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`dewelopera podrzędnego *azureuser1* to .

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Konfigurowanie kontrolera transferu danych przychodzących traefik do używania protokołu HTTPS

Użyj [cert-manager,][cert-manager] aby zautomatyzować zarządzanie certyfikatem TLS podczas konfigurowania kontrolera transferu danych przychodzących traefik do używania protokołu HTTPS. Służy `helm` do instalowania wykresu *certmanager.*

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Utwórz `letsencrypt-clusterissuer.yaml` plik i zaktualizuj pole wiadomości e-mail za pomocą adresu e-mail.

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
            class: traefik
```

> [!NOTE]
> Do testowania istnieje również [serwer przejściowy,][letsencrypt-staging-issuer] którego można użyć dla *programu ClusterIssuer*.

Użyj, `kubectl` `letsencrypt-clusterissuer.yaml`aby zastosować .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Usuń poprzedni *traefik* *ClusterRole* i *ClusterRoleBinding*, a następnie `helm`uaktualnij traefik do korzystania z protokołu HTTPS przy użyciu programu .

> [!NOTE]
> Jeśli klaster AKS nie ma włączonego rbac, usuń parametr *--set rbac.enabled=true.*

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Pobierz zaktualizowany adres IP usługi kontrolera transferu danych przychodzących traefik za pomocą [kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

Przykładowe dane wyjściowe pokazuje adresy IP dla wszystkich usług w przestrzeni nazwy *traefik.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Dodaj rekord *A* do strefy DNS z nowym zewnętrznym adresem IP usługi traefik przy użyciu rekordu [az network dns record-set a add-record][az-network-dns-record-set-a-add-record] i usuń poprzedni rekord *A* przy użyciu [rekordu dns az network dns-set a remove-record][az-network-dns-record-set-a-remove-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

Powyższy przykład aktualizuje rekord *A* w *MY_CUSTOM_DOMAIN* strefie DNS, aby użyć *PREVIOUS_EXTERNAL_IP*.

Zaktualizuj [plik values.yaml,][values-yaml] aby uwzględnić szczegóły dotyczące korzystania z *menedżera certyfikatów* i protokołu HTTPS. Poniżej znajduje się `values.yaml` przykład zaktualizowanego pliku:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Uaktualnij przykładową `helm`aplikację za pomocą:

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Przejdź do przykładowej aplikacji w przestrzeni podrzędnej *dev/azureuser1* i zwróć uwagę, że zostaniesz przekierowany do korzystania z protokołu HTTPS.

> [!IMPORTANT]
> Może upłynąć 30 minut lub więcej, aby zmiany DNS zostały ukończone i przykładowa aplikacja, aby była dostępna.

Należy również zauważyć, że strona jest wczytytyna, ale przeglądarka pokazuje pewne błędy. Otwarcie konsoli przeglądarki pokazuje, że błąd odnosi się do strony HTTPS próbującej załadować zasoby HTTP. Przykład:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Aby naprawić ten błąd, zaktualizuj [bikesharingweb/azds.yaml,][azds-yaml] aby użyć *traefik* dla *kubernetes.io/ingress.class* i domeny niestandardowej dla *$(hostSuffix)*. Przykład:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Aktualizacja [BikeSharingWeb/package.json][package-json] z zależnością dla pakietu *url.*

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Zaktualizuj metodę *getApiHostAsync* w [BikeSharingWeb/lib/helpers.js,][helpers-js] aby używać protokołu HTTPS:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Przejdź do `BikeSharingWeb` katalogu i `azds up` użyj do uruchomienia zaktualizowanej usługi BikeSharingWeb.

```console
cd ../BikeSharingWeb/
azds up
```

Przejdź do przykładowej aplikacji w przestrzeni podrzędnej *dev/azureuser1* i zwróć uwagę, że jesteś przekierowywane do korzystania z protokołu HTTPS bez żadnych błędów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć tworzenie współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych przestrzeniach.

> [!div class="nextstepaction"]
> [Tworzenie zespołów w usłudze Azure Dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml