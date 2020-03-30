---
title: Wyzwalacz rozgrzewania usług Azure Functions
description: Dowiedz się, jak używać wyzwalacza rozgrzewki w usłudze Azure Functions.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: funkcje azure, funkcje, przetwarzanie zdarzeń, rozgrzewka, zimny start, premium, dynamiczne obliczeń, architektura bezserwerowa
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167328"
---
# <a name="azure-functions-warm-up-trigger"></a>Wyzwalacz rozgrzewania usług Azure Functions

W tym artykule wyjaśniono, jak pracować z wyzwalaczem rozgrzewki w usłudze Azure Functions. Wyzwalacz rozgrzewki jest obsługiwany tylko dla aplikacji funkcyjnych działających w [planie Premium.](functions-premium-plan.md) Wyzwalacz rozgrzewania jest wywoływany, gdy wystąpienie jest dodawane do skalowania uruchomionej aplikacji funkcji. Wyzwalacza rozgrzewki można użyć do wstępnego ładowania zależności niestandardowych podczas [procesu wstępnego ocieplenia,](./functions-premium-plan.md#pre-warmed-instances) dzięki czemu funkcje są gotowe do natychmiastowego rozpoczęcia przetwarzania żądań. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakiety — funkcje 2.x lub nowsze

[Pakiet Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, wersja **3.0.5 lub nowsza** jest wymagana. Kod źródłowy pakietu znajduje się w repozytorium GitHub w [zakresie azure-webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Wyzwalacz

Wyzwalacz rozgrzewki umożliwia zdefiniowanie funkcji, która będzie uruchamiana w nowym wystąpieniu po dodaniu go do uruchomionej aplikacji. Za pomocą funkcji rozgrzewania można otwierać połączenia, ładować zależności lub uruchamiać dowolną inną niestandardową logikę, zanim aplikacja zacznie odbierać ruch. 

Wyzwalacz rozgrzewania jest przeznaczony do tworzenia współużytków udostępnionych, które będą używane przez inne funkcje w aplikacji. [Zobacz przykłady współdzielonych zależności tutaj](./manage-connections.md#client-code-examples).

Należy zauważyć, że wyzwalacz rozgrzewki jest wywoływany tylko podczas operacji skalowania w poziomie, a nie podczas ponownego uruchamiania lub innych startupów nieskażających. Należy upewnić się, że logika może załadować wszystkie niezbędne zależności bez użycia wyzwalacza rozgrzewki. Leniwe ładowanie jest dobrym wzorcem, aby to osiągnąć.

## <a name="trigger---example"></a>Wyzwalacz — przykład

# <a name="c"></a>[C #](#tab/csharp)

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która będzie uruchamiana w każdym nowym wystąpieniu po dodaniu go do aplikacji. Atrybut wartości zwracanej nie jest wymagany.


* Funkcja musi mieć ```warmup``` nazwę (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.
* Aby użyć rozgrzewki jako funkcji biblioteki klas .NET, upewnij się, że masz odwołanie do pakietu **Microsoft.Azure.WebJobs.Extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Komentarze zastępcze pokazują, gdzie w aplikacji do deklarowania i inicjowania współużytkowania współużytkowania. 
[Dowiedz się więcej o współdzielonych zależnościach tutaj](./manage-connections.md#client-code-examples).

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)


W poniższym przykładzie pokazano wyzwalacz rozgrzewki w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która będzie uruchamiana w każdym nowym wystąpieniu po dodaniu go do aplikacji.

Funkcja musi mieć ```warmup``` nazwę (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

Oto plik *function.json:*

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

W sekcji [konfiguracji](#trigger---configuration) opisano te właściwości.

Oto kod skryptu C#, `HttpRequest`który wiąże się z:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano wyzwalacz rozgrzewki w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która będzie uruchamiana w każdym nowym wystąpieniu po dodaniu go do aplikacji.

Funkcja musi mieć ```warmup``` nazwę (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

Oto plik *function.json:*

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

W sekcji [konfiguracji](#trigger---configuration) opisano te właściwości.

Oto kod JavaScript:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano wyzwalacz rozgrzewki w pliku *function.json* i [funkcję Języka Python,](functions-reference-python.md) która będzie uruchamiana w każdym nowym wystąpieniu po dodaniu go do aplikacji.

Funkcja musi mieć ```warmup``` nazwę (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

Oto plik *function.json:*

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

W sekcji [konfiguracji](#trigger---configuration) opisano te właściwości.

Oto kod Pythona:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

W poniższym przykładzie pokazano wyzwalacz rozgrzewki, który jest uruchamiany po dodaniu każdego nowego wystąpienia do aplikacji.

Funkcja musi mieć `warmup` nazwę (bez uwzględniania wielkości liter) i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Trigger - atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md) `WarmupTrigger` atrybut jest dostępny do skonfigurowania funkcji.

# <a name="c"></a>[C #](#tab/csharp)

W tym przykładzie pokazano, jak używać atrybutu [rozgrzewki.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs)

Należy pamiętać, że ```Warmup``` funkcja musi być wywoływana i może istnieć tylko jedna funkcja rozgrzewania na aplikację.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Pełny przykład można znaleźć w [przykładzie wyzwalacza](#trigger---example).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Wyzwalacz rozgrzewki nie jest obsługiwany w języku Java jako atrybut.

---

## <a name="trigger---configuration"></a>Trigger - konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `WarmupTrigger` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
| **Typu** | Nie dotyczy| Wymagane - musi być `warmupTrigger`ustawiona na . |
| **Kierunku** | Nie dotyczy| Wymagane - musi być `in`ustawiona na . |
| **Nazwa** | Nie dotyczy| Wymagane — nazwa zmiennej używana w kodzie funkcji.|

## <a name="trigger---usage"></a>Trigger - użycie

Żadne dodatkowe informacje nie są dostarczane do funkcji wyzwalane rozgrzewką, gdy jest wywoływana.

## <a name="trigger---limits"></a>Wyzwalacz - limity

* Wyzwalacz rozgrzewki jest dostępny tylko dla aplikacji działających w [planie Premium.](./functions-premium-plan.md)
* Wyzwalacz rozgrzewania jest wywoływany tylko podczas skalowania w górę operacji, a nie podczas ponownego uruchamiania lub innych startupów nieskażania. Należy upewnić się, że logika może załadować wszystkie niezbędne zależności bez użycia wyzwalacza rozgrzewki. Leniwe ładowanie jest dobrym wzorcem, aby to osiągnąć.
* Nie można wywołać wyzwalacza rozgrzewania, gdy wystąpienie jest już uruchomione.
* Może istnieć tylko jedna funkcja wyzwalania rozgrzewania na aplikację funkcji.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o wyzwalaczach i powiązaniach funkcji platformy Azure](functions-triggers-bindings.md)
