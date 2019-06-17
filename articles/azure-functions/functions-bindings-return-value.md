---
title: Przy użyciu wartości zwracanej z funkcji platformy Azure
description: Dowiedz się, jak zarządzać wartości zwracane dla usługi Azure Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 4ccfe192eaea94cb9b199bd3c6f0bdacf1685519
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61437722"
---
# <a name="using-the-azure-function-return-value"></a>Przy użyciu wartości zwracanej funkcji platformy Azure

W tym artykule opisano sposób zwrócenia wartości pracy wewnątrz funkcji.

W przypadku języków, które mają wartość zwracaną można powiązać funkcji [powiązania danych wyjściowych](./functions-triggers-bindings.md#binding-direction) zwracaną wartość:

* W języku C# biblioteki klas należy zastosować atrybut wiązania danych wyjściowych na wartość zwracaną metody.
* W innych językach, należy ustawić `name` właściwość *function.json* do `$return`.

W przypadku wielu powiązania danych wyjściowych, użyj wartości zwracanej tylko dla jednego z nich.

W języku C# i skrypt języka C#, są alternatywne sposoby wysyłania danych do powiązania danych wyjściowych `out` parametrów i [obiektów modułu zbierającego](functions-reference-csharp.md#writing-multiple-output-values).

Zobacz przykład specyficzny dla języka, przedstawiający użycie zwracanej wartości:

* [C#](#c-example)
* [Skryptu C# (csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>Przykład w języku C#

W tym C# kod, który używa wartość zwracaną dla powiązania danych wyjściowych, następuje przykład async:

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

## <a name="c-script-example"></a>Przykładowy skrypt w języku C#

Oto powiązania danych wyjściowych w *function.json* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Poniżej przedstawiono kod C# script, następuje przykład async:

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

## <a name="f-example"></a>F#przykład

Oto powiązania danych wyjściowych w *function.json* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Oto F# kodu:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="javascript-example"></a>Przykład JavaScript

Oto powiązania danych wyjściowych w *function.json* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

W języku JavaScript, zwracana wartość znajduje się w drugi parametr dla `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Przykładem w języku Python

Oto powiązania danych wyjściowych w *function.json* pliku:

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Poniżej przedstawiono kod języka Python:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Obsługa błędów powiązań usługi Azure Functions](./functions-bindings-errors.md)
