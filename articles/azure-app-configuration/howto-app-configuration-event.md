---
title: Wysyłanie zdarzeń do punktu końcowego sieci Web przy użyciu konfiguracji aplikacji platformy Azure
description: Dowiedz się, jak używać subskrypcji zdarzeń konfiguracji aplikacji platformy Azure do wysyłania zdarzeń modyfikacji wartości klucza do punktu końcowego sieci Web
services: azure-app-configuration
author: lisaguthrie
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: da64f22981cc33772783093cfe75daa3eac5cef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672153"
---
# <a name="route-azure-app-configuration-events-to-a-web-endpoint-with-azure-cli"></a>Rozsyłanie zdarzeń konfiguracji aplikacji platformy Azure do punktu końcowego sieci Web za pomocą interfejsu wiersza polecenia platformy Azure

W tym artykule dowiesz się, jak skonfigurować subskrypcje zdarzeń konfiguracji aplikacji platformy Azure, aby wysłać zdarzenia modyfikacji wartości klucza do punktu końcowego sieci web. Użytkownicy usługi Azure App Configuration mogą subskrybować zdarzenia emitowane za każdym razem, gdy wartości kluczy są modyfikowane. Te zdarzenia mogą wyzwalać haki sieci web, usługi Azure Functions, kolejki usługi Azure Storage lub inny program obsługi zdarzeń obsługiwany przez usługę Azure Event Grid. Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. Jednak aby uprościć ten artykuł, zdarzenia zostaną wysłane do aplikacji internetowej, która zbiera i wyświetla komunikaty.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję.](https://azure.microsoft.com/free/) Opcjonalnie można użyć usługi Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, aby uruchomić najnowszą wersję interfejsu wiersza polecenia platformy Azure (2.0.70 lub nowsze). Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Jeśli nie korzystasz z usługi Cloud Shell, musisz się najpierw zalogować za pomocą polecenia `az login`.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Tematy usługi Event Grid to zasoby platformy Azure i muszą być umieszczone w grupie zasobów platformy Azure. Grupa zasobów to kolekcja logiczna przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). 

Poniższy przykład tworzy grupę zasobów o nazwie `<resource_group_name>` w lokalizacji *westus.*  Zamień `<resource_group_name>` na unikatową nazwę grupy zasobów.

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration-store"></a>Tworzenie sklepu konfiguracji aplikacji

Zamień `<appconfig_name>` unikatową nazwę magazynu `<resource_group_name>` konfiguracji i grupy zasobów utworzonej wcześniej. Nazwa musi być unikatowa, ponieważ jest używana jako nazwa DNS.

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name> \
  --sku free
```

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem tematu utwórzmy punkt końcowy dla komunikatów o zdarzeniach. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. Aby uprościć ten przewodnik Szybki Start, wdrożysz [wstępnie zbudowaną aplikację sieci Web](https://github.com/Azure-Samples/azure-event-grid-viewer), która będzie wyświetlać komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

Zastąp `<your-site-name>` unikatową nazwą aplikacji internetowej. Nazwa aplikacji internetowej musi być unikatowa, ponieważ stanowi część wpisu DNS.

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

## <a name="subscribe-to-your-app-configuration-store"></a>Subskrybuj swój sklep konfiguracji aplikacji

Subskrybowanie tematu to dla usługi Event Grid informacja o tym, które zdarzenia chcesz śledzić i gdzie mają być one wysyłane. Poniższy przykład subskrybuje konfigurację aplikacji, która została utworzona, i przekazuje adres URL z aplikacji sieci web jako punkt końcowy powiadomienia o zdarzeniu. Zastąp element `<event_subscription_name>` nazwą Twojej subskrypcji zdarzeń. Jako parametrów `<resource_group_name>` i `<appconfig_name>` użyj utworzonych wcześniej wartości.

Punkt końcowy dla aplikacji internetowej musi zawierać sufiks `/api/updates/`.

```azurecli-interactive
appconfigId=$(az appconfig show --name <appconfig_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $appconfigId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Aplikacja internetowa zawiera kod do sprawdzania poprawności subskrypcji.

![Wyświetlanie zdarzenia subskrypcji](./media/quickstarts/event-grid/view-subscription-event.png)

## <a name="trigger-an-app-configuration-event"></a>Wyzwalanie zdarzenia konfiguracji aplikacji

Teraz wyzwólmy zdarzenie, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego. Utwórz wartość klucza `<appconfig_name>` przy użyciu wcześniejszego.

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
Jeśli planujesz kontynuować pracę z tej konfiguracji aplikacji i subskrypcji zdarzeń, nie czyść zasobów utworzonych w tym artykule. Jeśli nie planujesz kontynuowania pracy, użyj poniższego polecenia, aby usunąć zasoby utworzone w ramach tego artykułu.

Zamień `<resource_group_name>` na utworzoną powyżej grupę zasobów.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz, jak tworzyć tematy i subskrypcje wydarzeń, dowiedz się więcej o zdarzeniach o wartości klucza i o tym, co może ci pomóc w siatce zdarzeń:

- [Reagowanie na zdarzenia wartości klucza](concept-app-configuration-event.md)
- [Event Grid — informacje](../event-grid/overview.md)
- [Programy obsługi usługi Azure Event Grid](../event-grid/event-handlers.md)
