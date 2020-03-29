---
title: Odbieranie zdarzeń z usługi Azure Event Grid do punktu końcowego HTTP
description: W tym artykule opisano sposób sprawdzania poprawności punktu końcowego HTTP, a następnie odbierania i deserializacji zdarzeń z usługi Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: babanisa
ms.openlocfilehash: cb38fd17c0c1bfbe3e5957d8f432f0a43b285c93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60803756"
---
# <a name="receive-events-to-an-http-endpoint"></a>Odbieranie zdarzeń w punkcie końcowym HTTP

W tym artykule opisano sposób [sprawdzania poprawności punktu końcowego HTTP,](security-authentication.md#webhook-event-delivery) aby odbierać zdarzenia z subskrypcji zdarzeń, a następnie odbierać i deserializacji zdarzeń. W tym artykule używa funkcji platformy Azure do celów demonstracyjnych, jednak te same pojęcia mają zastosowanie niezależnie od tego, gdzie aplikacja jest hostowana.

> [!NOTE]
> **Zdecydowanie** zaleca się użycie [wyzwalacza siatki zdarzeń](../azure-functions/functions-bindings-event-grid.md) podczas wyzwalania funkcji platformy Azure z siatką zdarzeń. Użycie ogólnego wyzwalacza elementu WebHook w tym miejscu jest demonstracyjne.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebna jest aplikacja funkcyjna z funkcją wyzwalaną przez HTTP.

## <a name="add-dependencies"></a>Dodawanie zależności

Jeśli tworzysz w .NET, [dodaj zależność](../azure-functions/functions-reference-csharp.md#referencing-custom-assemblies) do `Microsoft.Azure.EventGrid` funkcji [pakietu Nuget](https://www.nuget.org/packages/Microsoft.Azure.EventGrid). Przykłady w tym artykule wymagają wersji 1.4.0 lub nowszej.

SDK dla innych języków są dostępne za pośrednictwem odwołania [SDK publikowania.](./sdk-overview.md#data-plane-sdks) Te pakiety mają modele dla `EventGridEvent`natywnych typów zdarzeń, takich jak , `StorageBlobCreatedEventData`i `EventHubCaptureFileCreatedEventData`.

Kliknij łącze "Wyświetl pliki" w funkcji platformy Azure (w prawym okienku większość w portalu funkcji platformy Azure) i utwórz plik o nazwie project.json. Dodaj do pliku `project.json` następującą zawartość i zapisz ją:

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

![Dodano pakiet NuGet](./media/receive-events/add-dependencies.png)

## <a name="endpoint-validation"></a>Sprawdzanie poprawności punktu końcowego

Pierwszą rzeczą, którą chcesz `Microsoft.EventGrid.SubscriptionValidationEvent` zrobić, to obsługiwać zdarzenia. Za każdym razem, gdy ktoś subskrybuje zdarzenie, usługa Event `validationCode` Grid wysyła zdarzenie sprawdzania poprawności do punktu końcowego z ładunkiem danych. Punkt końcowy jest wymagany do odwidnienia tego z powrotem w treści odpowiedzi, aby [udowodnić, że punkt końcowy jest prawidłowy i należy do Ciebie.](security-authentication.md#webhook-event-delivery) Jeśli używasz [wyzwalacza siatki zdarzeń,](../azure-functions/functions-bindings-event-grid.md) a nie funkcji wyzwalanej elementami WebHook, sprawdzanie poprawności punktu końcowego jest obsługiwane za Ciebie. Jeśli używasz usługi interfejsu API innej firmy (takiej jak [Zapier](https://zapier.com) lub [IFTTT),](https://ifttt.com/)programowo nie można programowo powtórzyć kodu sprawdzania poprawności. W przypadku tych usług można ręcznie sprawdzić poprawność subskrypcji przy użyciu adresu URL sprawdzania poprawności, który jest wysyłany w zdarzeniu sprawdzania poprawności subskrypcji. Skopiuj `validationUrl` ten adres URL we właściwości i wyślij żądanie GET za pośrednictwem klienta REST lub przeglądarki sieci Web.

W języku C# `DeserializeEventGridEvents()` funkcja deserializes zdarzenia siatki zdarzeń zdarzeń. Deserializuje dane zdarzenia do odpowiedniego typu, takich jak StorageBlobCreatedEventData. Użyj `Microsoft.Azure.EventGrid.EventTypes` klasy, aby uzyskać obsługiwane typy zdarzeń i nazwy.

Aby programowo echo kodu sprawdzania poprawności, należy użyć następującego kodu. Powiązane przykłady można znaleźć w [przykładzie konsumenta siatki zdarzeń](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridConsumer).

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

### <a name="test-validation-response"></a>Odpowiedź sprawdzania poprawności testu

Przetestuj funkcję odpowiedzi sprawdzania poprawności, wklejając zdarzenie próbki do pola testowego dla funkcji:

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

Po kliknięciu przycisku Uruchom, dane wyjściowe `{"ValidationResponse":"512d38b6-c7b8-40c8-89fe-f46f9e9622b6"}` powinny być 200 OK i w treści:

![odpowiedź na walidację](./media/receive-events/validation-response.png)

## <a name="handle-blob-storage-events"></a>Obsługa zdarzeń magazynu obiektów Blob

Teraz rozszerzyjmy funkcję `Microsoft.Storage.BlobCreated`do obsługi:

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

### <a name="test-blob-created-event-handling"></a>Test blob Utworzono obsługę zdarzeń

Przetestuj nową funkcjonalność funkcji, umieszczając [zdarzenie magazynu obiektów Blob](./event-schema-blob-storage.md#example-event) w polu testowym i uruchamiając:

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

Powinien zostać wyświetlony wyjściowy adres URL obiektu blob w dzienniku funkcji:

![Dziennik danych wyjściowych](./media/receive-events/blob-event-response.png)

Można również przetestować, tworząc konto magazynu obiektów Blob lub konto magazynu ogólnego przeznaczenia V2 (GPv2), [dodając i subskrypcję zdarzeń](../storage/blobs/storage-blob-event-quickstart.md)i ustawiając punkt końcowy na adres URL funkcji:

![Adres URL funkcji](./media/receive-events/function-url.png)

## <a name="handle-custom-events"></a>Obsługa zdarzeń niestandardowych

Na koniec pozwala rozszerzyć funkcję jeszcze raz, tak aby można było również obsługiwać zdarzenia niestandardowe. 

W języku C#zestaw SDK obsługuje mapowanie nazwy typu zdarzenia na typ danych zdarzenia. Użyj `AddOrUpdateCustomEventMapping()` funkcji, aby zamapować zdarzenie niestandardowe.

Dodaj czek dla `Contoso.Items.ItemReceived`wydarzenia . Twój ostateczny kod powinien wyglądać następująco:

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

### <a name="test-custom-event-handling"></a>Testowanie niestandardowej obsługi zdarzeń

Na koniec sprawdź, czy funkcja może teraz obsługiwać niestandardowy typ zdarzenia:

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

Tę funkcję można również przetestować na żywo, [wysyłając zdarzenie niestandardowe za pomocą funkcji CURL z portalu](./custom-event-quickstart-portal.md) lub publikując w temacie [niestandardowym](./post-to-custom-topic.md) przy użyciu dowolnej usługi lub aplikacji, która może wysyłać post do punktu końcowego, takiego jak [Listonosz.](https://www.getpostman.com/) Utwórz temat niestandardowy i subskrypcję zdarzeń z punktem końcowym ustawionym jako adres URL funkcji.

## <a name="next-steps"></a>Następne kroki

* Poznaj [pakiety SDK usługi Azure Event Grid i Publish](./sdk-overview.md)
* Dowiedz się, jak [opublikować temat niestandardowy](./post-to-custom-topic.md)
* Wypróbuj jeden z szczegółowych samouczków dotyczących siatki zdarzeń i funkcji, takich jak [zmiana rozmiaru obrazów przesłanych do magazynu obiektów Blob](resize-images-on-storage-blob-upload-event.md)
