---
title: Trasy do punktu końcowego sieci web niestandardowych zdarzeń usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Użyj siatki zdarzeń platformy Azure, aby subskrybować zdarzenia zmiany stanu zadania usługi Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6a098f43819bb6581b2c5978fbcc4a378a8514c1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638504"
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>Zdarzenia usługi Azure Media Services trasy do punktu końcowego niestandardowe sieci web przy użyciu interfejsu wiersza polecenia

Azure Event Grid to usługa obsługi zdarzeń dla chmury. W tym artykule Użyj interfejsu wiersza polecenia Azure, aby subskrybować zdarzenia zmiany stanu zadania usługi Azure Media Services, a wyzwalacz zdarzenia w celu wyświetlenia wyników. 

Zazwyczaj wysyła się zdarzenia do punktu końcowego, który na nie reaguje, takiego jak element webhook lub funkcja platformy Azure. Ten samouczek pokazuje, jak utworzyć i ustawić dla elementu webhook.

Po wykonaniu czynności opisanych w tym artykule dane powinny zostać wysłane do punktu końcowego.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się w witrynie [Azure Portal](http://portal.azure.com), a następnie uruchom usługę **CloudShell**, aby wykonywać polecenia interfejsu wiersza polecenia, jak pokazano w następnych krokach.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

Upewnij się, że Pamiętaj wartości, które są używane nazwy konta usługi Media Services, nazwa magazynu i nazwy zasobu.

## <a name="enable-event-grid-resource-provider"></a>Włącz siatki zdarzenia dostawcy zasobów

Pierwszą czynnością, którą należy wykonać jest, upewnij się, że dostawca zasobów siatki zdarzeń włączone w ramach subskrypcji. 

W **Azure** portalu, wykonaj następujące czynności:

1. Przejdź do subskrypcji.
2. Wybierz subskrypcję.
3. W obszarze Ustawienia zaznacz dostawców zasobów.
4. Wyszukaj "EventGrid".
5. Upewnij się, że zdarzenie siatki jest zarejestrowany. Jeśli nie, naciśnij klawisz **zarejestrować** przycisku.  

## <a name="create-a-generic-azure-function-webhook"></a>Tworzenie ogólnych webhook funkcji platformy Azure 

### <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed subskrybowanie artykułu siatki zdarzenia, utworzyć punktu końcowego, który zbiera komunikaty, aby je wyświetlić.

Utwórz funkcję wyzwalane przez ogólny element webhook zgodnie z opisem w [ogólny element webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) artykułu. W tym samouczku **C#** kod jest używany.

Po utworzeniu elementu webhook, skopiuj adres URL, klikając *uzyskać adres URL funkcji* łącze u góry **Azure** okna portalu. Nie trzeba ostatnia część adresu URL (*& clientID = domyślnie*).

![Tworzenie elementu webhook](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>Sprawdzanie poprawności elementu webhook

Po zarejestrowaniu własny punkt końcowy elementu webhook siatki zdarzeń wysyła możesz żądania POST z kodem poprawności potwierdzenie posiadania punktu końcowego. Twoja aplikacja powinna odpowiadać za wstecz wyświetlania kodu walidacji. Zdarzenie siatki nie dostarczyć zdarzeń do elementu webHook punktów końcowych, które nie zostały przekazane sprawdzania poprawności. Aby uzyskać więcej informacji, zobacz [siatki zdarzeń zabezpieczeń i uwierzytelniania](https://docs.microsoft.com/azure/event-grid/security-authentication). Ta sekcja definiuje dwie części, które muszą być zdefiniowane dla weryfikacji do przekazania.

#### <a name="update-the-source-code"></a>Zaktualizuj źródłowy kod

Po utworzeniu sieci webhook **run.csx** pliku zostanie wyświetlona w przeglądarce. Zastąp następujący kod w kodzie domyślnym. 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>Zaktualizuj treści żądania testu

Po prawej stronie **Azure** okna portalu, zobacz dwie karty: **wyświetlać pliki** i **testu**. Wybierz kartę **Test**. W **treść żądania**, wklej następujący kod json. Można go wkleić jako, nie jest wymagane w celu zmiany żadnych wartości.

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

Naciśnij klawisz **Zapisz i uruchom** w górnej części okna.

![Treść żądania](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>Zarejestruj subskrypcji zdarzeń siatki 

Zasubskrybować artykułu mówić zdarzenia, które chcesz śledzić zdarzenia siatki. Poniższy przykład subskrybuje konta usługi Media Services utworzone i przekazuje adres URL z elementu webhook funkcji platformy Azure, utworzony jako punkt końcowy powiadomienia o zdarzeniach. 

Zastąp `<event_subscription_name>` o unikatowej nazwie dla Twojej subskrypcji zdarzeń. Jako parametrów `<resource_group_name>` i `<ams_account_name>` użyj utworzonych wcześniej wartości.  Aby uzyskać `<endpoint_URL>` Wklej adres URL punktu końcowego. Usuń *& clientID = domyślnie* z adresu URL. Dzięki określeniu punktu końcowego podczas subskrybowania usługa Event Grid obsługuje kierowanie zdarzeń do tego punktu końcowego. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

Wartość identyfikatora zasobów konta usługi Media Services wygląda podobnie do następującej:

/Subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/Providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>Testowanie zdarzenia

Uruchom zadania kodowania. Na przykład, zgodnie z opisem w [strumieniowo pliki wideo](stream-files-dotnet-quickstart.md) Szybki Start.

Zdarzenie zostało wyzwolone, a usługa Event Grid wysłała komunikat do punktu końcowego skonfigurowanego podczas subskrybowania. Przejdź do utworzonego wcześniej elementu webhook. Kliknij przycisk **Monitor** i **Odśwież**. Zostanie wyświetlony stan zadania zmieni się zdarzenia: "W kolejce", "Zaplanowane", "Procesu", "Gotowe", "Błąd", "Anulowane", "Anulowanie".  Aby uzyskać więcej informacji, zobacz [schematów zdarzeń usługi Media Services](media-services-event-schemas.md).

Na przykład:

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![Testów](./media/job-state-events-cli-how-to/test_events.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz kontynuować pracę z tym kontem magazynu i tą subskrypcją zdarzeń, nie usuwaj zasobów utworzonych w tym artykule. Jeśli nie planujesz kontynuowania pracy, użyj poniższego polecenia, aby usunąć zasoby utworzone w ramach tego artykułu.

Zamień `<resource_group_name>` na utworzoną powyżej grupę zasobów.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Kolejne kroki

[Reaguje na zdarzenia](reacting-to-media-services-events.md)## Zobacz też

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia 2.0](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
