---
title: Azure Event Hubs powiązania dla usługi Azure Functions
description: Zrozumienie, jak używać usługi Azure Event Hubs powiązania w usługi Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: funkcje usługi Azure, funkcje, przetwarzania zdarzeń, dynamiczne obliczeń niekorzystającą architektury
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: 64914a1b3efe81a152f5463f74c70c22f01ec0c1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724048"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Event Hubs powiązania dla usługi Azure Functions

W tym artykule opisano sposób pracy z [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) powiązania dla usługi Azure Functions. Usługi Azure Functions obsługuje uruchomić i dane wyjściowe powiązania usługi Event hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Dla wersji usługi Azure Functions 1.x, powiązania usługi Event Hubs jest dostępny w [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) pakietu NuGet w wersji 2.x.
Kod źródłowy dla pakietu jest w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) repozytorium GitHub.


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Dla funkcji 2.x, użyj [Microsoft.Azure.WebJobs.Extensions.EventHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) pakietu, wersji 3.x.
Kod źródłowy dla pakietu jest w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz usługi Event Hubs umożliwia odpowiadanie na zdarzenia wysłanego do strumienia zdarzeń w Centrum zdarzeń. Musi mieć dostęp do odczytu do Centrum zdarzeń, aby skonfigurować wyzwalacz.

Po wyzwoleniu funkcja wyzwalacza usługi Event Hubs komunikat, który je uruchamia jest przekazywany do funkcji jako ciąg.

## <a name="trigger---scaling"></a>Wyzwalanie - skalowania

Każde wystąpienie funkcji Event Hub-Triggered nie jest obsługiwana przez wystąpienie klasy EventProcessorHost (EPH) tylko na 1. Usługa Event Hubs zapewnia tylko na 1 EPH można uzyskać dzierżawę na danej partycji.

Na przykład załóżmy, że możemy zaczynać się od następujących ustawień i założenia do Centrum zdarzeń:

1. 10 partycji.
1. zdarzenia 1000 równomiernie wszystkich partycji = > 100 wiadomości w każdej partycji.

Po włączeniu funkcji jest tylko 1 wystąpienie funkcji. Umożliwia wywołanie tego wystąpienia funkcji Function_0. Function_0 ma EPH 1, zarządzającej uzyskać dzierżawę na wszystkie partycje 10. Zostanie uruchomiony, odczytywanie zdarzeń z partycji 0-9. Z tego punktu nastąpi jedną z następujących czynności:

* **Wymagane jest wystąpienie funkcji tylko na 1** -Function_0 jest w stanie przetworzyć wszystkie 1000 przed logiki skalowania usługi Azure Functions jest uruchamiane. W związku z tym wszystkie komunikaty 1000 są przetwarzane przez Function_0.

* **Dodaj 1 więcej wystąpienie funkcji** -logiki skalowania usługi Azure Functions Określa, czy Function_0 ma więcej wiadomości nie może przetworzyć, więc tworzone jest nowe wystąpienie Function_1,. Centra zdarzeń wykrywa, że nowe wystąpienie EPH próbuje odczytać wiadomości. Centra zdarzeń zostanie uruchomiona Równoważenie obciążenia partycji w wystąpieniach EPH, np. partycje 0-4 są przypisane do Function_0, 5 – 9 partycje są przypisane do Function_1. 

* **Dodaj N działać więcej wystąpień** -logiki skalowania usługi Azure Functions Określa, że zarówno Function_0, jak i Function_1 mają więcej wiadomości nie może ich przetworzyć. Będzie ona skalować ponownie Function_2... n, gdzie N jest większa niż partycji Centrum zdarzeń. Centra zdarzeń załaduje równoważenie partycji na Function_0... 9 wystąpień.

Unikatowy dla bieżącej usługi Azure Functions skalowanie logiki jest fakt, że N jest większa niż liczba partycji. Można to zrobić, aby upewnić się, że zawsze są wystąpieniami klasy EPH łatwo dostępne szybko uzyskać blokady na partycje, udostępnianymi z innych wystąpień. Użytkownicy naliczane są tylko opłaty za zasoby używane podczas wystąpienie funkcji i nie są naliczane opłaty dotyczące tej przerostu.

Jeśli wszystkie wykonaniami funkcja działa bez błędów, punkty kontrolne zostaną dodane do skojarzonego konta magazynu. Podczas wskazanie wyboru zakończy się powodzeniem, wszystkie komunikaty 1000 powinien nigdy nie można pobrać ponownie.

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) który rejestruje treść wyzwalacz Centrum zdarzeń.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Aby uzyskać dostęp do [metadanych zdarzeń](#trigger---event-metadata) w kodzie funkcja powiązać [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) obiektu (wymaga użycia instrukcji dla `Microsoft.ServiceBus.Messaging`). Te same właściwości można także przejść za pomocą wyrażenia wiązania w podpisie metody.  W poniższym przykładzie przedstawiono oba sposobów uzyskania tych samych danych:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage, 
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Aby odbierać zdarzenia w partii, `string` lub `EventData` tablicy:

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono wyzwalacz Centrum zdarzeń powiązanie w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja rejestruje treść wyzwalacz Centrum zdarzeń.

W poniższych przykładach pokazano usługi Event Hubs powiązanie danych w *function.json* pliku. Przykład pierwszy funkcje 1.x, a drugi to dla funkcji 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto kod skryptu C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Aby uzyskać dostęp do [metadanych zdarzeń](#trigger---event-metadata) w kodzie funkcja powiązać [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) obiektu (wymaga użycia instrukcji dla `Microsoft.ServiceBus.Messaging`). Te same właściwości można także przejść za pomocą wyrażenia wiązania w podpisie metody.  W poniższym przykładzie przedstawiono oba sposobów uzyskania tych samych danych:

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Aby odbierać zdarzenia w partii, `string` lub `EventData` tablicy:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Wyzwalacz — przykład F #

W poniższym przykładzie przedstawiono wyzwalacz Centrum zdarzeń powiązanie w *function.json* pliku i [F # funkcja](functions-reference-fsharp.md) używającą powiązania. Funkcja rejestruje treść wyzwalacz Centrum zdarzeń.

W poniższych przykładach pokazano usługi Event Hubs powiązanie danych w *function.json* pliku. Przykład pierwszy funkcje 1.x, a drugi to dla funkcji 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto kod F #:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie przedstawiono wyzwalacz Centrum zdarzeń powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja odczytuje [metadanych zdarzeń](#trigger---event-metadata) i rejestruje komunikat.

W poniższych przykładach pokazano usługi Event Hubs powiązanie danych w *function.json* pliku. Przykład pierwszy funkcje 1.x, a drugi to dla funkcji 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto kod JavaScript:

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Event Hubs trigger function processed message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);
     
    context.done();
};
```

Aby odbierać zdarzenia w partii, ustaw `cardinality` do `many` w *function.json* plików, jak pokazano w poniższych przykładach. Przykład pierwszy funkcje 1.x, a drugi to dla funkcji 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto kod JavaScript:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);
    
    eventHubMessages.forEach(message => {
        context.log(`Processed message ${message}`);
    });

    context.done();
};
```

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę Centrum zdarzeń, nazwę grupy odbiorców i nazwa ustawienia aplikacji, która zawiera parametry połączenia. Aby uzyskać więcej informacji o tych ustawieniach, zobacz [wyzwolenia sekcji konfiguracji](#trigger---configuration). Oto `EventHubTriggerAttribute` przykład atrybutu:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Pełny przykład, zobacz [wyzwalacza — przykład C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `EventHubTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | należy wybrać opcję `eventHubTrigger`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure.|
|**direction** | Nie dotyczy | należy wybrać opcję `in`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje element zdarzeń w kodzie funkcji. | 
|**Ścieżka** |**EventHubName** | Działa tylko 1.x. Nazwa Centrum zdarzeń.  | 
|**EventHubName** |**EventHubName** | Działa tylko 2.x. Nazwa Centrum zdarzeń.  |
|**Grupy konsumentów** |**Grupy konsumentów** | Opcjonalna właściwość, która ustawia [grupy odbiorców](../event-hubs/event-hubs-features.md#event-consumers) umożliwia subskrybowanie zdarzeń w Centrum. Pominięcie `$Default` służy grupy odbiorców. | 
|**Kardynalność** | Nie dotyczy | Dla języka Javascript. Ustaw `many` Aby włączyć przetwarzanie wsadowe.  Jeśli pominięto, lub wartość `one`, pojedynczy komunikat przekazany do funkcji. | 
|**Połączenia** |**Połączenia** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia do Centrum zdarzeń w przestrzeni nazw. Skopiować te parametry połączenia, klikając **informacje o połączeniu** przycisk dla [przestrzeni nazw](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), nie Centrum zdarzeń samej siebie. Ten ciąg połączenia musi mieć co najmniej uprawnienia do odczytu wyzwalacz.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Wyzwalacz - metadanych zdarzeń

Wyzwalacz usługi Event Hubs udostępnia wiele [właściwości metadanych](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Te właściwości mogą służyć jako część wyrażenia powiązania w pozostałych powiązaniach lub parametrów w kodzie. Są to właściwości [EventData](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.eventdata) klasy.

|Właściwość|Typ|Opis|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` Wystąpienia.|
|`EnqueuedTimeUtc`|`DateTime`|Czas umieszczonych w kolejce w formacie UTC.|
|`Offset`|`string`|Przesunięcie danych względem strumienia partycji Centrum zdarzeń. Przesunięcie jest znacznika lub identyfikator zdarzenia w strumieniu usługi Event Hubs. Identyfikator jest unikatowy w ramach partycji strumienia centrów zdarzeń.|
|`PartitionKey`|`string`|Partycja, do zdarzenia, które mają być wysyłane dane.|
|`Properties`|`IDictionary<String,Object>`|Właściwości użytkownika danych zdarzenia.|
|`SequenceNumber`|`Int64`|Logiczne numerem zdarzenia.|
|`SystemProperties`|`IDictionary<String,Object>`|Właściwości systemu, w tym dane zdarzenia.|

Zobacz [przykłady kodu](#trigger---example) używające tych właściwości w tym artykule.

## <a name="trigger---hostjson-properties"></a>Wyzwalacz - host.json właściwości

[Host.json](functions-host-json.md#eventhub) plik zawiera ustawienia, które kontrolują zachowanie wyzwalacza centrów zdarzeń.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Dane wyjściowe

Za pomocą raportu usługi Event Hubs powiązanie się zapisać zdarzeń do strumienia zdarzeń. Musi mieć uprawnienie wysyłania do Centrum zdarzeń można zapisać zdarzenia.

## <a name="output---example"></a>OUTPUT — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) który zapisuje komunikat w Centrum zdarzeń za pomocą zwracana wartość metody jako dane wyjściowe:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Dane wyjściowe — przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono wyzwalacz Centrum zdarzeń powiązanie w *function.json* pliku i [funkcji skryptu C#](functions-reference-csharp.md) używającą powiązania. Funkcja zapisuje komunikat w Centrum zdarzeń.

W poniższych przykładach pokazano usługi Event Hubs powiązanie danych w *function.json* pliku. Przykład pierwszy funkcje 1.x, a drugi to dla funkcji 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Oto C# kodu skryptu, który tworzy jeden komunikat:

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

W tym C# kodu skryptu, który tworzy wiele komunikatów:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Dane wyjściowe — przykład F #

W poniższym przykładzie przedstawiono wyzwalacz Centrum zdarzeń powiązanie w *function.json* pliku i [F # funkcja](functions-reference-fsharp.md) używającą powiązania. Funkcja zapisuje komunikat w Centrum zdarzeń.

W poniższych przykładach pokazano usługi Event Hubs powiązanie danych w *function.json* pliku. Przykład pierwszy funkcje 1.x, a drugi to dla funkcji 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Oto kod F #:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

W poniższym przykładzie przedstawiono wyzwalacz Centrum zdarzeń powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja zapisuje komunikat w Centrum zdarzeń.

W poniższych przykładach pokazano usługi Event Hubs powiązanie danych w *function.json* pliku. Przykład pierwszy funkcje 1.x, a drugi to dla funkcji 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Kod JavaScript w tym wysyła pojedynczy komunikat:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Kod JavaScript w tym wysyła wiele komunikatów:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

Aby uzyskać [bibliotek klas C#](functions-dotnet-class-library.md), użyj [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę Centrum zdarzeń i nazwa ustawienia aplikacji, która zawiera parametry połączenia. Aby uzyskać więcej informacji o tych ustawieniach, zobacz [wyjście - konfiguracji](#output---configuration). Oto `EventHub` przykład atrybutu:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Pełny przykład, zobacz [dane wyjściowe — przykład C#](#output---c-example).

## <a name="output---configuration"></a>OUTPUT — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `EventHub` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa "eventHub". |
|**direction** | Nie dotyczy | Należy wybrać opcję "out". Ten parametr jest ustawiany automatycznie, podczas tworzenia powiązania w portalu Azure. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej używana w kodzie funkcja, która reprezentuje zdarzenia. | 
|**Ścieżka** |**EventHubName** | Działa tylko 1.x. Nazwa Centrum zdarzeń.  | 
|**EventHubName** |**EventHubName** | Działa tylko 2.x. Nazwa Centrum zdarzeń.  |
|**Połączenia** |**Połączenia** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia do Centrum zdarzeń w przestrzeni nazw. Skopiować te parametry połączenia, klikając **informacje o połączeniu** przycisk dla *przestrzeni nazw*, nie Centrum zdarzeń samej siebie. Ten ciąg połączenia musi mieć uprawnienia wysyłania do wysłania tej wiadomości do strumienia zdarzeń.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

W języku C# i skryptu C#, wysyłanie wiadomości przy użyciu parametru metody, takie jak `out string paramName`. W języku C# skryptu `paramName` jest wartością określoną w `name` właściwość *function.json*. Aby zapisać wiele wiadomości, można użyć `ICollector<string>` lub `IAsyncCollector<string>` zamiast `out string`.

W języku JavaScript, dostęp do danych wyjściowych zdarzeń przy użyciu `context.bindings.<name>`. `<name>` wartość jest określona w `name` właściwość *function.json*.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Informacje ogólne |
|---|---|
| Centrum zdarzeń | [Przewodnik obsługi](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)
