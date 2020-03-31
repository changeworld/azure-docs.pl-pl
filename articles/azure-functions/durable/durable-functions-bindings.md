---
title: Powiązania dla trwałych funkcji — Azure
description: Jak używać wyzwalaczy i powiązań dla rozszerzenia funkcje trwałe dla usługi Azure Functions.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278222"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Powiązania dla funkcji trwałych (usługi Azure)

Rozszerzenie [funkcje trwałe](durable-functions-overview.md) wprowadza dwa nowe powiązania wyzwalacza, które kontrolują wykonywanie funkcji orchestrator i działania. Wprowadza również powiązania danych wyjściowych, który działa jako klient dla środowiska wykonawczego funkcji trwałych.

## <a name="orchestration-trigger"></a>Wyzwalacz aranżacji

Wyzwalacz aranżacji umożliwia tworzenie [trwałych funkcji aranżatora.](durable-functions-types-features-overview.md#orchestrator-functions) Ten wyzwalacz obsługuje uruchamianie nowych wystąpień funkcji koordynatora i wznawianie istniejących wystąpień funkcji koordynatora, które "oczekują" na zadanie.

Korzystając z narzędzi programu Visual Studio dla usług Azure Functions, wyzwalacz aranżacji jest konfigurowany przy użyciu atrybutu [.NET.](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet)

Podczas pisania funkcji orkiestratora w językach skryptów (na przykład javascript lub C# skryptów), wyzwalacz `bindings` aranżacji jest zdefiniowany przez następujący obiekt JSON w tablicy pliku *function.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`jest nazwą aranżacji, której klienci muszą używać, gdy chcą uruchomić nowe wystąpienia tej funkcji orchestrator. Ta właściwość jest opcjonalna. Jeśli nie zostanie określona, używana jest nazwa funkcji.

Wewnętrznie to powiązanie wyzwalacza sonduje serię kolejek na domyślnym koncie magazynu dla aplikacji funkcji. Te kolejki są szczegóły implementacji wewnętrznej rozszerzenia, dlatego nie są jawnie skonfigurowane we właściwościach powiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalacza

Oto kilka uwag na temat wyzwalacza aranżacji:

* **Jednowątkowywania** — pojedynczy wątek dyspozytora jest używany dla wszystkich wykonywania funkcji aranżatora w jednym wystąpieniu hosta. Z tego powodu ważne jest, aby upewnić się, że kod funkcji koordynatora jest wydajny i nie wykonuje żadnych we/wy. Jest również ważne, aby upewnić się, że ten wątek nie wykonuje żadnej pracy asynchronicznego, z wyjątkiem sytuacji oczekiwania na typy zadań specyficzne dla funkcji trwałych.
* **Obsługa wiadomości poison** — nie ma obsługi trujących komunikatów w wyzwalaczach aranżacji.
* **Widoczność wiadomości** — komunikaty wyzwalacza aranżacji są usuwane z kolejki i przechowywane niewidoczne przez konfigurowalny czas trwania. Widoczność tych komunikatów jest odnawiana automatycznie tak długo, jak aplikacja funkcji jest uruchomiona i w dobrej kondycji.
* **Zwraca wartości** — zwraca wartości są serializowane do JSON i utrwalone do tabeli historii aranżacji w usłudze Azure Table Storage. Te wartości zwracane mogą być wyszukiwane przez powiązanie klienta aranżacji, opisane później.

> [!WARNING]
> Funkcje programu Orchestrator nigdy nie należy używać żadnych powiązań wejściowych lub wyjściowych innych niż powiązanie wyzwalacza aranżacji. W ten sposób może spowodować problemy z rozszerzeniem trwałe zadanie, ponieważ te powiązania nie mogą być przestrzegane reguł jednowątkowywania i we/wy. Jeśli chcesz użyć innych powiązań, dodaj je do funkcji działania wywoływanej z funkcji Orchestrator.

> [!WARNING]
> Nigdy nie należy deklarować `async`funkcji orkiestratora JavaScript.

### <a name="trigger-usage-net"></a>Użycie wyzwalacza (.NET)

Powiązanie wyzwalacza aranżacji obsługuje zarówno dane wejściowe, jak i wyjścia. Oto kilka rzeczy, które należy wiedzieć o obsłudze danych wejściowych i wyjściowych:

* **inputs** - funkcje aranżacji .NET obsługują tylko `DurableOrchestrationContext` jako typ parametru. Deserializacja danych wejściowych bezpośrednio w podpisie funkcji nie jest obsługiwana. Kod musi `GetInput<T>` używać metody (.NET) lub `getInput` (JavaScript) do pobierania danych wejściowych funkcji programu orchestrator. Te dane wejściowe muszą być typami serializowalnymi JSON.
* **wyjścia** — wyzwalacze aranżacji obsługują wartości wyjściowe, a także dane wejściowe. Zwracana wartość funkcji jest używana do przypisywania wartości wyjściowej i musi być serializable JSON. Jeśli funkcja .NET `Task` `void`zwraca `null` lub wartość zostanie zapisana jako dane wyjściowe.

### <a name="trigger-sample"></a>Próbka wyzwalacza

Poniższy przykładowy kod pokazuje, jak może wyglądać najprostsza funkcja orkiestry "Hello World":

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> Poprzedni kod jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

#### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Obiekt `context` w języku JavaScript nie reprezentuje DurableOrchestrationContext, ale [kontekst funkcji jako całości](../functions-reference-node.md#context-object). Dostęp do metod aranżacji `df` można uzyskać za pośrednictwem właściwości `context` obiektu.

> [!NOTE]
> Koordynatorzy JavaScript powinni `return`używać . Biblioteka `durable-functions` zajmuje się `context.done` wywoływaniem metody.

Większość funkcji koordynatora wywołać funkcje działania, więc oto przykład "Hello World", który pokazuje, jak wywołać funkcję działania:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> Poprzedni kod jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

#### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Wyzwalacz aktywności

Wyzwalacz działania umożliwia tworzenie funkcji, które są wywoływane przez funkcje orkiestratora, znane jako [funkcje działania](durable-functions-types-features-overview.md#activity-functions).

Jeśli używasz programu Visual Studio, wyzwalacz działania `ActivityTriggerAttribute` jest skonfigurowany przy użyciu atrybutu .NET.

Jeśli używasz vs code lub witryny Azure portal dla rozwoju, wyzwalacz działania `bindings` jest zdefiniowany przez następujący obiekt JSON w tablicy *function.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`jest nazwą działania. Ta wartość jest nazwą, której funkcje aranżatory używają do wywoływania tej funkcji działania. Ta właściwość jest opcjonalna. Jeśli nie zostanie określona, używana jest nazwa funkcji.

Wewnętrznie to powiązanie wyzwalacza sonduje kolejkę na domyślnym koncie magazynu dla aplikacji funkcji. Ta kolejka jest szczegóły implementacji wewnętrznej rozszerzenia, dlatego nie jest jawnie skonfigurowany we właściwościach powiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalacza

Oto kilka uwag dotyczących wyzwalacza aktywności:

* **Wątki** — w przeciwieństwie do wyzwalacza aranżacji wyzwalacze aktywności nie mają żadnych ograniczeń dotyczących wątków lub we/wy. Mogą być traktowane jak regularne funkcje.
* **Obsługa wiadomości poison** — nie ma obsługi trujących komunikatów w wyzwalaczach aktywności.
* **Widoczność wiadomości** — komunikaty wyzwalania aktywności są usuwane z kolejki i przechowywane niewidoczne przez konfigurowalny czas trwania. Widoczność tych komunikatów jest odnawiana automatycznie tak długo, jak aplikacja funkcji jest uruchomiona i w dobrej kondycji.
* **Zwraca wartości** — zwraca wartości są serializowane do JSON i utrwalone do tabeli historii aranżacji w usłudze Azure Table Storage.

> [!WARNING]
> Wewnętrznej bazy danych magazynu dla funkcji działania jest szczegóły implementacji i kod użytkownika nie powinny wchodzić w interakcje z tych jednostek magazynu bezpośrednio.

### <a name="trigger-usage-net"></a>Użycie wyzwalacza (.NET)

Powiązanie wyzwalacza działania obsługuje zarówno dane wejściowe, jak i wyjścia, podobnie jak wyzwalacz aranżacji. Oto kilka rzeczy, które należy wiedzieć o obsłudze danych wejściowych i wyjściowych:

* **inputs** - funkcje działania `DurableActivityContext` .NET używane są natywnie jako typ parametru. Alternatywnie funkcja działania można zadeklarować za pomocą dowolnego typu parametru, który jest serializable JSON. Podczas korzystania `DurableActivityContext`z programu `GetInput<T>` można wywołać pobieranie i deserializację danych wejściowych funkcji działania.
* **wyjścia** — funkcje działania obsługują wartości wyjściowe, a także wejścia. Zwracana wartość funkcji jest używana do przypisywania wartości wyjściowej i musi być serializable JSON. Jeśli funkcja .NET `Task` `void`zwraca `null` lub wartość zostanie zapisana jako dane wyjściowe.
* **metadane** — funkcje działania `string instanceId` platformy .NET mogą być powiązane z parametrem, aby uzyskać identyfikator wystąpienia nadrzędnej aranżacji.

### <a name="trigger-sample"></a>Próbka wyzwalacza

Poniższy przykładowy kod pokazuje, jak może wyglądać prosta funkcja działania "Hello World":

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> Poprzedni kod jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableActivityContext` zamiast `IDurableActivityContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

Domyślnym typem parametru `ActivityTriggerAttribute` powiązania `IDurableActivityContext`.NET jest . Jednak wyzwalacze aktywności .NET obsługują również powiązanie bezpośrednio z typami serializowania JSON (w tym typami pierwotnymi), więc ta sama funkcja może być uproszczona w następujący sposób:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2.0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Powiązania JavaScript mogą być również przekazywane jako dodatkowe parametry, więc ta sama funkcja może być uproszczona w następujący sposób:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Korzystanie z powiązań wejściowych i wyjściowych

Oprócz powiązania wyzwalacza działania można używać regularnych powiązań wejściowych i wyjściowych. Na przykład można wziąć dane wejściowe do powiązania działania i wysłać wiadomość do EventHub przy użyciu powiązania danych wyjściowych EventHub:

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

Powiązanie klienta aranżacji umożliwia zapisywanie funkcji, które współdziałają z funkcji aranżacji. Funkcje te są czasami określane jako [funkcje klienta](durable-functions-types-features-overview.md#client-functions). Na przykład można działać na wystąpienia aranżacji w następujący sposób:

* Uruchom je.
* Zapytanie o ich stan.
* Zakończ je.
* Wysyłaj do nich wydarzenia, gdy są uruchomione.
* Przeczyść historię wystąpień.

Jeśli używasz programu Visual Studio, można powiązać z `OrchestrationClientAttribute` klientem aranżacji przy użyciu atrybutu .NET dla funkcji trwałych 1.0. Począwszy od funkcji trwałych 2.0, można powiązać `DurableClientAttribute` z klientem aranżacji przy użyciu atrybutu .NET.

Jeśli używasz języków skryptów (na przykład *.csx* lub *.js* files) do tworzenia, wyzwalacz aranżacji jest zdefiniowany przez następujący obiekt JSON w `bindings` tablicy *function.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`- Używane w scenariuszach, w których wiele aplikacji funkcji współużytkuje to samo konto magazynu, ale muszą być odizolowane od siebie. Jeśli nie zostanie określona, używana jest wartość domyślna. `host.json` Ta wartość musi być zgodna z wartością używaną przez docelowe funkcje koordynatora.
* `connectionName`- Nazwa ustawienia aplikacji, która zawiera parametry połączenia konta magazynu. Konto magazynu reprezentowane przez ten ciąg połączenia musi być tym samym, które jest używane przez docelowe funkcje aranżatora. Jeśli nie zostanie określony, używany jest domyślny ciąg połączenia konta magazynu dla aplikacji funkcji.

> [!NOTE]
> W większości przypadków zaleca się pominięcie tych właściwości i poleganie na domyślnym zachowaniu.

### <a name="client-usage"></a>Użycie klienta

W .NET funkcje zazwyczaj `IDurableOrchestrationClient`wiążą się z , co daje pełny dostęp do wszystkich interfejsów API klienta aranżacji obsługiwane przez funkcje trwałe. W starszych funkcji trwałe 2.x zwalnia zamiast `DurableOrchestrationClient` powiązać do klasy. W języku JavaScript te same interfejsy API `getClient`są udostępniane przez obiekt zwrócony z pliku . Interfejsy API obiektu klienta obejmują:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternatywnie, funkcje .NET `IAsyncCollector<T>` `T` można `StartOrchestrationArgs` `JObject`powiązać z gdzie jest lub .

Aby uzyskać więcej informacji na `IDurableOrchestrationClient` temat tych operacji, zobacz dokumentację interfejsu API.

### <a name="client-sample-visual-studio-development"></a>Przykład klienta (program visual studio development)

Oto przykład kolejka wyzwalane funkcji, która uruchamia "HelloWorld" aranżacji.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Poprzedni kod języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy `OrchestrationClient` użyć `DurableClient` atrybutu zamiast atrybutu `DurableOrchestrationClient` i zamiast `IDurableOrchestrationClient`parametru . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

### <a name="client-sample-not-visual-studio"></a>Przykład klienta (nie visual studio)

Jeśli nie używasz programu Visual Studio do tworzenia, można utworzyć następujący plik *function.json.* W tym przykładzie pokazano, jak skonfigurować funkcję wyzwalaną kolejką, która używa trwałego powiązania klienta aranżacji:

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
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> Poprzedni JSON jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy użyć `orchestrationClient` zamiast `durableClient` typu wyzwalacza. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

Poniżej przedstawiono przykłady specyficzne dla języka, które uruchamiają nowe wystąpienia funkcji programu orchestrator.

#### <a name="c-script-sample"></a>Przykład skryptu języka C#

W poniższym przykładzie pokazano, jak używać trwałego powiązania klienta aranżacji, aby uruchomić nowe wystąpienie funkcji z funkcji C# wyzwalane kolejką:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Poprzedni kod jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy użyć typu `DurableOrchestrationClient` parametru `IDurableOrchestrationClient`zamiast . Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

#### <a name="javascript-sample"></a>Przykład javascript

W poniższym przykładzie pokazano, jak używać trwałego powiązania klienta aranżacji, aby uruchomić nowe wystąpienie funkcji z funkcji JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Więcej szczegółów na temat uruchamiania wystąpień można znaleźć w [zarządzaniu wystąpieniami](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Wyzwalacz jednostki

Wyzwalacze encji umożliwiają tworzenie [funkcji encji](durable-functions-entities.md). Ten wyzwalacz obsługuje przetwarzanie zdarzeń dla wystąpienia określonej jednostki.

Korzystając z narzędzi programu Visual Studio dla usług Azure Functions, wyzwalacz jednostki jest konfigurowany przy użyciu atrybutu `EntityTriggerAttribute` .NET.

> [!NOTE]
> Wyzwalacze jednostek są dostępne począwszy od funkcji trwałych 2.x.

Wewnętrznie to powiązanie wyzwalacza sonduje serię kolejek na domyślnym koncie magazynu dla aplikacji funkcji. Te kolejki są szczegóły implementacji wewnętrznej rozszerzenia, dlatego nie są jawnie skonfigurowane we właściwościach powiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalacza

Oto kilka uwag dotyczących wyzwalacza jednostki:

* **Jednowątkowy:** Pojedynczy wątek dyspozytora jest używany do przetwarzania operacji dla określonej jednostki. Jeśli wiele wiadomości są wysyłane do jednej jednostki jednocześnie, operacje będą przetwarzane jeden na raz.
* **Obsługa trujących wiadomości** — nie ma obsługi trujących komunikatów w wyzwalaczach jednostek.
* **Widoczność komunikatów** — komunikaty wyzwalacza jednostki są usuwane z kolejki i przechowywane jako niewidoczne przez konfigurowalny czas trwania. Widoczność tych komunikatów jest odnawiana automatycznie tak długo, jak aplikacja funkcji jest uruchomiona i w dobrej kondycji.
* **Zwraca wartości** — funkcje jednostki nie obsługują zwracanych wartości. Istnieją określone interfejsy API, które mogą służyć do zapisywania stanu lub przekazywania wartości z powrotem do aranżacji.

Wszelkie zmiany stanu wprowadzone do jednostki podczas jej wykonywania zostaną automatycznie utrwalone po zakończeniu wykonywania.

### <a name="trigger-usage-net"></a>Użycie wyzwalacza (.NET)

Każda funkcja jednostki ma `IDurableEntityContext`typ parametru , który ma następujące elementy członkowskie:

* **EntityName**: nazwa aktualnie wykonywanej jednostki.
* **EntityKey**: klucz aktualnie wykonywanej jednostki.
* **EntityId**: identyfikator aktualnie wykonywanej jednostki.
* **OperationName**: nazwa bieżącej operacji.
* **HasState**: czy jednostka istnieje, to znaczy, ma jakiś stan. 
* **GetState\<TState>()**: pobiera bieżący stan jednostki. Jeśli jeszcze nie istnieje, jest tworzony i `default<TState>`inicjowany do . Parametr `TState` musi być typem pierwotnym lub serializalnym JSON. 
* **GetState\<TState>(initfunction)**: pobiera bieżący stan jednostki. Jeśli jeszcze nie istnieje, jest tworzony przez `initfunction` wywołanie podanego parametru. Parametr `TState` musi być typem pierwotnym lub serializalnym JSON. 
* **SetState(arg)**: tworzy lub aktualizuje stan jednostki. Parametr `arg` musi być obiektem serializowanym JSON lub prymitywnym.
* **DeleteState()**: usuwa stan jednostki. 
* **GetInput\<TInput>()**: pobiera dane wejściowe dla bieżącej operacji. Parametr `TInput` type musi być typem pierwotnym lub serializowanym JSON.
* **Return(arg)**: zwraca wartość do aranżacji, która wywoływała operację. Parametr `arg` musi być obiektem prymitywnym lub serialialnym JSON.
* **SignalEntity(EntityId, scheduledTimeUtc, operation, input)**: wysyła komunikat jednokierunkowy do jednostki. Parametr `operation` musi być ciągiem zerowym, `scheduledTimeUtc` opcjonalny musi być datownikiem UTC, w `input` którym ma być wywołanie operacji, a parametr musi być obiektem pierwotnym lub serialialnym JSON.
* **CreateNewOrchestration(orchestratorFunctionName, input)**: rozpoczyna nową aranżację. Parametr `input` musi być obiektem prymitywnym lub serialialnym JSON.

Obiekt `IDurableEntityContext` przekazany do funkcji jednostki można `Entity.Current` uzyskać za pomocą właściwości asynchronizowno-lokalnej. Takie podejście jest wygodne podczas korzystania z modelu programowania opartego na klasach.

### <a name="trigger-sample-c-function-based-syntax"></a>Próbka wyzwalacza (składnia oparta na funkcji języka C#)

Poniższy kod jest przykładem *prostej counter* jednostki zaimplementowane jako funkcja trwałe. Ta funkcja definiuje trzy `add` `reset`operacje, `get`, i , z których każda działa w stanie liczby całkowitej.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Aby uzyskać więcej informacji na temat składni opartej na funkcjach i sposobu jej używania, zobacz [Składnia oparta na funkcjach](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Próbka wyzwalacza (składnia oparta na klasach C#)

Poniższy przykład jest równoważne `Counter` implementacji jednostki przy użyciu klas i metod.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Stan tej encji jest obiektem typu `Counter`, który zawiera pole, które przechowuje bieżącą wartość licznika. Aby utrwalić ten obiekt w magazynie, jest serializowany i deserializowany przez [bibliotekę Json.NET.](https://www.newtonsoft.com/json) 

Aby uzyskać więcej informacji na temat składni opartej na klasach i sposobu jej używania, zobacz [Definiowanie klas jednostek](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> Metoda punktu wejścia funkcji `[FunctionName]` z atrybutem `static` *musi* być zadeklarowana podczas korzystania z klas jednostek. Niestatyczne metody punktu wejścia mogą spowodować inicjowanie wielu obiektów i potencjalnie inne niezdefiniowane zachowania.

Klasy jednostek mają specjalne mechanizmy interakcji z powiązaniami i iniekcji zależności .NET. Aby uzyskać więcej informacji, zobacz [Budowa jednostki](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Przykład wyzwalacza (JavaScript)

Poniższy kod jest przykładem prostej *jednostki Counter* zaimplementowane jako trwała funkcja zapisana w języku JavaScript. Ta funkcja definiuje trzy `add` `reset`operacje, `get`, i , z których każda działa w stanie liczby całkowitej.

**funkcja.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**indeks.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> Trwałe jednostki są dostępne w języku JavaScript, począwszy od `durable-functions` wersji **1.3.0** pakietu npm.

## <a name="entity-client"></a>Klient jednostki

Powiązanie klienta jednostki umożliwia asynchronicznie wyzwalanie [funkcji jednostki.](#entity-trigger) Funkcje te są czasami określane jako [funkcje klienta](durable-functions-types-features-overview.md#client-functions).

Jeśli używasz programu Visual Studio, można powiązać z `DurableClientAttribute` klientem jednostki przy użyciu atrybutu .NET.

> [!NOTE]
> `[DurableClientAttribute]` Może być również używany do powiązania z [klientem aranżacji](#orchestration-client).

Jeśli używasz języków skryptów (na przykład *.csx* lub *.js* files) do tworzenia, wyzwalacz jednostki `bindings` jest definiowany przez następujący obiekt JSON w tablicy *function.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub`- Używane w scenariuszach, w których wiele aplikacji funkcji współużytkuje to samo konto magazynu, ale muszą być odizolowane od siebie. Jeśli nie zostanie określona, używana jest wartość domyślna. `host.json` Ta wartość musi być zgodna z wartością używaną przez funkcje jednostki docelowej.
* `connectionName`- Nazwa ustawienia aplikacji, która zawiera parametry połączenia konta magazynu. Konto magazynu reprezentowane przez ten parametry połączenia musi być tym samym kontem używanym przez funkcje jednostki docelowej. Jeśli nie zostanie określony, używany jest domyślny ciąg połączenia konta magazynu dla aplikacji funkcji.

> [!NOTE]
> W większości przypadków zaleca się pominięcie właściwości opcjonalnych i poleganie na domyślnym zachowaniu.

### <a name="entity-client-usage"></a>Użycie klienta jednostki

W .NET funkcje zazwyczaj `IDurableEntityClient`wiążą się z , co daje pełny dostęp do wszystkich interfejsów API klienta obsługiwanych przez trwałe jednostki. Można również powiązać `IDurableOrchestrationClient` z interfejsem, który zapewnia dostęp do interfejsów API klienta dla jednostek i aranżacji. Interfejsy API obiektu klienta obejmują:

* **ReadEntityStateAsync\<T>**: odczytuje stan jednostki. Zwraca odpowiedź, która wskazuje, czy jednostka docelowa istnieje, a jeśli tak, jaki jest jej stan.
* **SignalEntityAsync**: wysyła komunikat jednokierunkowy do jednostki i czeka na jej przesuń w kolejce.
* **ListEntitiesAsync**: zapytania dotyczące stanu wielu jednostek. Jednostki mogą być wyszukiwane według *nazwy* i *czasu ostatniej operacji*.

Nie ma potrzeby tworzenia jednostki docelowej przed wysłaniem sygnału — stan jednostki można utworzyć z wewnątrz funkcji jednostki, która obsługuje sygnał.

> [!NOTE]
> Ważne jest, aby zrozumieć, że "sygnały" wysyłane z klienta są po prostu ujmowane w kolejce, do przetworzenia asynchronicznie w późniejszym czasie. W szczególności `SignalEntityAsync` zwykle zwraca przed jednostki nawet rozpoczyna operację i nie jest możliwe, aby odzyskać wartość zwracaną lub przestrzegać wyjątków. Jeśli wymagane są silniejsze gwarancje (np. dla przepływów pracy), należy użyć *funkcji koordynatora,* które mogą czekać na zakończenie operacji jednostki i mogą przetwarzać wartości zwracane i przestrzegać wyjątków.

### <a name="example-client-signals-entity-directly---c"></a>Przykład: jednostka sygnałów klienta bezpośrednio - C #

Oto przykładowa funkcja wyzwalana przez kolejkę, która wywołuje jednostkę "Counter".

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="example-client-signals-entity-via-interface---c"></a>Przykład: jednostka sygnałów klienta za pośrednictwem interfejsu - C #

W miarę możliwości zaleca [się uzyskiwanie dostępu do jednostek za pośrednictwem interfejsów,](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) ponieważ zapewnia więcej sprawdzania typu. Załóżmy na `Counter` przykład, że jednostka `ICounter` wymieniona wcześniej zaimplementowała interfejs, zdefiniowany w następujący sposób:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Kod klienta można `SignalEntityAsync<ICounter>` następnie użyć do wygenerowania typu bezpieczny serwer proxy:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

Parametr `proxy` jest dynamicznie generowanym `ICounter`wystąpieniem , które wewnętrznie `Add` tłumaczy wywołanie na równoważne (nietypowe) wywołanie `SignalEntityAsync`.

> [!NOTE]
> Interfejsy `SignalEntityAsync` API reprezentują operacje jednokierunkowe. Jeśli interfejsy jednostki zwraca, `Task<T>` `T` wartość parametru zawsze `default`będzie null lub .

W szczególności nie ma sensu sygnalizować `Get` operacji, ponieważ nie zwracana jest żadna wartość. Zamiast tego klienci mogą `ReadStateAsync` używać albo do bezpośredniego dostępu do stanu licznika lub `Get` można uruchomić funkcję koordynatora, który wywołuje operację.

### <a name="example-client-signals-entity---javascript"></a>Przykład: jednostka sygnałów klienta - JavaScript

Oto przykładowa funkcja wyzwalana kolejką, która sygnalizuje encję "Licznik" w języku JavaScript.

**funkcja.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**indeks.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> Trwałe jednostki są dostępne w języku JavaScript, począwszy od `durable-functions` wersji **1.3.0** pakietu npm.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>ustawienia host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wbudowane odwołanie do interfejsu API HTTP do zarządzania wystąpieniami](durable-functions-http-api.md)
