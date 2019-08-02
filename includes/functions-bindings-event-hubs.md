---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 938d7e0cbd493dcb269418e9fd364611d734a085
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68589988"
---
## <a name="trigger"></a>Wyzwalacz

Użyj wyzwalacza funkcji, aby odpowiedzieć na zdarzenie wysłane do strumienia zdarzeń centrum zdarzeń. Aby skonfigurować wyzwalacz, musisz mieć dostęp do odczytu do odpowiedniego centrum zdarzeń. Gdy funkcja jest wyzwalana, komunikat przeszedł do funkcji jest wpisywany jako ciąg.

## <a name="trigger---scaling"></a>Skalowanie w poziomie

Każde wystąpienie funkcji wyzwalanej przez zdarzenie jest obsługiwane przez pojedyncze wystąpienie [klasy eventprocessorhost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) . Wyzwalacz (obsługiwany przez Event Hubs) gwarantuje, że tylko jedno wystąpienie [klasy eventprocessorhost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) może uzyskać dzierżawę dla danej partycji.

Rozważmy na przykład centrum zdarzeń w następujący sposób:

* 10 partycji
* zdarzenia 1 000 równomiernie rozłożone na wszystkie partycje, z 100 komunikatami w każdej partycji

Gdy funkcja jest włączona po raz pierwszy, istnieje tylko jedno wystąpienie funkcji. Wywołajmy pierwsze wystąpienie `Function_0`funkcji. Funkcja ma pojedyncze wystąpienie klasy eventprocessorhost, które przechowuje dzierżawę na wszystkich dziesięciu partycjach. [](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) `Function_0` To wystąpienie odczytuje zdarzenia z partycji 0-9. Od tego momentu następuje jedna z następujących sytuacji:

* **Nowe wystąpienia funkcji nie są konieczne**: `Function_0` można przetworzyć wszystkie zdarzenia 1 000 przed zastosowaniem logiki skalowania funkcji. W takim przypadku wszystkie komunikaty 1 000 są przetwarzane przez `Function_0`program.

* **Dodawane jest dodatkowe wystąpienie funkcji**: Jeśli logika skalowania funkcji określa `Function_0` , że program ma więcej komunikatów niż może przetworzyć, tworzone jest`Function_1`nowe wystąpienie aplikacji funkcji (). Ta nowa funkcja ma również skojarzone wystąpienie elementu [klasy eventprocessorhost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Ponieważ podstawowe Event Hubs wykrywają, że nowe wystąpienie hosta próbuje odczytywać komunikaty, obciążenie zawiera partycje w wystąpieniach hosta. Na przykład partycje 0-4 mogą być przypisane do `Function_0` i partycji 5-9 do. `Function_1`

* **Dodano więcej wystąpień funkcji**: Jeśli logika skalowania funkcji określa, `Function_0` że `Function_1` zarówno i ma więcej komunikatów niż można przetwarzać, tworzone są nowe `Functions_N` wystąpienia aplikacji funkcji.  Aplikacje są tworzone w punkcie, w `N` którym jest większa niż liczba partycji centrum zdarzeń. W naszym przykładzie Event Hubs ponownie równoważenia obciążenia w partycjach, w tym przypadku między wystąpieniami `Function_0`... `Functions_9`.

Gdy funkcje są skalowane `N` , wystąpienia są większe niż liczba partycji centrum zdarzeń. W tym celu należy upewnić się, że wystąpienia [klasy eventprocessorhost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) są dostępne do uzyskania blokad w partycjach, gdy staną się dostępne z innych wystąpień. Opłata jest naliczana tylko za zasoby używane, gdy wystąpienie funkcji jest wykonywane. Innymi słowy, nie jest naliczana opłata za tę nadmierną obsługę.

Po zakończeniu wykonywania wszystkich funkcji (z błędami lub bez nich) punkty kontrolne są dodawane do skojarzonego konta magazynu. Po pomyślnym sprawdzeniu, wszystkie komunikaty 1 000 nigdy nie są pobierane ponownie.

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład w języku C#

Poniższy przykład pokazuje [ C# funkcję](../articles/azure-functions/functions-dotnet-class-library.md) , która rejestruje treść komunikatu wyzwalacza centrum zdarzeń.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Aby uzyskać dostęp do [metadanych zdarzenia](#trigger---event-metadata) w kodzie funkcji, powiąż z obiektem [EVENTDATA](/dotnet/api/microsoft.servicebus.messaging.eventdata) (wymaga instrukcji using dla `Microsoft.Azure.EventHubs`). Możesz również uzyskać dostęp do tych samych właściwości przy użyciu wyrażeń powiązania w podpisie metody.  W poniższym przykładzie pokazano dwa sposoby uzyskania tych samych danych:

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

Do odbierania zdarzeń w partii, marka `string` lub `EventData` tablica.  

> [!NOTE]
> W przypadku odbierania w partii nie można powiązać parametrów metody, takich jak w powyższym `DateTime enqueuedTimeUtc` przykładzie z i muszą otrzymać te `EventData` dane z każdego obiektu  

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

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w pliku *Function. JSON* i [ C# funkcji skryptu](../articles/azure-functions/functions-reference-csharp.md) , która używa powiązania. Funkcja rejestruje treść komunikatu wyzwalacza centrum zdarzeń.

W poniższych przykładach pokazano Event Hubs powiązania danych w pliku *Function. JSON* .

#### <a name="version-2x"></a>Wersja 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Wersja 1. x

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

Aby uzyskać dostęp do [metadanych zdarzenia](#trigger---event-metadata) w kodzie funkcji, powiąż z obiektem [EVENTDATA](/dotnet/api/microsoft.servicebus.messaging.eventdata) (wymaga instrukcji using dla `Microsoft.Azure.EventHubs`). Możesz również uzyskać dostęp do tych samych właściwości przy użyciu wyrażeń powiązania w podpisie metody.  W poniższym przykładzie pokazano dwa sposoby uzyskania tych samych danych:

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

Aby odbierać zdarzenia w partii, Utwórz `string` lub `EventData` tablicę:

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

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w pliku *Function. JSON* i [ F# funkcję](../articles/azure-functions/functions-reference-fsharp.md) , która używa powiązania. Funkcja rejestruje treść komunikatu wyzwalacza centrum zdarzeń.

W poniższych przykładach pokazano Event Hubs powiązania danych w pliku *Function. JSON* . 

#### <a name="version-2x"></a>Wersja 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Wersja 1. x

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

W poniższym przykładzie pokazano powiązanie wyzwalacza centrum zdarzeń w pliku *Function. JSON* oraz [funkcja języka JavaScript](../articles/azure-functions/functions-reference-node.md) , która używa powiązania. Funkcja odczytuje [metadane zdarzeń](#trigger---event-metadata) i rejestruje komunikat.

W poniższych przykładach pokazano Event Hubs powiązania danych w pliku *Function. JSON* .

#### <a name="version-2x"></a>Wersja 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Wersja 1. x

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
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Aby odbierać zdarzenia w partii, ustaw wartość `cardinality` `many` w pliku *Function. JSON* , jak pokazano w poniższych przykładach.

#### <a name="version-2x"></a>Wersja 2. x

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

#### <a name="version-1x"></a>Wersja 1. x

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

### <a name="trigger---python-example"></a>Wyzwalacz — przykład w języku Python

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w pliku *Function. JSON* i [funkcji języka Python](../articles/azure-functions/functions-reference-python.md) , która używa powiązania. Funkcja odczytuje [metadane zdarzeń](#trigger---event-metadata) i rejestruje komunikat.

W poniższych przykładach pokazano Event Hubs powiązania danych w pliku *Function. JSON* .

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto kod języka Python:

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

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w pliku *Function. JSON* i [funkcję języka Java](../articles/azure-functions/functions-reference-java.md) , która używa powiązania. Funkcja rejestruje treść komunikatu wyzwalacza centrum zdarzeń.

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

 W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `EventHubTrigger` adnotacji w parametrach, których wartość pochodzi z centrum zdarzeń. Parametry z tymi adnotacjami powodują, że funkcja jest uruchamiana po nadejściu zdarzenia.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy\<użyciu opcjonalnego T >.

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [ C# bibliotekach klas](../articles/azure-functions/functions-dotnet-class-library.md)Użyj atrybutu [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) .

Konstruktor atrybutu przyjmuje nazwę centrum zdarzeń, nazwę grupy odbiorców i nazwę ustawienia aplikacji, które zawiera parametry połączenia... Więcej informacji o tych ustawieniach znajduje się w [sekcji Konfiguracja wyzwalacza](#trigger---configuration). Oto przykład atrybutu `EventHubTriggerAttribute` :

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
|**Ścieżka** |**EventHubName** | Tylko funkcje 1. x. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecna w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**eventHubName** |**EventHubName** | Tylko funkcje 2. x. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecna w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**odbiorca** |**Odbiorca** | Opcjonalna właściwość, która ustawia [grupę konsumentów](../articles/event-hubs/event-hubs-features.md)#event — odbiorcy) służącą do subskrybowania zdarzeń w centrum. W `$Default` przypadku pominięcia zostanie użyta Grupa konsumentów. |
|**kardynalności** | Nie dotyczy | Dla języka JavaScript. `many` Ustaw wartość na, aby włączyć przetwarzanie wsadowe.  W przypadku pominięcia lub `one`ustawienia jako, pojedynczy komunikat przeszedł do funkcji. |
|**połączenia** |**połączenia** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia z przestrzenią nazw centrum zdarzeń. Skopiuj te parametry połączenia, klikając przycisk **Informacje o połączeniu** dla [przestrzeni nazw](../articles/event-hubs/event-hubs-create.md)#create-a-Event-Hub-przestrzeń nazw), a nie do samego centrum zdarzeń. Te parametry połączenia muszą mieć co najmniej uprawnienia do odczytu w celu aktywowania wyzwalacza.|
|**Ścieżka**|**EventHubName**|Nazwa centrum zdarzeń. Można odwoływać się za pomocą ustawień aplikacji`%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Wyzwalacz — metadane zdarzenia

Wyzwalacz Event Hubs zawiera kilka [właściwości metadanych](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Te właściwości może służyć jako część wyrażenia wiązania w pozostałych powiązaniach lub jako parametry w kodzie. Są to właściwości klasy [EVENTDATA](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) .

|Właściwość|Typ|Opis|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` Wystąpienie.|
|`EnqueuedTimeUtc`|`DateTime`|Godzina umieszczonych w kolejce w formacie UTC.|
|`Offset`|`string`|Przesunięcie danych względem strumienia partycji centrum zdarzeń. Przesunięcie jest znacznikiem lub identyfikatorem zdarzenia w strumieniu Event Hubs. Identyfikator jest unikatowy w obrębie partycji Event Hubs strumienia.|
|`PartitionKey`|`string`|Partycja, do której mają być wysyłane dane zdarzeń.|
|`Properties`|`IDictionary<String,Object>`|Właściwości użytkownika danych zdarzenia.|
|`SequenceNumber`|`Int64`|Numer sekwencji logicznej zdarzenia.|
|`SystemProperties`|`IDictionary<String,Object>`|Właściwości systemu, w tym dane zdarzenia.|

Zobacz [przykłady kodu](#trigger---example) używające tych właściwości we wcześniejszej części tego artykułu.

## <a name="trigger---hostjson-properties"></a>Wyzwalacz — właściwości host.json

Plik [host. JSON](../articles/azure-functions/functions-host-json.md#eventhub) zawiera ustawienia sterujące zachowaniem wyzwalacza Event Hubs.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Output

Użyj powiązania danych wyjściowych Event Hubs do zapisywania zdarzeń w strumieniu zdarzeń. Użytkownik musi mieć uprawnienia do wysyłania do centrum zdarzeń w celu zapisywania do niego zdarzeń.

Upewnij się, że istnieją wymagane odwołania do pakietów: Functions 1. x lub Functions 2. x

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

W poniższym przykładzie pokazano [ C# funkcję](../articles/azure-functions/functions-dotnet-class-library.md) , która zapisuje komunikat w centrum zdarzeń przy użyciu wartości zwracanej przez metodę jako dane wyjściowe:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

Poniższy przykład pokazuje, `IAsyncCollector` jak za pomocą interfejsu wysyłać partie komunikatów. Ten scenariusz jest typowy podczas przetwarzania komunikatów pochodzących z jednego centrum zdarzeń i wysyłania wyników do innego centrum zdarzeń.

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

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w pliku *Function. JSON* i [ C# funkcji skryptu](../articles/azure-functions/functions-reference-csharp.md) , która używa powiązania. Funkcja zapisuje komunikat w centrum zdarzeń.

W poniższych przykładach pokazano Event Hubs powiązania danych w pliku *Function. JSON* . Pierwszy przykład dotyczy funkcji 2. x, a drugi jest dla funkcji 1. x. 

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

Kod C# skryptu, który tworzy jeden komunikat:

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

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w pliku *Function. JSON* i [ F# funkcję](../articles/azure-functions/functions-reference-fsharp.md) , która używa powiązania. Funkcja zapisuje komunikat w centrum zdarzeń.

W poniższych przykładach pokazano Event Hubs powiązania danych w pliku *Function. JSON* . Pierwszy przykład dotyczy funkcji 2. x, a drugi jest dla funkcji 1. x. 

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

W poniższym przykładzie pokazano powiązanie wyzwalacza centrum zdarzeń w pliku *Function. JSON* oraz [funkcja języka JavaScript](../articles/azure-functions/functions-reference-node.md) , która używa powiązania. Funkcja zapisuje komunikat w centrum zdarzeń.

W poniższych przykładach pokazano Event Hubs powiązania danych w pliku *Function. JSON* . Pierwszy przykład dotyczy funkcji 2. x, a drugi jest dla funkcji 1. x. 

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

Kod JavaScript, który wysyła pojedynczy komunikat:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Kod JavaScript, który wysyła wiele komunikatów:

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

### <a name="output---python-example"></a>Dane wyjściowe — przykład w języku Python

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w pliku *Function. JSON* i [funkcji języka Python](../articles/azure-functions/functions-reference-python.md) , która używa powiązania. Funkcja zapisuje komunikat w centrum zdarzeń.

W poniższych przykładach pokazano Event Hubs powiązania danych w pliku *Function. JSON* .

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Oto kod języka Python, który wysyła pojedynczy komunikat:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

### <a name="output---java-example"></a>Dane wyjściowe — przykładzie w języku Java

Poniższy przykład pokazuje funkcję języka Java, która zapisuje komunikat contianing bieżący czas do centrum zdarzeń.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@EventHubOutput` adnotacji w parametrach, których wartość zostanie opublikowana w centrum zdarzeń.  Parametr powinien być typu `OutputBinding<T>` , gdzie T jest Pojo lub dowolnym natywnym typem języka Java.

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W przypadku [ C# bibliotek klas](../articles/azure-functions/functions-dotnet-class-library.md)Użyj atrybutu [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) .

Konstruktor atrybutu przyjmuje nazwę centrum zdarzeń i nazwę ustawienia aplikacji, które zawiera parametry połączenia. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [Output-Configuration](#output---configuration). Oto przykład atrybutu `EventHub` :

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
|**type** | Nie dotyczy | Wartość musi być równa "eventHub". |
|**direction** | Nie dotyczy | Musi być równa "out". Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w Azure Portal. |
|**name** | Nie dotyczy | Nazwa zmiennej używana w kodzie funkcji, która reprezentuje zdarzenie. |
|**Ścieżka** |**EventHubName** | Tylko funkcje 1. x. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecna w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**eventHubName** |**EventHubName** | Tylko funkcje 2. x. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecna w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**połączenia** |**połączenia** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia z przestrzenią nazw centrum zdarzeń. Skopiuj te parametry połączenia, klikając przycisk **Informacje o połączeniu** dla *obszaru nazw*, a nie samego centrum zdarzeń. Te parametry połączenia muszą mieć uprawnienia do wysyłania, aby wysłać wiadomość do strumienia zdarzeń.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

W C# skryptach i C# Wysyłaj komunikaty przy użyciu parametru metody, takiego `out string paramName`jak. W C# skrypcie `paramName` jest `name` wartością określoną we właściwości *Function. JSON*. Aby napisać wiele komunikatów, można użyć `ICollector<string>` lub `IAsyncCollector<string>` `out string`zamiast.

W języku JavaScript dostęp do zdarzenia wyjściowego przy użyciu `context.bindings.<name>`. `<name>`jest wartością określoną we `name` właściwości *Function. JSON*.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Tematy pomocy |
|---|---|
| Centrum zdarzeń | [Przewodnik obsługi](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

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
|maxBatchSize|64|Maksymalna liczba zdarzeń odebranych na pętlę odbierania.|
|prefetchCount|Nie dotyczy|Domyślny PrefetchCount, który będzie używany przez bazowe klasy eventprocessorhost.|
|batchCheckpointFrequency|1|Liczba partii zdarzeń do przetworzenia przed utworzeniem punktu kontrolnego kursora centrum EventHub.|
