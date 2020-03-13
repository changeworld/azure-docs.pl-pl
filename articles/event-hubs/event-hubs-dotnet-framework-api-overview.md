---
title: Omówienie interfejsów API usługi Azure Event Hubs .NET Framework | Microsoft Docs
description: Ten artykuł zawiera podsumowanie niektórych kluczowych Event Hubs .NET Framework interfejsów API klienta (zarządzania i środowiska uruchomieniowego).
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: b14759ed39037bfa172366a2ed8f8ca089786ec6
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137615"
---
# <a name="event-hubs-net-framework-api-overview"></a>Omówienie interfejsu API Event Hubs .NET Framework

W tym artykule przedstawiono podsumowanie najważniejszych interfejsów API usługi Azure Event Hubs [.NET Framework Client](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/). Istnieją dwie kategorie: zarządzanie i interfejsy API czasu wykonywania. Interfejsy API czasu wykonywania zawierają wszystkie operacje, które są konieczne do wysyłania i odbierania wiadomości. Operacje zarządzania umożliwiają zarządzanie Event Hubs stanem jednostki przez tworzenie, aktualizowanie i usuwanie jednostek.

[Scenariusze monitorowania](event-hubs-metrics-azure-monitor.md) obejmują zarówno zarządzanie, jak i czas wykonywania. Aby uzyskać szczegółowe informacje na temat dokumentacji dotyczącej interfejsów API platformy .NET, zobacz odwołania do [interfejsów api](/dotnet/api/microsoft.azure.eventhubs.processor) [.NET Framework](/dotnet/api/microsoft.servicebus.messaging.eventhubclient), [.NET Standard](/dotnet/api/microsoft.azure.eventhubs)i klasy eventprocessorhost.

## <a name="management-apis"></a>Interfejsy API zarządzania

Aby wykonać następujące operacje zarządzania, musisz mieć uprawnienia do **zarządzania** w przestrzeni nazw Event Hubs:

### <a name="create"></a>Tworzenie

```csharp
// Create the event hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>Aktualizacja

```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>Usuń

```csharp
await namespaceManager.DeleteEventHubAsync("event hub name");
```

## <a name="run-time-apis"></a>Interfejsy API czasu wykonywania
### <a name="create-publisher"></a>Utwórz wydawcę

```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("event hub name");
```

### <a name="publish-message"></a>Opublikuj wiadomość

```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Utwórz odbiorcę

```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Użyj komunikatu

```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>Interfejsy API hosta procesora zdarzeń

Te interfejsy API zapewniają odporność na procesy robocze, które mogą stać się niedostępne, przez dystrybuowanie partycji między dostępnymi pracownikami.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

Interfejs [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) został zdefiniowany w następujący sposób:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o scenariuszach usługi Event Hubs, skorzystaj z następujących linków:

* [Co to jest usługa Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Przewodnik programowania w usłudze Event Hubs](event-hubs-programming-guide.md)

Dokumentacja interfejsu API platformy .NET jest tutaj:

* [Microsoft.ServiceBus.Messaging](/dotnet/api/microsoft.servicebus.messaging)
* [Microsoft. Azure. EventHubs. klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)
