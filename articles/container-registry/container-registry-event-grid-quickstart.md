---
title: Przewodnik Szybki Start — zdarzenia wysyłania usługi Azure Container Registry do usługi Event Grid
description: W tym przewodniku Szybki Start Włącz zdarzeń usługi Event Grid dla usługi container registry, Wyślij wypychania obraz kontenera i Usuń zdarzenia do przykładowej aplikacji.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/23/2018
ms.author: danlep
ms.custom: seodec18
ms.openlocfilehash: f5c075942a29968ea57c684cd817e578df951989
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60427695"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Szybki start: Wysyłanie zdarzeń z prywatnego rejestru kontenerów do usługi Event Grid

Azure Event Grid to w pełni zarządzanej usługi routingu zdarzeń, która umożliwia ujednolicone używanie zdarzeń przy użyciu funkcji publikowania-subskrypcji modelu. Tworzenie rejestru kontenerów, subskrybowanie zdarzeń rejestru, a następnie wdrożysz przykładową aplikację sieci web do odbierania zdarzeń, w tym przewodniku Szybki Start Użyj wiersza polecenia platformy Azure. Na koniec możesz wyzwolić obraz kontenera `push` i `delete` zdarzeń i widok ładunek zdarzenia w przykładowej aplikacji.

Po wykonaniu kroków opisanych w tym artykule, w przykładowej aplikacji sieci web wyświetlane są zdarzenia wysyłane z usługi container registry do usługi Event Grid:

![Przeglądarki sieci Web, renderowanie przykładowej aplikacji sieci web z trzema odebranych zdarzeń.][sample-app-01]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][azure-account].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Polecenia wiersza polecenia platformy Azure, w tym artykule są sformatowane, aby **Bash** powłoki. Jeśli używasz innego powłoki, takich jak program PowerShell lub wierszu polecenia, może być konieczne odpowiednio dostosować znaki kontynuacji wiersza lub przypisanie zmiennej wierszy. W tym artykule używa zmiennych, aby zminimalizować ilość polecenia edycji wymagane.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupę zasobów platformy Azure to logiczny kontener, w którym wdrażania i zarządzania zasobami platformy Azure. Następujące [Tworzenie grupy az] [ az-group-create] polecenie tworzy grupę zasobów o nazwie *myResourceGroup* w *eastus* regionu. Jeśli chcesz użyć innej nazwy grupy zasobów, ustaw `RESOURCE_GROUP_NAME` na inną wartość.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Następnie Wdróż rejestru kontenerów w grupie zasobów za pomocą następujących poleceń. Przed uruchomieniem [tworzenie az acr] [ az-acr-create] polecenia, należy ustawić `ACR_NAME` nazwy dla rejestru. Nazwa musi być unikatowa w obrębie platformy Azure i jest ograniczona do 5 do 50 znaków alfanumerycznych.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Po utworzeniu rejestru interfejs wiersza polecenia platformy Azure zwraca dane wyjściowe podobne do następujących:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
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

## <a name="create-an-event-endpoint"></a>Tworzenie punktu końcowego zdarzeń

W tej sekcji używasz szablon usługi Resource Manager znajduje się w repozytorium GitHub do wdrożenia aplikacji sieci web wstępnie utworzonych przykładowych w usłudze Azure App Service. Później subskrybowanie zdarzeń usługi Event Grid w rejestrze i określić tę aplikację jako punkt końcowy, do którego są wysyłane zdarzenia.

Aby wdrożyć przykładową aplikację, należy ustawić `SITE_NAME` na unikatową nazwę aplikacji sieci web, a następnie wykonaj następujące polecenia. Nazwa witryny musi być unikatowa na platformie Azure, ponieważ stanowi ona część w pełni kwalifikowana nazwa domeny (FQDN) aplikacji sieci web. W dalszej części tego tematu przejdź do nazwy FQDN aplikacji w przeglądarce sieci web do wyświetlania zdarzeń w rejestrze.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Po pomyślnym wdrożeniu (może upłynąć kilka minut), otwórz przeglądarkę i przejdź do aplikacji sieci web, aby upewnić się, że jest uruchomiona:

`http://<your-site-name>.azurewebsites.net`

Powinny zostać wyświetlone przykładową aplikację, renderowane przy użyciu wyświetlane nie komunikaty o zdarzeniach:

![W sieci Web przeglądarce przedstawiający przykładową aplikację sieci web za pomocą żadne zdarzenia wyświetlane][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subskrybowanie do zdarzeń rejestru

W usłudze Event Grid, możesz zasubskrybować *tematu* stwierdzenie, które zdarzenia chcesz śledzić i gdzie można je wysłać. Następujące [Tworzenie subskrypcji zdarzeń az eventgrid] [ az-eventgrid-event-subscription-create] polecenia subskrybuje rejestru kontenerów, utworzono i określa adres URL aplikacji sieci web jako punkt końcowy, do którego jest w stanie wysyłać zdarzenia. Zmienne środowiskowe, które wypełnione we wcześniejszych sekcjach są używane w tym miejscu ponownie, więc zmiany nie są wymagane

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Po zakończeniu subskrypcji, powinny pojawić się dane wyjściowe podobne do następujących:

```JSON
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Wyzwalacz zdarzenia rejestru

Przykładowa aplikacja jest włączony i uruchomiony i że zasubskrybował w rejestrze, korzystając z usługi Event Grid, możesz generować niektóre zdarzenia. W tej sekcji używasz zadań usługi ACR do tworzenia i wypchniesz obraz kontenera do rejestru. Zadania usługi ACR jest funkcją usługi Azure Container Registry, która pozwala na tworzenie obrazów kontenerów w chmurze, bez konieczności aparat platformy Docker zainstalowany na komputerze lokalnym.

### <a name="build-and-push-image"></a>Zbuduj i Wypchnij obraz

Wykonaj następujące polecenie interfejsu wiersza polecenia platformy Azure, aby utworzyć obraz kontenera z zawartości repozytorium GitHub. Domyślnie zadania ACR automatycznie wypycha dane pomyślnie skompilowany obraz do rejestru, która powoduje wygenerowanie `ImagePushed` zdarzeń.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Dane wyjściowe powinny być podobne do następującego podczas ACR zadania kompilacji, a następnie wypycha obraz. Następujące przykładowe dane wyjściowe zostały obcięte w celu skrócenia programu.

```console
$ az acr build -r $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Aby sprawdzić, czy skompilowany obraz w rejestrze, wykonaj następujące polecenie, aby wyświetlić tagi w repozytorium "myimage":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

Tag "v1" obraz, który utworzoną powinna zostać wyświetlona w danych wyjściowych podobny do następującego:

```console
$ az acr repository show-tags --name $ACR_NAME --repository myimage
[
  "v1"
]
```

### <a name="delete-the-image"></a>Usuń obraz

Teraz wygenerować `ImageDeleted` zdarzeń przez usunięcie obrazu z [az acr repozytorium delete] [ az-acr-repository-delete] polecenia:

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Dane wyjściowe podobne do następujących, powinny być monitowania o potwierdzenie usunięcia manifestu i skojarzone obrazy:

```console
$ az acr repository delete --name $ACR_NAME --image myimage:v1
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): y
```

## <a name="view-registry-events"></a>Wyświetl zdarzenia dotyczące rejestru

Już teraz wypchnięto obraz do rejestru, a następnie usuwany go. Przejdź do aplikacji sieci web podglądu siatki zdarzeń i powinny być widoczne oba `ImageDeleted` i `ImagePushed` zdarzenia. Może być również wyświetlany subskrypcji zdarzeń sprawdzania poprawności, generowane przez wykonanie polecenia w [subskrybowanie zdarzeń rejestru](#subscribe-to-registry-events) sekcji.

Poniższy zrzut ekranu przedstawia przykładową aplikację przy użyciu trzech zdarzeń i `ImageDeleted` zdarzenie zostanie rozszerzona, aby wyświetlić jego szczegóły.

![Przeglądarki sieci Web, przedstawiający przykładową aplikację przy użyciu zdarzenia ImagePushed i ImageDeleted][sample-app-03]

Gratulacje! Jeśli widzisz `ImagePushed` i `ImageDeleted` zdarzenia, rejestru wysyła zdarzenia do usługi Event Grid i przekazuje te zdarzenia do punktu końcowego usługi sieci web aplikacji usługi Event Grid.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu korzystania z zasobów utworzonych w tym przewodniku Szybki Start, możesz je usunąć wszystkie za pomocą następującego polecenia wiersza polecenia platformy Azure. Podczas usuwania grupy zasobów są wszystkie zasoby, zawartych w nim trwale usunięte.

**OSTRZEŻENIE**: Ta operacja jest nieodwracalna. Upewnij się, że nie są już potrzebne żadne zasoby w grupie przed uruchomieniem polecenia.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Schemat zdarzeń siatki zdarzeń

Odwołanie do schematu komunikatów usługi Azure Container Registry zdarzenia można znaleźć w dokumentacji usługi Event Grid:

[Schemat zdarzeń Azure Event Grid dla rejestru kontenerów](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start wdrożono rejestru kontenerów, utworzono obraz rekordu ACR zadania związane z, usunięty oraz użytych w rejestrze zdarzeń z usługi Event Grid, z przykładową aplikacją. Następnie przejdź do samouczka usługi ACR zadania do dalszych informacji na temat tworzenia obrazów kontenerów w chmurze, w tym zautomatyzowane kompilacje w aktualizacji obrazów podstawowych:

> [!div class="nextstepaction"]
> [Kompilowanie obrazów kontenerów w chmurze za pomocą zadań usługi ACR](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
