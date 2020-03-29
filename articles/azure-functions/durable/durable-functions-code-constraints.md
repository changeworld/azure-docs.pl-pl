---
title: Trwałe ograniczenia kodu programu orchestrator — usługi Azure Functions
description: Funkcja aranżacji powtarzania i ograniczenia kodu dla usługi Azure trwałe funkcje.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4ed604302ca187ad4953e865d68dc73030a37c02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562143"
---
# <a name="orchestrator-function-code-constraints"></a>Ograniczenia kodu funkcji programu Orchestrator

Funkcje trwałe jest rozszerzeniem [usługi Azure Functions,](../functions-overview.md) który umożliwia tworzenie aplikacji stanowych. Za pomocą [funkcji orchestrator](durable-functions-orchestrations.md) do organizowania wykonywania innych trwałych funkcji w aplikacji funkcji. Funkcje orchestrator są stanowe, niezawodne i potencjalnie długotrwałe.

## <a name="orchestrator-code-constraints"></a>Ograniczenia kodu orkiestratora

Funkcje orchestrator używać [pozyskiwania zdarzeń,](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) aby zapewnić niezawodne wykonanie i utrzymać stan zmiennej lokalnej. [Zachowanie powtarzania](durable-functions-orchestrations.md#reliability) kodu orchestrator tworzy ograniczenia na typ kodu, który można zapisać w funkcji koordynatora. Na przykład funkcje orkiestratora muszą być *deterministyczne:* funkcja koordynatora będzie odtwarzana wiele razy i musi produkować ten sam wynik za każdym razem.

### <a name="using-deterministic-apis"></a>Korzystanie z deterministycznych interfejsów API

Ta sekcja zawiera kilka prostych wskazówek, które pomagają upewnić się, że kod jest deterministyczny.

Funkcje orchestrator można wywołać dowolny interfejs API w ich językach docelowych. Jednak ważne jest, aby funkcje programu orchestrator wywoływać tylko deterministyczne interfejsy API. *Deterministyczny interfejs API* jest interfejsem API, który zawsze zwraca tę samą wartość, biorąc pod uwagę te same dane wejściowe, bez względu na to, kiedy i jak często jest wywoływana.

W poniższej tabeli przedstawiono przykłady interfejsów API, których należy unikać, ponieważ *nie* są one deterministyczne. Ograniczenia te dotyczą tylko funkcji koordynatora. Inne typy funkcji nie mają takich ograniczeń.

| Kategoria interfejsu API | Przyczyna | Obejście |
| ------------ | ------ | ---------- |
| Daty i godziny  | Interfejsy API, które zwracają bieżącą datę lub godzinę są niedeterministyczne, ponieważ zwracana wartość jest inna dla każdej powtórki. | Użyj`CurrentUtcDateTime` interfejsu API w `currentUtcDateTime` .NET lub interfejsu API w języku JavaScript, które są bezpieczne dla powtórek. |
| Identyfikatory GUID i identyfikatory UUID  | Interfejsy API, które zwracają losowy identyfikator GUID lub UUID są niedeterministyczne, ponieważ wygenerowana wartość jest inna dla każdej powtórki. | Użyj `NewGuid` w .NET lub `newGuid` JavaScript do bezpiecznego generowania losowych identyfikatorów GUID. |
| Liczby losowe | Interfejsy API zwracające liczby losowe są niedeterministyczne, ponieważ wygenerowana wartość jest inna dla każdej powtórki. | Funkcja działania służy do zwracania liczb losowych do aranżacji. Zwracane wartości funkcji działania są zawsze bezpieczne dla powtórki. |
| Powiązania | Powiązania wejściowe i wyjściowe zazwyczaj wykonują we/wy i są niedeterministyczne. Funkcja koordynatora nie może bezpośrednio używać nawet [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client) i [klienta jednostki.](durable-functions-bindings.md#entity-client) | Użyj powiązania danych wejściowych i wyjściowych wewnątrz funkcji klienta lub działania. |
| Network (Sieć) | Wywołania sieciowe obejmują systemy zewnętrzne i są niedeterministyczne. | Użyj funkcji aktywności do nawiązywać połączenia sieciowe. Jeśli chcesz wykonać wywołanie HTTP z funkcji koordynatora, możesz również użyć [trwałych interfejsów API HTTP](durable-functions-http-features.md#consuming-http-apis). |
| Blokowanie interfejsów API | Blokowanie interfejsów `Thread.Sleep` API, takich jak w .NET i podobnych interfejsów API może powodować problemy z wydajnością i skalowaniem dla funkcji programu orchestrator i należy ich unikać. W planie zużycia funkcji platformy Azure mogą nawet powodować niepotrzebne opłaty za środowisko uruchomieniowe. | Użyj alternatyw do blokowania interfejsów API, gdy są one dostępne. Na przykład `CreateTimer` użyj do wprowadzenia opóźnień w wykonywaniu aranżacji. Trwałe opóźnienia [czasomierza](durable-functions-timers.md) nie są wliczane do czasu wykonania funkcji koordynatora. |
| Interfejsy API asynchronii | Kod koordynatora nigdy nie można uruchomić żadnej `IDurableOrchestrationContext` operacji asynchronicznej, z wyjątkiem przy użyciu interfejsu API lub `context.df` interfejsu API obiektu. Na przykład nie można `Task.Run`użyć `Task.Delay`, `HttpClient.SendAsync` , i `setTimeout` `setInterval` w .NET lub i javascript. Struktura zadań trwałe uruchamia kod koordynatora w jednym wątku. Nie może wchodzić w interakcje z innymi wątkami, które mogą być wywoływane przez inne interfejsy API asynchronii. | Funkcja koordynatora powinna wykonywać tylko trwałe wywołania asynchronicznej. Funkcje działania powinny wykonywać wszelkie inne wywołania asynchronii interfejsu API. |
| Funkcje Asynchronii JavaScript | Nie można zadeklarować funkcji orkiestratora JavaScript, ponieważ `async` środowisko uruchomieniowe node.js nie gwarantuje, że funkcje asynchroniczne są deterministyczne. | Zadeklaruj funkcje orkiestratora JavaScript jako funkcje generatora synchronicznego. |
| Interfejsy API wątków | Struktura zadań trwałe uruchamia kod koordynatora w jednym wątku i nie można wchodzić w interakcje z innymi wątkami. Wprowadzenie nowych wątków do wykonania aranżacji może spowodować wykonanie niedeterministyczne lub zakleszczenia. | Funkcje orchestrator prawie nigdy nie należy używać interfejsów API wątków. Na przykład w .NET `ConfigureAwait(continueOnCapturedContext: false)`należy unikać używania ; gwarantuje to kontynuację zadań uruchamianych na oryginalnej `SynchronizationContext`funkcji koordynatora. Jeśli takie interfejsy API są konieczne, należy ograniczyć ich użycie tylko funkcje działania. |
| Zmienne statyczne | Należy unikać używania niestałych zmiennych statycznych w funkcjach aranżacji, ponieważ ich wartości mogą się zmieniać w czasie, co powoduje niedeterministyczne zachowanie środowiska uruchomieniowego. | Użyj stałych lub ogranicz użycie zmiennych statycznych do funkcji działania. |
| Zmienne środowiskowe | Nie używaj zmiennych środowiskowych w funkcjach aranżacji. Ich wartości mogą się zmieniać w czasie, co powoduje niedeterministyczne zachowanie środowiska uruchomieniowego. | Zmienne środowiskowe muszą być odwoływane tylko z funkcji klienta lub funkcji działania. |
| Nieskończone pętle | Unikaj nieskończonych pętli w funkcjach orkiestratora. Ponieważ trwała struktura zadań zapisuje historię wykonywania w miarę postępu funkcji aranżacji, nieskończona pętla może spowodować, że wystąpienie koordynatora zabraknie pamięci. | W przypadku scenariuszy nieskończonej pętli `ContinueAsNew` użyj interfejsów API, takich jak w .NET lub `continueAsNew` JavaScript, aby ponownie uruchomić wykonanie funkcji i odrzucić poprzednią historię wykonywania. |

Chociaż stosowanie tych ograniczeń może wydawać się trudne na początku, w praktyce są łatwe do naśladowania.

Struktura zadań trwałe próbuje wykryć naruszenia poprzednich reguł. Jeśli znajdzie naruszenie, struktura zgłasza **NonDeterministicOrchestrationException** wyjątek. Jednak to zachowanie wykrywania nie złapie wszystkich naruszeń i nie należy od niego zależeć.

## <a name="versioning"></a>Przechowywanie wersji

Trwała aranżacja może działać nieprzerwanie przez dni, miesiące, lata, a nawet [wiecznie.](durable-functions-eternal-orchestrations.md) Wszelkie aktualizacje kodu wprowadzone do aplikacji funkcji trwałych, które wpływają na niedokończone aranżacji może złamać zachowanie powtarzania aranżacji. Dlatego ważne jest, aby dokładnie zaplanować podczas dokonywania aktualizacji kodu. Aby uzyskać bardziej szczegółowy opis sposobu wersji kodu, zobacz [artykuł o przechowywanie wersji](durable-functions-versioning.md).

## <a name="durable-tasks"></a>Trwałe zadania

> [!NOTE]
> W tej sekcji opisano szczegóły implementacji wewnętrznej trwałej struktury zadań. Można użyć funkcji trwałych bez znajomości tych informacji. Jest przeznaczony tylko do pomocy w zrozumieniu zachowania powtórki.

Zadania, które mogą bezpiecznie czekać w funkcjach orkiestratora, są czasami określane jako *trwałe zadania.* Struktura zadań trwałe tworzy i zarządza tymi zadaniami. Przykładami są zadania zwracane przez **CallActivityAsync**, **WaitForExternalEvent**i **CreateTimer** w funkcjach orkiestry .NET.

Te trwałe zadania są wewnętrznie zarządzane `TaskCompletionSource` przez listę obiektów w .NET. Podczas odtwarzania te zadania są tworzone jako część wykonywania kodu orchestrator. Są one zakończone, jak dyspozytor wylicza odpowiednie zdarzenia historii.

Zadania są wykonywane synchronicznie przy użyciu pojedynczego wątku, dopóki cała historia nie zostanie powtórzona. Trwałe zadania, które nie są zakończone do końca powtórki historii mają odpowiednie działania wykonywane. Na przykład wiadomość może być w kolejce do wywołania funkcji działania.

W tej sekcji opis zachowania środowiska uruchomieniowego powinien pomóc zrozumieć, `await` `yield` dlaczego funkcja koordynatora nie można użyć lub w zadaniu nietrwale. Istnieją dwa powody: wątek dyspozytora nie może czekać na zakończenie zadania, a każde wywołanie zwrotne przez to zadanie może potencjalnie uszkodzić stan śledzenia funkcji koordynatora. Niektóre kontrole środowiska uruchomieniowego są na miejscu, aby pomóc wykryć te naruszenia.

Aby dowiedzieć się więcej o tym, jak trwała struktura zadań wykonuje funkcje programu orchestrator, zapoznaj się z [kodem źródłowym trwałe zadanie w usłudze GitHub](https://github.com/Azure/durabletask). Zobacz [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) i [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak wywoływać podaranżacji](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [Dowiedz się, jak radzić sobie z przechowywaniem wersji](durable-functions-versioning.md)
