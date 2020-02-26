---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589742"
---
Użyj powiązania danych wyjściowych Event Hubs do zapisywania zdarzeń w strumieniu zdarzeń. Użytkownik musi mieć uprawnienia do wysyłania do centrum zdarzeń w celu zapisywania do niego zdarzeń.

Przed podjęciem próby wdrożenia powiązania wyjściowego upewnij się, że istnieją odwołania do wymaganych pakietów.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

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

W poniższym przykładzie pokazano, jak za pomocą interfejsu `IAsyncCollector` wysyłać partie komunikatów. Ten scenariusz jest typowy podczas przetwarzania komunikatów pochodzących z jednego centrum zdarzeń i wysyłania wyników do innego centrum zdarzeń.

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

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w pliku *Function. JSON* i [ C# funkcji skryptu](../articles/azure-functions/functions-reference-csharp.md) , która używa powiązania. Funkcja zapisuje komunikat w centrum zdarzeń.

W poniższych przykładach pokazano Event Hubs powiązania danych w pliku *Function. JSON* . Pierwszy przykład dotyczy funkcji 2. x i wyższych, a drugi jest dla funkcji 1. x. 

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza centrum zdarzeń w pliku *Function. JSON* oraz [funkcja języka JavaScript](../articles/azure-functions/functions-reference-node.md) , która używa powiązania. Funkcja zapisuje komunikat w centrum zdarzeń.

W poniższych przykładach pokazano Event Hubs powiązania danych w pliku *Function. JSON* . Pierwszy przykład dotyczy funkcji 2. x i wyższych, a drugi jest dla funkcji 1. x. 

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład pokazuje funkcję języka Java, która zapisuje komunikat zawierający bieżący czas do centrum zdarzeń.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji `@EventHubOutput` w przypadku parametrów, których wartość zostanie opublikowana w centrum zdarzeń.  Parametr powinien być typu `OutputBinding<T>`, gdzie T to POJO lub dowolny natywny typ Java.

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W przypadku [ C# bibliotek klas](../articles/azure-functions/functions-dotnet-class-library.md)Użyj atrybutu [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) .

Konstruktor atrybutu przyjmuje nazwę centrum zdarzeń i nazwę ustawienia aplikacji, które zawiera parametry połączenia. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [Output-Configuration](#configuration). Oto przykład `EventHub` atrybutu:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Aby uzyskać pełny przykład, zobacz [Output- C# example](#example).

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego funkcji Java](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)Użyj adnotacji [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) w przypadku parametrów, których wartość zostanie opublikowana w centrum zdarzeń. Parametr powinien być typu `OutputBinding<T>`, gdzie `T` jest POJO lub dowolnym natywnym typem języka Java.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `EventHub` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Wartość musi być równa "eventHub". |
|**direction** | Nie dotyczy | Musi być równa "out". Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w Azure Portal. |
|**Nazwij** | Nie dotyczy | Nazwa zmiennej używana w kodzie funkcji, która reprezentuje zdarzenie. |
|**path** |**EventHubName** | Tylko funkcje 1. x. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecna w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**eventHubName** |**EventHubName** | Funkcje 2. x i nowsze. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecna w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**połączenia** |**Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia z przestrzenią nazw centrum zdarzeń. Skopiuj te parametry połączenia, klikając przycisk **Informacje o połączeniu** dla *obszaru nazw*, a nie samego centrum zdarzeń. Te parametry połączenia muszą mieć uprawnienia do wysyłania, aby wysłać wiadomość do strumienia zdarzeń.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

Wysyłaj komunikaty przy użyciu parametru metody, takiego jak `out string paramName`. W C# skrypcie `paramName` jest wartością określoną we właściwości `name` *funkcji Function. JSON*. Aby pisać wiele komunikatów, można użyć `ICollector<string>` lub `IAsyncCollector<string>` zamiast `out string`.

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Wysyłaj komunikaty przy użyciu parametru metody, takiego jak `out string paramName`. W C# skrypcie `paramName` jest wartością określoną we właściwości `name` *funkcji Function. JSON*. Aby pisać wiele komunikatów, można użyć `ICollector<string>` lub `IAsyncCollector<string>` zamiast `out string`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do zdarzenia wyjściowego przy użyciu `context.bindings.<name>`, gdzie `<name>` jest wartością określoną we właściwości `name` *funkcji Function. JSON*.

# <a name="python"></a>[Python](#tab/python)

Istnieją dwie opcje wyprowadzania komunikatu centrum zdarzeń z funkcji:

- **Wartość zwracana**: ustaw właściwość `name` w *funkcji Function. JSON* na `$return`. W przypadku tej konfiguracji wartość zwracana przez funkcję jest utrwalana jako komunikat centrum zdarzeń.

- Bezwzględnie **: Przekaż**wartość do metody [Set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru zadeklarowanego jako typ [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) . Wartość przeniesiona do `set` jest utrwalana jako komunikat centrum zdarzeń.

# <a name="java"></a>[Java](#tab/java)

Istnieją dwie opcje wyprowadzania komunikatu centrum zdarzeń z funkcji przy użyciu adnotacji [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) :

- **Wartość zwracana**: przez zastosowanie adnotacji do samej funkcji, wartość zwracana funkcji jest utrwalana jako komunikat centrum zdarzeń.

- Bezwzględnie **: aby**jawnie ustawić wartość komunikatu, Zastosuj adnotację do określonego parametru typu [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), gdzie `T` jest Pojo lub dowolnym natywnym typem języka Java. W przypadku tej konfiguracji przekazywanie wartości do metody `setValue` utrzymuje wartość jako komunikat centrum zdarzeń.

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Informacje ogólne |
|---|---|
| Centrum zdarzeń | [Przewodnik obsługi](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Ustawienia host.JSON

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2. x i nowszych. Poniższy przykładowy plik host. JSON zawiera tylko ustawienia wersji 2. x dla tego powiązania. Aby uzyskać więcej informacji na temat ustawień konfiguracji globalnej w wersjach 2. x i więcej, zobacz informacje dotyczące pliku [host. JSON dla Azure Functions](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Aby uzyskać odwołanie do pliku host. JSON w funkcjach 1. x, zobacz informacje dotyczące pliku [host. JSON dla Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).

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

|Właściwość  |Domyślny | Opis |
|---------|---------|---------|
|`maxBatchSize`|10|Maksymalna liczba zdarzeń odebranych na pętlę odbierania.|
|`prefetchCount`|300|Domyślna liczba pobieranych przed pobraniem używana przez bazowe `EventProcessorHost`.|
|`batchCheckpointFrequency`|1|Liczba partii zdarzeń do przetworzenia przed utworzeniem punktu kontrolnego kursora centrum EventHub.|
