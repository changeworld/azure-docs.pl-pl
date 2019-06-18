---
title: Omówienie usługi Azure Event hubs standardowych interfejsów API platformy .NET | Dokumentacja firmy Microsoft
description: Omówienie interfejsu API programu .NET standard
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: b09f39f45936a7c43dbc1ef109780315d62c768f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821907"
---
# <a name="event-hubs-net-standard-api-overview"></a>Przegląd standardowy interfejs API .NET centrów zdarzeń

Ten artykuł zawiera podsumowanie niektórych klucza usługi Azure Event Hubs [interfejsów API klienta .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/). Obecnie istnieją dwie biblioteki klienta .NET Standard dla usługi Event Hubs:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): Zawiera wszystkie operacje podstawowe środowiska uruchomieniowego.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): Dodaje dodatkowe funkcjonalności, która umożliwia rejestrowanie informacji o przetworzonych zdarzeń i jest najprostszym sposobem odczytu z Centrum zdarzeń.

## <a name="event-hubs-client"></a>Event Hubs klienta

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) jest obiektem głównej, używane do wysyłania zdarzeń, tworzenie odbiorników i można pobrać informacji o czasie wykonywania. Ten klient jest połączony z koncentratorem konkretnego zdarzenia, a następnie tworzy nowe połączenie z punktem końcowym usługi Event Hubs.

### <a name="create-an-event-hubs-client"></a>Tworzenie klienta usługi Event Hubs

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) obiekt zostanie utworzony na podstawie parametrów połączenia. Najprostszym sposobem utworzenia wystąpienia nowego klienta zostały przedstawione w poniższym przykładzie:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

Aby programowo Edytuj parametry połączenia, można użyć [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) klasy, a następnie przekaż parametry połączenia jako parametru, aby [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Wysyłanie zdarzeń

Aby wysyłać zdarzenia do Centrum zdarzeń, użyj [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) klasy. Treść musi być `byte` tablicy, lub `byte` segmentem tablicy.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Odbieranie zdarzeń

Zalecanym sposobem odbierania zdarzeń z usługi Event Hubs używa [hosta procesora zdarzeń](#event-processor-host-apis), która zapewnia funkcjonalność do automatycznego śledzenia informacji przesunięcia i partycji Centrum zdarzeń. Istnieją jednak pewne sytuacje, w których warto elastyczność podstawowej biblioteki usługi Event Hubs umożliwia odbieranie zdarzeń.

#### <a name="create-a-receiver"></a>Utwórz odbiornik

Odbiorniki są powiązane z określonymi partycjami z tak, aby otrzymywać wszystkie zdarzenia w Centrum zdarzeń, należy utworzyć wiele wystąpień. Jest dobrą praktyką, aby uzyskać informacje o partycji programowo, zamiast kodować identyfikatorów partycji. Aby to zrobić, możesz skorzystać z [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) metody.

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Ponieważ zdarzenia nie są nigdy usuwane z Centrum zdarzeń i tylko wygaśnie, należy określić prawidłowego punktu wyjścia. Poniższy przykład przedstawia możliwych kombinacji:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Zużyć zdarzenie

```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>Interfejsy API hosta procesora zdarzeń

Te interfejsy API zapewniają odporność na procesach roboczych, które mogą stać się niedostępne, przekazując partycje dostępne procesy robocze:

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

Poniżej przedstawiono przykład implementacji [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) interfejsu:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o scenariuszach usługi Event Hubs, skorzystaj z następujących linków:

* [Co to jest usługa Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Zdarzenie dostępne koncentratory API](event-hubs-api-overview.md)

Dokumentacja interfejsu API platformy .NET znajdują się tutaj:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
