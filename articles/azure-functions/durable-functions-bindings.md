---
title: Powiązania dla funkcji trwałe - Azure
description: Jak używać wyzwalaczy i powiązań dla rozszerzenia Functons trwałe dla usługi Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 370e6e2c569aaf6d9289bddccde2174b4dd2ee97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Powiązania dla funkcji trwałe (funkcje platformy Azure)

[Trwałe funkcje](durable-functions-overview.md) rozszerzenia wprowadza dwa nowe powiązania wyzwalacza kontrolujących wykonywania funkcji programu orchestrator i działania. Podaj powiązania wyjściowego, który działa jako klient funkcji trwałe środowiska uruchomieniowego.

## <a name="orchestration-triggers"></a>Wyzwalacze aranżacji

Wyzwalacz orchestration umożliwia tworzenie funkcji trwałe programu orchestrator. Wyzwalacz obsługuje uruchamianie nowych wystąpień funkcji programu orchestrator i wznawianie istniejących wystąpień funkcji programu orchestrator, oczekujących "" zadania.

Korzystając z programu Visual Studio tools dla usługi Azure Functions, wyzwalacz orchestration jest konfigurowana przy użyciu [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) atrybut platformy .NET.

Podczas pisania funkcje programu orchestrator w językach skryptów (na przykład w portalu Azure), wyzwalacz orchestration jest zdefiniowany przez następujący obiekt JSON w `bindings` tablicę *function.json* pliku:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` to nazwa orchestration. Jest to wartość, której klienci muszą używać chcąc Uruchom nowe wystąpienia tej funkcji programu orchestrator. Ta właściwość jest opcjonalna. Jeśli nie zostanie określony, używany jest nazwa funkcji.

Wewnętrznie tego powiązania wyzwalacza sonduje serii kolejek w domyślne konto magazynu dla funkcji aplikacji. Te kolejki są szczegóły implementacji wewnętrzny rozszerzenia, dlatego nie są one jawnie skonfigurowane we właściwościach powiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalacza

Poniżej przedstawiono niektóre uwagi dotyczące wyzwalacza orchestration:

* **Wątkowość jednym** -wątku pojedynczego dyspozytora jest używany dla wszystkich wykonywania funkcji programu orchestrator w wystąpieniu jednego hosta. Z tego powodu jest należy się upewnić, że kod funkcji programu orchestrator jest wydajne i nie wykonać żadnych operacji We/Wy. Jest również należy się upewnić, że ten wątek wykonać pracę async z wyjątkiem podczas oczekiwania na trwałe funkcji specyficznych dla typów zadań.
* **Obsługa komunikatów poison** — nie obsługuje Trująca wiadomość w wyzwalaczy aranżacji.
* **Komunikat widoczność** -usuniętej i zachowane niewidoczne dla można skonfigurować czas trwania aranżacji wyzwalacza komunikatów. Widoczność te komunikaty są odnawiane automatycznie tak długo, jak funkcja aplikacji jest uruchomiony i działa prawidłowo.
* **Wartości zwracane** -zwrócić wartości do formatu JSON i utrwalone w tabeli historii aranżacji w magazynie tabel platformy Azure. Może być badana te wartości zwracanych przez klienta aranżacji powiązanie, w dalszej części.

> [!WARNING]
> Funkcje programu orchestrator nigdy nie należy użyć żadnych danych wejściowych lub wyjściowych powiązań innych niż orchestration wyzwolenia powiązania. W ten sposób może spowodować problemy z rozszerzeniem trwałe zadania, ponieważ te powiązania nie może przestrzegać wątkowość jednym i reguł we/wy.

### <a name="trigger-usage"></a>Użycie wyzwalacza

Wyzwalacz aranżacji powiązanie obsługuje danych wejściowych i danych wyjściowych. Poniżej przedstawiono niektóre czynności, aby wiedzieć o dane wejściowe i wyjściowe obsługi:

* **dane wejściowe** -funkcji aranżacji obsługują tylko [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) jako parametr typu. Deserializacja danych wejściowych bezpośrednio w sygnaturze funkcji nie jest obsługiwana. Należy użyć kodu [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) metodę, aby pobrać dane wejściowe funkcji programu orchestrator. Te dane wejściowe muszą być typów możliwych do serializacji JSON.
* **generuje** -wyzwalacze aranżacji obsługują wartości danych wyjściowych, a także dane wejściowe. Zwracana wartość funkcji jest używana do przypisywania wartości wyjściowych i musi być możliwy do serializacji JSON. Jeśli funkcja zwraca `Task` lub `void`, `null` wartość zostanie zapisany jako dane wyjściowe.

### <a name="trigger-sample"></a>Przykładowe wyzwalacza

Poniżej przedstawiono przykład jak może wyglądać najprostszym funkcja orchestrator "Hello World":

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Należy używać JavaScript orchestrators `return`. `durable-functions` Biblioteki zajmuje się wywoływania `context.done` metody.

Większość funkcji programu orchestrator wywołania funkcji działania, Oto przykład "Hello World", który demonstruje sposób wywołania funkcji działania:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivityAsync("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Wyzwalacze działania

Wyzwalacz działania umożliwia tworzenie funkcji wywoływanych przez funkcje programu orchestrator.

Jeśli używasz programu Visual Studio, wyzwalacz działania jest konfigurowana przy użyciu [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) atrybut platformy .NET. 

Jeśli używasz portalu Azure do tworzenia aplikacji, wyzwalacz działania jest zdefiniowany przez następujący obiekt JSON w `bindings` tablicę *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` to nazwa działania. Jest to wartość, która umożliwia wywołanie tej funkcji działania funkcji programu orchestrator. Ta właściwość jest opcjonalna. Jeśli nie zostanie określony, używany jest nazwa funkcji.

Wewnętrznie tego powiązania wyzwalacza sonduje kolejkę w domyślne konto magazynu dla funkcji aplikacji. Kolejka jest szczegółów wewnętrznej implementacji rozszerzenia, dlatego nie jest jawnie skonfigurowany we właściwościach powiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalacza

Poniżej przedstawiono niektóre uwagi dotyczące działania wyzwalacza:

* **Wątkowość** — w odróżnieniu od wyzwalacza aranżacji wyzwalaczy działania nie mają zastosowanie jakiekolwiek ograniczenia wokół wątkowość lub we/wy. Mogą one traktowane jak regularne funkcji.
* **Obsługa komunikatów poising** — nie obsługuje Trująca wiadomość w wyzwalaczy działania.
* **Komunikat widoczność** -usuniętej i zachowane niewidoczne dla można skonfigurować czas trwania działania wyzwalacza komunikatów. Widoczność te komunikaty są odnawiane automatycznie tak długo, jak funkcja aplikacji jest uruchomiony i działa prawidłowo.
* **Wartości zwracane** -zwrócić wartości do formatu JSON i utrwalone w tabeli historii aranżacji w magazynie tabel platformy Azure.

> [!WARNING]
> Wewnętrznej bazy danych magazynu dla funkcji działania jest szczegółów implementacji i kod użytkownika nie może mieć interakcji z tych jednostek magazynu bezpośrednio.

### <a name="trigger-usage"></a>Użycie wyzwalacza

Wyzwalacz działania powiązanie obsługuje danych wejściowych i danych wyjściowych, podobnie jak wyzwalacza aranżacji. Poniżej przedstawiono niektóre czynności, aby wiedzieć o dane wejściowe i wyjściowe obsługi:

* **dane wejściowe** — funkcje działania natywnie używają [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako parametr typu. Możesz też funkcję działania mogą być deklarowane z dowolnego typu parametru, który jest możliwy do serializacji JSON. Jeśli używasz `DurableActivityContext`, można wywołać [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) do pobrania i deserializacji działania funkcji danych wejściowych.
* **generuje** — działanie funkcji obsługi wartości danych wyjściowych, a także dane wejściowe. Zwracana wartość funkcji jest używana do przypisywania wartości wyjściowych i musi być możliwy do serializacji JSON. Jeśli funkcja zwraca `Task` lub `void`, `null` wartość zostanie zapisany jako dane wyjściowe.
* **metadane** -działania funkcji można powiązać z `string instanceId` parametru można pobrać Identyfikatora wystąpienia aranżacji nadrzędnej.

### <a name="trigger-sample"></a>Przykładowe wyzwalacza

Poniżej przedstawiono przykład prostego funkcja działania "Hello World" przykładową:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```javascript
module.exports = function(context) {
    context.done(null, `Hello ${context.bindings.name}!`);
};
```

Domyślny typ parametru dla `ActivityTriggerAttribute` powiązanie to `DurableActivityContext`. Jednak również obsługa powiązanie bezpośrednio z JSON serializeable typy (w tym typy pierwotne), można uprościć taką samą funkcję, jako wyzwalacze działania są następujące:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```javascript
module.exports = function(context, name) {
    context.done(null, `Hello ${name}!`);
};
```

### <a name="passing-multiple-parameters"></a>Przekazywanie wiele parametrów 

Nie jest możliwe do przekazania wiele parametrów bezpośrednio do działania funkcji. W takim przypadku zaleca przekazywanie tablicę obiektów lub użycie [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) obiektów.

Poniższy przykład korzysta z nowych funkcji programu [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) dodaje się [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<dynamic> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    dynamic courseRecommendations = await context.CallActivityAsync<dynamic>("CourseRecommendations", (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<dynamic> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university 
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="orchestration-client"></a>Klient aranżacji

Powiązanie klienta orchestration umożliwia pisanie funkcji, które interakcję z funkcjami programu orchestrator. Na przykład można było na wystąpieniach aranżacji w następujący sposób:
* Uruchom je.
* Wyślij zapytanie do ich stan.
* Je zakończyć.
* Wysyłanie zdarzeń do nich podczas korzystania z nich.

Jeśli używasz programu Visual Studio, można powiązać aranżacji klienta za pomocą [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) atrybut platformy .NET.

Jeśli używasz języków skryptów (np. *csx* plików) do tworzenia aplikacji, wyzwalacz orchestration jest zdefiniowany przez następujący obiekt JSON w `bindings` tablicę *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "out"
}
```

* `taskHub` -Używane w sytuacji, gdy wiele aplikacji funkcji Udostępnianie tego samego konta magazynu, ale musi być od siebie odizolowane. Jeśli nie zostanie określony, wartością domyślną z `host.json` jest używany. Ta wartość musi odpowiadać wartości przez funkcje programu orchestrator docelowej.
* `connectionName` -Nazwa ustawienia aplikacji, która zawiera parametry połączenia konta magazynu. Konto magazynu reprezentowany przez ten ciąg połączenia musi być taka sama, używany przez funkcje programu orchestrator docelowej. Jeśli nie zostanie określony, jest używany domyślny ciąg połączenia konta magazynu dla funkcji aplikacji.

> [!NOTE]
> W większości przypadków zaleca się, Pomiń te właściwości oraz polegać na zachowanie domyślne.

### <a name="client-usage"></a>Użycie klientów

W języku C# funkcji, należy zwykle powiązać `DurableOrchestrationClient`, które zapewnia pełny dostęp do wszystkich klientów obsługiwanych przez funkcje trwałe interfejsów API. Interfejsy API w obiekcie klienta obejmują:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)

Alternatywnie można powiązać `IAsyncCollector<T>` gdzie `T` jest [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) lub `JObject`.

Zobacz [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) dokumentacji interfejsu API, aby uzyskać więcej informacji na temat tych operacji.

### <a name="client-sample-visual-studio-development"></a>Przykładowe klienta (Programowanie w Visual Studio)

Oto przykład wyzwalane kolejki funkcję, która rozpoczyna się aranżacji "HelloWorld".

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Przykładowe klienta (nie Visual Studio)

Jeśli nie używasz programu Visual Studio do tworzenia aplikacji, można utworzyć następujące *function.json* pliku. W tym przykładzie przedstawiono sposób konfigurowania funkcji wyzwalane kolejki, które używa klienta aranżacji trwałego powiązania:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "out"
    }
  ],
  "disabled": false
} 
```

Poniżej przedstawiono przykłady specyficzne dla języka, które Uruchom nowe wystąpienia funkcji programu orchestrator.

#### <a name="c-sample"></a>Przykład C#

Poniższy przykład przedstawia sposób użycia klienta aranżacji trwałe powiązanie, aby uruchomić nowe wystąpienie funkcji z funkcją skryptu C#:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Przykład JavaScript

Poniższy przykład przedstawia sposób użycia klienta aranżacji trwałe powiązanie, aby uruchomić nowe wystąpienie funkcji z funkcją JavaScript:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null, id);
};
```

Więcej informacji na temat uruchamiania wystąpień znajdują się w [wystąpienie zarządzania](durable-functions-instance-management.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Więcej informacji na temat zachowania punkty kontrolne i powtórzeń](durable-functions-checkpointing-and-replay.md)

