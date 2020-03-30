---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589742"
---
Użyj powiązania danych wyjściowych centrum zdarzeń, aby zapisywać zdarzenia w strumieniu zdarzeń. Musisz mieć uprawnienie do wysłania do centrum zdarzeń, aby zapisywać w nim zdarzenia.

Upewnij się, że wymagane odwołania do pakietu są na miejscu przed podjęciem próby zaimplementowania powiązania danych wyjściowych.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

Poniższy przykład pokazuje [funkcję Języka C#,](../articles/azure-functions/functions-dotnet-class-library.md) która zapisuje komunikat do centrum zdarzeń, przy użyciu wartości zwracanej metody jako dane wyjściowe:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

W poniższym przykładzie `IAsyncCollector` pokazano, jak używać interfejsu do wysyłania partii wiadomości. Ten scenariusz jest typowy podczas przetwarzania wiadomości pochodzących z jednego Centrum zdarzeń i wysyłania wyników do innego Centrum zdarzeń.

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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza centrum zdarzeń w pliku *function.json* i [funkcję skryptu Języka C#,](../articles/azure-functions/functions-reference-csharp.md) która używa powiązania. Funkcja zapisuje komunikat do centrum zdarzeń.

Poniższe przykłady pokazują dane powiązania centrum zdarzeń w pliku *function.json.* Pierwszy przykład dotyczy funkcji 2.x i nowszych, a drugi dla funkcji 1.x. 

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

Oto kod skryptu języka C#, który tworzy jedną wiadomość:

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

Oto kod skryptu Języka C#, który tworzy wiele komunikatów:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza centrum zdarzeń w pliku *function.json* i [funkcję JavaScript,](../articles/azure-functions/functions-reference-node.md) która używa powiązania. Funkcja zapisuje komunikat do centrum zdarzeń.

Poniższe przykłady pokazują dane powiązania centrum zdarzeń w pliku *function.json.* Pierwszy przykład dotyczy funkcji 2.x i nowszych, a drugi dla funkcji 1.x. 

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

Oto kod JavaScript, który wysyła pojedynczą wiadomość:

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

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano powiązanie wyzwalacza centrum zdarzeń w pliku *function.json* i [funkcję Języka Python,](../articles/azure-functions/functions-reference-python.md) która używa powiązania. Funkcja zapisuje komunikat do centrum zdarzeń.

Poniższe przykłady pokazują dane powiązania centrum zdarzeń w pliku *function.json.*

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Oto kod Języka Python, który wysyła pojedynczą wiadomość:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

W poniższym przykładzie pokazano funkcję Języka Java, która zapisuje komunikat zawierający bieżący czas do Centrum zdarzeń.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

W [bibliotece środowiska wykonawczego](/java/api/overview/azure/functions/runtime)funkcji `@EventHubOutput` Java użyj adnotacji na temat parametrów, których wartość zostanie opublikowana w Centrum zdarzeń.  Parametr powinien być `OutputBinding<T>` typu , gdzie T jest POJO lub dowolnego natywnego typu Java.

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W przypadku [bibliotek klas języka C#](../articles/azure-functions/functions-dotnet-class-library.md)użyj atrybutu [EventHubAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs)

Konstruktor atrybutu przyjmuje nazwę centrum zdarzeń i nazwę ustawienia aplikacji, która zawiera parametry połączenia. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [Wyjście — konfiguracja](#configuration). Oto przykład `EventHub` atrybutu:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Aby uzyskać pełny przykład, zobacz [Output — Przykład C#.](#example)

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego funkcji Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)użyj adnotacji [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) na temat parametrów, których wartość zostanie opublikowana w Centrum zdarzeń. Parametr powinien być `OutputBinding<T>` typu `T` , gdzie jest POJO lub dowolnego natywnego typu Java.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `EventHub` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być ustawiona na "eventHub". |
|**Kierunku** | Nie dotyczy | Musi być ustawiona na "out". Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej używana w kodzie funkcji reprezentującej zdarzenie. |
|**Ścieżka** |**Nazwa eventhubname** | Funkcje tylko 1.x. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecny w ciągu połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**eventHubName** |**Nazwa eventhubname** | Funkcje 2.x lub wyższe. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecny w ciągu połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**Połączenia** |**Połączenia** | Nazwa ustawienia aplikacji zawierającego parametry połączenia z obszarem nazw centrum zdarzeń. Skopiuj ten ciąg połączenia, klikając przycisk **Informacje o połączeniu** dla obszaru *nazw,* a nie sam koncentrator zdarzeń. Ten ciąg połączenia musi mieć uprawnienia do wysyłania wiadomości do strumienia zdarzeń.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="c"></a>[C #](#tab/csharp)

Wysyłaj wiadomości przy użyciu `out string paramName`parametru metody, takiego jak . W skrypcie C# `paramName` jest `name` wartością określoną we właściwości *function.json*. Aby napisać wiele wiadomości, `ICollector<string>` `IAsyncCollector<string>` można użyć `out string`lub zamiast .

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Wysyłaj wiadomości przy użyciu `out string paramName`parametru metody, takiego jak . W skrypcie C# `paramName` jest `name` wartością określoną we właściwości *function.json*. Aby napisać wiele wiadomości, `ICollector<string>` `IAsyncCollector<string>` można użyć `out string`lub zamiast .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Dostęp do zdarzenia `context.bindings.<name>` wyjściowego przy `<name>` użyciu, `name` gdzie jest wartość określona we właściwości *function.json*.

# <a name="python"></a>[Python](#tab/python)

Istnieją dwie opcje wyprowadzania komunikatu Centrum zdarzeń z funkcji:

- **Zwracana wartość:** Ustaw `name` właściwość w `$return` *function.json* na . W tej konfiguracji wartość zwracana funkcji jest zachowywana jako komunikat Centrum zdarzeń.

- **Imperatyw:** Przekaż wartość do [metody zestawu](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru zadeklarowanego jako typ [Out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Wartość przekazana `set` do jest zachowywana jako komunikat Centrum zdarzeń.

# <a name="java"></a>[Java](#tab/java)

Istnieją dwie opcje wyprowadzania komunikatu Centrum zdarzeń z funkcji przy użyciu adnotacji [EventHubOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)

- **Zwracana wartość:** Stosując adnotację do samej funkcji, zwracana wartość funkcji jest zachowywana jako komunikat Centrum zdarzeń.

- **Imperatyw:** Aby jawnie ustawić wartość wiadomości, zastosuj adnotację do określonego parametru typu [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), gdzie `T` jest POJO lub dowolnego natywnego typu Java. W tej konfiguracji przekazywanie `setValue` wartości do metody utrwala wartość jako komunikat Centrum zdarzeń.

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody zwrotne

| Wiązanie | Tematy pomocy |
|---|---|
| Centrum zdarzeń | [Przewodnik po operacjach](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>ustawienia host.json

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersjach 2.x lub nowszych. Przykładowy plik host.json poniżej zawiera tylko ustawienia w wersji 2.x+ dla tego powiązania. Aby uzyskać więcej informacji na temat globalnych ustawień konfiguracji w wersjach 2.x i nowszych, zobacz [odwołanie do host.json dla usługi Azure Functions](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Aby uzyskać odwołanie do pliku host.json w usługach 1.x, zobacz [odwołanie do host.json dla usługi Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md).

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
|`maxBatchSize`|10|Maksymalna liczba zdarzeń odebranych na pętlę odbioru.|
|`prefetchCount`|300|Domyślna liczba pobierania wstępnego używana `EventProcessorHost`przez bazowy plik .|
|`batchCheckpointFrequency`|1|Liczba partii zdarzeń do przetworzenia przed utworzeniem punktu kontrolnego kursora EventHub.|
