---
title: (PRZESTARZAŁE) Projekt za pomocą usługi Azure Container Service i Azure Container Registry
description: Utwórz klaster ACS Kubernetes i rejestr Azure Container Registry, aby utworzyć swoją pierwszą aplikację na platformie Azure z użyciem narzędzia Draft.
services: container-service
author: squillace
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: fb34be09ec08957621517c957b3570cdbcfc0468
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787163"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>(PRZESTARZAŁE) Za pomocą projektu usługi Azure Container Service i Azure Container Registry do tworzenia i wdrażania aplikacji w rozwiązaniu Kubernetes

> [!TIP]
> Aby uzyskać zaktualizowaną wersję tego artykułu, który korzysta z usługi Azure Kubernetes Service, zobacz [użycie wersji próbnej za pomocą usługi Azure Kubernetes Service (AKS)](../../aks/kubernetes-draft.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Draft](https://aka.ms/draft) to nowe narzędzie open-source, które ułatwia programowanie aplikacji bazujących na kontenerach i wdrażanie ich w klastrach Kubernetes bez bliższej znajomości rozwiązań Docker i Kubernetes (a nawet bez ich instalacji). Narzędzia takie jak Draft pozwalają Tobie i Twoim zespołom skupiać się na tworzeniu aplikacji z użyciem rozwiązania Kubernetes bez poświęcania większej uwagi infrastrukturze.

Narzędzia Draft można użyć z dowolnym rejestrem obrazów Docker i dowolnym klastrem Kubernetes, w tym lokalnym. W tym samouczku pokazano, jak utworzyć potok na żywo, ale bezpieczny dla deweloperów w usłudze Kubernetes za pomocą narzędzia Draft, za pomocą usługi ACS z usługami Kubernetes i usługi ACR i jak używać usługi Azure DNS do udostępnienia tego potoku dla deweloperów, aby inne osoby zobaczyły w domenie.


## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry
Możesz z łatwością [utworzyć nowy rejestr Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md), przy czym kroki są następujące:

1. Utwórz grupę zasobów platformy Azure, aby zarządzać rejestrem ACR i klastrem Kubernetes usługi ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Tworzenie rejestru Azure container Registry obraz rejestru za pomocą [tworzenie az acr](/cli/azure/acr#az-acr-create) i upewnij się, że `--admin-enabled` ustawiono opcję `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Tworzenie usługi Azure Container Service za pomocą rozwiązania Kubernetes

Teraz można już użyć polecenia [az acs create](/cli/azure/acs#az-acs-create) do utworzenia klastra ACS za pomocą rozwiązania Kubernetes jako wartości `--orchestrator-type`.
```azurecli
az acs create --resource-group draft --name draft-kube-acs --dns-prefix draft-cluster --orchestrator-type kubernetes --generate-ssh-keys
```

> [!NOTE]
> Ponieważ rozwiązanie Kubernetes nie jest domyślnym typem koordynatora, pamiętaj o użyciu przełącznika `--orchestrator-type kubernetes`.

Dane wyjściowe po pomyślnym wykonaniu polecenia wyglądają podobnie do następujących.

```json
waiting for AAD role to propagate.done
{
  "id": "/subscriptions/<guid>/resourceGroups/draft/providers/Microsoft.Resources/deployments/azurecli14904.93snip09",
  "name": "azurecli1496227204.9323909",
  "properties": {
    "correlationId": "<guid>",
    "debugSetting": null,
    "dependencies": [],
    "mode": "Incremental",
    "outputs": null,
    "parameters": {
      "clientSecret": {
        "type": "SecureString"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.ContainerService",
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "locations": [
              "westus"
            ],
            "properties": null,
            "resourceType": "containerServices"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "template": null,
    "templateLink": null,
    "timestamp": "2017-05-31T10:46:29.434095+00:00"
  },
  "resourceGroup": "draft"
}
```

Po utworzeniu klastra można zaimportować poświadczenia za pomocą polecenia [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes). Zostanie utworzony lokalny plik konfiguracji klastra, którego narzędzia Helm i Draft wymagają do wykonania pracy.

## <a name="install-and-configure-draft"></a>Instalowanie i konfigurowanie narzędzia Draft


1. Pobierz wersję roboczą w danym środowisku, w https://github.com/Azure/draft/releases i zainstalować w ŚCIEŻCE, tak, że polecenia mogą być używane.
2. Pobierz narzędzia helm dla środowiska w https://github.com/kubernetes/helm/releases i [go zainstalować w ŚCIEŻCE, dzięki czemu można użyć polecenia](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Skonfiguruj narzędzie Draft do korzystania z Twojego rejestru i tworzenia poddomen dla każdego planu Helm, które to narzędzie utworzy. Aby skonfigurować narzędzie Draft, potrzebne są:
   - nazwa rejestru Azure Container Registry (w tym przykładzie: `draftacsdemo`);
   - klucz rejestru lub hasło z polecenia `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`;

   Wywołaj `draft init` i procesu konfiguracji wyświetli monit o podanie wartości wymienione powyżej; należy pamiętać, że adres URL format adresu URL rejestru jest nazwą rejestru (w tym przykładzie `draftacsdemo`) oraz `.azurecr.io`. Nazwa użytkownika jest nazwą rejestru samodzielnie. Gdy proces uruchomisz po raz pierwszy, będzie on wyglądać podobnie do następującego.
   ```bash
    $ draft init
    Creating /home/ralph/.draft 
    Creating /home/ralph/.draft/plugins 
    Creating /home/ralph/.draft/packs 
    Creating pack go...
    Creating pack python...
    Creating pack ruby...
    Creating pack javascript...
    Creating pack gradle...
    Creating pack java...
    Creating pack php...
    Creating pack csharp...
    $DRAFT_HOME has been configured at /home/ralph/.draft.

    In order to configure Draft, we need a bit more information...

    1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): draftacsdemo.azurecr.io
    2. Enter your username: draftacsdemo
    3. Enter your password: 
    Draft has been installed into your Kubernetes Cluster.
    Happy Sailing!
   ```

Teraz możesz wdrożyć aplikację.


## <a name="build-and-deploy-an-application"></a>Kompilowanie i wdrażanie aplikacji

W repozytorium narzędzia Draft znajduje się [sześć prostych, przykładowych aplikacji](https://github.com/Azure/draft/tree/master/examples). Sklonuj repozytorium i posłużmy [przykładzie w języku Java](https://github.com/Azure/draft/tree/master/examples/example-java). Przejdź do katalogu przykłady/java i typ `draft create` do skompilowania aplikacji. Powinno to wyglądać podobnie jak na poniższym przykładzie.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

Dane wyjściowe obejmują plik Docker i plan Helm. W celu skompilowania i wdrożenia wystarczy wpisać polecenie `draft up`. Dane wyjściowe są obszerne, ale powinien być jak w poniższym przykładzie.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Bezpiecznego przeglądania aplikacji

Kontener jest teraz uruchomiona w usłudze ACS. Aby go wyświetlić, należy użyć `draft connect` polecenia, które tworzy połączenia zabezpieczonego adres IP klastra z określonego portu dla aplikacji, tak, aby mogły go wyświetlać lokalnie. Jeśli to się powiedzie, Wyszukaj adres URL, aby nawiązać połączenie z aplikacją w pierwszym wierszu po **Powodzenie** wskaźnika.

> [!NOTE]
> Jeśli zostanie wyświetlony komunikat z informacją, że nie zasobników były gotowe, poczekaj, aż moment i spróbuj ponownie lub możesz obejrzeć zasobników gotowość z `kubectl get pods -w` a następnie spróbuj ponownie, gdy robią.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

W poprzednim przykładzie, można wpisać `curl -s http://localhost:46143` i uzyskać odpowiedź, `Hello World, I'm Java!`. Gdy ci CTRL + lub CMD + C (w zależności od środowiska systemu operacyjnego) bezpiecznego tunelu jest niszczona, i będziesz kontynuować, iteracja.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Twoja aplikacja do udostępniania, konfigurując domeny wdrażania za pomocą usługi Azure DNS

Już przeprowadzono pętli iteracji dla deweloperów, które narzędzie Draft tworzy w poprzednich krokach. Jednak można udostępniać aplikacji w Internecie przez:
1. Instalowanie ruchu przychodzącego w klastrze usługi ACS (w celu udostępnienia publicznego adresu IP, na którym ma zostać wyświetlona aplikacja)
2. Delegowanie domeny niestandardowej do usługi Azure DNS i mapowanie domenę na adres IP adres ACS przypisuje się do kontrolera danych przychodzących

### <a name="use-helm-to-install-the-ingress-controller"></a>Użyj narzędzia helm, aby zainstalować kontroler danych przychodzących.
Użyj **helm** aby wyszukać i zainstalować `stable/traefik`, kontroler danych przychodzących w celu umożliwienia obsługi żądań przychodzących dla kompilacji.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Teraz ustaw wyrażenie kontrolne na kontrolerze `ingress`, aby przechwycić wartość zewnętrznego adresu IP po jego wdrożeniu. Ten adres IP będzie mapowany na domenę wdrażania w następnej sekcji.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

W tym przypadku zewnętrzny adres IP dla domeny wdrażania to `13.64.108.240`. Teraz możesz zamapować swoją domenę na ten adres IP.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Mapowanie adresu IP ruchu przychodzącego na niestandardowej domeny podrzędnej

Narzędzie Draft tworzy wydanie dla każdego tworzonego planu Helm, czyli każdej aplikacji, nad którą pracujesz. Każde z nich otrzymuje wygenerowaną nazwę, która jest używana przez **projekt** jako _poddomeny_ na podstawie głównego _domeny wdrażania_ przez Ciebie. (W tym przykładzie jako domeny wdrażania używamy domeny `squillace.io`). Aby włączyć takie zachowanie poddomeny, musisz utworzyć rekord A dla wartości `'*.draft'` we wpisach usługi DNS dla swojej domeny wdrażania, aby każda wygenerowana poddomena była kierowana do kontrolera danych przychodzących rozwiązania Kubernetes. 

Twój dostawca domeny ma swój własny sposób przypisywania serwerów DNS. Aby [wydelegować serwery nazw Twojej domeny do usługi Azure DNS](../../dns/dns-delegate-domain-azure-dns.md), musisz wykonać następujące kroki:

1. Utwórz grupę zasobów dla swojej strefy.
    ```azurecli
    az group create --name squillace.io --location eastus
    {
      "id": "/subscriptions/<guid>/resourceGroups/squillace.io",
      "location": "eastus",
      "managedBy": null,
      "name": "zones",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "tags": null
    }
    ```

2. Utwórz strefę DNS dla swojej domeny.
   Użyj polecenia [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create), aby uzyskać serwery nazw w celu delegowania kontroli DNS do usługi Azure DNS dla Twojej domeny.
    ```azurecli
    az network dns zone create --resource-group squillace.io --name squillace.io
    {
      "etag": "<guid>",
      "id": "/subscriptions/<guid>/resourceGroups/zones/providers/Microsoft.Network/dnszones/squillace.io",
      "location": "global",
      "maxNumberOfRecordSets": 5000,
      "name": "squillace.io",
      "nameServers": [
        "ns1-09.azure-dns.com.",
        "ns2-09.azure-dns.net.",
        "ns3-09.azure-dns.org.",
        "ns4-09.azure-dns.info."
      ],
      "numberOfRecordSets": 2,
      "resourceGroup": "squillace.io",
      "tags": {},
      "type": "Microsoft.Network/dnszones"
    }
    ```
3. Dodaj uzyskane serwery DNS do dostawcy domeny dla Twojej domeny wdrażania, co pozwoli używać usługi Azure DNS do dowolnego zmieniania sposobu wskazywania Twojej domeny. Można to zrobić zależy od typu domeny podać; [delegować serwery nazw Twojej domeny w celu usługi Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) zawiera niektóre szczegółowe informacje, które należy znać. 
4. Po delegowania domeny do usługi Azure DNS, Utwórz wpis zestawu rekordów A dla mapowania swojej domeny wdrażania, aby `ingress` adres IP z kroku 2 w poprzedniej sekcji.
   ```azurecli
   az network dns record-set a add-record --ipv4-address 13.64.108.240 --record-set-name '*.draft' -g squillace.io -z squillace.io
   ```
   Dane wyjściowe wyglądają podobnie do tych:
   ```json
   {
    "arecords": [
      {
        "ipv4Address": "13.64.108.240"
      }
    ],
    "etag": "<guid>",
    "id": "/subscriptions/<guid>/resourceGroups/squillace.io/providers/Microsoft.Network/dnszones/squillace.io/A/*",
    "metadata": null,
    "name": "*.draft",
    "resourceGroup": "squillace.io",
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/A"
   }
   ```
5. Zainstaluj ponownie **wersję roboczą**

   1. Usuń **draftd** z klastra, wpisując `helm delete --purge draft`. 
   2. Zainstaluj ponownie **projekt** , korzystając z tych samych `draft-init` polecenia, ale z `--ingress-enabled` opcji:
      ```bash
      draft init --ingress-enabled
      ```
      Odpowiedz na monity, tak jak po raz pierwszy, powyżej. Jednak mieć jedno pytanie więcej odpowiedzi, przy użyciu ścieżki kompletna domena, który został skonfigurowany za pomocą usługi Azure DNS.

6. Wprowadź domenę najwyższego poziomu dla danych przychodzących (np. draft.example.com): draft.squillace.io
7. Gdy wywołujesz `draft up` ten czas można wyświetlić aplikację (lub `curl` go) pod adresem URL w postaci `<appname>.draft.<domain>.<top-level-domain>`. W tym przykładzie `http://handy-labradoodle.draft.squillace.io`. 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy już masz klaster ACS Kubernetes, możesz przyjrzeć się sposobie korzystania z usługi [Azure Container Registry](../../container-registry/container-registry-intro.md), aby utworzyć więcej innych wdrożeń tego scenariusza. Na przykład możesz utworzyć zestaw rekordów usługi DNS dla domeny draft._basedomain.toplevel_, który steruje działaniem na poziomie głębszej poddomeny dla specyficznych wdrożeń usługi ACS.






