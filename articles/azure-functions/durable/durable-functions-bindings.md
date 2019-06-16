---
title: Powiązania trwałe funkcje — Azure
description: Jak użyć wyzwalaczy i powiązań dla rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 678e370977cadae642207f91a02136404fb6c34e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60710547"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Powiązania dla funkcje trwałe (usługa Azure Functions)

[Funkcje trwałe](durable-functions-overview.md) rozszerzenia wprowadzono dwa nowe powiązania wyzwalacza, które kontrolują wykonanie funkcji programu orchestrator i działania. Przedstawia on również powiązania danych wyjściowych, który działa jako klient przez środowisko uruchomieniowe funkcji trwałe.

## <a name="orchestration-triggers"></a>Wyzwalacze aranżacji

Wyzwalacz orchestration umożliwia tworzenie funkcje trwałe programu orchestrator. Ten wyzwalacz obsługuje uruchomienia nowych wystąpień funkcji programu orchestrator i wznawianie istniejących wystąpień funkcji programu orchestrator, oczekujące "na" zadanie.

Korzystając z programu Visual Studio tools dla usługi Azure Functions, wyzwalacz orchestration jest skonfigurowany przy użyciu [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) atrybut platformy .NET.

Podczas wpisywania funkcje programu orchestrator w językach skryptów (na przykład, JavaScript lub C# skryptów), wyzwalacz orchestration jest definiowany przez następujący obiekt JSON w `bindings` tablicę *function.json* Plik:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` jest nazwą aranżacji. Jest to wartość, która klienci muszą używać, jeśli chcą uruchomienie nowych wystąpień tej funkcji programu orchestrator. Ta właściwość jest opcjonalna. Jeśli nie zostanie określony, nazwa funkcji jest używana.

Wewnętrznie tego powiązania wyzwalacza sonduje szereg kolejek domyślne konto magazynu dla aplikacji funkcji. Te kolejki są szczegóły wewnętrznej implementacji rozszerzenie, dlatego nie są one jawnie skonfigurowane we właściwościach wiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalacza

Poniżej przedstawiono kilka uwag o wyzwalacz orchestration:

* **Wątkowość pojedynczego** -Wątek dyspozytora pojedynczego jest używany dla wszystkich wykonywania funkcji programu orchestrator w wystąpieniu jednego hosta. Z tego powodu jest ważne upewnić się, że kod funkcji programu orchestrator jest wydajny i nie wykonuje żadnych operacji We/Wy. Jest również ważne, aby upewnić się, że ten wątek wykonać wszelkie prace asynchronicznej, z wyjątkiem sytuacji, gdy oczekując na trwałe funkcje specyficzne dla typów zadań.
* **Obsługa komunikatów poison** — nie obsługuje zarządzanie skażonymi komunikatami w wyzwalaczach aranżacji.
* **Komunikat o widoczności** -wiadomości wyzwalaczy aranżacji są usuwane z kolejki i przechowywane niewidoczne przez wartość typu duration można skonfigurować. Widoczność tych komunikatów jest odnawiany automatycznie, tak długo, jak aplikacja funkcji jest uruchomiony i działa prawidłowo.
* **Wartości zwracane** — zwracane wartości są serializować do notacji JSON i utrwalone w tabeli historii aranżacji w usłudze Azure Table storage. Te wartości zwracane mogą być przeszukiwane przez klienta orkiestracji powiązania, opisanym w dalszej części.

> [!WARNING]
> Funkcje programu orchestrator nigdy nie może używać żadnych danych wejściowych ani danych wyjściowych powiązania innych niż aranżacji wyzwolić powiązania. Ten sposób może potencjalnie spowodować problemy z rozszerzeniem trwałe zadania, ponieważ te powiązania nie może być przestrzegają wątków w jednym i reguł we/wy.

> [!WARNING]
> Funkcje programu orchestrator JavaScript nigdy nie powinny być zadeklarowane `async`.

### <a name="trigger-usage-net"></a>Użycie wyzwalacza (.NET)

Wyzwalacz aranżacji powiązanie obsługuje danych wejściowych i danych wyjściowych. Oto kilka rzeczy, aby wiedzieć o danych wejściowych i wyjściowych obsługi:

* **dane wejściowe** — .NET aranżacji funkcje obsługują tylko [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) jako parametr typu. Deserializacja danych wejściowych bezpośrednio w sygnaturze funkcji nie jest obsługiwane. Należy użyć kodu [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) lub `getInput` — metoda (JavaScript), można pobrać dane wejściowe funkcji programu orchestrator. Te dane wejściowe muszą być typów możliwych do serializacji JSON.
* **dane wyjściowe** -wyzwalacze aranżacji obsługują wartości danych wyjściowych, a także dane wejściowe. Wartość zwracana przez funkcję jest używany do przypisywania wartości danych wyjściowych i musi być możliwy do serializacji JSON. Jeśli funkcja .NET zwraca `Task` lub `void`, `null` wartość zostanie zapisana jako dane wyjściowe.

### <a name="trigger-sample"></a>Przykładowy wyzwalacz

Poniżej znajduje się przykład jak może wyglądać najprostszym funkcji orkiestratora "Hello World":

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> `context` Obiektu w języku JavaScript nie reprezentuje DurableOrchestrationContext, ale [kontekście funkcji jako całości.](../functions-reference-node.md#context-object). Możesz uzyskać dostęp metod organizowania za pośrednictwem `context` obiektu `df` właściwości.

> [!NOTE]
> Skorzystaj z koordynatorów JavaScript `return`. `durable-functions` Biblioteki dba o wywoływania `context.done` metody.

Większość funkcji programu orchestrator wywołania funkcji działań, Oto przykład "Hello World", który pokazuje, jak wywołać funkcję działania:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Wyzwalacze działania

Wyzwalacz działanie umożliwia tworzenie funkcji, które są wywoływane przez funkcje programu orchestrator.

Jeśli używasz programu Visual Studio wyzwalacza działania jest skonfigurowany przy użyciu [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) atrybut platformy .NET.

Jeśli używasz programu VS Code lub w portalu Azure do tworzenia aplikacji wyzwalacza działania jest definiowany przez następujący obiekt JSON w `bindings` tablicę *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` to nazwa działania. Jest to wartość, która umożliwia Wywołaj tę funkcję działania funkcji programu orchestrator. Ta właściwość jest opcjonalna. Jeśli nie zostanie określony, nazwa funkcji jest używana.

Wewnętrznie tego powiązania wyzwalacza sonduje kolejkę w domyślne konto magazynu dla aplikacji funkcji. Kolejka ta jest szczegółowo opisuje implementacja wewnętrzny rozszerzenia, dlatego nie jest jawnie skonfigurowane we właściwościach wiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalacza

Poniżej przedstawiono niektóre uwagi dotyczące działania wyzwalacza:

* **Wątkowość** — w przeciwieństwie do wyzwalacza aranżacji wyzwalaczy działania nie mają zastosowanie jakiekolwiek ograniczenia wokół wątkowości lub we/wy. Mogą one być traktowane funkcji regularnych.
* **Obsługa komunikatów poison** — nie obsługuje zarządzanie skażonymi komunikatami w wyzwalaczach działania.
* **Komunikat o widoczności** -działania wyzwalacza wiadomości są usuwane z kolejki i przechowywane niewidoczne czas można skonfigurować. Widoczność tych komunikatów jest odnawiany automatycznie, tak długo, jak aplikacja funkcji jest uruchomiony i działa prawidłowo.
* **Wartości zwracane** — zwracane wartości są serializować do notacji JSON i utrwalone w tabeli historii aranżacji w usłudze Azure Table storage.

> [!WARNING]
> Wewnętrznej bazy danych magazynu dla funkcji działań jest szczegółowo opisuje implementacja i kod użytkownika nie może mieć interakcji z tymi jednostkami magazynu bezpośrednio.

### <a name="trigger-usage-net"></a>Użycie wyzwalacza (.NET)

Wyzwalacz działanie obsługuje powiązanie danych wejściowych i danych wyjściowych, podobnie jak wyzwalacz aranżacji. Oto kilka rzeczy, aby wiedzieć o danych wejściowych i wyjściowych obsługi:

* **dane wejściowe** — natywne korzystanie z funkcji działań .NET [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako parametr typu. Alternatywnie funkcję działania mogą być deklarowane przy użyciu dowolnego typu parametru, który jest możliwy do serializacji JSON. Kiedy używasz `DurableActivityContext`, można wywołać [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) do pobrania i deserializować funkcja działanie danych wejściowych.
* **dane wyjściowe** — działanie funkcji obsługi wartości danych wyjściowych, a także dane wejściowe. Wartość zwracana przez funkcję jest używany do przypisywania wartości danych wyjściowych i musi być możliwy do serializacji JSON. Jeśli funkcja .NET zwraca `Task` lub `void`, `null` wartość zostanie zapisana jako dane wyjściowe.
* **metadane** — funkcje działań platformy .NET można powiązać `string instanceId` parametru, aby pobrać identyfikator wystąpienia aranżacji nadrzędnej.

### <a name="trigger-sample"></a>Przykładowy wyzwalacz

Poniżej znajduje się przykład jak może wyglądać prostej funkcji "Hello World" działania:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Domyślny typ parametru dla platformy .NET `ActivityTriggerAttribute` powiązanie jest `DurableActivityContext`. Jednak .NET działania wyzwalaczy również obsługę wiązanie bezpośrednio do JSON serializeable typów (w tym typów pierwotnych), tę samą funkcję można uproszczenie jako następująco:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Powiązania JavaScript może zostać przekazany w jako parametry dodatkowe, tak samo można uproszczony w następujący sposób:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

### <a name="passing-multiple-parameters"></a>Przekazywanie wielu parametrów

Nie jest możliwe do przekazania wiele parametrów do funkcji działania bezpośrednio. W tym przypadku zaleca, aby przekazać tablicę obiektów lub użyć [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) obiektów na platformie .NET.

Poniższy przykład korzysta z nowych funkcji [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) dodane za pomocą [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

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

## <a name="orchestration-client"></a>Klient orkiestracji

Klient orkiestracji powiązanie umożliwia pisanie funkcji, które wchodzić w interakcje z funkcjami programu orchestrator. Na przykład którymi można pracować w wystąpieniach aranżacji w następujący sposób:

* Uruchom je.
* Badać ich stan.
* Je zakończyć.
* Wysyłanie zdarzeń do nich, gdy są one uruchamiane.
* Wyczyść historię wystąpienia.

Jeśli używasz programu Visual Studio, można powiązać klient orkiestracji przy użyciu [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) atrybut platformy .NET.

Jeśli używasz języków skryptów (np. *csx* lub *js* plików) na potrzeby programowania dla wyzwalacza orchestration jest definiowany przez następujący obiekt JSON w `bindings` tablicę  *Function.JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` -Używane w sytuacjach, gdy wiele aplikacji funkcyjnych udostępnianie tego samego konta magazynu, ale muszą być izolowane od siebie nawzajem. Jeśli nie zostanie określony, wartością domyślną z `host.json` jest używany. Ta wartość musi odpowiadać wartości przez funkcje programu orchestrator docelowej.
* `connectionName` — Nazwa ustawienia aplikacji zawierającego parametry połączenia konta magazynu. Reprezentowane przez te parametry połączenia konta magazynu musi być taka sama, używany przez funkcje programu orchestrator docelowej. Jeśli nie zostanie określony, używany jest domyślne parametry połączenia konta magazynu dla aplikacji funkcji.

> [!NOTE]
> W większości przypadków zaleca się, Pomiń te właściwości i Polegaj na zachowaniu domyślnym.

### <a name="client-usage"></a>Użycie klienta

W przypadku funkcji .NET zazwyczaj powiąże z `DurableOrchestrationClient`, który daje pełny dostęp do wszystkich klientów obsługiwanych przez funkcje trwałe interfejsów API. W języku JavaScript, tych samych interfejsów API są udostępniane przez `DurableOrchestrationClient` obiekt zwracany z `getClient`. Interfejsy API w obiekcie klienta obejmują:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (obecnie tylko platforma .NET)

Alternatywnie można powiązać funkcji .NET `IAsyncCollector<T>` gdzie `T` jest [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) lub `JObject`.

Zobacz [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) dokumentacji interfejsu API, aby uzyskać więcej informacji na temat tych operacji.

> [!WARNING]
> Wdrażając aplikacje lokalnie w języku JavaScript, musisz ustawić zmienną środowiskową `WEBSITE_HOSTNAME` do `localhost:<port>`, np. `localhost:7071` na korzystanie z metod `DurableOrchestrationClient`. Aby uzyskać więcej informacji na temat tego wymagania, zobacz [problem w usłudze GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="client-sample-visual-studio-development"></a>Przykładem klienta (Tworzenie aplikacji programu Visual Studio)

Oto przykład wyzwalanej przez kolejkę funkcja, która rozpoczyna się aranżacji "nazwę HelloWorld".

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

### <a name="client-sample-not-visual-studio"></a>Przykładem klienta (nie w programie Visual Studio)

Jeśli nie używasz programu Visual Studio do tworzenia aplikacji, możesz utworzyć następujące *function.json* pliku. Ten przykład przedstawia sposób konfigurowania funkcji wyzwalanej przez kolejkę, które używa klienta orkiestracji trwałe powiązania:

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
      "direction": "in"
    }
  ]
}
```

Poniżej przedstawiono przykłady specyficzny dla języka, które uruchomienie nowych wystąpień funkcji programu orchestrator.

#### <a name="c-sample"></a>Przykładowy języka C#

Poniższy przykład pokazuje, jak używać klienta aranżacji trwałe powiązanie, aby rozpocząć nowe wystąpienie funkcji od funkcji skryptu języka C#:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Przykład JavaScript

Poniższy przykład pokazuje, jak używać klienta aranżacji trwałe powiązanie, aby uruchomić nowe wystąpienie funkcji z funkcji języka JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Szczegółowe informacje na temat uruchamiania wystąpienia można znaleźć w [wystąpienia zarządzania](durable-functions-instance-management.md).

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Ustawienia host.JSON

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Więcej informacji na temat zachowań punkt kontrolny i powtarzanie](durable-functions-checkpointing-and-replay.md)