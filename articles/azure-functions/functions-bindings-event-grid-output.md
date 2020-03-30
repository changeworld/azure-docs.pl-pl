---
title: Powiązanie danych wyjściowych usługi Azure Event Grid dla funkcji platformy Azure
description: Dowiedz się, jak wysłać zdarzenie w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368950"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Powiązanie danych wyjściowych usługi Azure Event Grid dla funkcji platformy Azure

Użyj powiązania danych wyjściowych siatki zdarzeń, aby zapisywać zdarzenia w temacie niestandardowym. Dla [tematu niestandardowego](../event-grid/security-authentication.md#custom-topic-publishing)musi być dostępny prawidłowy klucz dostępu .

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](./functions-bindings-event-grid.md).

> [!NOTE]
> Powiązanie danych wyjściowych usługi Event Grid nie obsługuje podpisów dostępu współdzielonego (tokeny sygnatury dostępu współdzielonego). Należy użyć klucza dostępu tematu.

> [!IMPORTANT]
> Powiązanie danych wyjściowych siatki zdarzeń jest dostępne tylko dla funkcji 2.x lub nowszego.

## <a name="example"></a>Przykład

# <a name="c"></a>[C #](#tab/csharp)

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która zapisuje komunikat do tematu niestandardowego usługi Event Grid, przy użyciu wartości zwracanej metody jako danych wyjściowych:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

W poniższym przykładzie `IAsyncCollector` pokazano, jak używać interfejsu do wysyłania partii wiadomości.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie przedstawiono dane powiązania danych powiązania danych wyjściowych siatki zdarzeń w pliku *function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Oto kod skryptu języka C#, który tworzy jedno zdarzenie:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Oto kod skryptu języka C#, który tworzy wiele zdarzeń:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie przedstawiono dane powiązania danych powiązania danych wyjściowych siatki zdarzeń w pliku *function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Oto kod JavaScript, który tworzy pojedyncze zdarzenie:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Oto kod JavaScript, który tworzy wiele zdarzeń:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Powiązanie danych wyjściowych siatki zdarzeń nie jest dostępne dla języka Python.

# <a name="java"></a>[Java](#tab/java)

Powiązanie danych wyjściowych siatki zdarzeń nie jest dostępne dla języka Java.

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W przypadku [bibliotek klas języka C#](functions-dotnet-class-library.md)użyj atrybutu [EventGridAttribute.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs)

Konstruktor atrybutu przyjmuje nazwę ustawienia aplikacji, która zawiera nazwę tematu niestandardowego i nazwę ustawienia aplikacji, który zawiera klucz tematu. Aby uzyskać więcej informacji na temat tych ustawień, zobacz [Wyjście — konfiguracja](#configuration). Oto przykład `EventGrid` atrybutu:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Pełny przykład można znaleźć na [przykładzie](#example).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Powiązanie danych wyjściowych siatki zdarzeń nie jest dostępne dla języka Python.

# <a name="java"></a>[Java](#tab/java)

Powiązanie danych wyjściowych siatki zdarzeń nie jest dostępne dla języka Java.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `EventGrid` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być ustawiona na "eventGrid". |
|**Kierunku** | Nie dotyczy | Musi być ustawiona na "out". Ten parametr jest ustawiany automatycznie podczas tworzenia powiązania w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej używana w kodzie funkcji reprezentującej zdarzenie. |
|**topicEndpointUri** |**TopicEndpointUri** | Nazwa ustawienia aplikacji zawierającego identyfikator URI dla tematu `MyTopicEndpointUri`niestandardowego, na przykład . |
|**topicKeySetting** |**TopicKeySetting (Wzespołowanie tematyczne)** | Nazwa ustawienia aplikacji zawierającego klucz dostępu dla tematu niestandardowego. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Upewnij się, że ustawiono wartość właściwości `TopicEndpointUri` konfiguracji na nazwę ustawienia aplikacji, która zawiera identyfikator URI tematu niestandardowego. Nie należy określać identyfikatora URI tematu niestandardowego bezpośrednio w tej właściwości.

## <a name="usage"></a>Sposób użycia

# <a name="c"></a>[C #](#tab/csharp)

Wysyłaj wiadomości przy użyciu `out EventGridEvent paramName`parametru metody, takiego jak . Aby napisać wiele wiadomości, `ICollector<EventGridEvent>` `IAsyncCollector<EventGridEvent>` można użyć `out EventGridEvent`lub zamiast .

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Wysyłaj wiadomości przy użyciu `out EventGridEvent paramName`parametru metody, takiego jak . W skrypcie C# `paramName` jest `name` wartością określoną we właściwości *function.json*. Aby napisać wiele wiadomości, `ICollector<EventGridEvent>` `IAsyncCollector<EventGridEvent>` można użyć `out EventGridEvent`lub zamiast .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Dostęp do zdarzenia `context.bindings.<name>` wyjściowego przy `<name>` użyciu, `name` gdzie jest wartość określona we właściwości *function.json*.

# <a name="python"></a>[Python](#tab/python)

Powiązanie danych wyjściowych siatki zdarzeń nie jest dostępne dla języka Python.

# <a name="java"></a>[Java](#tab/java)

Powiązanie danych wyjściowych siatki zdarzeń nie jest dostępne dla języka Java.

---

## <a name="next-steps"></a>Następne kroki

* [Wysyłanie zdarzenia w sieci zdarzeń](./functions-bindings-event-grid-trigger.md)
