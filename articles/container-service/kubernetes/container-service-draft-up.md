---
title: (PRZESTARZAŁE) Korzystanie z wersji roboczej z usługą Azure Container Service i rejestrem kontenerów platformy Azure
description: Utwórz klaster ACS Kubernetes i rejestr Azure Container Registry, aby utworzyć swoją pierwszą aplikację na platformie Azure z użyciem narzędzia Draft.
author: squillace
ms.service: container-service
ms.topic: conceptual
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 8d688d2918c9100019d033e93e9a3dca9e492de2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271132"
---
# <a name="deprecated-use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>(PRZESTARZAŁE) Tworzenie i wdrażanie aplikacji w usłudze Kubernetes za pomocą usługi roboczej z usługą Azure Container Service i rejestrem kontenerów platformy Azure

> [!TIP]
> Aby zapoznać się ze zaktualizowaną wersją tego artykułu, która korzysta z usługi Azure Kubernetes, zobacz [Korzystanie z wersji roboczej z usługą Azure Kubernetes Service (AKS)](../../aks/kubernetes-draft.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Draft](https://aka.ms/draft) to nowe narzędzie open-source, które ułatwia programowanie aplikacji bazujących na kontenerach i wdrażanie ich w klastrach Kubernetes bez bliższej znajomości rozwiązań Docker i Kubernetes (a nawet bez ich instalacji). Narzędzia takie jak Draft pozwalają Tobie i Twoim zespołom skupiać się na tworzeniu aplikacji z użyciem rozwiązania Kubernetes bez poświęcania większej uwagi infrastrukturze.

Narzędzia Draft można użyć z dowolnym rejestrem obrazów Docker i dowolnym klastrem Kubernetes, w tym lokalnym. W tym samouczku pokazano, jak używać usługi ACS z usługami Kubernetes i usługi ACR do tworzenia potoku dewelopera na żywo, ale bezpiecznego w usłudze Kubernetes przy użyciu wersji roboczej oraz sposobu używania usługi Azure DNS do udostępnienia tego potoku dewelopera dla innych osób w domenie.


## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry
Możesz z łatwością [utworzyć nowy rejestr Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md), przy czym kroki są następujące:

1. Utwórz grupę zasobów platformy Azure do zarządzania rejestrem usługi ACR i klastrem Kubernetes w usłudze ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Utwórz rejestr obrazów ACR za pomocą [az acr utworzyć](/cli/azure/acr#az-acr-create) i upewnij się, że `--admin-enabled` opcja jest ustawiona na `true`.
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


1. Pobierz wersję roboczą https://github.com/Azure/draft/releases dla swojego środowiska i zainstaluj ją w ścieżce, aby można było użyć polecenia.
2. Pobierz helm dla https://github.com/kubernetes/helm/releases swojego środowiska i [zainstaluj go w ścieżce, aby można było użyć polecenia.](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client)
3. Skonfiguruj narzędzie Draft do korzystania z Twojego rejestru i tworzenia poddomen dla każdego planu Helm, które to narzędzie utworzy. Aby skonfigurować narzędzie Draft, potrzebne są:
   - nazwa rejestru Azure Container Registry (w tym przykładzie: `draftacsdemo`);
   - klucz rejestru lub hasło z polecenia `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`;

   Wywołanie `draft init` i proces konfiguracji monituje o powyższe wartości; Należy zauważyć, że format adresu URL adresu URL rejestru `draftacsdemo`jest `.azurecr.io`nazwą rejestru (w tym przykładzie) plus . Twoja nazwa użytkownika to nazwa rejestru sama w sobie. Gdy proces uruchomisz po raz pierwszy, będzie on wyglądać podobnie do następującego.
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

W repozytorium narzędzia Draft znajduje się [sześć prostych, przykładowych aplikacji](https://github.com/Azure/draft/tree/master/examples). Sklonuj repozytorium i użyjmy [przykładu Java](https://github.com/Azure/draft/tree/master/examples/example-java). Zmień w katalogu examples/java i `draft create` wpisz, aby utworzyć aplikację. Powinno to wyglądać podobnie jak na poniższym przykładzie.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

Dane wyjściowe obejmują plik Docker i plan Helm. W celu skompilowania i wdrożenia wystarczy wpisać polecenie `draft up`. Dane wyjściowe są obszerne, ale powinny być podobne do poniższego przykładu.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Bezpieczne wyświetlanie aplikacji

Kontener jest teraz uruchomiony w usłudze ACS. Aby go wyświetlić, należy użyć `draft connect` polecenia, które tworzy zabezpieczone połączenie z adresem IP klastra z określonym portem dla aplikacji, dzięki czemu można go wyświetlić lokalnie. Jeśli to się powiedzie, poszukaj adresu URL, aby połączyć się z aplikacją w pierwszym wierszu po wskaźniku **SUKCESU.**

> [!NOTE]
> Jeśli pojawi się komunikat informujący, że żadne zasobniki nie były gotowe, poczekaj chwilę `kubectl get pods -w` i ponów próbę, lub możesz obejrzeć, jak zasobniki stają się gotowe, a następnie ponów próbę, gdy to zrobią.

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

W poprzednim przykładzie można `curl -s http://localhost:46143` wpisać, `Hello World, I'm Java!`aby otrzymać odpowiedź, . Po ctrl + lub CMD + C (w zależności od środowiska systemu operacyjnego), bezpieczny tunel jest rozdarty i można kontynuować iteracji.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Udostępnianie aplikacji przez skonfigurowanie domeny wdrażania za pomocą usługi Azure DNS

Wykonano już pętlę iteracji dewelopera, którą tworzy wersja robocza w poprzednich krokach. Można jednak udostępnić aplikację w Internecie, korzystając z:
1. Instalowanie transferu przychodzącego w klastrze acs (aby podać publiczny adres IP, pod którym ma być wyświetlana aplikacja)
2. Delegowanie domeny niestandardowej do usługi Azure DNS i mapowanie domeny na adres IP acs przypisuje do kontrolera transferu danych przychodzących

### <a name="use-helm-to-install-the-ingress-controller"></a>Użyj helma, aby zainstalować kontroler ruchu przychodzącego.
Użyj **helm** do wyszukiwania `stable/traefik`i instalowania, kontrolera transferu danych przychodzących, aby włączyć przychodzące żądania dla kompilacji.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Teraz ustaw wyrażenie kontrolne na kontrolerze `ingress`, aby przechwycić wartość zewnętrznego adresu IP po jego wdrożeniu. Ten adres IP zostanie zamapowany na domenę wdrażania w następnej sekcji.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

W tym przypadku zewnętrzny adres IP dla domeny wdrażania to `13.64.108.240`. Teraz możesz zamapować swoją domenę na ten adres IP.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Mapowanie adresu IP transferu przychodzącego na niestandardową poddomenę

Narzędzie Draft tworzy wydanie dla każdego tworzonego planu Helm, czyli każdej aplikacji, nad którą pracujesz. Każdy z nich otrzymuje wygenerowaną nazwę, która jest używana przez **wersje robocze** jako _poddomenę_ u góry _kontrolowanej domeny wdrożenia_ głównego. (W tym przykładzie `squillace.io` używamy jako domeny wdrażania). Aby włączyć to zachowanie poddomeny, należy `'*.draft'` utworzyć rekord A we wpisach DNS dla domeny wdrożenia, tak aby każda wygenerowana poddomena była kierowana do kontrolera transferu danych przychodzących klastra Kubernetes. 

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
3. Dodaj uzyskane serwery DNS do dostawcy domeny dla Twojej domeny wdrażania, co pozwoli używać usługi Azure DNS do dowolnego zmieniania sposobu wskazywania Twojej domeny. Sposób, w jaki to robisz, różni się w zależności od domeny; [delegować serwery nazw domeny do usługi Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) zawiera niektóre szczegóły, które należy znać. 
4. Po przekazaniu domeny do usługi Azure DNS utwórz wpis A dla mapowania domeny wdrożenia na `ingress` adres IP z kroku 2 poprzedniej sekcji.
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
5. Ponowne instalowanie **wersji roboczej**

   1. Usuń **pochylenia** z `helm delete --purge draft`klastra, wpisując . 
   2. Zainstaluj ponownie **pochyłość** za pomocą tego samego `draft-init` polecenia, ale z `--ingress-enabled` opcją:
      ```bash
      draft init --ingress-enabled
      ```
      Odpowiadaj na monity, tak jak za pierwszym razem powyżej. Jednak masz jeszcze jedno pytanie, na które należy odpowiedzieć, używając pełnej ścieżki domeny skonfigurowaną za pomocą usługi Azure DNS.

6. Wprowadź domenę najwyższego poziomu dla ruchu przychodzącego (np. draft.example.com): draft.squillace.io
7. Po wywołaniu `draft up` tej chwili, będzie można zobaczyć `curl` aplikację (lub go) `<appname>.draft.<domain>.<top-level-domain>`pod adresem URL formularza . W przypadku tego przykładu , `http://handy-labradoodle.draft.squillace.io`. 
   ```bash
   curl -s http://handy-labradoodle.draft.squillace.io
   Hello World, I'm Java!
   ```


## <a name="next-steps"></a>Następne kroki

Teraz, gdy już masz klaster ACS Kubernetes, możesz przyjrzeć się sposobie korzystania z usługi [Azure Container Registry](../../container-registry/container-registry-intro.md), aby utworzyć więcej innych wdrożeń tego scenariusza. Na przykład możesz utworzyć zestaw rekordów usługi DNS dla domeny draft._basedomain.toplevel_, który steruje działaniem na poziomie głębszej poddomeny dla specyficznych wdrożeń usługi ACS.






