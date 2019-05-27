---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 421e0db48f045c5cbce52a0641902e6d2a11276e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132462"
---
## <a name="trigger"></a>Wyzwalacz

Użyj wyzwalacza funkcji, aby reagować na zdarzenia wysłanego do strumienia zdarzeń Centrum zdarzeń. Musi mieć uprawnienia do odczytu w podstawowym Centrum zdarzeń, aby skonfigurować wyzwalacz. Gdy funkcja jest wyzwalana, komunikat przekazany do funkcji jest wpisana jako ciąg.

## <a name="trigger---scaling"></a>Wyzwalanie — skalowanie

Każde wystąpienie funkcji zdarzenie wyzwolone opiera się na pojedynczej [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) wystąpienia. Wyzwalacz (obsługiwane przez usługi Event Hubs) zapewnia, że tylko jeden [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) wystąpienia można uzyskać dzierżawy dla danej partycji.

Na przykład należy wziąć pod uwagę Centrum zdarzeń w następujący sposób:

* partycje 10
* 1000 zdarzeń rozkładane równomiernie pomiędzy wszystkie partycje, przy użyciu 100 wiadomości w poszczególnych partycjach

Po włączeniu funkcji jest tylko jedno wystąpienie funkcji. Nadajmy pierwsze wystąpienie funkcji `Function_0`. `Function_0` Funkcja ma pojedyncze wystąpienie [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) przechowuje dzierżawę na wszystkich partycjach dziesięciu. To wystąpienie odczytuje zdarzenia z partycji 0 – 9. Od tej pory jedną z następujących sytuacji:

* **Nowe wystąpienia funkcji nie są potrzebne**: `Function_0` jest w stanie przetworzyć wszystkie zdarzenia 1000, zanim zaczną działać funkcje skalowania logiki. W takim przypadku wszystkie 1000 komunikatów przetwarzanych przez `Function_0`.

* **Następuje dodanie wystąpienia dodatkową funkcję**: Jeśli funkcje skalowanie logika ustali, że `Function_0` ma komunikatów jest większa niż może przetworzyć, nowe wystąpienie aplikacji funkcji (`Function_1`) jest tworzony. Ta nowa funkcja ma również skojarzone wystąpienie [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Zgodnie z bazowego Event Hubs wykryje, że nowe wystąpienie hosta próbuje odczytać wiadomości, jego równoważenia obciążenia partycji w jego wystąpienia hosta. Na przykład partycje 0-4 może być przypisana do `Function_0` i dzieli na partycje 5-9, aby `Function_1`.

* **N dodaniu większej liczby wystąpień funkcji**: Jeśli funkcje skalowanie logika ustali, że oba `Function_0` i `Function_1` mają komunikatów jest większa niż ich może przetwarzać, nowe `Functions_N` są tworzone wystąpienia aplikacji funkcji.  Aplikacje są tworzone w punkcie gdzie `N` jest większa niż liczba partycji Centrum zdarzeń. W naszym przykładzie Usługa Event Hubs ponownie równoważy obciążenie partycji, w tym przypadku z wystąpień `Function_0`... `Functions_9`.

Gdy funkcje skale, `N` wystąpień jest liczba większa niż liczba partycji Centrum zdarzeń. W ten sposób zapewnienia [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) dostępnych wystąpień uzyskać blokad na partycje, gdy tylko staną się niedostępne z innych wystąpień. Opłaty są naliczane tylko za zasoby używane podczas wykonywania wystąpienia funkcji. Innymi słowy nie są naliczane nadmiernej aprowizacji.

Po zakończeniu wykonywania wszystkich funkcji (z lub bez błędów) punkty kontrolne zostaną dodane do skojarzonego konta magazynu. Po pomyślnym zakończeniu kontrolnych, wszystkie komunikaty 1000 nigdy nie są pobierane ponownie.

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](../articles/azure-functions/functions-dotnet-class-library.md) , dzienniki treść wyzwalacz Centrum zdarzeń.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Aby uzyskać dostęp do [metadanych zdarzenia](#trigger---event-metadata) w kodzie funkcji powiązać [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) obiektu (wymaga przy użyciu instrukcji dla `Microsoft.Azure.EventHubs`). Te same właściwości można także przejść za pomocą wyrażenia wiązania w podpisie metody.  Obu sposobów uzyskania tych samych danych można znaleźć w poniższym przykładzie:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Aby odbierać zdarzenia w zadaniu wsadowym, należy wprowadzić `string` lub `EventData` tablicy.  

> [!NOTE]
> Podczas odbierania w partii nie można powiązać parametry metody, jak pokazano w powyższym przykładzie, za pomocą `DateTime enqueuedTimeUtc` i musi odebrać je z każdego `EventData` obiektu  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [funkcji skryptu w języku C#](../articles/azure-functions/functions-reference-csharp.md) powiązania, który używa. Funkcja rejestruje treść wyzwalacz Centrum zdarzeń.

W poniższych przykładach pokazano danych powiązania usługi Event Hubs w *function.json* pliku.

#### <a name="version-2x"></a>W wersji 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>W wersji 1.x

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
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Aby uzyskać dostęp do [metadanych zdarzenia](#trigger---event-metadata) w kodzie funkcji powiązać [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) obiektu (wymaga przy użyciu instrukcji dla `Microsoft.Azure.EventHubs`). Te same właściwości można także przejść za pomocą wyrażenia wiązania w podpisie metody.  Obu sposobów uzyskania tych samych danych można znaleźć w poniższym przykładzie:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

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
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Wyzwalacz - F# przykład

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [ F# funkcja](../articles/azure-functions/functions-reference-fsharp.md) powiązania, który używa. Funkcja rejestruje treść wyzwalacz Centrum zdarzeń.

W poniższych przykładach pokazano danych powiązania usługi Event Hubs w *function.json* pliku. 

#### <a name="version-2x"></a>W wersji 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>W wersji 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto F# kodu:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [funkcji JavaScript](../articles/azure-functions/functions-reference-node.md) powiązania, który używa. Funkcja odczytuje [metadanych zdarzenia](#trigger---event-metadata) i rejestruje wiadomość.

W poniższych przykładach pokazano danych powiązania usługi Event Hubs w *function.json* pliku.

#### <a name="version-2x"></a>W wersji 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>W wersji 1.x

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
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Aby odbierać zdarzenia w zadaniu wsadowym, należy ustawić `cardinality` do `many` w *function.json* pliku, jak pokazano w poniższych przykładach.

#### <a name="version-2x"></a>W wersji 2.x

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

#### <a name="version-1x"></a>W wersji 1.x

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

### <a name="trigger---python-example"></a>Wyzwalacz — przykładem w języku Python

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [funkce Pythonu](../articles/azure-functions/functions-reference-python.md) powiązania, który używa. Funkcja odczytuje [metadanych zdarzenia](#trigger---event-metadata) i rejestruje wiadomość.

W poniższych przykładach pokazano danych powiązania usługi Event Hubs w *function.json* pliku.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Poniżej przedstawiono kod języka Python:

```python
import logging
import azure.functions as func

def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>Wyzwalacz - przykładzie w języku Java

W poniższym przykładzie pokazano wyzwalacza Centrum zdarzeń, powiązanie w *function.json* pliku i [funkcja Java](../articles/azure-functions/functions-reference-java.md) powiązania, który używa. Funkcja rejestruje treści wyzwalacza Centrum zdarzeń.

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

W [bibliotek klas języka C#](../articles/azure-functions/functions-dotnet-class-library.md), użyj [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę Centrum zdarzeń, nazwę grupy odbiorców i nazwa ustawienia aplikacji zawierającego parametry połączenia. Aby uzyskać więcej informacji o tych ustawieniach, zobacz [wyzwolić sekcji konfiguracji](#trigger---configuration). Oto `EventHubTriggerAttribute` przykład atrybutu:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
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
|**consumerGroup** |**ConsumerGroup** | Opcjonalna właściwość, która ustawia [grupy odbiorców](../articles/event-hubs/event-hubs-features.md)odbiorców event #) używany do subskrybowania zdarzenia w Centrum. W przypadku pominięcia `$Default` używanie grupy odbiorców. |
|**Kardynalność** | Nie dotyczy | Dla języka Javascript. Ustaw `many` w celu włączenia przetwarzania wsadowego.  Jeśli pominięty lub ustawiony jako `one`, jeden komunikat o przekazany do funkcji. |
|**połączenia** |**połączenia** | Nazwa ustawienia aplikacji zawierającego parametry połączenia do przestrzeni nazw Centrum zdarzeń. Skopiować te parametry połączenia, klikając pozycję **informacje o połączeniu** przycisku [przestrzeni nazw](../articles/event-hubs/event-hubs-create.md)#create na--hubs-przestrzeń nazw usługi event), nie Centrum zdarzeń, sam. Te parametry połączenia muszą mieć co najmniej uprawnienia do odczytu wyzwalacz.|
|**Ścieżka**|**EventHubName**|Nazwa Centrum zdarzeń. Można odwoływać się za pomocą ustawień aplikacji `%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Wyzwalacz - metadanych zdarzenia

Wyzwalacz usługi Event Hubs zapewnia wiele [właściwości metadanych](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Te właściwości może służyć jako część wyrażenia wiązania w pozostałych powiązaniach lub jako parametry w kodzie. Są to właściwości [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) klasy.

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

[Host.json](../articles/azure-functions/functions-host-json.md#eventhub) plik zawiera ustawienia, które kontrolują zachowanie wyzwalacza usługi Event Hubs.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Dane wyjściowe

Użyj usługi Event Hubs powiązania danych wyjściowych usługi można zapisać zdarzenia do strumienia zdarzeń. Konieczne jest posiadanie uprawnień wysłania do Centrum zdarzeń do zapisywania zdarzeń.

Upewnij się, odwołania do wymaganego pakietu zostały spełnione: Funkcje 1.x i 2.x funkcji

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](../articles/azure-functions/functions-dotnet-class-library.md) który zapisuje komunikat do Centrum zdarzeń za pomocą wartość zwracaną metody jako dane wyjściowe:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Poniższy przykład pokazuje sposób użycia `IAsyncCollector` interfejsu została wysłana partia komunikatów. Ten scenariusz jest typowy podczas przetwarzania komunikatów pochodzących z jednego Centrum zdarzeń i wysyła wynik do innego Centrum zdarzeń.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

### <a name="output---c-script-example"></a>Dane wyjściowe — przykładowy skrypt w języku C#

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [funkcji skryptu w języku C#](../articles/azure-functions/functions-reference-csharp.md) powiązania, który używa. Funkcja zapisuje komunikat do Centrum zdarzeń.

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
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

W tym C# script kod, który tworzy wiele wiadomości:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Dane wyjściowe — F# przykład

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [ F# funkcja](../articles/azure-functions/functions-reference-fsharp.md) powiązania, który używa. Funkcja zapisuje komunikat do Centrum zdarzeń.

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

Oto F# kodu:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [funkcji JavaScript](../articles/azure-functions/functions-reference-node.md) powiązania, który używa. Funkcja zapisuje komunikat do Centrum zdarzeń.

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
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Oto kod JavaScript, który wysyła wiele wiadomości:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>Dane wyjściowe — przykładem w języku Python

W poniższym przykładzie pokazano wyzwalacz Centrum zdarzeń, powiązanie w *function.json* pliku i [funkce Pythonu](../articles/azure-functions/functions-reference-python.md) powiązania, który używa. Funkcja zapisuje komunikat do Centrum zdarzeń.

W poniższych przykładach pokazano danych powiązania usługi Event Hubs w *function.json* pliku.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Oto Python kod, który wysyła pojedynczy komunikat:

```python
import datetime
import logging
import azure.functions as func

def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp);   
    return 'Message created at: {}'.format(timestamp)
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

W [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime), użyj `@EventHubOutput` adnotacji w parametrach, którego wartość będą publikowane do Centrum zdarzeń.  Parametr powinien być typu `OutputBinding<T>` , gdzie T jest obiektu typu POJO lub dowolnego typu natywnego języka Java.

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

Aby uzyskać [bibliotek klas języka C#](../articles/azure-functions/functions-dotnet-class-library.md), użyj [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) atrybutu.

Konstruktor atrybutu ma nazwę Centrum zdarzeń i nazwa ustawienia aplikacji zawierającego parametry połączenia. Aby uzyskać więcej informacji o tych ustawieniach, zobacz [dane wyjściowe — Konfiguracja](#output---configuration). Oto `EventHub` przykład atrybutu:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
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
|**name** | Nie dotyczy | Nazwa zmiennej użytą w kodzie funkcji, który reprezentuje zdarzenie. |
|**Ścieżka** |**EventHubName** | Funkcje 1.x tylko. Nazwa Centrum zdarzeń. Gdy nazwa Centrum zdarzeń jest również obecny w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**eventHubName** |**EventHubName** | Działa tylko 2.x. Nazwa Centrum zdarzeń. Gdy nazwa Centrum zdarzeń jest również obecny w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**połączenia** |**połączenia** | Nazwa ustawienia aplikacji zawierającego parametry połączenia do przestrzeni nazw Centrum zdarzeń. Skopiować te parametry połączenia, klikając pozycję **informacje o połączeniu** przycisku *przestrzeni nazw*, nie Centrum zdarzeń, sam. Te parametry połączenia muszą mieć uprawnienia do wysyłania do wysyłania wiadomości do strumienia zdarzeń.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

W języku C# i skrypt języka C#, wysyłanie komunikatów za pomocą parametru metody, takie jak `out string paramName`. W języku C# `paramName` jest wartością określoną w `name` właściwość *function.json*. Aby zapisać wiele wiadomości, można użyć `ICollector<string>` lub `IAsyncCollector<string>` zamiast `out string`.

W języku JavaScript, zdarzenie wyjściowe uzyskują dostęp przy użyciu `context.bindings.<name>`. `<name>` jest wartością określoną w `name` właściwość *function.json*.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Odwołanie |
|---|---|
| Centrum zdarzeń usługi Event Hubs | [Przewodnik obsługi](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Ustawienia host.JSON

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2.x. Przykład pliku host.json poniżej zawiera tylko ustawienia 2.x wersji dla tego powiązania. Aby uzyskać więcej informacji na temat globalne ustawienia konfiguracji w wersji 2.x, zobacz [dokumentacja pliku host.JSON dla usługi Azure Functions w wersji 2.x](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Odwołanie host.json w funkcjach 1.x, zobacz [dokumentacja pliku host.JSON dla usługi Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|maxBatchSize|64|Maksymalna otrzymana liczba zdarzeń na pętli odbioru.|
|prefetchCount|Nie dotyczy|Domyślnie PrefetchCount używanej przez podstawowej klasy EventProcessorHost.|
|batchCheckpointFrequency|1|Liczba zdarzeń wsadów przetwarzana przed utworzeniem punktu kontrolnego EventHub kursora.|