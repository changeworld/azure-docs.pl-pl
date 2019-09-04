---
title: Punkty kontrolne i powtarzanie w Durable Functions — Azure
description: Dowiedz się, w jaki sposób punkty kontrolne i odpowiedzi działają w rozszerzeniu Durable Functions Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 1e6d3b78887c9d195fdf0137553860c141bdaaba
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241055"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Punkty kontrolne i powtarzanie w Durable Functions (Azure Functions)

Jednym z kluczowych atrybutów Durable Functions jest **niezawodne wykonywanie**. Funkcje programu Orchestrator i funkcje działania mogą być uruchomione na różnych maszynach wirtualnych w centrum danych, a te maszyny wirtualne lub podstawowa infrastruktura sieciowa nie są 100% niezawodne.

Pomimo tego Durable Functions zapewnia niezawodne wykonywanie aranżacji. Robi to za pomocą kolejek magazynu do wywołania funkcji i przez okresowe Tworzenie punktów kontrolnych historii wykonywania w tabelach magazynu (przy użyciu wzorca projektowego w chmurze znanego jako [źródła zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Tę historię można następnie odtworzyć w celu automatycznego odbudowy stanu znajdującego się w pamięci funkcji programu Orchestrator.

## <a name="orchestration-history"></a>Historia aranżacji

Załóżmy, że masz następującą funkcję programu Orchestrator:

### <a name="c"></a>C#

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

W każdej `await` instrukcjiC#() `yield` lub (JavaScript), trwała struktura zadań określa stan wykonywania funkcji w usłudze Table Storage. Ten stan jest określany mianem *historii aranżacji*.

## <a name="history-table"></a>Tabela historii

Ogólnie rzecz biorąc, usługa trwałej struktury zadań w każdym punkcie kontrolnym wykonuje następujące czynności:

1. Zapisuje historię wykonywania w tabelach usługi Azure Storage.
2. Komunikaty enqueues dla funkcji wywoływanych przez koordynatora.
3. Komunikaty enqueues dla programu Orchestrator &mdash; na przykład trwałe komunikaty czasomierza.

Po zakończeniu punktu kontrolnego funkcja programu Orchestrator jest bezpłatna do usunięcia z pamięci, dopóki nie będzie więcej pracy.

> [!NOTE]
> Usługa Azure Storage nie zapewnia żadnych transakcyjnych gwarancji między zapisywaniem danych w usłudze Table Storage i kolejkami. Aby obsłużyć błędy, dostawca magazynu Durable Functions będzie używać wzorców *spójności ostatecznej* . Te wzorce zapewniają, że żadne dane nie zostaną utracone w przypadku awarii lub utraty łączności w środku punktu kontrolnego.

Po zakończeniu historia funkcji pokazanej wcześniej wygląda podobnie do następującej na platformie Azure Table Storage (skrócona dla celów ilustracyjnych):

| PartitionKey (identyfikator wystąpienia)                     | EventType             | Timestamp               | Dane wejściowe | Name             | Wynik                                                    | State |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | wartość null  | E1_HelloSequence |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | "" Hello Tokio! ""                                        |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763 Z |       |                  | "" Hello Seattle! ""                                      |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857 Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857 Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" Hello Londyn! ""                                       |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[" Hello Tokio! "", "Hello Seattle!" "," Hello Londyn! ""] " | Ukończone           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

Kilka informacji o wartościach kolumn:

* **PartitionKey**: Zawiera identyfikator wystąpienia aranżacji.
* **Typ zdarzenia**: Reprezentuje typ zdarzenia. Może być jednym z następujących typów:
  * **OrchestrationStarted**: Funkcja programu Orchestrator została wznowiona z oczekiwania lub jest uruchomiona po raz pierwszy. Kolumna służy do wypełniania wartości deterministycznej interfejsu API [CurrentUtcDateTime.](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) `Timestamp`
  * **ExecutionStarted**: Funkcja programu Orchestrator rozpoczęła wykonywanie po raz pierwszy. To zdarzenie zawiera również dane wejściowe funkcji w `Input` kolumnie.
  * **TaskScheduled**: Zaplanowano funkcję działania. Nazwa funkcji działania jest przechwytywana w `Name` kolumnie.
  * **TaskCompleted**: Ukończono funkcję działania. Wynik funkcji znajduje się w `Result` kolumnie.
  * **TimerCreated**: Utworzono trwały czasomierz. `FireAt` Kolumna zawiera zaplanowany czas UTC, po upływie którego czasomierz wygasa.
  * **TimerFired**: Wyzwolono trwały czasomierz.
  * **EventRaised**: Zewnętrzne zdarzenie zostało wysłane do wystąpienia aranżacji. Kolumna przechwytuje nazwę zdarzenia `Input` , a kolumna przechwytuje ładunek zdarzenia. `Name`
  * **OrchestratorCompleted**: Oczekiwano funkcji programu Orchestrator.
  * **ContinueAsNew**: Funkcja programu Orchestrator została zakończona i uruchomiona ponownie z nowym stanem. `Result` Kolumna zawiera wartość, która jest używana jako dane wejściowe w ponownie uruchomionym wystąpieniu.
  * **ExecutionCompleted**: Funkcja programu Orchestrator działała do ukończenia (lub nie powiodła się). Dane wyjściowe funkcji lub szczegóły błędu są przechowywane w `Result` kolumnie.
* **Sygnatura czasowa**: Sygnatura czasowa UTC zdarzenia historii.
* **Nazwa**: Nazwa funkcji, która została wywołana.
* **Dane wejściowe**: Dane wejściowe dla funkcji w formacie JSON.
* **Wynik**: Dane wyjściowe funkcji; oznacza to, że wartość zwracana.

> [!WARNING]
> Chociaż jest to narzędzie do debugowania, nie należy podejmować żadnych zależności od tej tabeli. Może ulec zmianie w miarę rozwoju rozszerzenia Durable Functions.

Za każdym razem, gdy funkcja wznawia działanie `await` zC#() `yield` lub (JavaScript), trwała struktura zadań ponownie uruchamia funkcję programu Orchestrator od podstaw. Na każdym z nich należy ponownie sprawdzić historię wykonywania, aby ustalić, czy bieżąca operacja asynchroniczna została wykonana.  Jeśli operacja miała miejsce, struktura odtwarza dane wyjściowe tej operacji natychmiast i przechodzi do następnego `await` (C#) lub `yield` (JavaScript). Ten proces jest kontynuowany, dopóki cała historia nie zostanie powtórzona, w tym momencie wszystkie zmienne lokalne w funkcji programu Orchestrator zostaną przywrócone do poprzednich wartości.

## <a name="orchestrator-code-constraints"></a>Ograniczenia kodu programu Orchestrator

Zachowanie powtarzania tworzy ograniczenia dotyczące typu kodu, który można napisać w funkcji programu Orchestrator:

* Kod programu Orchestrator musi być **deterministyczny**. Zostanie ona powtórzona wiele razy i musi generować ten sam wynik za każdym razem. Na przykład żadne bezpośrednie wywołania do pobrania bieżącej daty/godziny, pobrania losowych identyfikatorów GUID lub wywołania do zdalnych punktów końcowych.

  Jeśli kod programu Orchestrator wymaga pobrania bieżącej daty/godziny, powinien on używać interfejsu API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET) lub `currentUtcDateTime` (JavaScript), który jest bezpieczny do odtworzenia.

  Jeśli kod programu Orchestrator musi generować losowy identyfikator GUID, powinien korzystać z interfejsu API [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.NET), który jest bezpieczny do odtworzenia, lub delegowania generowania identyfikatora GUID do funkcji działania (JavaScript), jak w poniższym przykładzie:

  ```javascript
  const uuid = require("uuid/v1");

  module.exports = async function(context) {
    return uuid();
  }
  ```

  Operacje niedeterministyczne muszą być wykonywane w funkcjach działania. Obejmuje to wszelką interakcję z innymi powiązaniami wejściowymi lub wyjściowymi. Daje to pewność, że wszystkie wartości niedeterministyczne będą generowane raz podczas pierwszego wykonywania i zapisywane w historii wykonywania. Kolejne wykonania spowodują użycie zapisanej wartości automatycznie.

* Kod programu Orchestrator nie powinien być **blokowany**. Na przykład oznacza to brak operacji we/wy i brak wywołań do `Thread.Sleep` (.NET) lub równoważnych interfejsów API.

  Jeśli koordynator musi opóźnić korzystanie z interfejsu [API programu (](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) .NET) lub `createTimer` (JavaScript).

* Kod programu Orchestrator **nigdy nie może inicjować żadnej operacji asynchronicznej** , [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) chyba że jest `context.df` używany interfejs API DurableOrchestrationContext lub interfejs API obiektu. Na `Task.Run`przykład nie `setInterval()` , `Task.Delay` lub `HttpClient.SendAsync` w programie .NET lub `setTimeout()` w języku JavaScript. Trwała struktura zadań wykonuje kod programu Orchestrator w pojedynczym wątku i nie może współdziałać z innymi wątkami, które mogą być zaplanowane przez inne asynchroniczne interfejsy API. W `InvalidOperationException` takim przypadku wyjątek jest zgłaszany.

> [!NOTE]
> Interfejs API [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) wykonuje asynchroniczne operacje we/wy, które nie są dozwolone w funkcji programu Orchestrator i mogą być używane tylko w funkcjach innych niż program Orchestrator.

* W kodzie programu Orchestrator **należy unikać nieskończonych pętli** . Ponieważ trwała struktura zadań zapisuje historię wykonywania w miarę postępów funkcji aranżacji, pętla nieskończona może spowodować, że w wystąpieniu programu Orchestrator brakuje pamięci. W przypadku scenariuszy pętli nieskończonej Użyj interfejsów API, takich jak [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) ( `continueAsNew` .NET) lub (JavaScript), aby ponownie uruchomić wykonywanie funkcji i odrzucić poprzednią historię wykonania.

* Funkcje programu Orchestrator w języku `async`JavaScript nie mogą być. Muszą być zadeklarowane jako funkcje generatora synchronicznego.

Chociaż te ograniczenia mogą wydawać się zniechęcające na początku, w rzeczywistości nie są trudne do obserwowania. Nietrwała platforma zadań próbuje wykryć naruszenia powyższych reguł i wygeneruje `NonDeterministicOrchestrationException`. Takie zachowanie jest jednak najlepszym rozwiązaniem i nie powinno być od niego zależne.

> [!NOTE]
> Wszystkie te reguły mają zastosowanie tylko do funkcji wyzwalanych przez `orchestrationTrigger` powiązanie. Funkcje działania wyzwalane przez `activityTrigger` powiązanie oraz funkcje, które `orchestrationClient` używają powiązania, nie mają takich ograniczeń.

## <a name="durable-tasks-net"></a>Zadania trwałe (.NET)

> [!NOTE]
> W tej sekcji opisano wewnętrzne szczegóły implementacji dla trwałej struktury zadań. Możesz użyć Durable Functions bez znajomości tych informacji. Jest ona przeznaczona tylko do zrozumienia zachowania powtarzania.

Zadania, które można bezpiecznie oczekiwać w funkcjach programu Orchestrator, są czasami określane jako *zadania trwałe*. Są to zadania, które są tworzone i zarządzane przez strukturę zadań trwałych. Przykłady to zadania zwracane przez `CallActivityAsync`, `WaitForExternalEvent`i `CreateTimer`.

Te *zadania trwałe* są zarządzane wewnętrznie przy użyciu listy `TaskCompletionSource` obiektów. Podczas odtwarzania te zadania są tworzone w ramach wykonywania kodu programu Orchestrator i są wykonywane, gdy Dyspozytor wylicza odpowiednie zdarzenia historii. To wszystko zostało wykonane synchronicznie przy użyciu jednego wątku, dopóki cała historia nie zostanie powtórzona. Wszelkie zadania trwałe, które nie są wykonywane na końcu powtarzania historii, mają odpowiednie działania. Na przykład komunikat może być w kolejce do wywołania funkcji działania.

Opisane w tym miejscu zachowanie dotyczące wykonywania powinno ułatwić zrozumienie, dlaczego kod funkcji programu `await` Orchestrator nigdy nie jest nietrwały: wątek dyspozytora nie może czekać na ukończenie i każde wywołanie zwrotne przez to zadanie może spowodować uszkodzenie śledzenia stan funkcji programu Orchestrator. Niektóre testy środowiska uruchomieniowego są gotowe do tego celu.

Jeśli chcesz uzyskać więcej informacji na temat sposobu wykonywania funkcji programu Orchestrator przez strukturę zadań trwałych, najlepszym rozwiązaniem jest zapoznaj się z [kodem źródłowym zadania trwałego w witrynie GitHub](https://github.com/Azure/durabletask). W szczególności zobacz [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) i [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zarządzaniu wystąpieniami](durable-functions-instance-management.md)
