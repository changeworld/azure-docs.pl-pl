---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 438e3166e27511780dd871b5076a7b28ebade052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589716"
---
Użyj wyzwalacza funkcji, aby odpowiedzieć na zdarzenie wysłane do strumienia zdarzeń centrum zdarzeń. Aby skonfigurować wyzwalacz, musisz mieć dostęp do odczytu do centrum zdarzeń źródłowych. Po wyzwoleniu funkcji komunikat przekazany do funkcji jest wpisywany jako ciąg.

## <a name="scaling"></a>Skalowanie

Każde wystąpienie funkcji wyzwalanej zdarzeniem jest wspierane przez pojedyncze [wystąpienie EventProcessorHost.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) Wyzwalacz (zasilany przez centra zdarzeń) zapewnia, że tylko jedno [wystąpienie EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) może uzyskać dzierżawę na danej partycji.

Na przykład należy wziąć pod uwagę Centrum zdarzeń w następujący sposób:

* 10 partycji
* 1000 zdarzeń rozmieszczonych równomiernie na wszystkich partycjach, przy 100 wiadomościach na każdej partycji

Gdy funkcja jest po raz pierwszy włączona, istnieje tylko jedno wystąpienie funkcji. Nazwijmy pierwsze wystąpienie `Function_0`funkcji . Funkcja `Function_0` ma jedno wystąpienie [EventProcessorHost,](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) który posiada dzierżawy na wszystkich dziesięciu partycji. To wystąpienie odczytuje zdarzenia z partycji 0-9. Od tego momentu dzieje się jedna z następujących sytuacji:

* **Nowe wystąpienia funkcji nie są potrzebne:** `Function_0` jest w stanie przetworzyć wszystkie zdarzenia 1000 przed funkcje skalowania logiki efektu. W takim przypadku wszystkie 1000 wiadomości są `Function_0`przetwarzane przez .

* **Dodatkowe wystąpienie funkcji jest dodawany:** Jeśli funkcje skalowania logiki określa, że `Function_0` ma`Function_1`więcej komunikatów niż może przetwarzać, nowe wystąpienie aplikacji funkcji ( ) jest tworzony. Ta nowa funkcja ma również skojarzone wystąpienie [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Gdy podstawowe centra zdarzeń wykrywają, że nowe wystąpienie hosta próbuje odczytać wiadomości, ładuje partycje między wystąpieniami hosta. Na przykład partycje 0-4 mogą `Function_0` być przypisane do partycji `Function_1`5-9 do .

* **N więcej wystąpień funkcji są dodawane:** Jeśli funkcje `Function_1` skalowania logiki określa, `Functions_N` że zarówno `Function_0` i mają więcej komunikatów niż mogą przetwarzać, nowe wystąpienia aplikacji funkcji są tworzone.  Aplikacje są tworzone `N` do punktu, w którym jest większa niż liczba partycji centrum zdarzeń. W naszym przykładzie Usługi zdarzeń ponownie równoważy obciążenia partycji, `Function_0`w tym przypadku w wystąpieniach ... `Functions_9`.

W miarę `N` skalowania wystąpień jest liczba większa niż liczba partycji centrum zdarzeń. Ten wzorzec jest używany w celu zapewnienia [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) wystąpienia są dostępne do uzyskania blokad na partycjach, ponieważ stają się one dostępne z innych wystąpień. Opłaty są naliczane tylko za zasoby używane podczas wykonywania wystąpienia funkcji. Innymi słowy nie są pobierane za to nadmierne inicjowanie obsługi administracyjnej.

Po zakończeniu wykonywania wszystkich funkcji (z błędami lub bez), punkty kontrolne są dodawane do skojarzonego konta magazynu. Po pomyślnym wskazywaniem zaznaczenia wszystkie 1000 wiadomości nigdy nie zostaną pobrane ponownie.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

W poniższym przykładzie pokazano [funkcję Języka C#,](../articles/azure-functions/functions-dotnet-class-library.md) która rejestruje treść komunikatu wyzwalacza centrum zdarzeń.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Aby uzyskać dostęp do [metadanych zdarzeń](#event-metadata) w kodzie funkcji, należy `Microsoft.Azure.EventHubs`powiązać z obiektem [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (wymaga instrukcji using). Można również uzyskać dostęp do tych samych właściwości przy użyciu wyrażeń wiązania w podpisie metody.  Poniższy przykład pokazuje oba sposoby, aby uzyskać te same dane:

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

Aby odbierać zdarzenia w `string` `EventData` partii, zrobić lub tablicy.  

> [!NOTE]
> Podczas odbierania w partii nie można powiązać z `DateTime enqueuedTimeUtc` parametrami metody, `EventData` jak w powyższym przykładzie z i musi otrzymać je z każdego obiektu  

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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza centrum zdarzeń w pliku *function.json* i [funkcję skryptu Języka C#,](../articles/azure-functions/functions-reference-csharp.md) która używa powiązania. Funkcja rejestruje treść komunikatu wyzwalacza centrum zdarzeń.

Poniższe przykłady pokazują dane powiązania centrum zdarzeń w pliku *function.json.*

### <a name="version-2x-and-higher"></a>Wersja 2.x lub nowsza

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Wersja 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto kod skryptu języka C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Aby uzyskać dostęp do [metadanych zdarzeń](#event-metadata) w kodzie funkcji, należy `Microsoft.Azure.EventHubs`powiązać z obiektem [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (wymaga instrukcji using). Można również uzyskać dostęp do tych samych właściwości przy użyciu wyrażeń wiązania w podpisie metody.  Poniższy przykład pokazuje oba sposoby, aby uzyskać te same dane:

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

Aby odbierać zdarzenia w `string` `EventData` partii, make lub tablicy:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza centrum zdarzeń w pliku *function.json* i [funkcję JavaScript,](../articles/azure-functions/functions-reference-node.md) która używa powiązania. Funkcja odczytuje [metadane zdarzeń](#event-metadata) i rejestruje komunikat.

Poniższe przykłady pokazują dane powiązania centrum zdarzeń w pliku *function.json.*

### <a name="version-2x-and-higher"></a>Wersja 2.x lub nowsza

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Wersja 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto kod JavaScript:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Aby odbierać zdarzenia w `cardinality` `many` partii, należy ustawić w pliku *function.json,* jak pokazano w poniższych przykładach.

### <a name="version-2x-and-higher"></a>Wersja 2.x lub nowsza

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

### <a name="version-1x"></a>Wersja 1.x

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

Oto kod JavaScript:

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

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano powiązanie wyzwalacza centrum zdarzeń w pliku *function.json* i [funkcję Języka Python,](../articles/azure-functions/functions-reference-python.md) która używa powiązania. Funkcja odczytuje [metadane zdarzeń](#event-metadata) i rejestruje komunikat.

Poniższe przykłady pokazują dane powiązania centrum zdarzeń w pliku *function.json.*

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto kod Pythona:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

# <a name="java"></a>[Java](#tab/java)

W poniższym przykładzie pokazano powiązanie wyzwalacza centrum zdarzeń, które rejestruje treść komunikatu wyzwalacza Centrum zdarzeń.

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

 W [bibliotece środowiska wykonawczego](/java/api/overview/azure/functions/runtime)funkcji `EventHubTrigger` Java użyj adnotacji na temat parametrów, których wartość pochodziłaby z Centrum zdarzeń. Parametry z tymi adnotacjami powodują, że funkcja jest uruchamiana po nadejściu zdarzenia.  Ta adnotacja może być używana z natywnymi typami `Optional<T>`Java, POJO lub wartościami nullable przy użyciu .

 ---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](../articles/azure-functions/functions-dotnet-class-library.md)użyj atrybutu [EventHubTriggerAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs)

Konstruktor atrybutu przyjmuje nazwę centrum zdarzeń, nazwę grupy odbiorców i nazwę ustawienia aplikacji zawierającego parametry połączenia. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [sekcję konfiguracji wyzwalacza](#configuration). Oto przykład `EventHubTriggerAttribute` atrybutu:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Aby uzyskać pełny przykład, zobacz [Trigger - C# przykład](#example).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Z [biblioteki środowiska wykonawczego funkcji](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)Java użyj adnotacji [EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) na temat parametrów, których wartość będzie pochodzić z Centrum zdarzeń. Parametry z tymi adnotacjami powodują, że funkcja jest uruchamiana po nadejściu zdarzenia. Ta adnotacja może być używana z natywnymi typami `Optional<T>`Java, POJO lub wartościami nullable przy użyciu .

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `EventHubTrigger` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być `eventHubTrigger`ustawiona na . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal.|
|**Kierunku** | Nie dotyczy | Musi być `in`ustawiona na . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej reprezentującej element zdarzenia w kodzie funkcji. |
|**Ścieżka** |**Nazwa eventhubname** | Funkcje tylko 1.x. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecny w ciągu połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**eventHubName** |**Nazwa eventhubname** | Funkcje 2.x lub wyższe. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecny w ciągu połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. Można odwoływać się za pomocą ustawień aplikacji %eventHubName% |
|**grupa konsumentów** |**Grupa konsumentów** | Opcjonalna właściwość, która ustawia [grupę odbiorców](../articles/event-hubs/event-hubs-features.md#event-consumers) używaną do subskrybowania zdarzeń w centrum. W przypadku pominięcia `$Default` używana jest grupa odbiorców. |
|**Kardynalność** | Nie dotyczy | Dla Javascript. Ustaw `many` na, aby włączyć przetwarzanie wsadowe.  Jeśli pominięto lub `one`ustawiono na , do funkcji jest przekazywana pojedyncza wiadomość. |
|**Połączenia** |**Połączenia** | Nazwa ustawienia aplikacji zawierającego parametry połączenia z obszarem nazw centrum zdarzeń. Skopiuj ten ciąg połączenia, klikając przycisk **Informacje o połączeniu** dla obszaru [nazw,](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)a nie sam koncentrator zdarzeń. Ten ciąg połączenia musi mieć co najmniej uprawnienia do odczytu, aby aktywować wyzwalacz.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>Metadane zdarzeń

Wyzwalacz Centrum zdarzeń udostępnia kilka [właściwości metadanych.](../articles/azure-functions/./functions-bindings-expressions-patterns.md) Właściwości metadanych mogą być używane jako część wyrażeń wiązania w innych powiązaniach lub jako parametry w kodzie. Właściwości pochodzą z [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) klasy.

|Właściwość|Typ|Opis|
|--------|----|-----------|
|`PartitionContext`|[Kontekst partycji](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Wystąpienie elementu `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Czas w kolejce w utc.|
|`Offset`|`string`|Przesunięcie danych względem strumienia partycji Usługi Event Hub. Przesunięcie jest znacznikiem lub identyfikatorem zdarzenia w strumieniu Usługi zdarzeń. Identyfikator jest unikatowy w obrębie partycji strumienia Usługi event hubów.|
|`PartitionKey`|`string`|Partycja, do której mają być wysyłane dane zdarzeń.|
|`Properties`|`IDictionary<String,Object>`|Właściwości użytkownika danych zdarzenia.|
|`SequenceNumber`|`Int64`|Logiczny numer sekwencyjny zdarzenia.|
|`SystemProperties`|`IDictionary<String,Object>`|Właściwości systemu, w tym dane zdarzenia.|

Zobacz [przykłady kodu,](#example) które używają tych właściwości wcześniej w tym artykule.

## <a name="hostjson-properties"></a>właściwości host.json

Plik [host.json](../articles/azure-functions/functions-host-json.md#eventhub) zawiera ustawienia sterujące zachowaniem wyzwalania centrów zdarzeń.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]