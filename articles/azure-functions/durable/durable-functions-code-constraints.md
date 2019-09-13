---
title: Trwałe ograniczenia kodu programu Orchestrator — Azure Functions
description: Funkcje aranżacji i ograniczenia kodu dla Durable Functions platformy Azure.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: azfuncdf
ms.openlocfilehash: 87851a4879760c76950f765d05de4662ecb04bea
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935770"
---
# <a name="orchestrator-function-code-constraints"></a>Ograniczenia kodu funkcji programu Orchestrator

Durable Functions jest rozszerzeniem [Azure Functions](../functions-overview.md) , które pozwala tworzyć aplikacje stanowe. Za pomocą funkcji programu [Orchestrator](durable-functions-orchestrations.md) można organizować wykonywanie innych trwałych funkcji w ramach aplikacji funkcji. Funkcje programu Orchestrator są stanowe, niezawodne i potencjalnie długotrwałe.

## <a name="orchestrator-code-constraints"></a>Ograniczenia kodu programu Orchestrator

Funkcje programu Orchestrator używają określania [źródła zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) , aby zapewnić niezawodne wykonywanie i zachować stan zmiennej lokalnej. [Zachowanie powtarzania](durable-functions-orchestrations.md#reliability) kodu programu Orchestrator tworzy ograniczenia dotyczące typu kodu, który można napisać w funkcji programu Orchestrator. Na przykład kod programu Orchestrator musi być *deterministyczny*.  Funkcje programu Orchestrator będą odtwarzane wielokrotnie i muszą one generować ten sam wynik za każdym razem.

Poniższe sekcje zawierają pewne proste wskazówki dotyczące zagwarantowania, że kod jest deterministyczny.

### <a name="using-deterministic-apis"></a>Korzystanie z deterministycznych interfejsów API

Funkcje programu Orchestrator mogą wywołać dowolny interfejs API, który chcą w ich języku docelowym. Należy jednak pamiętać, że funkcje programu Orchestrator wywołują tylko *deterministyczne* interfejsy API. *Deterministyczny interfejs API* jest interfejsem API, który zawsze zwraca dokładnie tę samą wartość, niezależnie od tego, kiedy lub jak często jest wywoływana.

Poniżej przedstawiono kilka przykładów interfejsów API, które należy wyeliminować, ponieważ *nie* są one deterministyczne. Te ograniczenia mają zastosowanie tylko do funkcji programu Orchestrator. Inne typy funkcji nie mają takich ograniczeń.

| Kategoria interfejsu API | Reason | Obejście |
| ------------ | ------ | ---------- |
| Daty/godziny  | Interfejsy API, które zwracają _bieżącą_ datę lub godzinę, są niedeterministyczne, ponieważ zwracana wartość będzie różna dla każdego powtórzenia. | Użyj interfejsu API [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) (.NET) `currentUtcDateTime` lub (JavaScript), który jest bezpieczny do odtworzenia. |
| Identyfikatory GUID/UUID  | Interfejsy API, które zwracają _losowy_ identyfikator GUID/UUID, są niejednoznaczne, ponieważ wygenerowana wartość będzie inna dla każdego powtórzenia. | Użyj [NewGuid](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_NewGuid) (.NET) lub `newGuid` (JavaScript), aby bezpiecznie generować losowe identyfikatory GUID. |
| Liczby losowe | Interfejsy API, które zwracają liczby losowe, są niejednoznaczne, ponieważ wygenerowana wartość będzie różna dla każdego powtórzenia. | Użyj funkcji działania, aby zwrócić liczbę losową do aranżacji. Wartości zwracane funkcji działania są zawsze bezpieczne do powtórzenia. |
| Powiązania | Powiązania wejściowe i wyjściowe zwykle wykonują we/wy i nie są deterministyczne. Nawet powiązania klienta [aranżacji](durable-functions-bindings.md#orchestration-client) i [klienta jednostki](durable-functions-bindings.md#entity-client) nie mogą być używane bezpośrednio przez funkcję programu Orchestrator. | Użyj powiązań wejściowych i wyjściowych w ramach funkcji klienta lub działania. |
| Sieć | Wywołania sieciowe obejmują systemy zewnętrzne i nie są niejednoznaczne. | Użyj funkcji działania, aby wykonać wywołania sieciowe. Jeśli musisz wykonać wywołanie HTTP z funkcji programu Orchestrator, masz również możliwość korzystania z [trwałych interfejsów API protokołu HTTP](durable-functions-http-features.md#consuming-http-apis). |
| Blokowanie interfejsów API | Blokowanie interfejsów API, `Thread.Sleep` takich jak (.NET) lub innych podobnych interfejsów API, może spowodować problemy z wydajnością i skalowaniem dla funkcji programu Orchestrator. W planie zużycia Azure Functions mogą nawet spowodować niepotrzebne opłaty w czasie wykonywania. | Użyj alternatyw do blokowania interfejsów API, jeśli są dostępne `CreateTimer` , takich jak wprowadzanie opóźnień w wykonywaniu aranżacji. [Trwałe opóźnienia czasomierza](durable-functions-timers.md) nie są wliczane do czasu wykonywania funkcji programu Orchestrator. |
| Asynchroniczne interfejsy API | Kod programu Orchestrator **nigdy nie może inicjować żadnej operacji asynchronicznej** , [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) chyba że jest `context.df` używany interfejs API DurableOrchestrationContext lub interfejs API obiektu. Na `Task.Run`przykład nie `setInterval()` , `Task.Delay` lub `HttpClient.SendAsync` w programie .NET lub `setTimeout()` w języku JavaScript. Trwała struktura zadań wykonuje kod programu Orchestrator w pojedynczym wątku i nie może współdziałać z innymi wątkami, które mogą być zaplanowane przez inne asynchroniczne interfejsy API. | Funkcja programu Orchestrator powinna wykonywać tylko *trwałe* wywołania asynchroniczne. Wszystkie inne asynchroniczne wywołania interfejsu API powinny być wykonywane z poziomu funkcji działania. |
| Asynchroniczne funkcje języka JavaScript | Funkcje programu Orchestrator w języku `async` JavaScript nie mogą być spowodowane tym, że środowisko uruchomieniowe Node. js nie gwarantuje, że funkcje asynchroniczne są deterministyczne. | Funkcje programu Orchestrator w języku JavaScript muszą być zadeklarowane jako funkcje generatora synchronicznego. |
| Wątkowe interfejsy API | W ramach trwałej struktury zadań jest wykonywany kod programu Orchestrator w pojedynczym wątku i nie może on współdziałać z żadnym innym wątkiem. Wprowadzenie nowych wątków do wykonywania aranżacji może spowodować niedeterministyczne wykonanie lub zakleszczenia. | Interfejsy API wątków należy prawie nigdy nie używać w funkcjach programu Orchestrator. Jeśli jest to konieczne, powinny być ograniczone do funkcji działania. |
| Zmienne statyczne | Niestałe zmienne statyczne należy unikać w funkcjach programu Orchestrator, ponieważ ich wartości mogą ulegać zmianom w czasie, powodując zachowanie niedeterministycznego wykonania. | Używaj stałych lub ograniczaj użycie zmiennych statycznych do funkcji działania. |
| Zmienne środowiskowe | Nie należy używać zmiennych środowiskowych w funkcjach programu Orchestrator. Ich wartości mogą ulec zmianie z upływem czasu, co skutkuje niedeterministycznym zachowaniem wykonania. | Zmienne środowiskowe muszą być przywoływane tylko z poziomu funkcji klienta lub funkcji działania. |
| Nieskończone pętle | W funkcjach programu Orchestrator należy unikać nieskończonych pętli. Funkcja trwałej struktury zadań zapisuje historię wykonywania jako postęp funkcji aranżacji, dlatego nieskończona pętla może spowodować, że w wystąpieniu programu Orchestrator brakuje pamięci. | W przypadku scenariuszy pętli nieskończonej Użyj interfejsów API, takich jak [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) ( `continueAsNew` .NET) lub (JavaScript), aby ponownie uruchomić wykonywanie funkcji i odrzucić poprzednią historię wykonania. |

Chociaż te ograniczenia mogą wydawać się zniechęcające na początku, w rzeczywistości nie są trudne do obserwowania. Nietrwała platforma zadań próbuje wykryć naruszenia powyższych reguł i wygeneruje `NonDeterministicOrchestrationException`. Takie zachowanie jest jednak najlepszym rozwiązaniem i nie powinno być od niego zależne.

## <a name="versioning"></a>Obsługa wersji

Nietrwała aranżacja może działać w sposób ciągły przez dni, miesiące, lata lub nawet [Eternally](durable-functions-eternal-orchestrations.md). Wszelkie aktualizacje kodu wykonane w celu Durable Functions aplikacji, które mają wpływ na nieukończoną pracę aranżacji, mogą spowodować przerwanie ich działania odtwarzania. W związku z tym ważne jest dokładne zaplanowanie aktualizacji kodu. Aby uzyskać bardziej szczegółowy opis sposobu tworzenia wersji kodu, zobacz artykuł dotyczący [przechowywania wersji](durable-functions-versioning.md) .

## <a name="durable-tasks"></a>Zadania trwałe

> [!NOTE]
> W tej sekcji opisano wewnętrzne szczegóły implementacji dla trwałej struktury zadań. Możesz użyć Durable Functions bez znajomości tych informacji. Jest ona przeznaczona tylko do zrozumienia zachowania powtarzania.

Zadania, które można bezpiecznie oczekiwać w funkcjach programu Orchestrator, są czasami określane jako *zadania trwałe*. Te zadania są tworzone i zarządzane przez strukturę zadań trwałych. Przykłady to zadania zwracane przez `CallActivityAsync`, `WaitForExternalEvent`i `CreateTimer` w programie .NET Orchestrator Functions.

Te *zadania trwałe* są zarządzane wewnętrznie przy użyciu listy `TaskCompletionSource` obiektów w programie .NET. Podczas odtwarzania te zadania są tworzone w ramach wykonywania kodu programu Orchestrator i są wykonywane, gdy Dyspozytor wylicza odpowiednie zdarzenia historii. Wykonywanie jest wykonywane synchronicznie przy użyciu jednego wątku, dopóki cała historia nie zostanie powtórzona. Wszelkie zadania trwałe, które nie zostały ukończone na zakończenie odtwarzania historii, mają odpowiednie działania. Na przykład komunikat może być w kolejce do wywołania funkcji działania.

Opisane tutaj zachowanie dotyczące wykonywania powinno ułatwić zrozumienie, dlaczego kod funkcji programu Orchestrator nie `await` może `yield` być nigdy lub nietrwałego zadania: wątek dyspozytora nie może czekać na ukończenie i każde wywołanie zwrotne przez to zadanie może potencjalnie uszkodzony stan śledzenia funkcji programu Orchestrator. Niektóre testy środowiska uruchomieniowego są stosowane w celu wykrycia tych naruszeń.

Jeśli chcesz uzyskać więcej informacji na temat sposobu wykonywania funkcji programu Orchestrator przez strukturę zadań trwałych, najlepszym rozwiązaniem jest zapoznaj się z [kodem źródłowym zadania trwałego w witrynie GitHub](https://github.com/Azure/durabletask). W szczególności zobacz [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) i [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o sposobie wywoływania podaranżacji](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Dowiedz się, jak obsługiwać przechowywanie wersji](durable-functions-versioning.md)
