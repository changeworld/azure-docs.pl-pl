---
title: Usługa Azure powiązania usługi Event Hubs dla usługi Azure Functions
description: Dowiedz się, jak używać usługi Azure Event Hubs powiązań w usłudze Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/08/2017
ms.author: glenga
ms.openlocfilehash: c99ad77bba60a4573faae1c857b3e6dc0203c4ab
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434691"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Usługa Azure powiązania usługi Event Hubs dla usługi Azure Functions

W tym artykule wyjaśniono, jak pracować z [usługi Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) powiązania dla usługi Azure Functions. Usługi Azure Functions obsługuje wyzwalanie i danych wyjściowych powiązania usługi Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Dla usługi Azure Functions w wersji 1.x, powiązaniach usługi Event Hubs znajdują się w [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) pakietu NuGet w wersji 2.x.
Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) repozytorium GitHub.


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Dla funkcji 2.x, użyj [Microsoft.Azure.WebJobs.Extensions.EventHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) pakietu, wersji 3.x.
Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Wyzwalacz

Użyj wyzwalacza usługi Event Hubs, aby reagować na zdarzenia wysłanego do strumienia zdarzeń Centrum zdarzeń. Musi mieć dostęp do odczytu do Centrum zdarzeń, aby skonfigurować wyzwalacz.

Po wyzwoleniu funkcję wyzwalacza usługi Event Hubs komunikat, który ją wywołuje jest przekazywany do funkcji jako ciąg.

## <a name="trigger---scaling"></a>Wyzwalanie — skalowanie

Każde wystąpienie funkcji wyzwalanej przez Centrum zdarzeń jest obsługiwane tylko przez jeden [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) wystąpienia. Usługa Event Hubs zapewnia, że tylko jeden [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) wystąpienia można uzyskać dzierżawy dla danej partycji.

Na przykład należy wziąć pod uwagę Centrum zdarzeń w następujący sposób:

* 10 partycje.
* 1000 zdarzeń rozkładane równomiernie pomiędzy wszystkie partycje, przy użyciu 100 wiadomości w poszczególnych partycjach.

Po włączeniu funkcji jest tylko jedno wystąpienie funkcji. Nadajmy to wystąpienie funkcji `Function_0`. `Function_0` ma jeden [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) wystąpienie, które ma dzierżawę na wszystkie partycje dziesięciu. To wystąpienie odczytuje zdarzenia z partycji 0 – 9. Od tej pory jedną z następujących sytuacji:

* **Nowe wystąpienia funkcji nie są potrzebne**: `Function_0` jest w stanie przetworzyć wszystkie zdarzenia 1000 przed funkcje skalowanie logika aktywowany. W takim przypadku wszystkie 1000 komunikaty są przetwarzane przez `Function_0`.

* **Następuje dodanie wystąpienia dodatkową funkcję**: Functions skalowanie logika ustali, że `Function_0` ma komunikatów jest większa niż może przetworzyć. W tym przypadku nowe wystąpienie aplikacji funkcji (`Function_1`) jest tworzony wraz z nową [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) wystąpienia. Usługa Event Hubs wykrywa, że nowe wystąpienie hosta próbuje odczytać wiadomości. Zdarzenie Hubs równoważy obciążenie partycji między jego wystąpienia hosta. Na przykład partycje 0-4 może być przypisana do `Function_0` i dzieli na partycje 5-9, aby `Function_1`. 

* **N większej liczby wystąpień funkcji są dodawane**: Functions skalowanie logika ustali, że oba `Function_0` i `Function_1` mają komunikatów jest większa niż ich może przetwarzać. Nowe wystąpienia aplikacji funkcji `Function_2`... `Functions_N` są tworzone, gdy `N` jest większa niż liczba partycji Centrum zdarzeń. W naszym przykładzie Usługa Event Hubs ponownie równoważy obciążenie partycji, w tym przypadku z wystąpień `Function_0`... `Functions_9`. 

Należy pamiętać, że w przypadku funkcji można skalować do `N` wystąpień, czyli liczbę większą niż liczba partycji Centrum zdarzeń. W ten sposób upewnić się, że istnieją zawsze [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) wystąpienia można uzyskać blokad na partycje, gdy tylko staną się dostępne z innymi wystąpieniami. Opłaty są naliczane tylko za zasoby używane podczas wystąpienia funkcja wykonuje; nie są naliczane nadmiernej aprowizacji.

Po zakończeniu wykonywania wszystkich funkcji (z lub bez błędów) punkty kontrolne zostaną dodane do skojarzonego konta magazynu. Po pomyślnym zakończeniu kontrolnych, wszystkie komunikaty 1000 nigdy nie są pobierane ponownie.

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , dzienniki treść wyzwalacz Centrum zdarzeń.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Aby uzyskać dostęp do [metadanych zdarzenia](#trigger---event-metadata) w kodzie funkcji powiązać [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) obiektu (wymaga przy użyciu instrukcji dla `Microsoft.ServiceBus.Messaging`). Te same właściwości można także przejść za pomocą wyrażenia wiązania w podpisie metody.  Obu sposobów uzyskania tych samych danych można znaleźć w poniższym przykładzie:

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

Aby odbierać zdarzenia w zadaniu wsadowym, należy wprowadzić `string` lub `EventData` tablicy:

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

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja rejestruje treść wyzwalacz Centrum zdarzeń.

W poniższych przykładach pokazano danych powiązania usługi Event Hubs w *function.json* pliku. Pierwszy przykład jest dla funkcji 2.x, a drugi to funkcji 1.x. 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Poniżej przedstawiono kod skryptu języka C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Aby uzyskać dostęp do [metadanych zdarzenia](#trigger---event-metadata) w kodzie funkcji powiązać [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) obiektu (wymaga przy użyciu instrukcji dla `Microsoft.ServiceBus.Messaging`). Te same właściwości można także przejść za pomocą wyrażenia wiązania w podpisie metody.  Obu sposobów uzyskania tych samych danych można znaleźć w poniższym przykładzie:

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

Aby odbierać zdarzenia w zadaniu wsadowym, należy wprowadzić `string` lub `EventData` tablicy:

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

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [funkcja języka F #](functions-reference-fsharp.md) powiązania, który używa. Funkcja rejestruje treść wyzwalacz Centrum zdarzeń.

W poniższych przykładach pokazano danych powiązania usługi Event Hubs w *function.json* pliku. Pierwszy przykład jest dla funkcji 2.x, a drugi to funkcji 1.x. 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Poniżej przedstawiono kod F #:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja odczytuje [metadanych zdarzenia](#trigger---event-metadata) i rejestruje wiadomość.

W poniższych przykładach pokazano danych powiązania usługi Event Hubs w *function.json* pliku. Pierwszy przykład jest dla funkcji 2.x, a drugi to funkcji 1.x. 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Event Hubs trigger function processed message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);
     
    context.done();
};
```

Aby odbierać zdarzenia w zadaniu wsadowym, należy ustawić `cardinality` do `many` w *function.json* pliku, jak pokazano w poniższych przykładach. Pierwszy przykład jest dla funkcji 2.x, a drugi to funkcji 1.x. 

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

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);
    
    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---java-example"></a>Wyzwalacz - przykładzie w języku Java

W poniższym przykładzie pokazano wyzwalacza Centrum zdarzeń, powiązanie w *function.json* pliku i [funkcja Java](functions-reference-java.md) powiązania, który używa. Funkcja rejestruje treści wyzwalacza Centrum zdarzeń.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context ) 
       {
          context.getLogger().info(message);
 }
 ```

 W [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime), użyj `EventHubTrigger` adnotacji w parametrach, którego wartość może pochodzić z Centrum zdarzeń. Parametry z tymi adnotacjami spowodować, że funkcja do uruchomienia po odebraniu zdarzenia.  Ta adnotacja mogą być używane z natywnych typów języka Java, obiektów typu Pojo lub wartości dopuszczających wartości null przy użyciu opcjonalnie<T>. 

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę Centrum zdarzeń, nazwę grupy odbiorców i nazwa ustawienia aplikacji zawierającego parametry połączenia. Aby uzyskać więcej informacji o tych ustawieniach, zobacz [wyzwolić sekcji konfiguracji](#trigger---configuration). Oto `EventHubTriggerAttribute` przykład atrybutu:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Aby uzyskać kompletny przykład, zobacz [wyzwalacza — przykład w języku C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `EventHubTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa `eventHubTrigger`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Nie dotyczy | Musi być równa `in`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje element zdarzenia w kodzie funkcji. | 
|**Ścieżka** |**EventHubName** | Funkcje 1.x tylko. Nazwa Centrum zdarzeń. Gdy nazwa Centrum zdarzeń jest również obecny w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. | 
|**eventHubName** |**EventHubName** | Działa tylko 2.x. Nazwa Centrum zdarzeń. Gdy nazwa Centrum zdarzeń jest również obecny w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**grupy konsumentów** |**grupy konsumentów** | Opcjonalna właściwość, która ustawia [grupy odbiorców](../event-hubs/event-hubs-features.md#event-consumers) używany do subskrybowania zdarzenia w Centrum. W przypadku pominięcia `$Default` używanie grupy odbiorców. | 
|**Kardynalność** | Nie dotyczy | Dla języka Javascript. Ustaw `many` w celu włączenia przetwarzania wsadowego.  Jeśli pominięty lub ustawiony jako `one`, jeden komunikat o przekazany do funkcji. | 
|**połączenia** |**połączenia** | Nazwa ustawienia aplikacji zawierającego parametry połączenia do przestrzeni nazw Centrum zdarzeń. Skopiować te parametry połączenia, klikając pozycję **informacje o połączeniu** przycisku [przestrzeni nazw](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), nie Centrum zdarzeń, sam. Te parametry połączenia muszą mieć co najmniej uprawnienia do odczytu wyzwalacz.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Wyzwalacz - metadanych zdarzenia

Wyzwalacz usługi Event Hubs zapewnia wiele [właściwości metadanych](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Te właściwości może służyć jako część wyrażenia wiązania w pozostałych powiązaniach lub jako parametry w kodzie. Są to właściwości [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) klasy.

|Właściwość|Typ|Opis|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` Wystąpienia.|
|`EnqueuedTimeUtc`|`DateTime`|Godzina umieszczonych w kolejce w formacie UTC.|
|`Offset`|`string`|Przesunięcie danych względem strumienia partycji Centrum zdarzeń. Przesunięcie jest znacznik lub identyfikator zdarzenia w obrębie strumienia usługi Event Hubs. Identyfikator jest unikatowy w ramach partycji strumienia usługi Event Hubs.|
|`PartitionKey`|`string`|Partycja, zdarzenia, które dane mają być wysyłane.|
|`Properties`|`IDictionary<String,Object>`|Właściwości użytkownika danych zdarzenia.|
|`SequenceNumber`|`Int64`|Sekwencja logiczna liczba zdarzenia.|
|`SystemProperties`|`IDictionary<String,Object>`|Właściwości systemu, w tym dane zdarzenia.|

Zobacz [przykłady kodu](#trigger---example) używające tych właściwości we wcześniejszej części tego artykułu.

## <a name="trigger---hostjson-properties"></a>Wyzwalacz — właściwości host.json

[Host.json](functions-host-json.md#eventhub) plik zawiera ustawienia, które kontrolują zachowanie wyzwalacza usługi Event Hubs.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Dane wyjściowe

Użyj usługi Event Hubs powiązania danych wyjściowych usługi można zapisać zdarzenia do strumienia zdarzeń. Konieczne jest posiadanie uprawnień wysłania do Centrum zdarzeń do zapisywania zdarzeń.

Upewnij się, odwołania do wymaganego pakietu zostały spełnione: [funkcje 1.x](#packages---functions-1.x) lub [funkcje 2.x](#packages---functions-2.x) 

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) który zapisuje komunikat do Centrum zdarzeń za pomocą wartość zwracaną metody jako dane wyjściowe:

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

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja zapisuje komunikat do Centrum zdarzeń.

W poniższych przykładach pokazano danych powiązania usługi Event Hubs w *function.json* pliku. Pierwszy przykład jest dla funkcji 2.x, a drugi to funkcji 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Oto C# script kod, który tworzy jeden komunikat:

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

W tym C# script kod, który tworzy wiele wiadomości:

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

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [funkcja języka F #](functions-reference-fsharp.md) powiązania, który używa. Funkcja zapisuje komunikat do Centrum zdarzeń.

W poniższych przykładach pokazano danych powiązania usługi Event Hubs w *function.json* pliku. Pierwszy przykład jest dla funkcji 2.x, a drugi to funkcji 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Poniżej przedstawiono kod F #:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja zapisuje komunikat do Centrum zdarzeń.

W poniższych przykładach pokazano danych powiązania usługi Event Hubs w *function.json* pliku. Pierwszy przykład jest dla funkcji 2.x, a drugi to funkcji 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Oto kod JavaScript, który wysyła pojedynczy komunikat:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Oto kod JavaScript, który wysyła wiele wiadomości:

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

### <a name="output---java-example"></a>Dane wyjściowe — przykładzie w języku Java

Poniższy przykład przedstawia funkcję języka Java, która zapisuje bieżący czas pomocy komunikatów do Centrum zdarzeń.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
 ```

W [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime), użyj `@EventHubOutput` adnotacji w parametrach, którego wartość będzie wynosić poublished do Centrum zdarzeń.  Parametr powinien być typu `OutputBinding<T>` , gdzie T jest obiektu typu POJO lub dowolnego typu natywnego języka Java. 

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

Aby uzyskać [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę Centrum zdarzeń i nazwa ustawienia aplikacji zawierającego parametry połączenia. Aby uzyskać więcej informacji o tych ustawieniach, zobacz [dane wyjściowe — Konfiguracja](#output---configuration). Oto `EventHub` przykład atrybutu:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Aby uzyskać kompletny przykład, zobacz [dane wyjściowe — przykład w języku C#](#output---c-example).

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `EventHub` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa "eventHub". |
|**direction** | Nie dotyczy | Musi być równa "out". Ten parametr ma wartość automatycznie podczas tworzenia powiązania w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej użytą w kodzie funkcji, który reprezentuje zdarzenie. | 
|**Ścieżka** |**EventHubName** | Funkcje 1.x tylko. Nazwa Centrum zdarzeń. Gdy nazwa Centrum zdarzeń jest również obecny w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. | 
|**eventHubName** |**EventHubName** | Działa tylko 2.x. Nazwa Centrum zdarzeń. Gdy nazwa Centrum zdarzeń jest również obecny w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**połączenia** |**połączenia** | Nazwa ustawienia aplikacji zawierającego parametry połączenia do przestrzeni nazw Centrum zdarzeń. Skopiować te parametry połączenia, klikając pozycję **informacje o połączeniu** przycisku *przestrzeni nazw*, nie Centrum zdarzeń, sam. Te parametry połączenia muszą mieć uprawnienia do wysyłania do wysyłania wiadomości do strumienia zdarzeń.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

W języku C# i skrypt języka C#, wysyłanie komunikatów za pomocą parametru metody, takie jak `out string paramName`. W języku C# `paramName` jest wartością określoną w `name` właściwość *function.json*. Aby zapisać wiele wiadomości, można użyć `ICollector<string>` lub `IAsyncCollector<string>` zamiast `out string`.

W języku JavaScript, zdarzenie wyjściowe uzyskują dostęp przy użyciu `context.bindings.<name>`. `<name>` jest wartością określoną w `name` właściwość *function.json*.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Informacje ogólne |
|---|---|
| Centrum zdarzeń | [Przewodnik obsługi](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
