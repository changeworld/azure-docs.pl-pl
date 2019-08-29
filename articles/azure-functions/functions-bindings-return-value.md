---
title: Używanie wartości zwracanej z funkcji platformy Azure
description: Dowiedz się, jak zarządzać zwracanymi wartościami dla Azure Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 1ea7ec0444ba80d3494afba77ad9d7fdabd5f982
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086425"
---
# <a name="using-the-azure-function-return-value"></a>Korzystanie z wartości zwracanej przez funkcję platformy Azure

W tym artykule opisano sposób działania zwracanych wartości wewnątrz funkcji.

W językach, które mają wartość zwracaną, można powiązać [powiązanie danych wyjściowych](./functions-triggers-bindings.md#binding-direction) funkcji z wartością zwracaną:

* W bibliotece C# klas zastosuj atrybut wynik powiązania do wartości zwracanej przez metodę.
* W innych językach Ustaw `name` właściwość w *Function. JSON* na. `$return`

Jeśli istnieje wiele powiązań wyjściowych, użyj wartości zwracanej tylko dla jednego z nich.

W C# skryptach i C# alternatywnym sposobem wysyłania danych do powiązania wyjściowego są `out` parametry i [obiekty modułów zbierających](functions-reference-csharp.md#writing-multiple-output-values).

Zobacz przykład specyficzny dla języka przedstawiający użycie wartości zwracanej:

* [C#](#c-example)
* [Skryptu C# (csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>C#przyklad

Tutaj można C# znaleźć kod, który używa wartości zwracanej dla powiązania danych wyjściowych, po którym następuje asynchroniczny przykład:

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

## <a name="c-script-example"></a>C#przykład skryptu

Oto powiązanie danych wyjściowych w pliku *Function. JSON* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Oto kod C# skryptu, po którym następuje asynchroniczny przykład:

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

## <a name="f-example"></a>F#przyklad

Oto powiązanie danych wyjściowych w pliku *Function. JSON* :

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

Oto powiązanie danych wyjściowych w pliku *Function. JSON* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

W języku JavaScript wartość zwracana jest w drugim parametrze dla `context.done`:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Przykład języka Python

Oto powiązanie danych wyjściowych w pliku *Function. JSON* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Oto kod języka Python:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Obsługa błędów powiązań Azure Functions](./functions-bindings-errors.md)
