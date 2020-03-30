---
title: Monitorowanie zdarzeń usługi Azure Media Services za pomocą siatki zdarzeń przy użyciu interfejsu wiersza polecenia | Dokumenty firmy Microsoft
description: W tym artykule pokazano, jak subskrybować usługę Event Grid w celu monitorowania zdarzeń usługi Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 619d40ab56715b4444d8e5649c7fb3401b3f57ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71937288"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Tworzenie i monitorowanie zdarzeń usługi Media Services za pomocą usługi Event Grid przy użyciu interfejsu wiersza polecenia platformy Azure

Azure Event Grid to usługa obsługi zdarzeń dla chmury. Ta usługa używa subskrypcji zdarzeń do kierowania [komunikatów](../../event-grid/concepts.md#event-subscriptions) zdarzeń do subskrybentów. Zdarzenia usługi Media Services zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych. Można zidentyfikować zdarzenie usługi Media Services, ponieważ właściwość eventType zaczyna się od "Microsoft.Media.". Aby uzyskać więcej informacji, zobacz [Schematy zdarzeń usługi Media Services](media-services-event-schemas.md).

W tym artykule używasz interfejsu wiersza polecenia platformy Azure do subskrybowania zdarzeń dla konta usługi Azure Media Services. Następnie wyzwalasz zdarzenia, aby wyświetlić wynik. Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. W tym artykule wysyłasz zdarzenia do aplikacji sieci web, która zbiera i wyświetla wiadomości.

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) przed rozpoczęciem.
- Zainstaluj interfejs wiersza polecenia i korzystaj z niego lokalnie. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

    Obecnie nie wszystkie polecenia [interfejsu wiersza polecenia usługi Media Services w wersji 3](https://aka.ms/ams-v3-cli-ref) działają w usłudze Azure Cloud Shell. Zaleca się używanie interfejsu wiersza polecenia lokalnie.

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).

    Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowanie zdarzeń dla konta usługi Media Services, utwórzmy punkt końcowy dla komunikatu o zdarzeniu. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. W tym artykule można wdrożyć [wstępnie szesnastą aplikację sieci web,](https://github.com/Azure-Samples/azure-event-grid-viewer) która wyświetla komunikaty o zdarzeniu. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

1. Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. W witrynie Azure Portal podaj wartości parametrów.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Jeśli przełączysz się do witryny "Azure Event Grid Viewer", zobaczysz, że nie ma jeszcze żadnych zdarzeń.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Ustawianie subskrypcji platformy Azure

W poniższym poleceniu podaj identyfikator subskrypcji platformy Azure, który ma być używany dla konta usługi Media Services. Listę subskrypcji, do których masz dostęp, możesz wyświetlić, przechodząc do obszaru [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Subskrybowanie zdarzeń usługi Media Services

Subskrybujesz artykuł, aby poinformować o tym, które zdarzenia chcesz śledzić. Poniższy przykład subskrybuje utworzone konto usługi Media Services i przekazuje adres URL z witryny sieci Web utworzonej jako punkt końcowy powiadomienia o zdarzeniu. 

Zamień `<event_subscription_name>` unikatową nazwę subskrypcji wydarzenia. Dla `<resource_group_name>` `<ams_account_name>`i użyj wartości używanych podczas tworzenia konta usługi Media Services. Dla `<endpoint_URL>`, podaj adres URL `api/updates` aplikacji internetowej i dodać do adresu URL strony głównej. Określając punkt końcowy podczas subskrybowania, usługa Event Grid obsługuje routing zdarzeń do tego punktu końcowego. 

1. Pobierz identyfikator zasobu

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Przykład:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Subskrybuj wydarzenia

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Przykład:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Może pojawić się ostrzeżenie uzgadniania sprawdzania poprawności. Daj mu kilka minut, a uścisk dłoni powinien zostać zweryfikowany.

Teraz wyzwalajmy zdarzenia, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego.

## <a name="send-an-event-to-your-endpoint"></a>Wysyłanie zdarzenia do punktu końcowego

Zdarzenia dla konta usługi Media Services można wyzwolić, uruchamiając zadanie kodowania. Możesz wykonać [ten szybki start,](stream-files-dotnet-quickstart.md) aby zakodować plik i rozpocząć wysyłanie zdarzeń. 

Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Punkt końcowy musi `validationResponse` być `validationCode`ustawiony na . Aby uzyskać więcej informacji, zobacz [Zabezpieczenia i uwierzytelnianie w siatce zdarzeń](../../event-grid/security-authentication.md). Możesz wyświetlić kod aplikacji sieci web, aby zobaczyć, jak sprawdza poprawność subskrypcji.

> [!TIP]
> Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Nie odświeżaj strony, jeśli chcesz wyświetlić wszystkie zdarzenia.

![Wyświetlanie zdarzenia subskrypcji](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Następne kroki

[Przekazywanie, kodowanie i przesyłanie strumieniowe](stream-files-tutorial-with-api.md)

