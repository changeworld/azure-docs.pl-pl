---
title: Punkty kontrolne, jak i powtarzanie w funkcje trwałe - Azure
description: Dowiedz się, jak punkty kontrolne i odpowiedzi działa w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: b1fd31a758501620129fdbbc532b8defcf927045
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648503"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Punkty kontrolne, jak i powtarzanie w funkcje trwałe (usługi Azure Functions)

Jednym z kluczowych atrybutów funkcje trwałe jest **niezawodnego wykonania**. Działanie i funkcje programu orchestrator mogą być uruchamiane w różnych maszyn wirtualnych w centrum danych, a tych maszyn wirtualnych lub podstawowej infrastruktury sieci nie jest 100% czasu.

Pomimo tego funkcje trwałe zapewnia niezawodne wykonywanie aranżacji. Robi to przy użyciu kolejki magazynu do wywoływania funkcji dysku, jak również historię wykonywania okresowo punkty kontrolne do tabel magazynu (przy użyciu wzorca projektowego chmury, znane jako [określania źródła zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Następnie można odtworzyć tej historii może automatycznie odbudować miejsca w pamięci stan funkcji orkiestratora.

## <a name="orchestration-history"></a>Historia aranżacji

Załóżmy, że masz następującą funkcję programu orchestrator:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

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

W każdej `await` (C#) lub `yield` — instrukcja (JavaScript), punkty kontrolne trwałe Framework zadania stanu wykonywania funkcji do usługi table storage. Ten stan jest, co jest nazywane *historii aranżacji*.

## <a name="history-table"></a>Tabela historii

Ogólnie rzecz biorąc trwałe Framework zadanie wykonuje następujące czynności na każdy punkt kontrolny:

1. Zapisuje historię wykonywania do tabel usługi Azure Storage.
2. Komunikaty umieszczeniu funkcji koordynatora potrzebuje do wywołania.
3. Komunikaty umieszczeniu programu orchestrator, sama &mdash; na przykład czasomierz trwałych wiadomości.

Po zakończeniu punktu kontrolnego, funkcja orkiestratora jest bezpłatne ma zostać usunięty z pamięci, dopóki nie ma więcej pracy wykonania.

> [!NOTE]
> Usługi Azure Storage nie zapewnia żadnej gwarancji transakcyjnych między zapisywania danych do magazynu tabel i kolejek. Aby obsługiwać błędy, używa dostawcy magazynu funkcje trwałe *spójności ostatecznej* wzorców. Te wzorce upewnij się, że żadne dane utracone w przypadku awarii lub utraty łączności w środku punktu kontrolnego.

Po zakończeniu historię pokazanych wcześniej funkcji wygląda podobnie do poniższego obrazu w usłudze Azure Table Storage (skrót dla celów ilustracyjnych):

| PartitionKey (identyfikator wystąpienia)                     | EventType             | Znacznik czasu               | Dane wejściowe | Name (Nazwa)             | Wynik                                                    | Stan |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | wartość null  | E1_HelloSequence |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | "" "Hello Tokio!" ""                                        |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | "" "Hello Seattle!" ""                                      |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" "Hello Londyn!" ""                                       |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokio!" ",""Hello Seattle!" ",""Londyn Hello!" "]" | Zakończone           |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |

Kilka uwagi dotyczące wartości kolumn:

* **PartitionKey**: Zawiera identyfikator wystąpienia aranżacji.
* **Typ zdarzenia**: Reprezentuje typ zdarzenia. Może być jedną z następujących typów:
  * **OrchestrationStarted**: Funkcja orkiestratora wznowić jego działanie od await lub jest uruchomiony po raz pierwszy. `Timestamp` Kolumna jest używana do wypełniania deterministyczną wartość dla [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) interfejsu API.
  * **ExecutionStarted**: Funkcja orkiestratora Rozpoczęto wykonywanie po raz pierwszy. To zdarzenie zawiera również dane wejściowe funkcji w `Input` kolumny.
  * **TaskScheduled**: Funkcja działanie zostało zaplanowane. Nazwa funkcji działania są przechwytywane w `Name` kolumny.
  * **TaskCompleted**: Ukończono działanie funkcji działania. Wynik funkcji znajduje się w `Result` kolumny.
  * **TimerCreated**: Trwałe Czasomierz został utworzony. `FireAt` Kolumna zawiera zaplanowanego czasu UTC, w którym okresu działania czasomierza.
  * **TimerFired**: Trwałe czasomierza uruchamiane.
  * **EventRaised**: Wystąpienie zewnętrznego zdarzenia zostało wysłane do wystąpienia aranżacji. `Name` Kolumna zawiera nazwę zdarzenia i `Input` kolumny przechwytuje ładunek zdarzenia.
  * **OrchestratorCompleted**: Funkcja programu orchestrator, oczekiwane.
  * **ContinueAsNew**: Funkcja orkiestratora ukończone i ponownego uruchomienia sam nowy stan. `Result` Kolumna zawiera wartości, który jest używany jako dane wejściowe w uruchomiono ponownie wystąpienia.
  * **ExecutionCompleted**: Funkcja orkiestratora zakończyło się lub nie powiodło się. Dane wyjściowe funkcji lub szczegóły błędu są przechowywane w `Result` kolumny.
* **Sygnatura czasowa**: Sygnatura czasowa UTC zdarzenia historii.
* **Nazwa**: Nazwa funkcji, która została wywołana.
* **Dane wejściowe**: Formacie JSON dane wejściowe funkcji.
* **Wynik**: Dane wyjściowe funkcji; oznacza to, że jego wartość zwracaną.

> [!WARNING]
> Chociaż jest to przydatne jako narzędzie debugowania nie wykona wszelkich zależności w tej tabeli. Go mogą ulec zmianie w miarę rozwoju rozszerzenia funkcji trwałych.

Za każdym razem, gdy funkcja wznawiając `await` (C#) lub `yield` (JavaScript), trwałe Framework zadania uruchomienia funkcji programu orchestrator od podstaw. Po każdym ponownym uruchomieniu konsultować się historię wykonywania, aby ustalić, czy miała bieżącej operacji asynchronicznej należy umieścić.  Jeśli operacja miało miejsce, platformę natychmiast odtwarza wynik tej operacji, a następnie przechodzi do następnego `await` (C#) lub `yield` (JavaScript). Ten proces jest kontynuowany, dopóki powtórzone całą historię, w tym momencie zmiennych lokalnych w funkcji programu orchestrator zostaną przywrócone poprzednie wartości.

## <a name="orchestrator-code-constraints"></a>Ograniczenia kodu programu orchestrator

Zachowanie powtarzania tworzy ograniczenia dotyczące typu kodu, które mogą być zapisywane w funkcji programu orchestrator:

* Kod programu orchestrator musi być **deterministyczne**. Zostaną odtworzone wiele razy, a musi mieć ten sam wynik każdorazowo. Na przykład nie bezpośrednie wywołania pobieranie bieżącej daty/godziny, Pobierz liczb losowych, Generowanie losowe identyfikatory GUID lub wywoływać zdalne punkty końcowe.

  Jeśli kod orchestrator serwer musi pobrać bieżącej daty/godziny, powinna korzystać [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET) lub `currentUtcDateTime` (JavaScript) interfejsu API, który jest bezpieczny dla powtórzeń.

  Jeśli kod orchestrator musi wygenerować losowy identyfikator GUID, należy użyć [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) interfejsu API (.NET), który jest bezpieczny dla powtarzania lub generacji GUID delegata do działania — funkcja (JavaScript), ponieważ w tym przykładzie:

  ```javascript
  const uuid = require("uuid/v1");

  module.exports = async function(context) {
    return uuid();
  }
  ```

  Operacji nie deterministyczne musi odbywać się w funkcji działań. Obejmuje to dowolna interakcja z powiązaniami innych danych wejściowych lub wyjściowych. Dzięki temu wszystkie wartości niedeterministyczne wygenerowano po pierwszym wykonaniu i zapisywane w historii wykonywania. Kolejne wykonania będzie używać wartości zapisane automatycznie.

* Powinien zostać zwrócony kod orchestrator **nieblokującej na poziomie**. Na przykład, oznacza to nie operacji We/Wy i nie wywołania `Thread.Sleep` (.NET) lub równoważnych interfejsów API.

  Jeśli program orchestrator wymaga opóźnienie, można użyć [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) (.NET) lub `createTimer` (JavaScript) interfejsu API.

* Kod orchestrator musi **nigdy zainicjowana żadnych operacji asynchronicznej** z wyjątkiem przy użyciu [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) interfejsu API lub `context.df` obiektu interfejsu API. Na przykład nie `Task.Run`, `Task.Delay` lub `HttpClient.SendAsync` na platformie .NET, lub `setTimeout()` i `setInterval()` w języku JavaScript. Trwałe Framework zadanie wykonuje kod programu orchestrator w jednym wątku i nie mogą współdziałać z innymi wątkami, które można uwzględnić w harmonogramie przez inne asynchronicznych interfejsów API.

* **Należy unikać pętli nieskończonej** w kodzie programu orchestrator. Ponieważ trwałe Framework zadań zapisuje historię wykonywania w miarę postępów funkcji aranżacji, wejścia w nieskończoną pętlę może spowodować wystąpienie programu orchestrator mało pamięci. W przypadku scenariuszy nieskończoną pętlę przy użyciu interfejsów API takich jak [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) (.NET) lub `continueAsNew` (JavaScript), aby ponownie uruchomić wykonywanie funkcji i odrzucić poprzednie historii wykonywania.

* Funkcje programu orchestrator języka JavaScript nie może być `async`. Ich musi być zadeklarowana jako generator synchroniczne funkcje.

Podczas tych ograniczeń może wydawać się czasochłonnym zadaniem w najpierw w praktyce, które nie są trudne do postępuj zgodnie z. Trwałe Framework zadań podejmuje próbę wykrycia naruszenia powyższych zasad i zgłasza `NonDeterministicOrchestrationException`. Jednak to zachowanie wykrywania jest największej staranności i nie zależą od niej.

> [!NOTE]
> Wszystkie te reguły dotyczą tylko funkcje wyzwalane `orchestrationTrigger` powiązania. Działanie funkcji wyzwalanej przez `activityTrigger` powiązanie i funkcje, które używają `orchestrationClient` powiązania ma nie tych ograniczeń.

## <a name="durable-tasks-net"></a>Niezawodne zadania (.NET)

> [!NOTE]
> W tej sekcji opisano szczegóły wewnętrznej implementacji trwałe Framework zadania. Funkcje trwałe można użyć, nie wiedząc o tym te informacje. Ma się tylko po to, aby lepiej zrozumieć zachowanie powtarzania.

Zadania, które mogą być bezpiecznie oczekiwana w funkcjach programu orchestrator są czasami określane jako *trwałe zadania*. Są to zadania, które są tworzone i zarządzane przez platformę, trwałe zadania. Należą do nich zadania zwracany przez `CallActivityAsync`, `WaitForExternalEvent`, i `CreateTimer`.

Te *trwałe zadania* wewnętrznie są zarządzane za pomocą listy `TaskCompletionSource` obiektów. Podczas ponownego odtwarzania te zadania są tworzone w ramach wykonywania kodu programu orchestrator i zostały wykonane, ponieważ dyspozytor wylicza pokrewnych zdarzeń w historii. To wszystko odbywa się synchronicznie, dopóki cała historia zostały odtworzone, za pomocą pojedynczego wątku. Wszelkie trwałe zadania, które nie zostały zakończone przed końcem powtarzania historii ma odpowiednie działania przeprowadzone. Na przykład komunikat może być umieszczony w kolejce do wywołania funkcji działania.

Opisane tu zachowanie wykonywania pomoże zrozumieć, dlaczego kod funkcji programu orchestrator nie należy nigdy nie `await` zadań nietrwałe: Wątek dyspozytora nie może czekać na jego zakończenie i wszelkie wywołania zwrotnego przez to zadanie może potencjalnie uszkodzić śledzenia stan funkcji programu orchestrator. Niektóre testy środowiska uruchomieniowego są stosowane do wypróbowania zapobiec takiej sytuacji.

Jeśli chcesz dowiedzieć się więcej o tym, jak trwałe Framework zadanie wykonuje funkcje programu orchestrator, najlepiej, aby zrobić to zapoznać się z [trwałe zadań kod źródłowy w serwisie GitHub](https://github.com/Azure/durabletask). W szczególności, zobacz [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) i [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Zarządzanie wystąpieniami](durable-functions-instance-management.md)
