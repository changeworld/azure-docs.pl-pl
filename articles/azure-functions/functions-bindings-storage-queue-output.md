---
title: Powiązanie danych wyjściowych magazynu kolejki platformy Azure dla funkcji platformy Azure
description: Dowiedz się, jak tworzyć komunikaty magazynu kolejki platformy Azure w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 76af5f398edd736874fa79095f2e80c02298eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277338"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Powiązania danych wyjściowych magazynu kolejki platformy Azure dla usług Azure Functions

Usługa Azure Functions może tworzyć nowe komunikaty magazynu usługi Azure Queue, konfigurując powiązanie danych wyjściowych.

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](./functions-bindings-storage-queue.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C #](#tab/csharp)

W poniższym przykładzie przedstawiono [funkcję Języka C#,](functions-dotnet-class-library.md) która tworzy komunikat kolejki dla każdego odebranego żądania HTTP.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza HTTP w pliku *function.json* i kod [skryptu C#(csx),](functions-reference-csharp.md) który używa powiązania. Funkcja tworzy element kolejki z ładunkiem obiektu **CustomQueueMessage** dla każdego odebranego żądania HTTP.

Oto plik *function.json:*

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod skryptu języka C#, który tworzy komunikat pojedynczej kolejki:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Można wysyłać wiele wiadomości jednocześnie `IAsyncCollector` przy użyciu lub parametru. `ICollector` Oto kod skryptu języka C#, który wysyła wiele wiadomości, jeden z danymi żądania HTTP i jeden z wartościami zakodowanych na twardo:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza HTTP w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja tworzy element kolejki dla każdego odebranego żądania HTTP.

Oto plik *function.json:*

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Można wysyłać wiele wiadomości jednocześnie, definiując tablicę wiadomości dla powiązania danych wyjściowych. `myQueueItem` Poniższy kod JavaScript wysyła dwie wiadomości kolejki z wartościami zakodowanych na czas dla każdego odebranego żądania HTTP.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano, jak dane wyjściowe pojedyncze i wiele wartości do kolejek magazynu. Konfiguracja wymagana dla *function.json* jest taka sama w obu przypadkach.

Powiązanie kolejki magazynu jest zdefiniowane w *funkcji.json,* gdzie *typ* jest ustawiony na `queue`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Aby ustawić pojedynczą wiadomość w kolejce, należy `set` przekazać pojedynczą wartość do metody.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Aby utworzyć wiele komunikatów w kolejce, zadeklaruj parametr jako odpowiedni typ listy `set` i przekaż do metody tablicę wartości (która odpowiada typowi listy).

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 W poniższym przykładzie przedstawiono funkcję Java, która tworzy komunikat kolejki po wyzwoleniu przez żądanie HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

W [bibliotece środowiska wykonawczego](/java/api/overview/azure/functions/runtime)funkcji `@QueueOutput` Java użyj adnotacji na temat parametrów, których wartość zostanie zapisana w magazynie kolejki.  Typ parametru `OutputBinding<T>`powinien `T` być , gdzie jest dowolny natywny typ Java POJO.

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj [atrybutu QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

Atrybut ma zastosowanie do `out` parametru lub zwracanej wartości funkcji. Konstruktor atrybutu przyjmuje nazwę kolejki, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Można ustawić `Connection` właściwość, aby określić konto magazynu do użycia, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Aby uzyskać pełny przykład, zobacz [przykład wyjścia](#example).

Za pomocą `StorageAccount` atrybutu można określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz Trigger - atrybuty.

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Adnotacja `QueueOutput` umożliwia pisanie wiadomości jako dane wyjściowe funkcji. W poniższym przykładzie przedstawiono funkcję wyzwalaną przez protokół HTTP, która tworzy komunikat kolejki.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Właściwość    | Opis |
|-------------|-----------------------------|
|`name`       | Deklaruje nazwę parametru w podpisie funkcji. Po wyzwoleniu funkcji wartość tego parametru ma zawartość komunikatu kolejki. |
|`queueName`  | Deklaruje nazwę kolejki na koncie magazynu. |
|`connection` | Wskazuje parametry połączenia konta magazynu. |

Parametr skojarzony z `QueueOutput` adnotacją jest wpisywany jako [wystąpienie OutputBinding\<T.\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java)

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `Queue` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być `queue`ustawiona na . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal.|
|**Kierunku** | Nie dotyczy | Musi być `out`ustawiona na . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej reprezentującej kolejkę w kodzie funkcji. Ustaw, `$return` aby odwoływać się do wartości zwracanej funkcji.|
|**Queuename** |**Queuename** | Nazwa kolejki. |
|**Połączenia** | **Połączenia** |Nazwa ustawienia aplikacji, która zawiera ciąg połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy tutaj. Na przykład jeśli `connection` ustawisz "MyStorage", środowisko wykonawcze Functions wyszukuje ustawienie aplikacji o nazwie "MyStorage". Jeśli pozostawisz `connection` puste, środowisko wykonawcze Functions używa domyślnego ciągu połączenia `AzureWebJobsStorage`Magazyn w ustawieniu aplikacji o nazwie .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="c"></a>[C #](#tab/csharp)

Napisz wiadomość pojedynczej kolejki przy użyciu `out T paramName`parametru metody, takiego jak . Można użyć typu zwracanego metody `out` zamiast parametru i `T` może być dowolny z następujących typów:

* Obiekt serializowalny jako JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Jeśli spróbujesz `CloudQueueMessage` powiązać i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

W skrypcie C# i C# zapisuj wiele komunikatów kolejki przy użyciu jednego z następujących typów: 

* `ICollector<T>` lub `IAsyncCollector<T>`
* [CloudQueue (CloudQueue)](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Napisz wiadomość pojedynczej kolejki przy użyciu `out T paramName`parametru metody, takiego jak . Jest `paramName` to wartość określona `name` we właściwości *function.json*. Można użyć typu zwracanego metody `out` zamiast parametru i `T` może być dowolny z następujących typów:

* Obiekt serializowalny jako JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Jeśli spróbujesz `CloudQueueMessage` powiązać i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

W skrypcie C# i C# zapisuj wiele komunikatów kolejki przy użyciu jednego z następujących typów: 

* `ICollector<T>` lub `IAsyncCollector<T>`
* [CloudQueue (CloudQueue)](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[Javascript](#tab/javascript)

Element kolejki wyjściowej `context.bindings.<NAME>` jest `<NAME>` dostępny w miejscu, w którym jest zgodna z nazwą zdefiniowaną w *pliku function.json*. Można użyć ciągu lub obiektu serializable JSON dla ładunku elementu kolejki.

# <a name="python"></a>[Python](#tab/python)

Istnieją dwie opcje wyprowadzania komunikatu Centrum zdarzeń z funkcji:

- **Zwracana wartość:** Ustaw `name` właściwość w `$return` *function.json* na . W tej konfiguracji wartość zwracana funkcji jest zachowywana jako komunikat magazynu kolejki.

- **Imperatyw:** Przekaż wartość do [metody zestawu](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) parametru zadeklarowanego jako typ [Out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Wartość przekazana `set` do jest zachowywana jako komunikat magazynu kolejki.

# <a name="java"></a>[Java](#tab/java)

Istnieją dwie opcje wyprowadzania wiadomości Centrum zdarzeń z funkcji przy użyciu adnotacji [QueueOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput)

- **Zwracana wartość:** Stosując adnotację do samej funkcji, zwracana wartość funkcji jest zachowywana jako komunikat Centrum zdarzeń.

- **Imperatyw:** Aby jawnie ustawić wartość wiadomości, zastosuj adnotację do określonego parametru typu [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), gdzie `T` jest POJO lub dowolnego natywnego typu Java. W tej konfiguracji przekazywanie `setValue` wartości do metody utrwala wartość jako komunikat Centrum zdarzeń.

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody zwrotne

| Wiązanie |  Tematy pomocy |
|---|---|
| Kolejka | [Kody błędów kolejki](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Obiekt Blob, Tabela, Kolejka | [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt Blob, Tabela, Kolejka |  [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>ustawienia host.json

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersjach 2.x lub nowszych. Przykładowy plik host.json poniżej zawiera tylko ustawienia w wersji 2.x+ dla tego powiązania. Aby uzyskać więcej informacji na temat globalnych ustawień konfiguracji w wersjach 2.x i nowszych, zobacz [odwołanie do host.json dla usługi Azure Functions](functions-host-json.md).

> [!NOTE]
> Aby uzyskać odwołanie do pliku host.json w usługach 1.x, zobacz [odwołanie do host.json dla usługi Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|maxPollingInterval (właso.|00:00:01|Maksymalny interwał między ankietami kolejki. Minimalna wartość to 00:00:00.100 (100 ms) i przyrosty do 00:01:00 (1 min).  W 1.x typ danych jest milisekund, a w 2.x i wyższej jest TimeSpan.|
|widocznośćCzas|00:00:00|Przedział czasu między ponownych prób podczas przetwarzania wiadomości kończy się niepowodzeniem. |
|batchSize|16|Liczba komunikatów kolejki, które funkcje środowiska wykonawczego pobiera jednocześnie i procesów równolegle. Gdy liczba przetwarzana pobiera w `newBatchThreshold`dół do , środowisko wykonawcze pobiera inną partię i rozpoczyna przetwarzanie tych komunikatów. Tak więc maksymalna liczba równoczesnych komunikatów `batchSize` `newBatchThreshold`przetwarzanych na funkcję jest plus . Ten limit ma zastosowanie oddzielnie do każdej funkcji wyzwalaneją kolejki. <br><br>Jeśli chcesz uniknąć równoległego wykonywania wiadomości odebranych w `batchSize` jednej kolejce, można ustawić na 1. Jednak to ustawienie eliminuje współbieżność tylko tak długo, jak aplikacja funkcji działa na jednej maszynie wirtualnej (VM). Jeśli aplikacja funkcji jest skalowana w poziomie do wielu maszyn wirtualnych, każda maszyna wirtualna może uruchomić jedno wystąpienie każdej funkcji wyzwalanej kolejką.<br><br>Maksymalna `batchSize` wartość to 32. |
|maksymalna liczba kolejek nieliczna|5|Liczba razy, aby spróbować przetworzyć wiadomość przed przeniesieniem go do kolejki trucizny.|
|newBatchThreshold|batchSize/2|Za każdym razem, gdy liczba wiadomości przetwarzanych jednocześnie spada do tej liczby, środowisko wykonawcze pobiera inną partię.|

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji w miarę zmiany danych magazynu kolejek (wyzwalacz)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
