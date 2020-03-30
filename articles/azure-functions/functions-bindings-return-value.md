---
title: Korzystanie z wartości zwracanej z funkcji platformy Azure
description: Dowiedz się, jak zarządzać wartościami zwrotów dla funkcji platformy Azure
author: craigshoemaker
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 7ba104e288204dfbf3d24f5783bf69682a286553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74480576"
---
# <a name="using-the-azure-function-return-value"></a>Korzystanie z wartości zwracanej funkcji platformy Azure

W tym artykule wyjaśniono, jak zwracane wartości działają wewnątrz funkcji.

W językach, które mają wartość zwracaną, można powiązać [powiązanie danych wyjściowych](./functions-triggers-bindings.md#binding-direction) funkcji z wartością zwracaną:

* W bibliotece klas języka C# zastosuj atrybut wiązania danych wyjściowych do wartości zwracanej metody.
* W języku Java zastosuj adnotację wiązania wyjściowego do metody funkcji.
* W innych językach `name` ustaw właściwość w `$return` *function.json* na .

Jeśli istnieje wiele powiązań danych wyjściowych, należy użyć zwracanej wartości tylko dla jednego z nich.

W skrypcie C# i C# alternatywne `out` sposoby wysyłania danych do powiązania danych wyjściowych to parametry i [obiekty modułu zbierającego.](functions-reference-csharp.md#writing-multiple-output-values)

# <a name="c"></a>[C #](#tab/csharp)

Oto kod języka C#, który używa wartości zwracanej dla powiązania danych wyjściowych, a następnie przykład asynchronii:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Oto powiązanie danych wyjściowych w pliku *function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Oto kod skryptu C#, po którym następuje przykład asynchroniowy:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="f"></a>[F #](#tab/fsharp)

Oto powiązanie danych wyjściowych w pliku *function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Oto kod F#:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Oto powiązanie danych wyjściowych w pliku *function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

W języku JavaScript wartość zwracana jest `context.done`w drugim parametrze dla :

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="python"></a>[Python](#tab/python)

Oto powiązanie danych wyjściowych w pliku *function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Oto kod Pythona:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

Oto kod Java, który używa wartości zwracanej dla powiązania danych wyjściowych:

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Obsługa błędów wiązania usług Azure Functions](./functions-bindings-errors.md)
