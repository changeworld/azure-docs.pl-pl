---
title: Powiązania dla Durable Functions — Azure
description: Jak używać wyzwalaczy i powiązań dla rozszerzenia Durable Functions Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: fbee98d64d37b2cdfc515eb733324902e238a768
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383105"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Powiązania dla Durable Functions (Azure Functions)

Rozszerzenie [Durable Functions](durable-functions-overview.md) wprowadza dwa nowe powiązania wyzwalacza kontrolujące wykonywanie funkcji Orchestrator i Functions. Wprowadza również powiązanie danych wyjściowych, które działa jako klient dla środowiska uruchomieniowego Durable Functions.

## <a name="orchestration-triggers"></a>Wyzwalacze aranżacji

Wyzwalacz aranżacji umożliwia tworzenie trwałych funkcji programu Orchestrator. Ten wyzwalacz obsługuje uruchamianie nowych wystąpień funkcji programu Orchestrator i wznawianie istniejących wystąpień funkcji programu Orchestrator, które oczekują na zadanie.

W przypadku korzystania z narzędzi Visual Studio Tools for Azure Functions wyzwalacz aranżacji jest konfigurowany przy użyciu atrybutu platformy .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .

Podczas pisania funkcji programu Orchestrator w językach skryptów (na przykład JavaScript lub C# skrypty) wyzwalacz aranżacji jest definiowany przez następujący obiekt JSON w `bindings` tablicy pliku *Function. JSON* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`jest nazwą aranżacji. Jest to wartość, której klienci muszą używać, gdy chcą uruchamiać nowe wystąpienia tej funkcji programu Orchestrator. Ta właściwość jest opcjonalna. Jeśli nie zostanie określony, zostanie użyta nazwa funkcji.

Wewnętrznie to powiązanie wyzwalacza sonduje serię kolejek w domyślnym koncie magazynu dla aplikacji funkcji. Te kolejki są wewnętrznymi szczegółami implementacji rozszerzenia, dlatego nie są one jawnie skonfigurowane we właściwościach powiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalania

Poniżej znajdują się pewne uwagi dotyczące wyzwalacza aranżacji:

* **Jednowątkowy** — pojedynczy wątek dyspozytora jest używany dla wszystkich wykonań funkcji programu Orchestrator na jednym wystąpieniu hosta. Z tego powodu ważne jest, aby upewnić się, że kod funkcji programu Orchestrator jest wydajny i nie wykonuje żadnych operacji we/wy. Ważne jest również, aby upewnić się, że ten wątek nie wykonuje żadnych działań asynchronicznych, z wyjątkiem przypadków, gdy oczekuje na Durable Functions typów zadań.
* **Obsługa komunikatów trujących** — nie istnieje Trująca obsługa komunikatów w wyzwalaczach aranżacji.
* **Widoczność komunikatów** — komunikaty wyzwalacza aranżacji są dekolejkowane i niewidoczne przez konfigurowalny czas trwania. Widoczność tych komunikatów jest odnawiana automatycznie, o ile aplikacja funkcji jest uruchomiona i w dobrej kondycji.
* **Zwracane wartości** — zwracane wartości są serializowane do formatu JSON i utrwalane w tabeli historii aranżacji w usłudze Azure Table Storage. Te zwracane wartości mogą być badane przez powiązanie klienta aranżacji opisane w dalszej części.

> [!WARNING]
> Funkcje programu Orchestrator nigdy nie powinny używać żadnych powiązań wejściowych ani wyjściowych innych niż powiązanie wyzwalacza aranżacji. Takie działanie może spowodować problemy z rozszerzeniem zadania trwałego, ponieważ te powiązania mogą nie przestrzegać reguł jednowątkowych i operacji we/wy. Jeśli chcesz użyć innych powiązań, Dodaj je do funkcji działania wywołanej z funkcji programu Orchestrator.

> [!WARNING]
> Funkcje programu Orchestrator w języku JavaScript nigdy `async`nie powinny być deklarowane.

### <a name="trigger-usage-net"></a>Użycie wyzwalacza (.NET)

Powiązanie wyzwalacza aranżacji obsługuje dane wejściowe i wyjściowe. Oto kilka rzeczy, które należy wiedzieć na temat obsługi danych wejściowych i wyjściowych:

* **wejścia** — funkcje aranżacji platformy .NET obsługują tylko [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) jako typ parametru. Deserializacja danych wejściowych bezpośrednio w sygnaturze funkcji nie jest obsługiwana. Aby można było pobrać dane wejściowe funkcji programu Orchestrator, kod musi `getInput` używać metody [\<getinput T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) lub (JavaScript). Te dane wejściowe muszą być typami możliwymi do serializacji JSON.
* Generowanie **danych wyjściowych —** wyzwalacze aranżacji obsługują wartości wyjściowe oraz dane wejściowe. Wartość zwracana funkcji służy do przypisywania wartości wyjściowej i musi być możliwa do serializacji JSON. Jeśli funkcja .NET zwraca `Task` lub `void`, `null` wartość zostanie zapisana jako dane wyjściowe.

### <a name="trigger-sample"></a>Przykład wyzwalacza

Poniżej przedstawiono przykład sposobu, w jaki najprostsza funkcja programu Orchestrator "Hello world" może wyglądać następująco:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Obiekt w języku JavaScript nie reprezentuje DurableOrchestrationContext, ale [kontekst funkcji jako całości..](../functions-reference-node.md#context-object) `context` Dostęp do metod aranżacji można uzyskać za `context` pomocą `df` właściwości obiektu.

> [!NOTE]
> Koordynator JavaScript powinien używać `return`. Biblioteka zajmuje się `context.done` wywołaniem metody. `durable-functions`

Większość funkcji działania wywoływanych przez program Orchestrator ma w tym miejscu następujący przykład "Hello world", który pokazuje, jak wywołać funkcję działania:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Wyzwalacze aktywności

Wyzwalacz działania umożliwia tworzenie funkcji, które są wywoływane przez funkcje programu Orchestrator.

Jeśli używasz programu Visual Studio, wyzwalacz aktywności jest konfigurowany przy użyciu atrybutu [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .NET.

Jeśli używasz vs Code lub Azure Portal do programowania, wyzwalacz aktywności jest definiowany przez następujący obiekt JSON w `bindings` tablicy *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`to nazwa działania. Jest to wartość, której funkcje programu Orchestrator używają do wywołania tej funkcji działania. Ta właściwość jest opcjonalna. Jeśli nie zostanie określony, zostanie użyta nazwa funkcji.

Wewnętrznie to powiązanie wyzwalacza sonduje kolejkę w domyślnym koncie magazynu dla aplikacji funkcji. Ta kolejka jest wewnętrznymi szczegółami implementacji rozszerzenia, dlatego nie jest on jawnie skonfigurowany we właściwościach powiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalania

Poniżej przedstawiono niektóre uwagi dotyczące wyzwalacza działania:

* **Wątkowość** — w przeciwieństwie do wyzwalacza aranżacji, wyzwalacze aktywności nie mają żadnych ograniczeń dotyczących wątków lub operacji we/wy. Mogą być traktowane jak regularne funkcje.
* **Obsługa komunikatów trujących** — w wyzwalaczach aktywności nie ma obsługi skażonych komunikatów.
* **Widoczność komunikatów** — komunikaty wyzwalacza aktywności są dekolejkowane i niewidoczne przez konfigurowalny czas trwania. Widoczność tych komunikatów jest odnawiana automatycznie, o ile aplikacja funkcji jest uruchomiona i w dobrej kondycji.
* **Zwracane wartości** — zwracane wartości są serializowane do formatu JSON i utrwalane w tabeli historii aranżacji w usłudze Azure Table Storage.

> [!WARNING]
> Zaplecze magazynu dla funkcji działania to szczegóły implementacji, a kod użytkownika nie powinien bezpośrednio korzystać z tych jednostek magazynu.

### <a name="trigger-usage-net"></a>Użycie wyzwalacza (.NET)

Powiązanie wyzwalacza działania obsługuje dane wejściowe i wyjściowe, podobnie jak wyzwalacz aranżacji. Oto kilka rzeczy, które należy wiedzieć na temat obsługi danych wejściowych i wyjściowych:

* **wejścia** — funkcje działania .NET natywnie używają [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) jako typu parametru. Alternatywnie można zadeklarować funkcję działania z dowolnym typem parametru, który jest możliwy do serializacji notacji JSON. [\<](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) Gdy używasz `DurableActivityContext`, możesz wywołać metodę getinput T >, aby pobrać i zdeserializować dane wejściowe funkcji działania.
* funkcje **danych wyjściowych** obsługują wartości wyjściowe oraz dane wejściowe. Wartość zwracana funkcji służy do przypisywania wartości wyjściowej i musi być możliwa do serializacji JSON. Jeśli funkcja .NET zwraca `Task` lub `void`, `null` wartość zostanie zapisana jako dane wyjściowe.
* **metadane** — funkcje działania .NET można powiązać z `string instanceId` parametrem w celu uzyskania identyfikatora wystąpienia aranżacji nadrzędnej.

### <a name="trigger-sample"></a>Przykład wyzwalacza

Poniżej przedstawiono przykład prostej funkcji działania "Hello world", która może wyglądać następująco:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Domyślny typ parametru dla powiązania .NET `ActivityTriggerAttribute` to. `DurableActivityContext` Jednak wyzwalacze aktywności .NET obsługują również powiązanie bezpośrednio z typami serializowanych JSON (w tym typami pierwotnymi), dzięki czemu ta sama funkcja może być uproszczona w następujący sposób:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Powiązania języka JavaScript mogą być również przesyłane jako dodatkowe parametry, dlatego ta sama funkcja może być uproszczona w następujący sposób:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

### <a name="passing-multiple-parameters"></a>Przekazywanie wielu parametrów

Nie jest możliwe bezpośrednie przekazywanie wielu parametrów do funkcji działania. Zalecenie w tym przypadku jest przekazywane do tablicy obiektów lub do używania obiektów [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) w programie .NET.

Poniższy przykład korzysta z nowych funkcji [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) dodanych z [ C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples):

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

### <a name="using-input-and-output-bindings"></a>Używanie powiązań wejściowych i wyjściowych

Oprócz powiązania wyzwalacza aktywności można użyć zwykłych powiązań danych wejściowych i wyjściowych. Na przykład możesz przełączyć dane wejściowe do powiązania działania i wysłać komunikat do centrum EventHub przy użyciu powiązania danych wyjściowych EventHub:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Klient aranżacji

Powiązanie klienta aranżacji pozwala pisać funkcje, które współdziałają z funkcjami programu Orchestrator. Na przykład można działać na wystąpieniach aranżacji w następujący sposób:

* Je uruchomić.
* Zbadaj ich stan.
* Zakończyć te działania.
* Wysyłaj do nich zdarzenia, gdy są one uruchomione.
* Przeczyść historię wystąpień.

Jeśli używasz programu Visual Studio, możesz powiązać się z klientem aranżacji przy użyciu atrybutu [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .NET.

Jeśli używasz języków skryptów (np. *CSX* lub *. js* ) do programowania, wyzwalacz aranżacji jest definiowany przez następujący `bindings` obiekt JSON w tablicy *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`— Używane w scenariuszach, w których wiele aplikacji funkcji współużytkuje to samo konto magazynu, ale musi być od siebie odizolowane. Jeśli nie zostanie określony, zostanie użyta `host.json` wartość domyślna od. Ta wartość musi być zgodna z wartością używaną przez docelowe funkcje programu Orchestrator.
* `connectionName`— Nazwa ustawienia aplikacji, które zawiera parametry połączenia konta magazynu. Konto magazynu reprezentowane przez te parametry połączenia musi być takie samo, jak używane przez docelowe funkcje programu Orchestrator. Jeśli nie zostanie określony, używane są domyślne parametry połączenia konta magazynu dla aplikacji funkcji.

> [!NOTE]
> W większości przypadków zaleca się pominięcie tych właściwości i zagwarantowanie zachowania domyślnego.

### <a name="client-usage"></a>Użycie klienta

W programie .NET Functions zwykle wiąże `DurableOrchestrationClient`się z, który zapewnia pełny dostęp do wszystkich interfejsów API klienta obsługiwanych przez Durable Functions. W języku JavaScript te same interfejsy API są uwidaczniane `DurableOrchestrationClient` przez obiekt zwrócony `getClient`z. Interfejsy API w obiekcie klienta obejmują:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) (obecnie tylko platforma .NET)

Alternatywnie funkcje programu .NET mogą wiązać się `IAsyncCollector<T>` z `T` miejscem, gdzie `JObject`jest [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) lub.

Dodatkowe szczegóły dotyczące tych operacji można znaleźć w dokumentacji interfejsu API [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) .

> [!WARNING]
> Podczas tworzenia lokalnie w języku JavaScript należy ustawić zmienną `WEBSITE_HOSTNAME` środowiskową na `localhost:<port>`, np. `localhost:7071`Aby użyć metod w `DurableOrchestrationClient`. Aby uzyskać więcej informacji na temat tego wymagania, zobacz artykuł [dotyczący usługi GitHub](https://github.com/Azure/azure-functions-durable-js/issues/28).

### <a name="client-sample-visual-studio-development"></a>Przykład klienta (Programowanie Visual Studio)

Oto przykład funkcji wyzwalanej przez kolejkę, która uruchamia organizację "HelloWorld".

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

### <a name="client-sample-not-visual-studio"></a>Przykład klienta (nie Visual Studio)

Jeśli nie używasz programu Visual Studio do tworzenia aplikacji, możesz utworzyć następujący plik *Function. JSON* . Ten przykład przedstawia sposób konfigurowania funkcji wyzwalanej przez kolejkę, która używa powiązania klienta trwałej aranżacji:

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

Poniżej przedstawiono przykłady charakterystyczne dla języka, które uruchamiają nowe wystąpienia funkcji programu Orchestrator.

#### <a name="c-sample"></a>Przykładowy języka C#

Poniższy przykład pokazuje, jak używać trwałego powiązania klienta aranżacji do uruchomienia nowego wystąpienia funkcji z funkcji C# skryptu:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Przykład JavaScript

Poniższy przykład pokazuje, jak używać trwałego powiązania klienta aranżacji do uruchomienia nowego wystąpienia funkcji z funkcji JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Więcej informacji o wystąpieniach początkowych można znaleźć w temacie [Zarządzanie wystąpieniami](durable-functions-instance-management.md).

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Ustawienia host.JSON

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat punktów kontrolnych i zachowań powtarzania](durable-functions-checkpointing-and-replay.md)
