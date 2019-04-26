---
title: Monitorowanie zdarzeń dotyczących usługi Azure Media Services za pomocą usługi Event Grid przy użyciu interfejsu wiersza polecenia | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono sposób subskrybowania usługi Event Grid, aby można było monitorować zdarzenia usługi Azure Media Services.
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
ms.openlocfilehash: f6243bbc21466361aed7cbb7193f3a7b7c7e539f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322688"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Tworzenie i monitorowanie zdarzeń usługi Media Services za pomocą usługi Event Grid przy użyciu wiersza polecenia platformy Azure

Azure Event Grid to usługa obsługi zdarzeń dla chmury. Ta usługa używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) aby komunikaty o zdarzeniach trasy dla subskrybentów. Zdarzenia usługi Media Services zawierają wszystkie informacje potrzebne do reagowania na zmiany w danych. Zdarzenia usługi Media Services można zidentyfikować, ponieważ właściwość Typ zdarzenia, który rozpoczyna się od "Microsoft.Media.". Aby uzyskać więcej informacji, zobacz [schematów zdarzeń usługi Media Services](media-services-event-schemas.md).

W tym artykule używasz interfejsu wiersza polecenia platformy Azure do subskrybowania zdarzenia dla swojego konta usługi Azure Media Services. Następnie możesz wyzwolić zdarzeń w celu wyświetlenia wyniku. Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. W tym artykule wysyła zdarzenia do aplikacji sieci web, która gromadzi i wyświetla komunikaty.

## <a name="prerequisites"></a>Wymagania wstępne

- Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Zainstaluj interfejs wiersza polecenia i korzystaj z niego lokalnie. Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

    Obecnie nie wszystkie polecenia [interfejsu wiersza polecenia usługi Media Services w wersji 3](https://aka.ms/ams-v3-cli-ref) działają w usłudze Azure Cloud Shell. Zaleca się używanie interfejsu wiersza polecenia lokalnie.

- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).

    Koniecznie zapamiętaj wartości, które zostały użyte jako nazwa grupy zasobów i nazwa konta usługi Media Services.

## <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem zdarzeń dla konta usługi Media Services, Utwórzmy punkt końcowy dla komunikatów o zdarzeniach. Zazwyczaj w punkcie końcowym akcje są wykonywane na podstawie danych zdarzenia. W tym artykule wdrożysz [aplikacji sieci web wstępnie skompilowanych](https://github.com/Azure-Samples/azure-event-grid-viewer) która wyświetla komunikaty o zdarzeniach. Wdrożone rozwiązanie zawiera plan usługi App Service, aplikację internetową usługi App Service i kod źródłowy z repozytorium GitHub.

1. Wybierz pozycję **Wdróż na platformie Azure** w celu wdrożenia rozwiązania w subskrypcji. W witrynie Azure Portal podaj wartości parametrów.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. Wdrożenie może potrwać kilka minut. Po pomyślnym wdrożeniu należy wyświetlić aplikację sieci Web i upewnić się, że jest uruchomiona. W przeglądarce sieci Web przejdź do: `https://<your-site-name>.azurewebsites.net`

Jeśli przełączysz się do witryny "Przeglądarka usługi Azure Event Grid" widoczne jeszcze nie żadnych zdarzeń.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Ustawianie subskrypcji platformy Azure

W poniższym poleceniu podaj identyfikator subskrypcji platformy Azure, który ma być używany dla konta usługi Media Services. Listę subskrypcji, do których masz dostęp, możesz wyświetlić, przechodząc do obszaru [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Subskrybowanie zdarzeń usługi Media Services

Możesz zasubskrybować artykuł, aby poinformować usługę Event Grid zdarzenia, które mają być śledzone. Poniższy przykład ilustruje subskrybowanie konta usługi Media Services został utworzony i przekazanie adresu URL z witryny sieci Web, utworzony jako punktu końcowego dla powiadomień o zdarzeniach. 

Zastąp `<event_subscription_name>` unikatową nazwę subskrypcji zdarzeń. Aby uzyskać `<resource_group_name>` i `<ams_account_name>`, użyj wartości, którego użyto podczas tworzenia konta usługi Media Services. Aby uzyskać `<endpoint_URL>`, podaj adres URL aplikacji sieci web i dodać `api/updates` na adres URL strony głównej. Określając punktu końcowego podczas subskrybowania usługi Event Grid obsługuje kierowanie zdarzeń do określonego punktu końcowego. 

1. Pobierz identyfikator zasobu

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Na przykład:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Subskrybowanie do zdarzeń

    ```azurecli
    az eventgrid event-subscription create \
    --resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Na przykład:

    ```
    az eventgrid event-subscription create --resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Możesz otrzymać ostrzeżenie dotyczące sprawdzania poprawności uzgadniania. Nadaj za kilka minut, a następnie należy sprawdzić, czy uzgadnianie.

Teraz wyzwólmy zdarzeń, aby zobaczyć, jak usługa Event Grid dystrybuuje komunikat do punktu końcowego usługi.

## <a name="send-an-event-to-your-endpoint"></a>Wysyłanie zdarzenia do punktu końcowego

Uruchamiając zadania kodowania, możesz wyzwolić zdarzeń dla konta usługi Media Services. Możesz wykonać [ten przewodnik Szybki Start](stream-files-dotnet-quickstart.md) kodowanie pliku i Rozpocznij wysyłanie zdarzeń. 

Wyświetl aplikację sieci Web ponownie i zwróć uwagę, że zdarzenie sprawdzania poprawności subskrypcji zostało do niej wysłane. Usługa Event Grid wysyła zdarzenie weryfikacji, aby w punkcie końcowym mogło nastąpić sprawdzenie, czy dane zdarzenia mają być odbierane. Punkt końcowy musi ustawić `validationResponse` do `validationCode`. Aby uzyskać więcej informacji, zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](../../event-grid/security-authentication.md). Można wyświetlić kodu aplikacji sieci web, aby zobaczyć, jak sprawdza poprawność subskrypcji.

> [!TIP]
> Wybierz ikonę oka, aby rozwinąć dane zdarzenia. Nie odświeżaj strony, jeśli chcesz wyświetlić wszystkie zdarzenia.

![Wyświetlanie zdarzenia subskrypcji](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Kolejne kroki

[Przekazywanie, kodowanie i przesyłanie strumieniowe](stream-files-tutorial-with-api.md)

