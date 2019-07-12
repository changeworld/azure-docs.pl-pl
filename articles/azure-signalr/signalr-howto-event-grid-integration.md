---
title: Jak wysyłać zdarzenia usługi Azure SignalR Service do usługi Event Grid
description: Przewodnik dotyczący dowiesz się, jak włączyć zdarzeń usługi Event Grid dla usługi SignalR wyśle połączenie klienta połączone odłączony zdarzenia do przykładowej aplikacji.
services: azure-signalr
author: chenyl
ms.service: azure-signalr
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: chenyl
ms.openlocfilehash: 2d782306938136ce6d21a331185f591316f58a29
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789178"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Jak wysyłać zdarzenia z usługi Azure SignalR Service do usługi Event Grid

Usługa Azure Event Grid to w pełni zarządzanej usługi routingu zdarzeń, która umożliwia ujednolicone używanie zdarzeń za pomocą modelu pub-sub. W tym przewodniku użyjesz wiersza polecenia platformy Azure do tworzenia usługi Azure SignalR Service, Subskrybuj zdarzenia połączeń, a następnie wdrożysz przykładową aplikację sieci web do odbierania zdarzeń. Na koniec można połączyć i odłączyć i zobacz ładunek zdarzenia w przykładowej aplikacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto][azure-account].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Polecenia wiersza polecenia platformy Azure, w tym artykule są sformatowane, aby **Bash** powłoki. Jeśli używasz innego powłoki, takich jak program PowerShell lub wierszu polecenia, może być konieczne odpowiednio dostosować znaki kontynuacji wiersza lub przypisanie zmiennej wierszy. W tym artykule używa zmiennych, aby zminimalizować ilość polecenia edycji wymagane.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupę zasobów platformy Azure to logiczny kontener, w którym wdrażania i zarządzania zasobami platformy Azure. Następujące [Tworzenie grupy az][az-group-create] polecenie tworzy grupę zasobów o nazwie *myResourceGroup* w *eastus* regionu. Jeśli chcesz użyć innej nazwy grupy zasobów, ustaw `RESOURCE_GROUP_NAME` na inną wartość.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Tworzenie usługi SignalR Service

Następnie można wdrożyć usługi Azure Signalr Service w grupie zasobów za pomocą następujących poleceń.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Po utworzeniu usługi SignalR wiersza polecenia platformy Azure zwraca dane wyjściowe podobne do następujących:

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

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subskrybowanie do zdarzeń rejestru

W usłudze Event Grid, możesz zasubskrybować *tematu* stwierdzenie, które zdarzenia chcesz śledzić i gdzie można je wysłać. Następujące [Tworzenie subskrypcji zdarzeń az eventgrid][az-eventgrid-event-subscription-create] polecenia subskrybuje usługi Azure SignalR Service został utworzony i określa adres URL aplikacji sieci web jako punkt końcowy, do którego jest w stanie wysyłać zdarzenia. Zmienne środowiskowe, które wypełnione we wcześniejszych sekcjach są używane w tym miejscu ponownie, więc zmiany nie są wymagane

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Po zakończeniu subskrypcji, powinny pojawić się dane wyjściowe podobne do następujących:

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

## <a name="trigger-registry-events"></a>Wyzwalacz zdarzenia rejestru

Przełącz do trybu usługi do `Serverless Mode` i skonfiguruj połączenie klienta do usługi SignalR. Można wykonać [bez użycia serwera przykładowe](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) jako odwołanie.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a seperate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Wyświetl zdarzenia dotyczące rejestru

Masz teraz połączenie klienta usługi SignalR. Przejdź do aplikacji sieci web podglądu siatki zdarzeń i powinien zostać wyświetlony `ClientConnectionConnected` zdarzeń. Jeżeli klient zakończy klienta pojawi się także `ClientConnectionDisconnected` zdarzeń.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
