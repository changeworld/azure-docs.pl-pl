---
title: Użyj projekt z usługą kontenera Azure i Azure rejestru kontenera
description: Utwórz klaster ACS Kubernetes i rejestr Azure Container Registry, aby utworzyć swoją pierwszą aplikację na platformie Azure z użyciem narzędzia Draft.
services: container-service
author: squillace
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 09/14/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 68ad44bae0856ff000f2847049a15a946d83c0a3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="use-draft-with-azure-container-service-and-azure-container-registry-to-build-and-deploy-an-application-to-kubernetes"></a>Użycie narzędzia Draft z usługami Azure Container Service i Azure Container Registry w celu utworzenia i wdrożenia aplikacji w rozwiązaniu Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Draft](https://aka.ms/draft) to nowe narzędzie open-source, które ułatwia programowanie aplikacji bazujących na kontenerach i wdrażanie ich w klastrach Kubernetes bez bliższej znajomości rozwiązań Docker i Kubernetes (a nawet bez ich instalacji). Narzędzia takie jak Draft pozwalają Tobie i Twoim zespołom skupiać się na tworzeniu aplikacji z użyciem rozwiązania Kubernetes bez poświęcania większej uwagi infrastrukturze.

Narzędzia Draft można użyć z dowolnym rejestrem obrazów Docker i dowolnym klastrem Kubernetes, w tym lokalnym. Ten samouczek pokazuje, jak używać usług ACS z Kubernetes i ACR utworzyć potok dewelopera na żywo, ale bezpieczny w Kubernetes przy użyciu wersji roboczej i jak używać usługi Azure DNS do udostępnienia tego potoku developer dla innych osób w domenie.


## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry
Możesz z łatwością [utworzyć nowy rejestr Azure Container Registry](../../container-registry/container-registry-get-started-azure-cli.md), przy czym kroki są następujące:

1. Utwórz grupę zasobów Azure, aby zarządzać rejestrem ACR i klastrem Kubernetes w usłudze ACS.
      ```azurecli
      az group create --name draft --location eastus
      ```

2. Tworzenie ACR obrazu rejestru przy użyciu [utworzyć az acr](/cli/azure/acr#az_acr_create) i upewnij się, że `--admin-enabled` ustawiono opcję `true`.
      ```azurecli
      az acr create --resource-group draft --name draftacs --sku Basic
      ```


## <a name="create-an-azure-container-service-with-kubernetes"></a>Tworzenie usługi Azure Container Service za pomocą rozwiązania Kubernetes

Teraz można już użyć polecenia [az acs create](/cli/azure/acs#az_acs_create) do utworzenia klastra ACS za pomocą rozwiązania Kubernetes jako wartości `--orchestrator-type`.
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

Po utworzeniu klastra można zaimportować poświadczenia za pomocą polecenia [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). Zostanie utworzony lokalny plik konfiguracji klastra, którego narzędzia Helm i Draft wymagają do wykonania pracy.

## <a name="install-and-configure-draft"></a>Instalowanie i konfigurowanie narzędzia Draft


1. Pobieranie projektu dla danego środowiska na https://github.com/Azure/draft/releases i zainstalować w ŚCIEŻCE, dzięki czemu można użyć polecenia.
2. Pobierz helm dla danego środowiska na https://github.com/kubernetes/helm/releases i [zainstalować w ŚCIEŻCE, dzięki czemu można użyć polecenia](https://github.com/kubernetes/helm/blob/master/docs/install.md#installing-the-helm-client).
3. Skonfiguruj narzędzie Draft do korzystania z Twojego rejestru i tworzenia poddomen dla każdego planu Helm, które to narzędzie utworzy. Aby skonfigurować narzędzie Draft, potrzebne są:
  - nazwa rejestru Azure Container Registry (w tym przykładzie: `draftacsdemo`);
  - klucz rejestru lub hasło z polecenia `az acr credential show -n <registry name> --output tsv --query "passwords[0].value"`;

  Wywołanie `draft init` i procesu konfiguracji wyświetla monit o podanie wartości powyżej; należy zauważyć, że adres URL format adresu URL rejestru jest nazwa rejestru (w tym przykładzie `draftacsdemo`) oraz `.azurecr.io`. Nazwa użytkownika jest nazwą rejestru samodzielnie. Gdy proces uruchomisz po raz pierwszy, będzie on wyglądać podobnie do następującego.
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

W repozytorium narzędzia Draft znajduje się [sześć prostych, przykładowych aplikacji](https://github.com/Azure/draft/tree/master/examples). Klonowanie repozytorium i Użyjmy [przykładzie w języku Java](https://github.com/Azure/draft/tree/master/examples/java). Zmiany w katalogu przykładów/java i typ `draft create` do skompilowania aplikacji. Powinno to wyglądać podobnie jak na poniższym przykładzie.
```bash
$ draft create
--> Draft detected the primary language as Java with 91.228814% certainty.
--> Ready to sail
```

Dane wyjściowe obejmują plik Docker i plan Helm. W celu skompilowania i wdrożenia wystarczy wpisać polecenie `draft up`. Dane wyjściowe są obszerne, ale powinien być jak w następującym przykładzie.
```bash
$ draft up
Draft Up Started: 'handy-labradoodle'
handy-labradoodle: Building Docker Image: SUCCESS ⚓  (35.0232s)
handy-labradoodle: Pushing Docker Image: SUCCESS ⚓  (17.0062s)
handy-labradoodle: Releasing Application: SUCCESS ⚓  (3.8903s)
handy-labradoodle: Build ID: 01BT0ZJ87NWCD7BBPK4Y3BTTPB
```

## <a name="securely-view-your-application"></a>Bezpiecznego przeglądania aplikacji

Teraz działa z kontenera usługi ACS. Aby go wyświetlić, należy użyć `draft connect` polecenia, który tworzy połączenie zabezpieczonych adres IP klastra z określonym portem dla aplikacji, dzięki czemu można je wyświetlać lokalnie. Jeśli to się powiedzie, Wyszukaj adres URL do nawiązania połączenia aplikacji w pierwszym wierszu po **Powodzenie** wskaźnika.

> [!NOTE]
> Jeśli zostanie wyświetlony komunikat informujący o tym, że nie stanowiskami były gotowe, zaczekaj chwilę i spróbuj ponownie, lub możesz obserwować stanowiskami gotowość z `kubectl get pods -w` , a następnie spróbuj ponownie, gdy tak robią.

```bash
draft connect
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

W poprzednim przykładzie, można wpisać `curl -s http://localhost:46143` odebrać odpowiedzi, `Hello World, I'm Java!`. Gdy CTRL + lub CMD + C (w zależności od środowiska systemu operacyjnego), które będzie działo bezpieczny tunel i można kontynuować iteracja.

## <a name="sharing-your-application-by-configuring-a-deployment-domain-with-azure-dns"></a>Skonfigurowanie domeny wdrożenia usługi Azure DNS do udostępniania usługi aplikacji

Już przeprowadzono pętli iteracji developer, która tworzy projekt w poprzednich krokach. Jednak można udostępniać aplikacji w Internecie przez:
1. Instalowanie transfer danych przychodzących z klastrem usługi ACS (w celu zapewnienia publicznego adresu IP, w którym ma zostać wyświetlona aplikacja)
2. Delegowanie domeny niestandardowej do usługi Azure DNS i mapowanie domenę na adres IP adres ACS przypisuje do kontrolera wejściowych

### <a name="use-helm-to-install-the-ingress-controller"></a>Helm należy zainstalować na kontrolerze transfer danych przychodzących.
Użyj **helm** na wyszukiwanie i instalowanie `stable/traefik`, kontrolera transfer danych przychodzących, aby umożliwić żądania przychodzące kompilacji.
```bash
$ helm search traefik
NAME            VERSION DESCRIPTION
stable/traefik  1.3.0   A Traefik based Kubernetes ingress controller w...

$ helm install stable/traefik --name ingress
```
Teraz ustaw wyrażenie kontrolne na kontrolerze `ingress`, aby przechwycić wartość zewnętrznego adresu IP po jego wdrożeniu. Ten adres IP zostanie [zamapowany na domenę wdrażania](#wire-up-deployment-domain) w następnej sekcji.

```bash
$ kubectl get svc -w
NAME                          CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
ingress-traefik               10.0.248.104   13.64.108.240   80:31046/TCP,443:32556/TCP   1h
kubernetes                    10.0.0.1       <none>          443/TCP                      7h
```

W tym przypadku zewnętrzny adres IP dla domeny wdrażania to `13.64.108.240`. Teraz możesz zamapować swoją domenę na ten adres IP.

### <a name="map-the-ingress-ip-to-a-custom-subdomain"></a>Mapowanie IP ruch przychodzący do niestandardowych poddomeny

Narzędzie Draft tworzy wydanie dla każdego tworzonego planu Helm, czyli każdej aplikacji, nad którą pracujesz. Każda z nich zostanie wygenerowana nazwa, który jest używany przez **projekt** jako _poddomeny_ na katalog główny _domeny wdrożenia_ przez Ciebie harmonogramem. (W tym przykładzie jako domeny wdrażania używamy domeny `squillace.io`). Aby włączyć takie zachowanie poddomeny, musisz utworzyć rekord A dla wartości `'*.draft'` we wpisach usługi DNS dla swojej domeny wdrażania, aby każda wygenerowana poddomena była kierowana do kontrolera danych przychodzących rozwiązania Kubernetes. 

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
Użyj polecenia [az network dns zone create](/cli/azure/network/dns/zone#az_network_dns_zone_create), aby uzyskać serwery nazw w celu delegowania kontroli DNS do usługi Azure DNS dla Twojej domeny.
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
3. Dodaj uzyskane serwery DNS do dostawcy domeny dla Twojej domeny wdrażania, co pozwoli używać usługi Azure DNS do dowolnego zmieniania sposobu wskazywania Twojej domeny. W tym zależy od typu domeny podać; [delegować nameservers użytkownika domeny do usługi Azure DNS](../../dns/dns-delegate-domain-azure-dns.md) zawiera niektóre szczegółowe informacje, które należy poznać. 
4. Po delegowania domeny do usługi Azure DNS należy utworzyć wpis zestaw rekordów A dla wdrożenia mapowanie domeny do `ingress` IP w kroku 2 opisanego w poprzedniej sekcji.
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
5. Zainstaluj ponownie **projektu**

   1. Usuń **draftd** z klastra, wpisując `helm delete --purge draft`. 
   2. Zainstaluj ponownie **projekt** przez korzystającej z tego samego `draft-init` polecenia, ale `--ingress-enabled` opcji:
    ```bash
    draft init --ingress-enabled
    ```
   Odpowiedz na monity tak samo jak po raz pierwszy, powyżej. Jednak mieć jedno pytanie więcej reagować, przy użyciu ścieżki pełne domeny skonfigurowaną z usługi Azure DNS.

6. Wprowadź domenę najwyższego poziomu transfer danych przychodzących (np. draft.example.com): draft.squillace.io
7. Podczas wywoływania `draft up` tego czasu można wyświetlić aplikacji (lub `curl` on) pod adresem URL w postaci `<appname>.draft.<domain>.<top-level-domain>`. W tym przykładzie `http://handy-labradoodle.draft.squillace.io`. 
```bash
curl -s http://handy-labradoodle.draft.squillace.io
Hello World, I'm Java!
```


## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy już masz klaster ACS Kubernetes, możesz przyjrzeć się sposobie korzystania z usługi [Azure Container Registry](../../container-registry/container-registry-intro.md), aby utworzyć więcej innych wdrożeń tego scenariusza. Na przykład możesz utworzyć zestaw rekordów usługi DNS dla domeny draft._basedomain.toplevel_, który steruje działaniem na poziomie głębszej poddomeny dla specyficznych wdrożeń usługi ACS.






