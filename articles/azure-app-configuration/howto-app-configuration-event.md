---
title: Samouczek dotyczący konfigurowania konfiguracji aplikacji platformy Azure do wysyłania zdarzeń do punktu końcowego sieci web | Dokumentacja firmy Microsoft
description: W tym samouczku dowiesz się, jak skonfigurować subskrypcje zdarzeń konfiguracji aplikacji platformy Azure do wysyłania zdarzeń modyfikacji pary klucz wartość do punktu końcowego sieci web.
services: azure-app-configuration
documentationcenter: ''
author: jimmyca
manager: yegu
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 2cb9ad28a21842987f8c0f7c75151ab8c7fe6fa0
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735360"
---
# <a name="quickstart-route-azure-app-configuration-events-to-a-web-endpoint-with-azure-cli"></a>Szybki start: Kierowanie zdarzeń usługi Azure konfiguracji aplikacji do punktu końcowego sieci web przy użyciu wiersza polecenia platformy Azure

Użytkownicy platformy Azure z konfiguracji aplikacji mogą subskrybować zdarzenia, które są emitowane zawsze wtedy, gdy wartości klucza są modyfikowane. Te zdarzenia mogą wyzwalać elementy webhook, usługi Azure Functions, kolejek usługi Azure Storage lub innych obsługi zdarzeń, który jest obsługiwany przez [usługi Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers). W tym artykule dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure do subskrybowania zdarzenia konfiguracji aplikacji platformy Azure.

Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. Jednak aby uprościć ten artykuł, zdarzenia zostaną wysłane do aplikacji sieci Web, która zbiera i wyświetla komunikaty.

Po wykonaniu czynności opisanych w tym artykule dane powinny zostać wysłane do aplikacji internetowej.

![Wyświetlanie zdarzenia subskrypcji](./media/quickstarts/event-grid/view-results.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu Azure CLI w najnowszej wersji (2.0.24 lub nowszej). Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Jeśli nie korzystasz z usługi Cloud Shell, musisz się najpierw zalogować za pomocą polecenia `az login`.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tematy usługi Event Grid to zasoby platformy Azure i muszą być umieszczone w grupie zasobów platformy Azure. Grupa zasobów to kolekcja logiczna przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). 

Poniższy przykład tworzy grupę zasobów o nazwie `<resource_group_name>` w *westus* lokalizacji.  Zamień `<resource_group_name>` na unikatową nazwę grupy zasobów.

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration"></a>Tworzenie konfiguracji aplikacji

Zastąp `<appconfig_name>` unikatową nazwą dla danej konfiguracji aplikacji i `<resource_group_name>` z wcześniej utworzoną grupę zasobów. Nazwa musi być unikatowa, ponieważ jest on używany jako nazwa DNS.

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name>
```

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem tematu utwórzmy punkt końcowy dla komunikatów o zdarzeniach. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. Aby uprościć ten przewodnik Szybki Start, wdrożysz [wstępnie zbudowaną aplikację sieci Web](https://github.com/Azure-Samples/azure-event-grid-viewer), która będzie wyświetlać komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

Zastąp `<your-site-name>` unikatową nazwą aplikacji sieci Web. Nazwa aplikacji sieci Web musi być unikatowa, ponieważ stanowi część wpisu DNS.

```azurecli-interactive
$sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Powinna być widoczna witryna internetowa bez żadnych aktualnie wyświetlanych komunikatów.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration"></a>Subskrybowanie do konfiguracji aplikacji

Subskrybowanie tematu to dla usługi Event Grid informacja o tym, które zdarzenia chcesz śledzić i gdzie mają być one wysyłane. Poniższy przykład ilustruje subskrybowanie konfiguracji aplikacji został utworzony i przekazanie adresu URL z aplikacji sieci web jako punktu końcowego dla powiadomień o zdarzeniach. Zastąp element `<event_subscription_name>` nazwą Twojej subskrypcji zdarzeń. Jako parametrów `<resource_group_name>` i `<appconfig_name>` użyj utworzonych wcześniej wartości.

Punkt końcowy dla aplikacji sieci Web musi zawierać sufiks `/api/updates/`.

```azurecli-interactive
appconfigId=$(az appconfig show --name <appconfig_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $appconfigId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Aplikacja sieci Web zawiera kod do sprawdzania poprawności subskrypcji.

![Wyświetlanie zdarzenia subskrypcji](./media/quickstarts/event-grid/view-subscription-event.png)

## <a name="trigger-an-app-configuration-event"></a>Wyzwól zdarzenie konfiguracji aplikacji

Teraz wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego. Tworzenie przy użyciu pary klucz wartość `<appconfig_name>` z wcześniej.

```azurecli-interactive
az appconfig kv set --name <appconfig_name> --key Foo --value Bar --yes
```

Zdarzenie zostało wyzwolone, a usługa Event Grid wysłała komunikat do punktu końcowego skonfigurowanego podczas subskrybowania. Wyświetl aplikację sieci Web, aby wyświetlić właśnie wysłane zdarzenie.

```json
[{
  "id": "deb8e00d-8c64-4b6e-9cab-282259c7674f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/eventDemoGroup/providers/microsoft.appconfiguration/configurationstores/{appconfig-name}",
  "subject": "https://{appconfig-name}.azconfig.io/kv/Foo",
  "data": {
    "key": "Foo",
    "etag": "a1LIDdNEIV6wCnfv3xaip7fMXD3"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T18:59:54Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli planujesz kontynuować pracę z tym konfiguracji aplikacji i subskrypcji zdarzeń, nie usuwaj zasobów utworzonych w tym artykule. Jeśli nie planujesz kontynuowania pracy, użyj poniższego polecenia, aby usunąć zasoby utworzone w ramach tego artykułu.

Zamień `<resource_group_name>` na utworzoną powyżej grupę zasobów.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Kolejne kroki

Skoro już wiesz, jak tworzyć tematy i subskrypcje zdarzeń, Dowiedz się więcej o zdarzeniach pary klucz wartość i jakie usługi Event Grid pozwala wykonać:

- [Reagowanie na zdarzenia pary klucz wartość](concept-app-configuration-event.md)
- [Event Grid — informacje](../event-grid/overview.md)