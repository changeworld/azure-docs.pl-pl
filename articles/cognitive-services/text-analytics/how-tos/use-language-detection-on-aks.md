---
title: Uruchom usługę Kubernetes
titleSuffix: Text Analytics - Azure Cognitive Services
description: Wdróż kontener wykrywania języka z uruchomionym przykładem w usłudze Azure Kubernetes i przetestuj go w przeglądarce sieci Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: ba6fde66b6173bdbff8e9acc08b16f47c5bf7ea4
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377098"
---
# <a name="deploy-the-language-detection-container-to-azure-kubernetes-service"></a>Wdróż kontener wykrywania języka w usłudze Azure Kubernetes Service

Dowiedz się, jak wdrożyć kontener wykrywania języka. W tej procedurze przedstawiono sposób tworzenia lokalnych kontenerów platformy Docker, wypchnięcia kontenerów do prywatnego rejestru kontenerów, uruchomienia kontenera w klastrze Kubernetes i przetestowania go w przeglądarce sieci Web. Korzystanie z kontenerów może przesunąć uwagę na zarządzanie infrastrukturą, aby zamiast tego koncentrować się na tworzeniu aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura wymaga kilku narzędzi, które muszą być zainstalowane i uruchomione lokalnie. Nie używaj Azure Cloud Shell. Potrzebne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Narzędzie [git](https://git-scm.com/downloads) dla systemu operacyjnego umożliwiające klonowanie [przykładu](https://github.com/Azure-Samples/cognitive-services-containers-samples) użytego w tej procedurze.
* [Wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Aparat platformy Docker](https://www.docker.com/products/docker-engine)i sprawdza, czy interfejs wiersza polecenia platformy Docker działa w oknie konsoli.
* [polecenia kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Zasób platformy Azure z poprawną warstwą cenową. Nie wszystkie warstwy cenowe pracują z tym kontenerem:
    * Zasób **platformy Azure analiza tekstu** tylko przy użyciu warstw cenowych F0 lub standard.
    * Zasób **Cognitive Services platformy Azure** z warstwą cenową S0.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Ta procedura polega na załadowaniu i uruchomieniu przykładowego kontenera Cognitive Services na potrzeby wykrywania języka. Przykład ma dwa kontenery, jeden dla aplikacji klienckiej i jeden dla kontenera Cognitive Services. Należy wypchnąć oba te obrazy do własnego wystąpienia Azure Container Registry. Po wprowadzeniu własnego rejestru Utwórz wystąpienie usługi Azure Kubernetes Service (AKS), aby uzyskać dostęp do tych obrazów i uruchomić kontenery. Gdy kontenery są uruchomione, użyj interfejsu wiersza polecenia **polecenia kubectl** , aby obejrzeć wydajność kontenerów. Uzyskaj dostęp do aplikacji klienckiej za pomocą żądania HTTP i Zobacz wyniki.

![Koncepcja koncepcyjna uruchamiania przykładowych kontenerów](../media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Przykładowe kontenery

Przykład zawiera dwa obrazy kontenerów. Jeden z nich jest przeznaczony dla frontonu sieci Web. Drugi obraz jest kontenerem wykrywania języka, który zwraca wykryty język (kulturę) tekstu. Oba kontenery są dostępne z zewnętrznego adresu IP po zakończeniu.

### <a name="the-language-front-end-container"></a>Kontener frontonu języka

Ta witryna sieci Web jest równoważna z własną aplikacją po stronie klienta, która wysyła żądania punktu końcowego wykrywania języka. Po zakończeniu procedury można uzyskać wykryty język ciągu znaków, uzyskując dostęp do kontenera witryny sieci Web w przeglądarce `http://<external-IP>/<text-to-analyze>`. Przykładem tego adresu URL jest `http://132.12.23.255/helloworld!`. Wynikiem jest `English`przeglądarka.

### <a name="the-language-container"></a>Kontener języka

W ramach tej konkretnej procedury kontener wykrywania języka jest dostępny dla każdego żądania zewnętrznego. Kontener nie jest w żaden sposób zmieniany, więc dostępny jest interfejs API wykrywania języka standardowego Cognitive Services specyficznego dla kontenera.

W przypadku tego kontenera ten interfejs API to żądanie POST dotyczące wykrywania języka. Podobnie jak w przypadku wszystkich kontenerów Cognitive Services, można dowiedzieć się więcej na temat kontenera ze swoich `http://<external-IP>:5000/swagger/index.html`informacji o hostowanej strukturze Swagger.

Port 5000 jest domyślnym portem używanym z kontenerami Cognitive Services.

## <a name="create-an-azure-container-registry-service"></a>Tworzenie usługi Azure Container Registry

Aby wdrożyć kontener w usłudze Azure Kubernetes Service, obrazy kontenerów muszą być dostępne. Utwórz własną usługę Azure Container Registry, aby hostować obrazy.

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az login
    ```

1. Utwórz grupę zasobów o nazwie `cogserv-container-rg` , aby przechowywać wszystkie zasoby utworzone w tej procedurze.

    ```azurecli
    az group create --name cogserv-container-rg --location westus
    ```

1. Utwórz własne wystąpienie Azure Container Registry przy użyciu formatu nazwy, po którym następuje `registry`. Może to być na przykład `pattyregistry`. Nie używaj kresek ani podkreślać znaków w nazwie.

    ```azurecli
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Zapisz wyniki, aby uzyskać Właściwość **loginServer** . Ta właściwość jest częścią adresu hostowanego kontenera, który jest używany później w `language.yml` pliku.

    ```console
    > az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
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

1. Zaloguj się do rejestru kontenerów. Musisz się zalogować, aby można było wypchnąć obrazy do rejestru.

    ```azurecli
    az acr login --name pattyregistry
    ```

## <a name="get-the-website-docker-image"></a>Pobieranie obrazu platformy Docker witryny sieci Web

1. Przykładowy kod używany w tej procedurze znajduje się w repozytorium Cognitive Services kontenerów przykładów. Sklonuj repozytorium, aby zawierało lokalną kopię przykładu.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Gdy repozytorium znajduje się na komputerze lokalnym, Znajdź witrynę sieci Web w katalogu [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) . Ta witryna sieci Web działa jako aplikacja kliencka, która wywołuje interfejs API wykrywania języka hostowanego w kontenerze wykrywania języka. 

1. Utwórz obraz platformy Docker dla tej witryny sieci Web. Upewnij się, że konsola znajduje się w katalogu [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) , w którym znajduje się plik platformy Docker, gdy uruchomisz następujące polecenie:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Aby śledzić wersję w rejestrze kontenerów, Dodaj tag w formacie wersji, np `v1`. 

1. Wypchnij obraz do rejestru kontenerów. Ten krok może potrwać kilka minut. 

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Jeśli wystąpi błąd, zaloguj się `az acr login --name <your-container-registry-name>` za pomocą polecenia. `unauthorized: authentication required` 

    Po zakończeniu procesu wyniki są podobne do:

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

## <a name="get-the-language-detection-docker-image"></a>Pobierz obraz platformy Docker wykrywania języka 

1. Pobierz najnowszą wersję obrazu platformy Docker na komputer lokalny. Ten krok może potrwać kilka minut. Jeśli istnieje nowsza wersja tego kontenera, Zmień wartość z `1.1.006770001-amd64-preview` do nowszej wersji. 

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Oznacz obraz za pomocą rejestru kontenerów. Znajdź najnowszą wersję i Zastąp ją `1.1.006770001-amd64-preview` , jeśli masz nowszą wersję. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Wypchnij obraz do rejestru kontenerów. Ten krok może potrwać kilka minut. 

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Pobierz poświadczenia Container Registry

Poniższe kroki są niezbędne do uzyskania informacji wymaganych do połączenia rejestru kontenerów z wystąpieniem usługi Azure Kubernetes, którą utworzysz w dalszej części tej procedury.

1. Tworzenie jednostki usługi.

    ```azurecli
    az ad sp create-for-rbac --skip-assignment
    ```

    Zapisz wartość wyników `appId` dla parametru przydzielonego w kroku 3, `<appId>`. Zapisz hasło dla parametru `<client-secret>`Client-Secret w następnej sekcji.

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

1. Pobierz identyfikator rejestru kontenerów.

    ```azurecli
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Zapisz dane wyjściowe dla wartości parametru zakresu, `<acrId>`w następnym kroku. Wygląda na to:

    ```console
    > az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Zapisz pełną wartość dla kroku 3 w tej sekcji. 

1. Aby udzielić poprawnego dostępu do klastra AKS do używania obrazów przechowywanych w rejestrze kontenerów, Utwórz przypisanie roli. Zamień `<appId>` i`<acrId>` na wartości zebrane w poprzednich dwóch krokach.

    ```azurecli
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-the-azure-kubernetes-service-cluster"></a>Tworzenie klastra usługi Azure Kubernetes Service

1. Utwórz klaster Kubernetes. Wszystkie wartości parametrów pochodzą z poprzednich sekcji, z wyjątkiem parametru name. Wybierz nazwę wskazującą, kto je utworzył, i przeznaczenie, na `patty-kube`przykład. 

    ```azurecli
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Ten krok może potrwać kilka minut. Wynik: 

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

    Usługa zostanie utworzona, ale nie ma jeszcze kontenera witryny sieci Web ani kontenera wykrywania języka. 

1. Pobierz poświadczenia klastra Kubernetes. 

    ```azurecli
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Załaduj definicję aranżacji do usługi Kubernetes

Ta sekcja używa interfejsu wiersza polecenia **polecenia kubectl** , aby komunikować się z wystąpieniem usługi Azure Kubernetes. 

1. Przed załadowaniem definicji aranżacji upewnij się, że **polecenia kubectl** ma dostęp do węzłów.

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

1. Skopiuj następujący plik i nadaj mu `language.yml`nazwę. Plik zawiera `service` sekcję `deployment` i sekcję dla `language-frontend` dwóch typów kontenerów, kontenera witryny sieci Web i `language` kontenera wykrywania. 

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Zmień wiersze `language.yml` wdrożenia frontonu języka w oparciu o poniższą tabelę, aby dodać własne nazwy obrazów rejestru kontenerów, klucz tajny klienta i ustawienia analiza tekstu.

    Ustawienia wdrażania frontonu języka|Cel|
    |--|--|
    |Wiersz 32<br> `image`wartość|Lokalizacja obrazu dla obrazu frontonu w rejestrze kontenerów<br>`<container-registry-name>.azurecr.io/language-frontend:v1`.|
    |Wiersz 44<br> `name`wartość|Wpis tajny rejestru kontenerów dla obrazu, nazywany `<client-secret>` w poprzedniej sekcji.|

1. Zmień wiersze `language.yml` wdrożenia języka w oparciu o poniższą tabelę, aby dodać własne nazwy obrazów rejestru kontenerów, klucz tajny klienta i ustawienia analiza tekstu.

    |Ustawienia wdrażania języka|Cel|
    |--|--|
    |Wiersz 78<br> `image`wartość|Lokalizacja obrazu dla obrazu języka w rejestrze kontenerów<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`.|
    |Wiersz 95<br> `name`wartość|Wpis tajny rejestru kontenerów dla obrazu, nazywany `<client-secret>` w poprzedniej sekcji.|
    |Wiersz 91<br> `apiKey`wartość|Klucz zasobu analiza tekstu.|
    |Wiersz 92<br> `billing`wartość|Punkt końcowy rozliczeń dla zasobu analiza tekstu.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`.|

    Ponieważ właściwości **apiKey** i **punkt końcowy** dotyczący rozliczeń są ustawiane w ramach definicji aranżacji Kubernetes, kontener witryny sieci Web nie musi wiedzieć o nich ani przekazać ich w ramach żądania. Kontener witryny sieci Web odnosi się do kontenera wykrywania języka według jego `language`nazwy programu Orchestrator. 

1. Załaduj plik definicji aranżacji dla tego przykładu z folderu, w którym został utworzony i `language.yml`zapisany. 

    ```console
    kubectl apply -f language.yml
    ```

    Odpowiedź:

    ```console
    > kubectl apply -f language.yml
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Pobierz zewnętrzne adresy IP kontenerów

W przypadku dwóch kontenerów Sprawdź `language-frontend` , czy usługi i `language` są uruchomione i uzyskaj zewnętrzny adres IP. 

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

`EXTERNAL-IP` Jeśli dla usługi jest wyświetlana jako oczekująca, uruchom ponownie polecenie do momentu wyświetlenia adresu IP przed przejściem do następnego kroku. 

## <a name="test-the-language-detection-container"></a>Testowanie kontenera wykrywania języka

Otwórz przeglądarkę i przejdź do zewnętrznego adresu IP `language` kontenera z poprzedniej sekcji:. `http://<external-ip>:5000/swagger/index.html` `Try it` Użyj funkcji interfejsu API, aby przetestować punkt końcowy wykrywania języka. 

![Wyświetlanie dokumentacji struktury Swagger kontenera](../media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testowanie kontenera aplikacji klienckiej

Zmień adres URL w przeglądarce na zewnętrzny adres IP `language-frontend` kontenera, używając następującego formatu:. `http://<external-ip>/helloworld` Tekst `helloworld` kultury angielskiej jest przewidywany jako `English`.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z klastrem Usuń grupę zasobów platformy Azure. 

```azure-cli
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Informacje pokrewne

* [polecenia kubectl dla użytkowników platformy Docker](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Następne kroki 

* Użyj więcej [kontenerów Cognitive Services](../../cognitive-services-container-support.md).
* Użyj [Analiza tekstu połączonych usług](../vs-text-connected-service.md).


<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->