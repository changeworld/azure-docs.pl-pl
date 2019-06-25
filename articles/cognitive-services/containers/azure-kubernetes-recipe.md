---
title: Uruchamianie usługi Kubernetes
titleSuffix: Text Analytics -  Azure Cognitive Services
description: To wdrożenie kontenera wykrywania języka, przy użyciu przykładu uruchomione, usługi Azure Kubernetes Service i przetestuj ją w przeglądarce sieci web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 61a76412d7619784582e1fddd2a5b60244159ebc
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272937"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>Wdrażanie kontenera wykrywanie języka w usłudze Azure Kubernetes Service

Dowiedz się, jak wdrożyć kontener wykrywanie języka. Ta procedura pokazuje, jak tworzenie lokalnego kontenerów platformy Docker, Wypchnij kontenerów do rejestru kontenera prywatnych, uruchamiania kontenera w klastrze Kubernetes i przetestować go w przeglądarce sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura wymaga kilku narzędzi, które musi być zainstalowany i uruchamiane lokalnie. Nie należy używać usługi Azure Cloud shell.

* Korzystanie z subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* [Git](https://git-scm.com/downloads) systemu operacyjnego, dzięki czemu można sklonować [przykładowe](https://github.com/Azure-Samples/cognitive-services-containers-samples) używane w ramach tej procedury.
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Aparat platformy docker](https://www.docker.com/products/docker-engine) i Zweryfikuj, że interfejs wiersza polecenia platformy Docker działa w oknie konsoli.
* [narzędzia kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Zasób platformy Azure z warstwą cenową poprawne. Nie wszystkie warstwy cenowe pracować z tego kontenera:
  * **Analiza tekstu** zasobu o F0 lub standardowej ceny warstwy tylko.
  * **Usługi cognitive Services** warstwa cenowa zasobu z S0.

## <a name="running-the-sample"></a>Uruchamianie przykładowej aplikacji

Ta procedura ładuje i uruchamia kontener usług Cognitive przykład wykrywanie języka. Przykład ma dwa kontenery: jeden dla aplikacji klienckiej i jeden dla kontenera usług Cognitive Services. Należy do obu tych obrazów do własnych usługi Azure Container Registry. Gdy są one na własnego rejestru, należy utworzyć usługi Azure Kubernetes Service dostęp do tych obrazów i uruchamiaj kontenery. Gdy są uruchomione kontenery, należy użyć **kubectl** interfejsu wiersza polecenia, aby obejrzeć wydajności kontenerów. Dostęp do aplikacji klienckiej, za pomocą żądania HTTP i wyświetlić wyniki.

![Ogólne informacje o tym, uruchamianie przykładowych kontenerów](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Przykładowych kontenerów

Przykład ma dwa obrazy kontenera, jeden dla witryny sieci Web frontonu. Drugi obraz jest kontener wykrywania języka zwraca wykryty język tekstu (kultury). Oba kontenery są dostępne z zewnętrzny adres IP, gdy wszystko będzie gotowe.

### <a name="the-language-frontend-container"></a>Kontener frontonu języka

Ta witryna sieci Web jest równoznaczne z własnej aplikacji po stronie klienta, który sprawia, że żądania punktu końcowego wykrywania języka. Po zakończeniu procedury Pobierz wykryty język ciąg znaków, uzyskując dostęp do kontenera witryny sieci Web w przeglądarce z `http://<external-IP>/<text-to-analyze>`. Przykładem tego adresu URL jest `http://132.12.23.255/helloworld!`. Wynik w przeglądarce jest `English`.

### <a name="the-language-container"></a>Kontener języka

Kontenerze wykrywanie języka w tej procedurze określonych jest dostępna dla każdego żądania zewnętrznych. Kontener nie został zmodyfikowany w jakikolwiek sposób, dzięki czemu standardowe wykrywanie języka specyficznego dla kontenera usług Cognitive Services interfejsu API jest dostępny.

Dla tego kontenera tego interfejsu API jest żądaniem POST do wykrywania języka. Jak za pomocą wszystkich kontenerów usług Cognitive Services, możesz dodatkowe informacje na temat kontenerów w jego hostowanej informacji struktury Swagger `http://<external-IP>:5000/swagger/index.html`.

Port 5000 jest domyślnym portem używanym z kontenerami usługi Cognitive Services.

## <a name="create-azure-container-registry-service"></a>Tworzenie usługi Azure Container Registry

Aby wdrożyć kontener usługi Azure Kubernetes Service, obrazy kontenera muszą być dostępne. Utwórz własną usługę Azure Container Registry do obsługi obrazów.

1. Zaloguj się do wiersza polecenia platformy Azure

    ```azurecli
    az login
    ```

1. Utwórz grupę zasobów o nazwie `cogserv-container-rg` do przechowywania wszystkich zasobów utworzonych w tej procedurze.

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. Następnie utwórz własne usługi Azure Container Registry przy użyciu formatu nazwy `registry`, takich jak `pattyregistry`. Nie używać łączników lub podkreślenia znaków w nazwie.

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Zapisz wyniki, aby uzyskać **loginServer** właściwości. Są to część adresu hostowanej kontenera, użyty później w `language.yml` pliku.

    ```console
    >az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. Zaloguj się do rejestru kontenerów. Należy się zalogować, zanim będzie można wypchnąć obrazy do rejestru.

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Pobierz obraz platformy Docker w witrynie sieci Web

1. Przykładowy kod używany w ramach tej procedury znajduje się w repozytorium przykładów kontenerów usług Cognitive Services. Sklonuj repozytorium mieć kopię lokalną próbki.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Po repozytorium znajduje się na komputerze lokalnym, należy znaleźć witryny sieci Web w [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) katalogu. Ta witryna sieci Web działa jako aplikacja kliencka, wykrywanie języka interfejsu API hostowanego w kontenerze wykrywania języka podczas wywoływania.  

1. Budowanie obrazu Docker dla tej witryny sieci Web. Upewnij się, że konsola jest w [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) katalogu, w którym plik Dockerfile znajduje się po uruchomieniu następującego polecenia:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Aby śledzić wersji na rejestrze kontenera, Dodaj tag z formatem wersji, takich jak `v1`.

1. Wypchnij obraz do rejestru kontenerów. Może to potrwać kilka minut.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Jeśli otrzymasz `unauthorized: authentication required` błąd, zaloguj się przy użyciu `az acr login --name <your-container-registry-name>` polecenia. 

    Po zakończeniu procesu wyniki powinny być podobne do:

    ```console
    > docker push pattyregistry.azurecr.io/language-frontend:v1
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Pobierz obraz platformy Docker wykrywanie języka

1. Ściągnij najnowszej wersji obrazu platformy Docker na komputerze lokalnym. Może to potrwać kilka minut. Jeśli istnieje nowsza wersja tego kontenera, zmień wartość z `1.1.006770001-amd64-preview` do nowszej wersji.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Tag obrazu z rejestru kontenerów. Znajdź najnowszą wersję i Zamień wersji `1.1.006770001-amd64-preview` jeśli ma nowszą wersję. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Wypchnij obraz do rejestru kontenerów. Może to potrwać kilka minut.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Pobieranie poświadczeń rejestru kontenerów

Poniższe kroki są niezbędne można pobrać informacji wymaganych do łączenia z rejestru kontenerów za pomocą usługi Azure Kubernetes Service tworzonego w dalszej części tej procedury.

1. Tworzenie jednostki usługi.

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    Zapisz wyniki `appId` wartość parametru osoby przypisanej w kroku 3, `<appId>`. Zapisz `password` dla parametru klucz tajny klienta w następnej sekcji `<client-secret>`.

    ```console
    > az ad sp create-for-rbac --skip-assignment
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Pobieranie identyfikatora rejestru kontenerów

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Zapisz dane wyjściowe z wartością parametru scope `<acrId>`, w następnym kroku. Wygląda ona następująco:

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Zapisz pełną wartość w kroku 3 w tej sekcji.

1. Aby udzielić prawidłowy dostęp do klastra AKS używać obrazów przechowywanych w rejestrze kontenerów, Utwórz przypisanie roli. Zastąp `<appId>` i `<acrId>` wartościami zebrane w dwa poprzednie kroki.

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Tworzenie usługi Azure Kubernetes

1. Utwórz klaster Kubernetes. Wartości parametrów są z poprzedniej sekcji, z wyjątkiem parametru name. Wybierz nazwę, która wskazuje, kto utworzył on i jego przeznaczenie, takich jak `patty-kube`.

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Może to potrwać kilka minut. Wynik jest:

    ```console
    > az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    Usługa zostanie utworzona, ale nie ma kontenera witryny sieci Web lub kontener wykrywania języka jeszcze.  

1. Pobieranie poświadczeń klastra usługi Kubernetes.

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Ładowanie definicji aranżacji do usługi Kubernetes

Ta sekcja używa **kubectl** interfejsu wiersza polecenia, aby porozmawiać z usługi Azure Kubernetes Service.

1. Przed załadowaniem definicji aranżacji, sprawdź **kubectl** ma dostęp do węzłów.

    ```console
    kubectl get nodes
    ```

    Odpowiedź wygląda następująco:

    ```console
    > kubectl get nodes
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Skopiuj poniższy plik i nadaj mu nazwę `language.yml`. Plik ma `service` sekcji i `deployment` sekcji dla typów dwóch kontenerów `language-frontend` kontenera w witrynie sieci Web i `language` wykrywania kontenera.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Zmień język frontonu wierszy wdrożenia `language.yml` oparte na w poniższej tabeli, aby dodać własne nazwy obrazów w rejestrze kontenera, klucz tajny klienta i ustawień analizy tekstu.

    Ustawienia wdrożenia frontonu języka|Przeznaczenie|
    |--|--|
    |Wiersz 32<br> `image` Właściwość|Lokalizacji obrazu dla obrazów frontonu w rejestrze kontenerów<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Wiersz 44<br> `name` Właściwość|Hasło rejestru kontenera dla obrazu, nazywane `<client-secret>` w poprzedniej sekcji.|

1. Zmień język wierszy wdrożenia `language.yml` oparte na w poniższej tabeli, aby dodać własne nazwy obrazów w rejestrze kontenera, klucz tajny klienta i ustawień analizy tekstu.

    |Ustawienia wdrożenia języka|Przeznaczenie|
    |--|--|
    |W wierszu 78<br> `image` Właściwość|Lokalizacji obrazu dla obrazów języka w rejestrze kontenerów<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |95 wiersza<br> `name` Właściwość|Hasło rejestru kontenera dla obrazu, nazywane `<client-secret>` w poprzedniej sekcji.|
    |91 wiersza<br> `apiKey` Właściwość|Klucz zasobu analizy tekstu|
    |92 wiersza<br> `billing` Właściwość|Punkt końcowy rozliczeń zasobu bazy danych analizy tekstu.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Ponieważ **apiKey** i **rozliczeń punktu końcowego** są ustawiane jako część definicji aranżacji Kubernetes, kontener witryny sieci Web nie musi wiedzieć o tych lub przekazać je jako część żądania. Kontener witryny sieci Web odnosi się do kontenera wykrywania języka za pomocą nazwy orchestrator `language`.

1. Załaduj plik definicji aranżacji dla tego przykładu z folderu, w której została utworzona i zapisana `language.yml`.

    ```console
    kubectl apply -f language.yml
    ```

    Odpowiedź jest:

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Pobierz zewnętrzne adresy IP kontenerów

W przypadku dwóch kontenerów, upewnij się `language-frontend` i `language` usługi są uruchomione i Pobierz zewnętrzny adres IP.

```console
kubectl get all
```

```console
> kubectl get all
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

Jeśli `EXTERNAL-IP` dla usługi jest wyświetlany jako oczekujące, uruchom ponownie polecenie, aż adres IP jest wyświetlany przed przejściem do następnego kroku.

## <a name="test-the-language-detection-container"></a>Testowanie kontenera wykrywanie języka

Otwórz przeglądarkę i przejdź do zewnętrznego adresu IP z `language` kontenera z poprzedniej sekcji: `http://<external-ip>:5000/swagger/index.html`. Możesz użyć `Try it` funkcji interfejsu API, aby przetestować punktu końcowego wykrywania języka.

![Wyświetl dokumentację programu swagger kontenera](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testowanie kontenera aplikacji klienta

Zmień adres URL w przeglądarce na zewnętrzny adres IP z `language-frontend` kontenera przy użyciu następującego formatu: `http://<external-ip>/helloworld`. Angielska kulturze tekst `helloworld` przewiduje się, jak `English`.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu z klastrem, Usuń grupę zasobów platformy Azure.

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Informacje pokrewne

* [narzędzia kubectl dla użytkowników platformy Docker](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Kolejne kroki

* Użycie [kontenerów usługi Cognitive Services](../cognitive-services-container-support.md)
* Użyj analizy tekstu usługa połączona] (.. / vs-text połączone service.md)

<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->