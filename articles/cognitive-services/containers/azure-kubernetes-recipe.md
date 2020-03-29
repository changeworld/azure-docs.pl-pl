---
title: Uruchom kontener wykrywania języka w usłudze Kubernetes
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Wdrażanie kontenera wykrywania języka z uruchomionym przykładem w usłudze Azure Kubernetes i testowanie go w przeglądarce sieci Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 1968bc03bfddb9d6f6c8fe743a2a1a99722c074d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399177"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>Wdrażanie kontenera wykrywania języka analizy tekstu w usłudze Azure Kubernetes

Dowiedz się, jak wdrożyć kontener wykrywania języka. W tej procedurze pokazano, jak utworzyć lokalne kontenery platformy Docker, wypchnąć kontenery do własnego rejestru kontenerów prywatnych, uruchomić kontener w klastrze Kubernetes i przetestować go w przeglądarce sieci web.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura wymaga kilku narzędzi, które muszą być zainstalowane i uruchamiane lokalnie. Nie należy używać powłoki usługi Azure Cloud.

* Użyj subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* [Git](https://git-scm.com/downloads) dla systemu operacyjnego, dzięki czemu można sklonować [próbki](https://github.com/Azure-Samples/cognitive-services-containers-samples) używane w tej procedurze.
* [Narzędzie CLI platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Docker aparatu](https://www.docker.com/products/docker-engine) i sprawdź, czy docker CLI działa w oknie konsoli.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* Zasób platformy Azure z poprawną warstwą cenową. Nie wszystkie warstwy cenowe działają z tym kontenerem:
  * **Zasób analizy tekstu** tylko w warstwach cenowych F0 lub Standard.
  * Zasób **usług Cognitive Services** z warstwą cenową S0.

## <a name="running-the-sample"></a>Uruchamianie przykładowej aplikacji

Ta procedura ładuje i uruchamia przykład kontenera usług Cognitive Services do wykrywania języka. Przykład ma dwa kontenery, jeden dla aplikacji klienckiej i jeden dla kontenera usług Cognitive Services. Wypchniemy oba te obrazy do rejestru kontenerów platformy Azure. Gdy znajdują się one we własnym rejestrze, utwórz usługę Azure Kubernetes, aby uzyskać dostęp do tych obrazów i uruchomić kontenery. Gdy kontenery są uruchomione, użyj interfejsu wiersza polecenia **kubectl,** aby obserwować wydajność kontenerów. Dostęp do aplikacji klienckiej z żądaniem HTTP i zobacz wyniki.

![Koncepcyjne pomysły uruchamiania pojemników próbek](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>Pojemniki na próbki

Próbka ma dwa obrazy kontenera, jeden dla witryny frontendu. Drugi obraz to kontener wykrywania języka zwracający wykryty język (kulturę) tekstu. Oba kontenery są dostępne z zewnętrznego adresu IP po zakończeniu.

### <a name="the-language-frontend-container"></a>Kontener language-frontend

Ta witryna sieci Web jest odpowiednikiem własnej aplikacji po stronie klienta, która sprawia, że żądania punktu końcowego wykrywania języka. Po zakończeniu procedury, można uzyskać wykryty język ciągu znaków, uzyskując dostęp `http://<external-IP>/<text-to-analyze>`do kontenera witryny w przeglądarce z . Przykładem tego adresu `http://132.12.23.255/helloworld!`URL jest . Wynikiem w przeglądarce `English`jest .

### <a name="the-language-container"></a>Kontener języka

Kontener wykrywania języka, w tej konkretnej procedurze, jest dostępny dla każdego żądania zewnętrznego. Kontener nie został zmieniony w żaden sposób, więc standardowy interfejs API wykrywania języka specyficzne dla kontenera usług Cognitive Services jest dostępny.

Dla tego kontenera tego interfejsu API jest żądaniem POST do wykrywania języka. Podobnie jak w odniesieniu do wszystkich kontenerów usług Cognitive Services, możesz `http://<external-IP>:5000/swagger/index.html`dowiedzieć się więcej o kontenerze z jego hostowanych informacji Swagger, .

Port 5000 jest portem domyślnym używanym z kontenerami usług Cognitive Services.

## <a name="create-azure-container-registry-service"></a>Tworzenie usługi Azure Container Registry

Aby wdrożyć kontener w usłudze Azure Kubernetes, obrazy kontenerów muszą być dostępne. Utwórz własną usługę Azure Container Registry do obsługi obrazów.

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure

    ```azurecli-interactive
    az login
    ```

1. Utwórz grupę `cogserv-container-rg` zasobów o nazwie, aby pomieścić każdy zasób utworzony w tej procedurze.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. Utwórz własny rejestr kontenerów platformy Azure `registry`w `pattyregistry`formacie swojego imienia i nazwiska, a następnie , takim jak . Nie należy używać kresek ani podkreślać znaków w nazwie.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    Zapisz wyniki, aby uzyskać **właściwość loginServer.** Będzie to część adresu hostowanego kontenera, używanego `language.yml` w dalszej części pliku.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
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

1. Zaloguj się do rejestru kontenerów. Musisz się zalogować, zanim będzie można wypchnąć obrazy do rejestru.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>Pobierz obraz platformy Docker w witrynie sieci Web

1. Przykładowy kod używany w tej procedurze znajduje się w repozytorium kontenerów usług Cognitive Services. Sklonuj repozytorium, aby mieć lokalną kopię próbki.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    Gdy repozytorium znajduje się na komputerze lokalnym, znajdź witrynę sieci Web w katalogu [\dotnet\Language\FrontendService.](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) Ta witryna sieci Web działa jako aplikacja kliencka wywołująca interfejs API wykrywania języka hostowany w kontenerze wykrywania języka.  

1. Tworzenie obrazu platformy Docker dla tej witryny sieci Web. Upewnij się, że konsola znajduje się w katalogu [\FrontendService,](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) w którym znajduje się plik Dockerfile po uruchomieniu następującego polecenia:

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    Aby śledzić wersję w rejestrze kontenerów, dodaj znacznik `v1`w formacie wersji, na przykład .

1. Wypchnij obraz do rejestru kontenerów. Może to potrwać kilka minut.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    Jeśli zostanie `unauthorized: authentication required` wyświetlony błąd, `az acr login --name <your-container-registry-name>` zaloguj się za pomocą polecenia. 

    Po zakończeniu procesu wyniki powinny być podobne do:

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>Pobierz obraz platformy Docker z wykrywaniem języka

1. Ciągnie najnowszą wersję obrazu platformy Docker do komputera lokalnego. Może to potrwać kilka minut. Jeśli istnieje nowsza wersja tego kontenera, `1.1.006770001-amd64-preview` zmień wartość z nowszej wersji.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. Oznaczanie obrazu za pomocą rejestru kontenerów. Znajdź najnowszą wersję i `1.1.006770001-amd64-preview` zastąp wersję, jeśli masz nowszą wersję. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. Wypchnij obraz do rejestru kontenerów. Może to potrwać kilka minut.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>Pobierz poświadczenia rejestru kontenerów

Poniższe kroki są potrzebne, aby uzyskać wymagane informacje, aby połączyć rejestr kontenerów z usługą Azure Kubernetes, którą utworzysz w dalszej części tej procedury.

1. Utwórz jednostkę usługi.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    Zapisz wartość `appId` wyników dla parametru cesjonariusza `<appId>`w kroku 3, . Zapisz `password` parametr `<client-secret>`klucza tajnego następnej sekcji .

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Pobierz identyfikator rejestru kontenera.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    Zapisz dane wyjściowe dla `<acrId>`wartości parametru zakresu, w następnym kroku. Wygląda na to, że:

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    Zapisz pełną wartość kroku 3 w tej sekcji.

1. Aby udzielić klastrowi AKS prawidłowego dostępu do używania obrazów przechowywanych w rejestrze kontenerów, utwórz przypisanie roli. Zamień `<appId>` i `<acrId>` wartości zebrane w poprzednich dwóch krokach.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Tworzenie usługi Azure Kubernetes

1. Utwórz klaster Kubernetes. Wszystkie wartości parametrów pochodzą z poprzednich sekcji z wyjątkiem parametru name. Wybierz nazwę, która wskazuje, kto ją utworzył `patty-kube`i jaki jest jego cel, na przykład .

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    Może to potrwać kilka minut. Rezultatem jest:

    ```output
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

    Usługa jest tworzona, ale nie ma jeszcze kontenera witryny sieci Web ani kontenera wykrywania języka.  

1. Pobierz poświadczenia klastra Kubernetes.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>Ładowanie definicji aranżacji do usługi Kubernetes

W tej sekcji używa interfejsu wiersza polecenia **kubectl** do rozmowy z usługą Azure Kubernetes.

1. Przed załadowaniem definicji aranżacji, sprawdź **kubectl** ma dostęp do węzłów.

    ```console
    kubectl get nodes
    ```

    Odpowiedź wygląda następująco:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. Skopiuj następujący `language.yml`plik i nazwij go . Plik zawiera `service` sekcję `deployment` i sekcję dla dwóch `language-frontend` typów kontenerów, kontenera witryny sieci Web i kontenera `language` wykrywania.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. Zmień wiersze wdrażania frontendu języka na podstawie poniższej `language.yml` tabeli, aby dodać własne nazwy obrazów rejestru kontenerów, klucz tajny klienta i ustawienia analizy tekstu.

    Ustawienia wdrażania między frontem języka|Przeznaczenie|
    |--|--|
    |Linia 32.<br> `image`Właściwość|Lokalizacja obrazu obrazu frontendu w rejestrze kontenerów<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |Linia 44<br> `name`Właściwość|Klucz tajny rejestru kontenerów dla `<client-secret>` obrazu, o którym mowa w poprzedniej sekcji.|

1. Zmień wiersze wdrażania `language.yml` języka na podstawie poniższej tabeli, aby dodać własne nazwy obrazów rejestru kontenerów, klucz tajny klienta i ustawienia analizy tekstu.

    |Ustawienia wdrażania języka|Przeznaczenie|
    |--|--|
    |Linia 78<br> `image`Właściwość|Lokalizacja obrazu obrazu w rejestrze kontenerów<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |Linia 95<br> `name`Właściwość|Klucz tajny rejestru kontenerów dla `<client-secret>` obrazu, o którym mowa w poprzedniej sekcji.|
    |Linia 91<br> `apiKey`Właściwość|Klucz zasobów analizy tekstu|
    |Linia 92<br> `billing`Właściwość|Punkt końcowy rozliczeń dla zasobu analizy tekstu.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    Ponieważ **apiKey** i **punkt końcowy rozliczeń** są ustawione jako część definicji aranżacji Kubernetes, kontener witryny sieci Web nie musi wiedzieć o nich lub przekazać je w ramach żądania. Kontener witryny sieci Web odnosi się do `language`kontenera wykrywania języka według jego nazwy koordynatora .

1. Załaduj plik definicji aranżacji dla `language.yml`tego przykładu z folderu, w którym utworzono i zapisano plik .

    ```console
    kubectl apply -f language.yml
    ```

    Odpowiedź brzmi:

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>Uzyskaj zewnętrzne ip kontenerów

W przypadku dwóch kontenerów sprawdź, czy `language-frontend` `language` usługi są uruchomione i uzyskaj zewnętrzny adres IP.

```console
kubectl get all
```

```output
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

## <a name="test-the-language-detection-container"></a>Testowanie kontenera wykrywania języka

Otwórz przeglądarkę i przejdź do `language` zewnętrznego adresu IP `http://<external-ip>:5000/swagger/index.html`kontenera z poprzedniej sekcji: . Za pomocą `Try it` funkcji interfejsu API można przetestować punkt końcowy wykrywania języka.

![Wyświetlanie dokumentacji kontenera](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>Testowanie kontenera aplikacji klienckiej

Zmień adres URL w przeglądarce na `language-frontend` zewnętrzny adres `http://<external-ip>/helloworld`IP kontenera w następującym formacie: . Tekst kultury angielskiej `helloworld` jest `English`przewidywany jako .

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu z klastrem usuń grupę zasobów platformy Azure.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>Informacje pokrewne

* [kubectl dla użytkowników platformy Docker](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Kontenery usług Cognitive Services](../cognitive-services-container-support.md)

<!--
kubectl get secrets

>az aks browse --resource-group diberry-cogserv-container-rg --name diberry-kubernetes-languagedetection

kubectl proxy

http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/pod/default/language-frontend-6d65bdb77c-8f4qv?namespace=default

kubectl describe pod language-frontend-6d65bdb77c
-->