---
title: Omówienie standardowych interfejsów API usługi Azure Event Hubs .NET | Dokumenty firmy Microsoft
description: Omówienie standardowego interfejsu API platformy .NET
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: b09f39f45936a7c43dbc1ef109780315d62c768f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60821907"
---
# <a name="event-hubs-net-standard-api-overview"></a>Omówienie interfejsu API usługi Event Hubs .NET Standard

W tym artykule podsumowano niektóre z kluczowych interfejsów API klienta platformy Azure Event [Hubs .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/). Obecnie istnieją dwie biblioteki klientów .NET Standard dla centrów zdarzeń:

* [Microsoft.Azure.EventHubs:](/dotnet/api/microsoft.azure.eventhubs)Zapewnia wszystkie podstawowe operacje w czasie wykonywania.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): Dodaje dodatkowe funkcje, które umożliwia śledzenie przetworzonych zdarzeń i jest najprostszym sposobem odczytu z centrum zdarzeń.

## <a name="event-hubs-client"></a>Klient centrów zdarzeń

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) jest podstawowym obiektem używanym do wysyłania zdarzeń, tworzenia odbiorników i uzyskania informacji o czasie wykonywania. Ten klient jest połączony z centrum zdarzeń określonego i tworzy nowe połączenie z punktem końcowym usługi Event Hubs.

### <a name="create-an-event-hubs-client"></a>Tworzenie klienta usługi Event Hubs

Obiekt [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) jest tworzony na podstawie ciągu połączenia. Najprostszy sposób wystąpienia nowego klienta jest pokazany w poniższym przykładzie:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

Aby programowo edytować parametry połączenia, można użyć klasy [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) i przekazać parametry połączenia jako parametr do [eventhubclient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Wysyłanie zdarzeń

Aby wysłać zdarzenia do centrum zdarzeń, należy użyć [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) klasy. Treść musi być `byte` tablicą `byte` lub segmentem tablicy.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Odbieranie zdarzeń

Zalecanym sposobem odbierania zdarzeń z Centrum zdarzeń jest użycie [hosta procesora zdarzeń,](#event-processor-host-apis)który zapewnia funkcje do automatycznego śledzenia informacji o przesunięciu centrum zdarzeń i partycji. Istnieją jednak pewne sytuacje, w których można użyć elastyczności podstawowej biblioteki centrum zdarzeń do odbierania zdarzeń.

#### <a name="create-a-receiver"></a>Tworzenie odbiornika

Odbiorniki są powiązane z określonymi partycjami, więc aby odbierać wszystkie zdarzenia w centrum zdarzeń, należy utworzyć wiele wystąpień. Jest dobrą praktyką, aby uzyskać informacje o partycji programowo, a nie hard-coding identyfikatorów partycji. W tym celu można użyć [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) metody.

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

Ponieważ zdarzenia nigdy nie są usuwane z centrum zdarzeń (i tylko wygasają), należy określić właściwy punkt początkowy. Poniższy przykład pokazuje możliwe kombinacje:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Korzystanie ze zdarzenia

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

Te interfejsy API zapewniają odporność procesów roboczych, które mogą stać się niedostępne, rozdzielając partycje między dostępnymi pracownikami:

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

Poniżej przedstawiono przykładową implementację interfejsu [IEventProcessor:](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)

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

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o scenariuszach usługi Event Hubs, skorzystaj z następujących linków:

* [Co to jest usługa Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Dostępne centra zdarzeń apis](event-hubs-api-overview.md)

Odwołania do interfejsu API platformy .NET znajdują się tutaj:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
