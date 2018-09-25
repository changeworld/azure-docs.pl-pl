---
title: Kierowanie zdarzeń usługi Azure Media Services do niestandardowego internetowego punktu końcowego | Dokumentacja firmy Microsoft
description: Usługa Azure Event Grid, aby subskrybować zdarzenia zmiany stanu zadania usługi Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/20/2018
ms.author: juliako
ms.openlocfilehash: e7268a066acf41c454de0c66aa21603199d85a60
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034845"
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>Kierowanie zdarzeń usługi Azure Media Services do niestandardowego internetowego punktu końcowego przy użyciu interfejsu wiersza polecenia

Azure Event Grid to usługa obsługi zdarzeń dla chmury. W tym artykule możesz użyć wiersza polecenia platformy Azure do subskrybowania zdarzenia zmiany stanu zadania usługi Azure Media Services i wyzwalanie zdarzenia w celu wyświetlenia wyniku. 

Zazwyczaj wysyła się zdarzenia do punktu końcowego, który na nie reaguje, takiego jak element webhook lub funkcja platformy Azure. Ten samouczek pokazuje, jak utworzyć i ustawić dla elementu webhook.

Po wykonaniu czynności opisanych w tym artykule dane powinny zostać wysłane do punktu końcowego.

## <a name="prerequisites"></a>Wymagania wstępne

- Mieć aktywną subskrypcję platformy Azure.
- [Utwórz konto usługi Media Services](create-account-cli-how-to.md).

    Upewnij się, że należy pamiętać, wartości, które były używane nazwy grupy zasobów i nazwę konta usługi Media Services.

- Zainstaluj [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, z jakiej wersji korzystasz. Można również użyć [usługi Azure Cloud Shell](https://shell.azure.com/bash).

## <a name="enable-event-grid-resource-provider"></a>Włączanie dostawcy zasobów usługi Event Grid

Pierwszą rzeczą, jaką należy zrobić to, upewnij się, że dostawca zasobów usługi Event Grid włączone w ramach Twojej subskrypcji. 

W **Azure** portal, wykonaj następujące czynności:

1. Przejdź do subskrypcji.
2. Wybierz subskrypcję.
3. W obszarze Ustawienia zaznacz dostawców zasobów.
4. Wyszukaj "EventGrid".
5. Upewnij się, że usługi Event Grid jest zarejestrowany. Jeśli nie, naciśnij klawisz **zarejestrować** przycisku.  

## <a name="create-a-generic-azure-function-webhook"></a>Utwórz ogólny element webhook funkcji platformy Azure 

### <a name="create-a-message-endpoint"></a>Tworzenie punktu końcowego komunikatów

Przed zasubskrybowaniem usługi Event Grid artykułu, należy utworzyć punkt końcowy, który będzie zbierać komunikaty, aby można było je wyświetlić.

Tworzenie funkcji wyzwalanej przez ogólny element webhook, zgodnie z opisem w [ogólny element webhook](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) artykułu. W tym samouczku **C#** kod jest używany.

Po utworzeniu elementu webhook, skopiuj adres URL, klikając *Pobierz adres URL funkcji* link u góry **Azure** okna portalu. Nie trzeba ostatnią część adresu URL (*& clientID = default*).

![Tworzenie elementu webhook](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>Sprawdzanie poprawności elementu webhook

Po zarejestrowaniu własny punkt końcowy elementu webhook z usługą Event Grid go wysyła żądanie POST kodem prostej weryfikacji udowodnić własność punktu końcowego. Twoja aplikacja powinna odpowiadać za wyświetlania ponownie kod sprawdzania poprawności. Usługi Event Grid nie dostarczają zdarzenia do punktów końcowych elementu webHook, które nie przeszły sprawdzanie poprawności. Aby uzyskać więcej informacji, zobacz [usługi Event Grid zabezpieczeń i uwierzytelniania](https://docs.microsoft.com/azure/event-grid/security-authentication). Ta sekcja definiuje dwie części, które muszą być zdefiniowane dla sprawdzania poprawności do przekazania.

#### <a name="update-the-source-code"></a>Aktualizowanie kodu źródłowego

Utworzony element webhook, **run.csx** plik pojawia się w przeglądarce. Zamień domyślny kod następującym kodem. 

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

#### <a name="update-test-request-body"></a>Aktualizuj treści żądania testu

Po prawej stronie **Azure** okna portalu zostaną wyświetlone dwie karty: **wyświetlać pliki** i **testu**. Wybierz kartę **Test**. W **treść żądania**, wklej następujący kod json. Możesz wkleić go tak jak, nie trzeba zmienić żadnych wartości.

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

## <a name="register-for-the-event-grid-subscription"></a>Zarejestruj subskrypcję usługi Event Grid 

Możesz zasubskrybować artykuł, aby poinformować usługę Event Grid zdarzenia, które mają być śledzone. Poniższy przykład ilustruje subskrybowanie konta usługi Media Services został utworzony i przekazanie adresu URL od elementu webhook funkcji platformy Azure, utworzony jako punktu końcowego dla powiadomień o zdarzeniach. 

Zastąp `<event_subscription_name>` unikatową nazwę subskrypcji zdarzeń. Aby uzyskać `<resource_group_name>` i `<ams_account_name>`, użyj wartości, którego użyto podczas tworzenia konta usługi Media Services. Aby uzyskać `<endpoint_URL>` Wklej adres URL punktu końcowego. Usuń *& clientID = default* z adresu URL. Dzięki określeniu punktu końcowego podczas subskrybowania usługa Event Grid obsługuje kierowanie zdarzeń do tego punktu końcowego. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

Wartość identyfikatora zasobu konta usługi Media Services wygląda podobnie do poniższego:

```
/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount
```

## <a name="test-the-events"></a>Testowanie zdarzenia

Uruchom zadania kodowania. Na przykład, zgodnie z opisem w [Stream pliki wideo](stream-files-dotnet-quickstart.md) Szybki Start.

Zdarzenie zostało wyzwolone, a usługa Event Grid wysłała komunikat do punktu końcowego skonfigurowanego podczas subskrybowania. Przejdź do elementu webhook, która została utworzona wcześniej. Kliknij przycisk **Monitor** i **Odśwież**. Zostanie wyświetlony stan zadania zmieni się zdarzenia: "W kolejce", "Zaplanowane", "Przetwarzania", "Zakończono", "Error", "Anulowane", "Anulowanie".  Aby uzyskać więcej informacji, zobacz [schematów zdarzeń usługi Media Services](media-services-event-schemas.md).

Poniższy przykład przedstawia schematu zdarzeń JobStateChange:

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

![Badanie zdarzeń](./media/job-state-events-cli-how-to/test_events.png)

## <a name="next-steps"></a>Kolejne kroki

[Reagowanie na zdarzenia](reacting-to-media-services-events.md)

## <a name="see-also"></a>Zobacz także

[Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
