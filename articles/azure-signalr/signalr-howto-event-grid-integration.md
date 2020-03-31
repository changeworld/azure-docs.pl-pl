---
title: Jak wysyłać zdarzenia usługi Azure SignalR do siatki zdarzeń
description: Przewodnik, aby pokazać, jak włączyć zdarzenia usługi SignalR grid, a następnie wysłać połączenie klienta połączone/rozłączone zdarzenia do przykładowej aplikacji.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: a76c9aaabf984723e2b60a7cd42425c9b29c916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710830"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Jak wysyłać zdarzenia z usługi Azure SignalR Service do usługi Event Grid

Usługa Azure Event Grid to w pełni zarządzana usługa routingu zdarzeń, która zapewnia jednolite zużycie zdarzeń przy użyciu modelu podduszu pubowego. W tym przewodniku używasz interfejsu wiersza polecenia platformy Azure do utworzenia usługi Azure SignalR, zasubskrybuj zdarzenia połączenia, a następnie wdrożyć przykładową aplikację sieci web, aby odbierać zdarzenia. Na koniec można połączyć i odłączyć i zobaczyć ładunek zdarzenia w przykładowej aplikacji.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto][azure-account] przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Polecenia interfejsu wiersza polecenia platformy Azure w tym artykule są sformatowane dla powłoki **Bash.** Jeśli używasz innej powłoki, takiej jak Program PowerShell lub Wiersz polecenia, może być konieczne odpowiednie dostosowanie znaków kontynuacji wiersza lub wierszy przypisania zmiennych. W tym artykule użyto zmiennych, aby zminimalizować wymaganą edycję poleceń.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to kontener logiczny, w którym można wdrażać zasoby platformy Azure i zarządzać nimi. Następujące polecenie [tworzenia grupy az][az-group-create] tworzy grupę zasobów o nazwie *myResourceGroup* w regionie *eastus.* Jeśli chcesz użyć innej nazwy dla grupy `RESOURCE_GROUP_NAME` zasobów, ustaw inną wartość.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Tworzenie usługi SignalR Service

Następnie wdrożyć usługę Azure Signalr w grupie zasobów za pomocą następujących poleceń.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Po utworzeniu usługi SignalR, interfejsu wiersza polecenia platformy Azure zwraca dane wyjściowe podobne do następujących:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
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

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subskrybowanie zdarzeń rejestru

W uszami zdarzeń subskrybujesz *temat,* aby poinformować go, które zdarzenia chcesz śledzić i gdzie je wysłać. Następujące [polecenie az eventgrid eventgrid event-subscription][az-eventgrid-event-subscription-create] subskrybuje utworzoną usługę Azure SignalR i określa adres URL aplikacji sieci web jako punkt końcowy, do którego powinna wysyłać zdarzenia. Zmienne środowiskowe wypełnione we wcześniejszych sekcjach są ponownie w tym miejscu, więc nie są wymagane żadne zmiany.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Po zakończeniu subskrypcji powinny być widoczne dane wyjściowe podobne do następujących:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Zdarzenia rejestru wyzwalacza

Przełącz się do `Serverless Mode` trybu serwisowego i konfiguruj połączenie klienta z usługą SignalR. Próbki [bezserwerowe](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) można pobrać jako punkt odniesienia.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Wyświetlanie zdarzeń rejestru

Klient został połączony z usługą SignalR. Przejdź do aplikacji sieci Web Podglądu siatki `ClientConnectionConnected` zdarzeń, a zdarzenie powinno zostać wyświetle. Jeśli zakończysz klienta, zobaczysz również `ClientConnectionDisconnected` zdarzenie.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
