---
title: Odbieranie zdarzeń z usługi Azure Event Grid w punkcie końcowym HTTP
description: Opisuje sposób weryfikacji punktu końcowego HTTP, a następnie odbierania i wykonać deserializacji zdarzenia z usługi Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: babanisa
ms.openlocfilehash: cb38fd17c0c1bfbe3e5957d8f432f0a43b285c93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803756"
---
# <a name="receive-events-to-an-http-endpoint"></a>Odbieranie zdarzeń w punkcie końcowym HTTP

W tym artykule opisano sposób [weryfikacji punktu końcowego HTTP](security-authentication.md#webhook-event-delivery) odbieranie zdarzeń z subskrypcji zdarzeń odbierania i wykonać deserializacji zdarzenia. W tym artykule używa funkcji platformy Azure w celach demonstracyjnych, jednak zastosować te same pojęcia, niezależnie od tego, gdzie aplikacja jest obsługiwana.

> [!NOTE]
> Jest **silnie** zaleca się używanie [wyzwalacza usługi Event Grid](../azure-functions/functions-bindings-event-grid.md) podczas wyzwalania funkcji platformy Azure przy użyciu usługi Event Grid. Użycie ogólnego elementu WebHook wyzwalacz w tym miejscu w celach pokazowych.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz aplikacji funkcji przy użyciu funkcji wyzwalanej przez protokół HTTP.

## <a name="add-dependencies"></a>Dodaj zależności

Jeśli tworzysz na platformie .NET, [dodać zależność](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) do funkcji dla `Microsoft.Azure.EventGrid` [pakietu Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Przykłady w niniejszym artykule wymagają wersji 1.4.0 lub nowszej.

Zestawy SDK dla innych języków są dostępne za pośrednictwem [publikowania zestawów SDK](./sdk-overview.md#data-plane-sdks) odwołania. Te pakiety mają takie jak modele dla typów macierzystych zdarzeń `EventGridEvent`, `StorageBlobCreatedEventData`, i `EventHubCaptureFileCreatedEventData`.

Kliknij link "Wyświetl pliki" w funkcji platformy Azure (większość po prawej w portalu usługi Azure functions), a następnie utwórz plik o nazwie pliku project.json. Dodaj następującą zawartość do `project.json` plik i zapisz go:

 ```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.Azure.EventGrid": "2.0.0"
      }
    }
   }
}
```

![Dodano Pakiet NuGet](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Weryfikacja punktu końcowego

Pierwszą rzeczą, co chcesz zrobić to obsługi `Microsoft.EventGrid.SubscriptionValidationEvent` zdarzenia. Za każdym razem, gdy ktoś subskrybuje zdarzenie usługi Event Grid wysyła zdarzenie sprawdzania poprawności do punktu końcowego z `validationCode` w ładunku danych. Punkt końcowy jest wymagany do echo, w tym w treści odpowiedzi do [udowodnić, że punkt końcowy jest prawidłowy i należące do Ciebie](security-authentication.md#webhook-event-delivery). Jeśli używasz [wyzwalacza usługi Event Grid](../azure-functions/functions-bindings-event-grid.md) zamiast funkcji wyzwalanej przez element WebHook, weryfikacja punktu końcowego jest obsługiwane dla Ciebie. Jeśli używasz usługi interfejsu API innych firm (np. [Zapier](https://zapier.com) lub [IFTTT](https://ifttt.com/)), nie może być możliwość programowego echo kod sprawdzania poprawności. Dla tych usług można ręcznie zweryfikować subskrypcji przy użyciu sprawdzania poprawności adresu URL, który będzie wysyłany w subskrypcji zdarzeń sprawdzania poprawności. Skopiuj ten adres URL w `validationUrl` właściwości i Wyślij GET żądania za pomocą klienta REST lub przeglądarki sieci web.

W języku C# `DeserializeEventGridEvents()` funkcja deserializuje zdarzeń usługi Event Grid. Jego deserializuje dane zdarzenia do odpowiedniego typu, na przykład StorageBlobCreatedEventData. Użyj `Microsoft.Azure.EventGrid.EventTypes` klasy można pobrać nazwy i obsługiwane typy zdarzeń.

Aby programowo wyświetlić kod sprawdzania poprawności, użyj następującego kodu. Powiązane przykłady można znaleźć [przykład odbiorcy siatki zdarzeń](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer).

```csharp
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }
    }
    context.done();
};
```

### <a name="test-validation-response"></a>Testowanie poprawności odpowiedzi

Testowanie funkcji odpowiedzi weryfikacji przez wklejenie zdarzenie próbkowania w polu testu dla tej funkcji:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Po kliknięciu przycisku Uruchom danych wyjściowych powinien być równy 200 OK i `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` w treści:

![Sprawdzanie poprawności odpowiedzi](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Obsługa zdarzeń usługi Blob storage

Teraz sklonujemy rozszerzenia funkcji obsługi `Microsoft.Storage.BlobCreated`:

```cs
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();

    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
        else if (eventGridEvent.Data is StorageBlobCreatedEventData)
        {
            var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type  
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }
    }
    context.done();
};

```

### <a name="test-blob-created-event-handling"></a>Obsługa zdarzeń utworzony obiekt Blob testu

Przetestuj nowe funkcje, funkcji, umieszczając [zdarzenia magazynu obiektów Blob](./event-schema-blob-storage.md#example-event) pole testu i uruchomiona:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Powinny zostać wyświetlone dane wyjściowe adres URL obiektu blob w dzienniku funkcji:

![Dane wyjściowe dziennika](./media/receive-events/blob-event-response.png)

Możesz również przetestować przez utworzenie konta usługi Blob storage lub ogólnego przeznaczenia w wersji 2 konta magazynu (GPv2), [dodawania i tą subskrypcją zdarzeń](../storage/blobs/storage-blob-event-quickstart.md)i ustawienie punktu końcowego adresu URL funkcji:

![Adres URL funkcji](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Obsługa zdarzeń niestandardowych

Ponadto umożliwia raz rozszerzenia funkcji, dzięki czemu może również obsługiwać zdarzenia niestandardowe. 

W języku C# zestaw SDK obsługuje mapowania nazwy typu zdarzenia na typ danych zdarzenia. Użyj `AddOrUpdateCustomEventMapping()` funkcję, aby zamapować zdarzenia niestandardowego.

Dodaj sprawdzanie wydarzenia `Contoso.Items.ItemReceived`. Końcowe kod powinien wyglądać podobnie jak:

```cs
using System.Net;
using Newtonsoft.Json;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.EventGrid;

class ContosoItemReceivedEventData
{
    [JsonProperty(PropertyName = "itemSku")]
    public string ItemSku { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function begun");
    string response = string.Empty;

    string requestContent = await req.Content.ReadAsStringAsync();
    log.Info($"Received events: {requestContent}");

    EventGridSubscriber eventGridSubscriber = new EventGridSubscriber();
    eventGridSubscriber.AddOrUpdateCustomEventMapping("Contoso.Items.ItemReceived", typeof(ContosoItemReceivedEventData));
    EventGridEvent[] eventGridEvents = eventGridSubscriber.DeserializeEventGridEvents(requestContent);

    foreach (EventGridEvent eventGridEvent in eventGridEvents)
    {
        if (eventGridEvent.Data is SubscriptionValidationEventData)
        {
            var eventData = (SubscriptionValidationEventData)eventGridEvent.Data;
            log.Info($"Got SubscriptionValidation event data, validation code: {eventData.ValidationCode}, topic: {eventGridEvent.Topic}");
            // Do any additional validation (as required) and then return back the below response

            var responseData = new SubscriptionValidationResponse()
            {
                ValidationResponse = eventData.ValidationCode
            };

            return req.CreateResponse(HttpStatusCode.OK, responseData);
        }
        else if (eventGridEvent.Data is StorageBlobCreatedEventData)
        {
            var eventData = (StorageBlobCreatedEventData)eventGridEvent.Data;
            log.Info($"Got BlobCreated event data, blob URI {eventData.Url}");
        }
        else if (eventGridEvent.Data is ContosoItemReceivedEventData)
        {
            var eventData = (ContosoItemReceivedEventData)eventGridEvent.Data;
            log.Info($"Got ContosoItemReceived event data, item SKU {eventData.ItemSku}");
        }
    }

    return req.CreateResponse(HttpStatusCode.OK, response);
}
```

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function begun');
    var validationEventType = "Microsoft.EventGrid.SubscriptionValidationEvent";
    var storageBlobCreatedEvent = "Microsoft.Storage.BlobCreated";
    var customEventType = "Contoso.Items.ItemReceived";

    for (var events in req.body) {
        var body = req.body[events];
        // Deserialize the event data into the appropriate type based on event type
        if (body.data && body.eventType == validationEventType) {
            context.log("Got SubscriptionValidation event data, validation code: " + body.data.validationCode + " topic: " + body.topic);

            // Do any additional validation (as required) and then return back the below response
            var code = body.data.validationCode;
            context.res = { status: 200, body: { "ValidationResponse": code } };
        }

        else if (body.data && body.eventType == storageBlobCreatedEvent) {
            var blobCreatedEventData = body.data;
            context.log("Relaying received blob created event payload:" + JSON.stringify(blobCreatedEventData));
        }

        else if (body.data && body.eventType == customEventType) {
            var payload = body.data;
            context.log("Relaying received custom payload:" + JSON.stringify(payload));
        }
    }
    context.done();
};
```

### <a name="test-custom-event-handling"></a>Obsługa zdarzeń niestandardowych testu

Na koniec testu, że funkcja obsługuje teraz zdarzenia niestandardowego typu:

```json
[{
    "subject": "Contoso/foo/bar/items",
    "eventType": "Contoso.Items.ItemReceived",
    "eventTime": "2017-08-16T01:57:26.005121Z",
    "id": "602a88ef-0001-00e6-1233-1646070610ea",
    "data": { 
            "itemSku": "Standard"
            },
    "dataVersion": "",
    "metadataVersion": "1"
}]
```

Możesz również przetestować tę funkcję na żywo przez [wysłanie zdarzenia niestandardowego za pomocą programu CURL z poziomu portalu](./custom-event-quickstart-portal.md) lub [wysłanie postu do tematu niestandardowego](./post-to-custom-topic.md) korzystającego z dowolnej usługi lub aplikacji, która może UMIESZCZAĆ do punktu końcowego, takie jak [Postman](https://www.getpostman.com/). Utwórz niestandardowe tematy i subskrypcje zdarzeń z punktem końcowym, ustawić jako adres URL funkcji.

## <a name="next-steps"></a>Kolejne kroki

* Zapoznaj się z [usługi Azure Event Grid zarządzania i publikować zestawy SDK](./sdk-overview.md)
* Dowiedz się, jak [wpis do tematu niestandardowego](./post-to-custom-topic.md)
* Wypróbuj jedną z szczegółowe samouczki usługi Event Grid i funkcje, takie jak [zmiana rozmiaru obrazów przekazanych do magazynu obiektów Blob](resize-images-on-storage-blob-upload-event.md)
