---
title: Punkty kontrolne i powtarzania w funkcjach trwałe - Azure
description: Dowiedz się, jak punkty kontrolne i odpowiedzi działa w rozszerzeniu trwałe funkcji dla usługi Azure Functions.
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
ms.openlocfilehash: 39cdb9b2c6eae9a3176aedc64b8d187e298fdfdd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764574"
---
# <a name="checkpoints-and-replay-in-durable-functions-azure-functions"></a>Punkty kontrolne i powtarzania w funkcjach trwałe (usługi Azure Functions)

Jednym z kluczowych atrybutów trwałe funkcji jest **niezawodnego wykonania**. Funkcje programu orchestrator i funkcje działania może działać na różnych maszyn wirtualnych w obrębie centrum danych i tych maszyn wirtualnych lub podstawowej infrastruktury sieciowej nie jest niezawodna 100%.

Mimo to trwałe funkcje zapewnia niezawodnego wykonania orchestrations. Robi to przy użyciu magazynu kolejek w celu wywołania funkcji dysku i okresowo historii wykonywania procesu tworzenia punktów kontrolnych do magazynu tabel (przy użyciu wzorca projektowego chmury, znany jako [źródłem zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)). Następnie można odtworzyć tej historii może automatycznie odbudować stan w pamięci funkcji programu orchestrator.

## <a name="orchestration-history"></a>Historia aranżacji

Załóżmy, że istnieje następująca funkcja programu orchestrator:

#### <a name="c"></a>C#

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const output = [];
    output.push(yield context.df.callActivityAsync("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivityAsync("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivityAsync("E1_SayHello", "London"));

    return output;
});
```

W każdej `await` (C#) lub `yield` — instrukcja (JavaScript), punkty kontrolne trwałe Framework zadań stan wykonywania funkcji do magazynu tabel. Ten stan jest, co jest nazywane *historii aranżacji*.

## <a name="history-table"></a>Tabela historii

Ogólnie rzecz biorąc trwałe Framework zadań wykonuje następujące czynności w każdym punkcie kontrolnym:

1. Zapisuje historii wykonywania do tabel usługi Azure Storage.
2. Komunikaty Enqueues dla funkcji orchestrator potrzebuje do wywołania.
3. Komunikaty Enqueues programu orchestrator, sama &mdash; na przykład trwałe czasomierza wiadomości.

Po zakończeniu punktu kontrolnego funkcji programu orchestrator jest bezpłatna ma zostać usunięty z pamięci momentu więcej pracy wykonania.

> [!NOTE]
> Usługa Azure Storage daje żadnej gwarancji transakcyjnych między zapisywania danych do magazynu tabel i kolejek. Do obsługi błędów, używa dostawcy magazynu trwałego funkcji *spójność ostateczna* wzorców. Te wzorce upewnij się, że w przypadku awarii lub utraty połączenia w trakcie punkt kontrolny nie zostały utracone nie dane.

Po zakończeniu historii funkcji przedstawiona wcześniej wygląda następujące w usłudze Azure Table Storage (skrót w celach ilustracyjnych):

| PartitionKey (identyfikator wystąpienia)                     | Typ zdarzenia             | Sygnatura czasowa               | Dane wejściowe | Name (Nazwa)             | Wynik                                                    | Stan | 
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|---------------------| 
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     | 
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | Wartość null  | E1_HelloSequence |                                                           |                     | 
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
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | "" "Hello Londynie!" ""                                       |                     | 
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokio!" ",""Hello Seattle!" ",""Hello Londynie!" "]" | Ukończone           | 
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     | 

Kilka uwagi o wartości kolumn:
* **PartitionKey**: zawiera identyfikator wystąpienia orchestration.
* **Typ zdarzenia**: reprezentuje typ zdarzenia. Może być jednym z następujących typów:
    * **OrchestrationStarted**: funkcja orchestrator wznowieniu await lub jest uruchomiony po raz pierwszy. `Timestamp` Kolumna jest używana do wypełniania deterministyczne wartość [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) interfejsu API.
    * **ExecutionStarted**: funkcja orchestrator Rozpoczęto wykonywanie po raz pierwszy. To zdarzenie zawiera również dane wejściowe funkcji w `Input` kolumny.
    * **TaskScheduled**: funkcja działanie zostało zaplanowane. Nazwa funkcji działania są przechwytywane w `Name` kolumny.
    * **TaskCompleted**: funkcja działania została ukończona. Wynik funkcji jest `Result` kolumny.
    * **TimerCreated**: utworzono trwałe czasomierza. `FireAt` Kolumna zawiera zaplanowanego czasu UTC, jaką wygaśnięciu czasomierza.
    * **TimerFired**: wywoływane trwałe czasomierza.
    * **EventRaised**: zdarzenie zewnętrzne został wysłany do wystąpienia aranżacji. `Name` Kolumny przechwytuje Nazwa zdarzenia i `Input` kolumny przechwytuje ładunek zdarzenia.
    * **OrchestratorCompleted**: funkcja orchestrator oczekiwane.
    * **ContinueAsNew**: funkcja orchestrator ukończone i ponownego uruchomienia się nowy stan. `Result` Kolumna zawiera wartości, który jest używany jako dane wejściowe w wystąpieniu uruchomić ją ponownie.
    * **ExecutionCompleted**: funkcja orchestrator uruchomienia do ukończenia (lub nie powiodła się). Dane wyjściowe funkcji lub szczegóły błędu są przechowywane w `Result` kolumny.
* **Sygnatura czasowa**: znacznik czasu UTC zdarzenia historii.
* **Nazwa**: Nazwa funkcji, który został wywołany.
* **Wejściowy**: formacie JSON dane wejściowe funkcji.
* **Wynik**: dane wyjściowe funkcji; oznacza to jego wartości zwracanej.

> [!WARNING]
> Mimo że jest użyteczne jako narzędzie debugowania, nie wykonuj żadnych zależności w tej tabeli. Mogą ulec zmianie w miarę rozwoju środowisko rozszerzenie funkcji trwałe.

Za każdym razem, gdy funkcji zostanie wznowione po `await`, trwałe Framework zadanie zwracające funkcja orchestrator od początku. Na każdym Uruchom ponownie sprawdza on historię wykonywania, aby ustalić, czy miała bieżącej operacji asynchronicznej należy umieścić.  Jeśli działanie miało miejsce, platformę natychmiast odtwarzaniem wynik tej operacji, a następnie przechodzi do następnego `await`. Ten proces jest kontynuowany do momentu odtwarzany całą historię, po czym zmiennych lokalnych w funkcji orchestrator zostaną przywrócone poprzednie wartości.

## <a name="orchestrator-code-constraints"></a>Ograniczenia kodu programu orchestrator

Zachowanie powtarzania tworzy ograniczenia dotyczące typu kodu, które mogą być zapisywane w funkcji programu orchestrator:

* Kod orchestrator musi być **deterministyczne**. Zostaną odtworzone wiele razy, a musi mieć takiego samego wyniku zawsze. Na przykład nie bezpośrednich wywołań pobrać bieżącej daty/godziny, Pobierz liczb losowych, generowania losowego identyfikatory GUID lub wywołują zdalne punkty końcowe.

  Jeśli kod orchestrator musi pobrać bieżącej daty/godziny, powinna używać [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) interfejsu API, który jest bezpieczne powtarzania.

  Operacje nie do przewidzenia musi odbywać się w działania funkcji. W tym interakcji z powiązaniami innych danych wejściowych lub wyjściowych. Dzięki temu wartości deterministyczna wygenerowany raz po wykonaniu pierwszej i zapisywane w historii wykonywania. Podczas kolejnych wykonań kodu będzie używać wartości zapisane automatycznie.

* Orchestrator kod powinien być **nieblokujące**. Na przykład, oznacza to nie we/wy i połączeń w celu `Thread.Sleep` lub równoważne interfejsów API.

  Jeśli orchestrator wymaga opóźnienia, można użyć [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) interfejsu API.

* Kod orchestrator musi **nigdy zainicjowana żadnej operacji asynchronicznej** z wyjątkiem przy użyciu [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) interfejsu API. Na przykład nie `Task.Run`, `Task.Delay` lub `HttpClient.SendAsync`. Trwałe Framework zadania wykonuje kod orchestrator w jednym wątku i nie mogą oddziaływać na inne wątki, które można zaplanować inne async interfejsów API.

* **Nieskończone pętle należy unikać** w kodzie programu orchestrator. Ponieważ trwałe Framework zadań zapisuje historii wykonywania w miarę postępów funkcji aranżacji, Pętla nieskończona może spowodować wystąpienie orchestrator mało pamięci. W przypadku scenariuszy nieskończoną pętlę przy użyciu interfejsów API takich jak [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) Aby ponownie uruchomić wykonywania funkcji i odrzucić poprzedniej historii wykonywania.

Gdy może wydawać czasochłonnym zadaniem w najpierw, w praktyce, które nie są trudne do postępuj zgodnie z tych warunków ograniczających. Trwałe Framework zadań podejmuje próbę wykrycia naruszenia powyższe zasady i zgłasza `NonDeterministicOrchestrationException`. Jednak to zachowanie wykrywania jest optymalny i nie zależą od niej.

> [!NOTE]
> Wszystkie te reguły dotyczą tylko funkcje wyzwalane przez `orchestrationTrigger` powiązania. Działanie funkcji wyzwalanych przez `activityTrigger` powiązania i funkcje, które używają `orchestrationClient` powiązanie, mieć nie tych ograniczeń.

## <a name="durable-tasks"></a>Trwałe zadania

> [!NOTE]
> W tej sekcji opisano szczegóły wewnętrznej implementacji platformy trwałe zadań. Możesz użyć funkcji trwałe bez uprzedniego uzyskania informacji o tych informacji. Ma ona tylko ułatwić zrozumienie zachowania powtarzania.

Zadania, które mogą być bezpiecznie oczekiwane w funkcjach programu orchestrator są czasami określane jako *trwałe zadania*. Są to zadania, które są tworzone i zarządzane przez trwałe Framework zadań. Przykłady są zwracane przez `CallActivityAsync`, `WaitForExternalEvent`, i `CreateTimer`.

Te *trwałe zadania* zarządzane wewnętrznie przy użyciu listy `TaskCompletionSource` obiektów. Podczas powtarzania te zadania tworzone w ramach wykonywania kodu programu orchestrator i zostały zakończone, ponieważ dyspozytor wylicza pokrewnych zdarzeń w historii. Wszystkie odbywa się synchronicznie przy użyciu pojedynczego wątku, dopóki cała historia zostały odtworzone. Wszystkie zadania trwałe, które nie zostały zakończone do końca powtarzania historii ma odpowiednie działania przeprowadzone. Na przykład komunikat może być umieszczonych w kolejce do wywołania funkcji działania.

Tutaj opisano sposób wykonywania powinna ułatwić zrozumienie, dlaczego kod funkcji programu orchestrator nie należy nigdy nie `await` zadań nietrwałe: Wątek dyspozytora nie może czekać na jego ukończenie i wszystkie wywołania zwrotnego przez zadanie to może potencjalnie uszkodzić śledzenia stan funkcji programu orchestrator. Niektóre sprawdzenia środowiska uruchomieniowego znajdują się w miejscu możesz wypróbować zapobiec takiej sytuacji.

Jeśli chcesz więcej informacji na temat sposobu trwałe Framework zadania wykonuje funkcje programu orchestrator, najlepiej przeprowadzić jest skontaktować się [trwałe zadań kodu źródłowego w serwisie GitHub](https://github.com/Azure/durabletask). W szczególności, zobacz [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) i [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Więcej informacji na temat zarządzania wystąpienia](durable-functions-instance-management.md)
