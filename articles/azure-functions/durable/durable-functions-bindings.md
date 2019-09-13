---
title: Powiązania dla Durable Functions — Azure
description: Jak używać wyzwalaczy i powiązań dla rozszerzenia Durable Functions Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: ba3a30328df488dfe79bf445da550b5c96019081
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933500"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Powiązania dla Durable Functions (Azure Functions)

Rozszerzenie [Durable Functions](durable-functions-overview.md) wprowadza dwa nowe powiązania wyzwalacza kontrolujące wykonywanie funkcji Orchestrator i Functions. Wprowadza również powiązanie danych wyjściowych, które działa jako klient dla środowiska uruchomieniowego Durable Functions.

## <a name="orchestration-trigger"></a>Wyzwalacz aranżacji

Wyzwalacz aranżacji umożliwia tworzenie [trwałych funkcji programu Orchestrator](durable-functions-types-features-overview.md#orchestrator-functions). Ten wyzwalacz obsługuje uruchamianie nowych wystąpień funkcji programu Orchestrator i wznawianie istniejących wystąpień funkcji programu Orchestrator, które oczekują na zadanie.

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

* Jednowątkowy — pojedynczy wątek dyspozytora jest używany dla wszystkich wykonań funkcji programu Orchestrator na jednym wystąpieniu hosta. Z tego powodu ważne jest, aby upewnić się, że kod funkcji programu Orchestrator jest wydajny i nie wykonuje żadnych operacji we/wy. Ważne jest również, aby upewnić się, że ten wątek nie wykonuje żadnych działań asynchronicznych, z wyjątkiem przypadków, gdy oczekuje na Durable Functions typów zadań.
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
* generowanie danych wyjściowych — wyzwalacze aranżacji obsługują wartości wyjściowe oraz dane wejściowe. Wartość zwracana funkcji służy do przypisywania wartości wyjściowej i musi być możliwa do serializacji JSON. Jeśli funkcja .NET zwraca `Task` lub `void`, `null` wartość zostanie zapisana jako dane wyjściowe.

### <a name="trigger-sample"></a>Przykład wyzwalacza

Poniższy przykładowy kod pokazuje, jak najprostsza funkcja programu Orchestrator "Hello world" może wyglądać następująco:

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
> Obiekt w języku JavaScript nie reprezentuje DurableOrchestrationContext, ale [kontekst funkcji jako całości.](../functions-reference-node.md#context-object) `context` Dostęp do metod aranżacji można uzyskać za `context` pomocą `df` właściwości obiektu.

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

## <a name="activity-trigger"></a>Wyzwalacz działania

Wyzwalacz działania umożliwia tworzenie funkcji, które są wywoływane przez funkcje programu Orchestrator, znane jako [funkcje działania](durable-functions-types-features-overview.md#activity-functions).

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

* `activity`to nazwa działania. Ta wartość jest nazwą, której funkcje programu Orchestrator używają do wywołania tej funkcji działania. Ta właściwość jest opcjonalna. Jeśli nie zostanie określony, zostanie użyta nazwa funkcji.

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
* funkcje danych wyjściowych obsługują wartości wyjściowe oraz dane wejściowe. Wartość zwracana funkcji służy do przypisywania wartości wyjściowej i musi być możliwa do serializacji JSON. Jeśli funkcja .NET zwraca `Task` lub `void`, `null` wartość zostanie zapisana jako dane wyjściowe.
* **metadane** — funkcje działania .NET można powiązać z `string instanceId` parametrem w celu uzyskania identyfikatora wystąpienia aranżacji nadrzędnej.

### <a name="trigger-sample"></a>Przykład wyzwalacza

Poniższy przykładowy kod pokazuje, jak prosta funkcja działania "Hello world" może wyglądać następująco:

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

Powiązanie klienta aranżacji pozwala pisać funkcje, które współdziałają z funkcjami programu Orchestrator. Te funkcje są czasami określane jako [funkcje klienta](durable-functions-types-features-overview.md#client-functions). Na przykład można działać na wystąpieniach aranżacji w następujący sposób:

* Je uruchomić.
* Zbadaj ich stan.
* Zakończyć te działania.
* Wysyłaj do nich zdarzenia, gdy są one uruchomione.
* Przeczyść historię wystąpień.

Jeśli używasz programu Visual Studio, możesz powiązać się z klientem aranżacji przy użyciu atrybutu [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .net dla Durable Functions 1,0. Począwszy od wersji zapoznawczej Durable Functions 2,0, można powiązać z klientem aranżacji przy użyciu `DurableClientAttribute` atrybutu platformy .NET.

Jeśli używasz języków skryptów (na przykład plików *. CSX* lub *. js* ) do programowania, wyzwalacz aranżacji jest definiowany przez następujący `bindings` obiekt JSON w tablicy *Function. JSON*:

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

W programie .NET Functions zwykle wiąże `DurableOrchestrationClient`się z, który zapewnia pełny dostęp do wszystkich interfejsów API klienta obsługiwanych przez Durable Functions. Począwszy od Durable Functions 2,0, zamiast tego należy powiązać z `IDurableOrchestrationClient` interfejsem. W języku JavaScript te same interfejsy API są uwidaczniane przez obiekt zwrócony `getClient`z. Interfejsy API w obiekcie klienta obejmują:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)

Alternatywnie funkcje programu .NET mogą wiązać się `IAsyncCollector<T>` z `T` miejscem, gdzie `JObject`jest [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) lub.

Aby uzyskać więcej informacji na temat tych operacji, zobacz dokumentację interfejsu API [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) .

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

## <a name="entity-trigger"></a>Wyzwalacz jednostki

Wyzwalacze jednostek umożliwiają tworzenie [funkcji jednostek](durable-functions-entities.md). Ten wyzwalacz obsługuje przetwarzanie zdarzeń dla określonego wystąpienia jednostki.

W przypadku korzystania z narzędzi Visual Studio Tools for Azure Functions wyzwalacz jednostki jest konfigurowany przy użyciu `EntityTriggerAttribute` atrybutu .NET.

> [!NOTE]
> Wyzwalacze jednostek są dostępne w Durable Functions 2,0 i nowszych. Wyzwalacze jednostek nie są jeszcze dostępne dla języka JavaScript.

Wewnętrznie to powiązanie wyzwalacza sonduje serię kolejek w domyślnym koncie magazynu dla aplikacji funkcji. Te kolejki są wewnętrznymi szczegółami implementacji rozszerzenia, dlatego nie są one jawnie skonfigurowane we właściwościach powiązania.

### <a name="trigger-behavior"></a>Zachowanie wyzwalania

Poniżej przedstawiono niektóre uwagi dotyczące wyzwalacza jednostki:

* **Pojedyncze wątki**: Pojedynczy wątek dyspozytora jest używany do przetwarzania operacji dla określonej jednostki. Jeśli wiele komunikatów jest wysyłanych jednocześnie do pojedynczej jednostki, operacje zostaną przetworzone jeden w czasie.
* **Obsługa komunikatów trujących** — nie istnieje Trująca obsługa komunikatów w wyzwalaczach jednostek.
* **Widoczność komunikatów** — komunikaty wyzwalacza jednostki są dekolejkowane i niewidoczne przez konfigurowalny czas trwania. Widoczność tych komunikatów jest odnawiana automatycznie, o ile aplikacja funkcji jest uruchomiona i w dobrej kondycji.
* **Wartości zwracane** — funkcje jednostek nie obsługują zwracanych wartości. Istnieją określone interfejsy API, których można użyć w celu zapisania stanu lub przekazania wartości do aranżacji.

Wszystkie zmiany stanu wprowadzone w jednostce podczas jej wykonywania zostaną automatycznie utrwalone po zakończeniu wykonywania.

### <a name="trigger-usage-net"></a>Użycie wyzwalacza (.NET)

Każda funkcja jednostki ma typ `IDurableEntityContext`parametru, który ma następujące elementy członkowskie:

* **Obiekt EntityName**: Pobiera nazwę aktualnie wykonywanej jednostki.
* Obiekt **EntityKey**: Pobiera klucz aktualnie wykonywanej jednostki.
* **EntityId**: Pobiera identyfikator aktualnie wykonywanej jednostki.
* **OperationName**: Pobiera nazwę bieżącej operacji.
* **IsNewlyConstructed**: zwraca `true` czy jednostka nie istnieje przed operacją.
* **GetState\<TState > ()** : Pobiera bieżący stan jednostki. `TState` Parametr musi być typem pierwotnym lub obiektem serializacji JSON.
* **Setstate (Object)** : aktualizuje stan jednostki. `object` Parametr musi być obiektem pierwotnym lub serializowanym w formacie JSON.
* **Getinput\<TInput > ()** : Pobiera dane wejściowe dla bieżącej operacji. Parametr `TInput` typu musi reprezentować typ pierwotny lub obiekt z serializowaćm danych JSON.
* **Return (Object)** : zwraca wartość aranżacji, która wywołała operację. `object` Parametr musi być obiektem pierwotnym lub serializowanym w formacie JSON.
* **DestructOnExit ()** : usuwa jednostkę po zakończeniu bieżącej operacji.
* **SignalEntity (EntityId, String, Object)** : wysyła jednokierunkową wiadomość do jednostki. `object` Parametr musi być obiektem pierwotnym lub serializowanym w formacie JSON.

W przypadku korzystania z trybu `IDurableEntityContext` programowania jednostek opartych na klasie można odwoływać się do obiektu `Entity.Current` za pomocą właściwości Thread-static.

### <a name="trigger-sample---entity-function"></a>Wyzwól przykład — funkcja jednostki

Poniższy kod jest przykładem prostej jednostki *licznika* zaimplementowaną jako funkcja standardowa. Ta funkcja definiuje trzy *operacje*, `add`, `reset`, i `get`, z których `currentValue`każda działa na wartości stanu liczby całkowitej.

```csharp
[FunctionName(nameof(Counter))]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

### <a name="trigger-sample---entity-class"></a>Wyzwól przykładową klasę klasy

Poniższy przykład jest równoważną implementacją poprzedniej `Counter` jednostki przy użyciu klas i metod platformy .NET.

```csharp
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

> [!NOTE]
> Metoda punktu wejścia funkcji z `[FunctionName]` atrybutem *musi* być zadeklarowana `static` w przypadku używania klas jednostek. Niestatyczne metody punktu wejścia mogą spowodować inicjalizację wielu obiektów oraz inne niezdefiniowane zachowania.

Klasy jednostek mają specjalne mechanizmy współpracy z powiązaniami i iniekcją zależności platformy .NET. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [jednostek trwałych](durable-functions-entities.md) .

## <a name="entity-client"></a>Klient jednostki

Powiązanie klienta jednostki umożliwia asynchroniczne wyzwalanie [funkcji jednostek](#entity-trigger). Te funkcje są czasami określane jako [funkcje klienta](durable-functions-types-features-overview.md#client-functions).

Jeśli używasz programu Visual Studio, możesz powiązać z klientem jednostki przy użyciu `DurableClientAttribute` atrybutu .NET.

> [!NOTE]
> Może również służyć do powiązania z [klientem aranżacji.](#orchestration-client) `[DurableClientAttribute]`

Jeśli używasz języków skryptów (na przykład plików *. CSX* lub *. js* ) do programowania, wyzwalacz jednostki jest zdefiniowany przez następujący `bindings` obiekt JSON w tablicy *Function. JSON*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub`— Używane w scenariuszach, w których wiele aplikacji funkcji współużytkuje to samo konto magazynu, ale musi być od siebie odizolowane. Jeśli nie zostanie określony, zostanie użyta `host.json` wartość domyślna od. Ta wartość musi być zgodna z wartością używaną przez funkcje jednostki docelowej.
* `connectionName`— Nazwa ustawienia aplikacji, które zawiera parametry połączenia konta magazynu. Konto magazynu reprezentowane przez te parametry połączenia musi być takie samo, jak używane przez funkcje jednostki docelowej. Jeśli nie zostanie określony, używane są domyślne parametry połączenia konta magazynu dla aplikacji funkcji.

> [!NOTE]
> W większości przypadków zaleca się pominięcie opcjonalnych właściwości i zagwarantowanie zachowania domyślnego.

### <a name="entity-client-usage"></a>Użycie klienta jednostki

W programie .NET Functions zwykle wiąże `IDurableEntityClient`się z, który zapewnia pełny dostęp do wszystkich interfejsów API klienta obsługiwanych przez trwałe jednostki. Można również powiązać z `IDurableClient` interfejsem, który zapewnia dostęp do interfejsów API klienta zarówno dla jednostek, jak i aranżacji. Interfejsy API w obiekcie klienta obejmują:

* **ReadEntityStateAsync\<T >** : odczytuje stan jednostki.
* **SignalEntityAsync**: wysyła komunikat jednokierunkowy do jednostki i czeka na jego przekolejkowanie.
* **SignalEntityAsync\<TEntityInterface >** : taki sam `SignalEntityAsync` jak, ale używa wygenerowanego obiektu `TEntityInterface`serwera proxy typu.
* **CreateEntityProxy\<TEntityInterface >** : dynamicznie generuje dynamiczny serwer proxy typu `TEntityInterface` służącego do wykonywania wywołań bezpiecznych dla typu do jednostek.

> [!NOTE]
> Ważne jest, aby zrozumieć, że poprzednie operacje "sygnał" są wszystkie asynchroniczne. Nie jest możliwe wywoływanie funkcji Entity i przywrócenie wartości zwracanej z klienta. `SignalEntityAsync` Podobnie może powrócić przed rozpoczęciem wykonywania operacji przez jednostkę. Tylko funkcje programu Orchestrator mogą wywoływać funkcje jednostki synchronicznie i przetwarzać wartości zwracane.

Interfejsy API wymagają określenia unikatowego identyfikatora jednostki jako elementu `EntityId`. `SignalEntityAsync` Te interfejsy API również opcjonalnie przyjmuje nazwę operacji jednostki jako `string` i ładunku operacji jako `object`serializacji JSON. Jeśli obiekt docelowy nie istnieje, zostanie utworzony automatycznie przy użyciu określonego identyfikatora jednostki.

### <a name="client-sample-untyped"></a>Przykład klienta (niewpisany)

Oto przykład funkcji wyzwalanej przez kolejkę, która wywołuje jednostkę "Counter".

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

### <a name="client-sample-typed"></a>Przykład klienta (wpisana)

Istnieje możliwość wygenerowania obiektu proxy w celu bezpiecznego dostępu do operacji jednostek. Aby wygenerować bezpieczny dla typu serwer proxy, typ jednostki musi implementować interfejs. Załóżmy na przykład, że `Counter` jednostka wymieniona wcześniej `ICounter` implementuje interfejs, zdefiniowane w następujący sposób:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Kod klienta może następnie użyć `SignalEntityAsync<TEntityInterface>` i `ICounter` określić interfejs jako parametr typu, aby wygenerować bezpieczny dla typu serwer proxy. Użycie bezpiecznych typów serwerów proxy jest zademonstrowane w następującym przykładzie kodu:

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

W poprzednim przykładzie `proxy` parametr jest dynamicznie generowanym `ICounter`wystąpieniem, które wewnętrznie tłumaczy wywołanie na `Add` równoważne (bez typu) wywołania do `SignalEntityAsync`.

Istnieje kilka reguł definiowania interfejsów jednostek:

* Parametr `TEntityInterface` typu w `SignalEntityAsync<TEntityInterface>` musi być interfejsem.
* Interfejsy jednostek mogą definiować tylko metody.
* Metody interfejsu jednostki nie mogą definiować więcej niż jednego parametru.
* Metody interfejsu jednostki muszą zwracać `void`, `Task`lub `Task<T>` gdzie `T` jest wartością zwracaną.
* Interfejsy jednostek muszą mieć dokładnie jedną konkretną klasę implementacji w ramach tego samego zestawu (czyli klasy jednostki).

Jeśli którykolwiek z tych reguł `InvalidOperationException` zostanie naruszony, zostanie zgłoszony w czasie wykonywania. Komunikat o wyjątku wyjaśni, która reguła została przerwana.

> [!NOTE]
> `SignalEntityAsync` Interfejsy API reprezentują operacje jednokierunkowe. Jeśli interfejsy jednostki zwracają `Task<T>`, wartość `T` parametru zawsze będzie równa null lub `default`.

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Ustawienia host.JSON

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wbudowana dokumentacja interfejsu API protokołu HTTP do zarządzania wystąpieniami](durable-functions-http-api.md)