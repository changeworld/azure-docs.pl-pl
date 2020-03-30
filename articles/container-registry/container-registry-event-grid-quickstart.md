---
title: Szybki start — wysyłanie zdarzeń do siatki zdarzeń
description: W tym przewodniku Szybki start można włączyć zdarzenia usługi Event Grid dla rejestru kontenerów, a następnie wysłać obraz kontenera wypychania i usuwania zdarzeń do przykładowej aplikacji.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18
ms.openlocfilehash: dbeba56820a520e3435eeb0c5c8dbc5aae981241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403241"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Szybki start: wysyłanie zdarzeń z rejestru kontenerów prywatnych do siatki zdarzeń

Usługa Azure Event Grid to w pełni zarządzana usługa routingu zdarzeń, która zapewnia jednolite zużycie zdarzeń przy użyciu modelu publikowania i subskrybowania. W tym przewodniku Szybki start można użyć interfejsu wiersza polecenia platformy Azure, aby utworzyć rejestr kontenerów, subskrybować zdarzenia rejestru, a następnie wdrożyć przykładową aplikację sieci web, aby odbierać zdarzenia. Na koniec wyzwolić `push` obraz `delete` kontenera i zdarzenia i wyświetlić ładunek zdarzenia w przykładowej aplikacji.

Po wykonaniu kroków w tym artykule zdarzenia wysyłane z rejestru kontenerów do usługi Event Grid są wyświetlane w przykładowej aplikacji sieci web:

![Przeglądarka internetowa renderowania przykładowej aplikacji sieci web z trzema odebranymi zdarzeniami][sample-app-01]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto][azure-account] przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Polecenia interfejsu wiersza polecenia platformy Azure w tym artykule są sformatowane dla powłoki **Bash.** Jeśli używasz innej powłoki, takiej jak Program PowerShell lub Wiersz polecenia, może być konieczne odpowiednie dostosowanie znaków kontynuacji wiersza lub wierszy przypisania zmiennych. W tym artykule użyto zmiennych, aby zminimalizować wymaganą edycję poleceń.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to kontener logiczny, w którym można wdrażać zasoby platformy Azure i zarządzać nimi. Następujące polecenie [tworzenia grupy az][az-group-create] tworzy grupę zasobów o nazwie *myResourceGroup* w regionie *eastus.* Jeśli chcesz użyć innej nazwy dla grupy `RESOURCE_GROUP_NAME` zasobów, ustaw inną wartość.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Następnie należy wdrożyć rejestr kontenerów w grupie zasobów za pomocą następujących poleceń. Przed [uruchomieniem az acr][az-acr-create] utworzyć `ACR_NAME` polecenie, ustawić nazwę rejestru. Nazwa musi być unikatowa na platformie Azure i jest ograniczona do 5-50 znaków alfanumeryczne.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Po utworzeniu rejestru interfejsu wiersza polecenia platformy Azure zwraca dane wyjściowe podobne do następujących:

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

## <a name="create-an-event-endpoint"></a>Tworzenie punktu końcowego zdarzenia

W tej sekcji używasz szablonu Menedżera zasobów znajdującego się w repozytorium Usługi GitHub, aby wdrożyć wstępnie skompiltową przykładową aplikację sieci web w usłudze Azure App Service. Później subskrybujesz zdarzenia w usztywnienia usługi Event Grid rejestru i określ tę aplikację jako punkt końcowy, do którego są wysyłane zdarzenia.

Aby wdrożyć przykładową `SITE_NAME` aplikację, ustaw unikatową nazwę aplikacji sieci web i wykonaj następujące polecenia. Nazwa witryny musi być unikatowa na platformie Azure, ponieważ stanowi część w pełni kwalifikowanej nazwy domeny (FQDN) aplikacji sieci web. W dalszej części można przejść do sieci FQDN aplikacji w przeglądarce sieci web, aby wyświetlić zdarzenia rejestru.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Po pomyślnym zakończeniu wdrożenia (może to potrwać kilka minut), otwórz przeglądarkę i przejdź do aplikacji sieci web, aby upewnić się, że jest uruchomiona:

`http://<your-site-name>.azurewebsites.net`

Powinna zostać wyświetlona przykładowa aplikacja renderowana bez wyświetlanych komunikatów o zdarzeniach:

![Przeglądarka internetowa z przykładową aplikacją internetową bez wyświetlania zdarzeń][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subskrybowanie zdarzeń rejestru

W uszami zdarzeń subskrybujesz *temat,* aby poinformować go, które zdarzenia chcesz śledzić i gdzie je wysłać. Następujące [polecenie az eventgrid eventgrid event-subscription create][az-eventgrid-event-subscription-create] subskrybuje utworzony rejestr kontenerów i określa adres URL aplikacji sieci web jako punkt końcowy, do którego powinna wysyłać zdarzenia. Zmienne środowiskowe wypełnione we wcześniejszych sekcjach są ponownie w tym miejscu, więc nie są wymagane żadne zmiany.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Po zakończeniu subskrypcji powinny być widoczne dane wyjściowe podobne do następujących:

```json
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

## <a name="trigger-registry-events"></a>Zdarzenia rejestru wyzwalacza

Teraz, gdy przykładowa aplikacja jest uruchomiona i subskrybujesz rejestr za pomocą usługi Event Grid, możesz przystąpić do generowania niektórych zdarzeń. W tej sekcji można użyć zadań usługi ACR do tworzenia i wypychania obrazu kontenera do rejestru. Zadania usługi ACR to funkcja usługi Azure Container Registry, która umożliwia tworzenie obrazów kontenerów w chmurze bez konieczności instalowania aparatu platformy Docker na komputerze lokalnym.

### <a name="build-and-push-image"></a>Tworzenie i wypychanie obrazu

Wykonaj następujące polecenie interfejsu wiersza polecenia platformy Azure, aby utworzyć obraz kontenera z zawartości repozytorium Usługi GitHub. Domyślnie program ACR Tasks automatycznie wypycha pomyślnie utworzony obraz `ImagePushed` do rejestru, który generuje zdarzenie.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Powinno być widoczne dane wyjściowe podobne do następujących podczas tworzenia zadań usługi ACR, a następnie wypychania obrazu. Poniższe dane wyjściowe próbki zostały obcięty dla zwięzłości.

```output
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

Aby sprawdzić, czy wbudowany obraz znajduje się w rejestrze, wykonaj następujące polecenie, aby wyświetlić znaczniki w repozytorium "myimage":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

Na wyjściu powinien pojawić się znacznik "v1" wbudowanego obrazu, podobnie jak następujące elementy:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>Usuwanie obrazu

Teraz wygeneruj `ImageDeleted` zdarzenie, usuwając obraz za pomocą polecenia [az acr repozytorium delete:][az-acr-repository-delete]

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Powinieneś zobaczyć dane wyjściowe podobne do następujących, prosząc o potwierdzenie usunięcia manifestu i skojarzonych obrazów:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Wyświetlanie zdarzeń rejestru

Obraz został wypchnięty do rejestru, a następnie usunięty. Przejdź do aplikacji sieci Web Podglądu siatki `ImageDeleted` `ImagePushed` zdarzeń, a zobaczysz zarówno zdarzenia, jak i zdarzenia. Może również zostać wyświetlona zdarzenie sprawdzania poprawności subskrypcji wygenerowane przez wykonanie polecenia w sekcji [Subskrybuj zdarzenia rejestru.](#subscribe-to-registry-events)

Poniższy zrzut ekranu przedstawia przykładową aplikację `ImageDeleted` z trzema zdarzeniami, a zdarzenie jest rozwinięte, aby wyświetlić jego szczegóły.

![Przeglądarka internetowa przedstawiająca przykładową aplikację ze zdarzeniami ImagePushed i ImageDeleted][sample-app-03]

Gratulacje! Jeśli widzisz `ImagePushed` i `ImageDeleted` zdarzenia, rejestr wysyła zdarzenia do siatki zdarzeń, a usługa Event Grid przekazuje te zdarzenia do punktu końcowego aplikacji sieci web.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z zasobami utworzonymi w tym przewodniku Szybki start można usunąć je wszystkie za pomocą następującego polecenia interfejsu wiersza polecenia platformy Azure. Po usunięciu grupy zasobów wszystkie zasoby, które zawiera, są trwale usuwane.

**OSTRZEŻENIE:** Ta operacja jest nieodwracalna. Upewnij się, że nie potrzebujesz już żadnych zasobów w grupie przed uruchomieniem polecenia.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Schemat zdarzenia w siatce zdarzeń

Odwołanie do schematu komunikatu zdarzenia rejestru kontenerów platformy Azure w dokumentacji siatki zdarzeń:

[Schemat zdarzenia usługi Azure Event Grid dla rejestru kontenerów](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono rejestr kontenerów, zbudowano obraz z zadaniami usługi ACR, usunięto go i wykorzystano zdarzenia rejestru z usługi Event Grid przy zastosowaniu przykładowej aplikacji. Następnie przejdź do samouczka Zadania usługi ACR, aby dowiedzieć się więcej na temat tworzenia obrazów kontenerów w chmurze, w tym automatycznych kompilacji na aktualizacji obrazu podstawowego:

> [!div class="nextstepaction"]
> [Tworzenie obrazów kontenerów w chmurze za pomocą zadań ACR](container-registry-tutorial-quick-task.md)

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
