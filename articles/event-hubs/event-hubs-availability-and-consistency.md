---
title: Dostępność i spójność — usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Jak zapewnić maksymalną dostępność i spójność z usługi Azure Event Hubs przy użyciu partycji.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2020
ms.author: shvija
ms.openlocfilehash: 808e813ad90626acec893a021634566f091c895f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904484"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Availability and consistency in Event Hubs (Dostępność i spójność w usłudze Event Hubs)

## <a name="overview"></a>Omówienie
Usługa Azure Event Hubs używa [modelu partycjonowania](event-hubs-scalability.md#partitions) w celu zwiększenia dostępności i równoległości w ramach jednego centrum zdarzeń. Na przykład jeśli centrum zdarzeń ma cztery partycje, a jedna z tych partycji jest przenoszona z jednego serwera na inny w operacji równoważenia obciążenia, nadal można wysyłać i odbierać z trzech innych partycji. Ponadto posiadanie większej liczby partycji umożliwia posiadanie większej liczby równoczesnych czytników przetwarzających dane, poprawiając agregującą przepływność. Zrozumienie implikacji partycjonowania i zamawiania w systemie rozproszonym jest kluczowym aspektem projektowania rozwiązań.

Aby wyjaśnić kompromis między zamawianiem a [dostępnością, zobacz wyobcowanie wPR](https://en.wikipedia.org/wiki/CAP_theorem), znane również jako pozorowanie Brewera. W tym pozorze omówiono wybór między spójnością, dostępnością i tolerancją partycji. Stwierdza, że dla systemów podzielonych na partycje przez sieć zawsze istnieje kompromis między spójnością a dostępnością.

W yokreślenie browaru definiuje spójność i dostępność w następujący sposób:
* Tolerancja partycji: możliwość systemu przetwarzania danych do kontynuowania przetwarzania danych, nawet jeśli wystąpi błąd partycji.
* Dostępność: węzeł, który nie działa, zwraca uzasadnioną odpowiedź w rozsądnym czasie (bez błędów lub limitów czasu).
* Spójność: odczyt jest gwarantowany do zwrócenia najnowszego zapisu dla danego klienta.

## <a name="partition-tolerance"></a>Tolerancja partycji
Centra zdarzeń jest zbudowany na modelu danych podzielonych na partycje. Można skonfigurować liczbę partycji w centrum zdarzeń podczas instalacji, ale nie można zmienić tej wartości później. Ponieważ należy używać partycji z centrum zdarzeń, należy podjąć decyzję o dostępności i spójności dla aplikacji.

## <a name="availability"></a>Dostępność
Najprostszym sposobem rozpoczęcia pracy z centrum zdarzeń jest użycie zachowania domyślnego. Jeśli utworzysz nowy **[obiekt EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** i użyjesz **[metody Send,](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** zdarzenia są automatycznie dystrybuowane między partycjami w centrum zdarzeń. To zachowanie pozwala na największą ilość czasu.

W przypadkach użycia, które wymagają maksymalnego czasu wystania, ten model jest preferowany.

## <a name="consistency"></a>Spójność
W niektórych scenariuszach kolejność zdarzeń może być ważna. Na przykład system zaplecza może przetworzyć polecenie aktualizacji przed poleceniem usuwania. W tym przypadku można ustawić klucz partycji na zdarzenie `PartitionSender` lub użyć obiektu, aby wysyłać tylko zdarzenia do określonej partycji. W ten sposób zapewnia, że gdy te zdarzenia są odczytywane z partycji, są odczytywane w kolejności.

W tej konfiguracji należy pamiętać, że jeśli określona partycja, do której wysyłasz jest niedostępna, zostanie wyświetlone odpowiedzi na błąd. Jako punkt porównania, jeśli nie masz koligacji do jednej partycji, usługa Centrum zdarzeń wysyła zdarzenie do następnej dostępnej partycji.

Jednym z możliwych rozwiązań, aby zapewnić zamawianie, a jednocześnie maksymalizacji czasu, byłoby agregowanie zdarzeń w ramach aplikacji przetwarzania zdarzeń. Najprostszym sposobem, aby to osiągnąć jest stemplowanie zdarzenia za pomocą niestandardowej właściwości numeru sekwencyjnyego. Poniżej znajduje się kod przykładowy:

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Usługa Azure.Messaging.EventHubs (5.0.0 lub nowsze)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Witryna Microsoft.Azure.EventHubs (4.1.0 lub wcześniejsza)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

W tym przykładzie wysyła zdarzenie do jednej z dostępnych partycji w centrum zdarzeń i ustawia odpowiedni numer sekwencyjny z aplikacji. To rozwiązanie wymaga stanu, który ma być przechowywany przez aplikację przetwarzania, ale daje nadawcom punkt końcowy, który jest bardziej prawdopodobne, aby być dostępne.

## <a name="next-steps"></a>Następne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Usługi Usługi Usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
