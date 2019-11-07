---
title: Korzystanie z niestandardowego kontrolera traefik Ingres i Konfigurowanie protokołu HTTPS
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/13/2019
ms.topic: conceptual
description: Dowiedz się, jak skonfigurować Azure Dev Spaces, aby użyć niestandardowego kontrolera traefikal i skonfigurować protokół HTTPS za pomocą tego kontrolera
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, Helm, Service siatk, Service siatk Routing, polecenia kubectl, k8s
ms.openlocfilehash: c015fe8e7108f07d66d2464c4f8b6287e8f54446
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582323"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Korzystanie z niestandardowego kontrolera traefik Ingres i Konfigurowanie protokołu HTTPS

W tym artykule opisano sposób konfigurowania Azure Dev Spaces do używania niestandardowego kontrolera traefik. W tym artykule pokazano również, jak skonfigurować ten niestandardowy kontroler komunikacji przychodzącej do korzystania z protokołu HTTPS.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz, możesz utworzyć [bezpłatne konto][azure-account-create].
* [Zainstalowany interfejs wiersza polecenia platformy Azure][az-cli].
* [Klaster usługi Azure Kubernetes Service (AKS) z włączonym Azure dev Spaces][qs-cli].
* [polecenia kubectl][kubectl] .
* [Helm 2,13 lub nowszy][helm-installed].
* [Domena niestandardowa][custom-domain] ze [strefą DNS][dns-zone] w tej samej grupie zasobów co klaster AKS.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Konfigurowanie niestandardowego kontrolera traefik Ingres

Nawiąż połączenie z klastrem za pomocą [polecenia kubectl][kubectl], Kubernetes klienta wiersza polecenia. Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Aby sprawdzić połączenie z klastrem, użyj polecenia [kubectl get][kubectl-get], aby powrócić do listy węzłów klastra.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Utwórz przestrzeń nazw Kubernetes dla kontrolera traefik transferu danych przychodzących i zainstaluj ją przy użyciu `helm`.

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

Pobierz adres IP usługi traefik Ingres Controller przy użyciu funkcji [polecenia kubectl Get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

Przykładowe dane wyjściowe przedstawiają adresy IP dla wszystkich usług w przestrzeni nazw *traefik* .

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Dodawanie rekordu *A* do strefy DNS z zewnętrznym adresem IP usługi traefik przy użyciu polecenia [AZ Network DNS record-Set A Add-Record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

Powyższy przykład dodaje rekord *A* do strefy DNS *MY_CUSTOM_DOMAIN* .

W tym artykule użyto [przykładowej aplikacji do udostępniania Azure dev Spaces roweru](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) do zademonstrowania przy użyciu Azure dev Spaces. Klonowanie aplikacji z usługi GitHub i przechodzenie do jej katalogu:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Otwórz [wartość. YAML][values-yaml] i Zastąp wszystkie wystąpienia *< REPLACE_ME_WITH_HOST_SUFFIX >* z *traefik. MY_CUSTOM_DOMAIN* przy użyciu domeny do *MY_CUSTOM_DOMAIN*. Zastąp także *Kubernetes.IO/Ingress.Class: traefik-azds # dev Spaces-swoisty* dla *Kubernetes.IO/Ingress.Class: traefik # Custom*. Poniżej znajduje się przykładowy zaktualizowany plik `values.yaml`:

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

Wdróż przykładową aplikację przy użyciu `helm install`.

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

Powyższy przykład służy do wdrażania przykładowej aplikacji w przestrzeni nazw *dev* .

Wybierz przestrzeń *dev* z przykładową aplikacją przy użyciu `azds space select` i Wyświetl adresy URL, aby uzyskać dostęp do przykładowej aplikacji przy użyciu `azds list-uris`.

```console
azds space select -n dev
azds list-uris
```

Poniższe dane wyjściowe pokazują przykładowe adresy URL z `azds list-uris`.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Przejdź do usługi *bikesharingweb* , otwierając publiczny adres URL za pomocą polecenia `azds list-uris`. W powyższym przykładzie publiczny adres URL dla usługi *bikesharingweb* jest `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

Użyj `azds space select` polecenie, aby utworzyć miejsce podrzędne w obszarze *dev* i wyświetlić listę adresów URL, aby uzyskać dostęp do podrzędnego obszaru dev.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Poniższe dane wyjściowe pokazują przykładowe adresy URL z `azds list-uris`, aby uzyskać dostęp do przykładowej aplikacji w miejscu dev *azureuser1* .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Przejdź do usługi *bikesharingweb* w podrzędnym obszarze deweloperskim *azureuser1* , otwierając publiczny adres URL za pomocą polecenia `azds list-uris`. W powyższym przykładzie, publiczny adres URL dla usługi *bikesharingweb* w *azureuser1* w potomnym obszarze deweloperskim jest `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Konfigurowanie kontrolera traefik Ingress do korzystania z protokołu HTTPS

Utwórz plik `dev-spaces/samples/BikeSharingApp/traefik-values.yaml` podobny do poniższego przykładu. Zaktualizuj wartość *e-mail* przy użyciu własnej poczty e-mail, która jest używana do generowania certyfikatu z szyfrowaniem.

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

Zaktualizuj usługę *traefik* przy użyciu `helm repo update` i łącznie z utworzonym plikiem *traefik-Values. YAML* .

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

Powyższe polecenie uruchamia nową wersję usługi traefik przy użyciu wartości z *traefik-Values. YAML* i usuwa poprzednią usługę. Usługa traefik tworzy również certyfikat TLS przy użyciu szyfrowania i uruchamia przekierowywanie ruchu internetowego do korzystania z protokołu HTTPS.

> [!NOTE]
> Uruchomienie nowej wersji usługi traefik może potrwać kilka minut. Postęp można sprawdzić przy użyciu `kubectl get pods --namespace traefik --watch`.

Przejdź do przykładowej aplikacji w obszarze podrzędnym *dev/azureuser1* i zwróć uwagę na to, że nastąpi przekierowanie do korzystania z protokołu HTTPS. Zauważ również, że strona jest ładowana, ale w przeglądarce są wyświetlane pewne błędy. Otwarcie konsoli przeglądarki pokazuje błąd dotyczący strony HTTPS próbującej załadować zasoby HTTP. Na przykład:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Aby naprawić ten błąd, zaktualizuj [BikeSharingWeb/azds. YAML][azds-yaml] , aby użyć *traefik* dla *Kubernetes.IO/Ingress.class* i niestandardowej domeny dla *$ (hostSuffix)* . Na przykład:

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

Zaktualizuj plik [BikeSharingWeb/Package. JSON][package-json] z zależnością dla pakietu *URL* .

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Zaktualizuj metodę *getApiHostAsync* w [BikeSharingWeb/Pages/helps. js][helpers-js] , aby używać protokołu https:

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

Przejdź do katalogu `BikeSharingWeb` i użyj `azds up`, aby uruchomić zaktualizowaną usługę BikeSharingWeb.

```console
cd BikeSharingWeb/
azds up
```

Przejdź do przykładowej aplikacji w obszarze podrzędnym *dev/azureuser1* i zwróć uwagę na to, że nastąpi przekierowanie do korzystania z protokołu HTTPS bez żadnych błędów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak Azure Dev Spaces ułatwiają tworzenie bardziej złożonych aplikacji w wielu kontenerach i jak można uprościć programowanie do współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych miejscach.

> [!div class="nextstepaction"]
> [Programowanie zespołowe w Azure Dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml