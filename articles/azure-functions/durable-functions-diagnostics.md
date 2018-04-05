---
title: Diagnostyka w funkcjach trwałe - Azure
description: Dowiedz się, jak diagnozować problemy z rozszerzeniem trwałe funkcji dla usługi Azure Functions.
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
ms.openlocfilehash: f2fc1c87a0eee9e822ffc997f67320ed23dd5916
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Diagnostyka w funkcji trwałe (funkcje platformy Azure)

Dostępnych jest kilka opcji do diagnozowania problemów z [trwałe funkcji](durable-functions-overview.md). Niektóre z tych opcji są takie same dla funkcji regularnych, a niektóre z nich są unikatowe dla funkcji trwałe.

## <a name="application-insights"></a>Application Insights

[Usługa Application Insights](../application-insights/app-insights-overview.md) jest zalecanym sposobem Diagnostyka i monitorowanie usługi Azure Functions. To samo dotyczy funkcji trwałe. Omówienie sposób korzystania z usługi Application Insights w aplikacji funkcji, zobacz [Monitor usługi Azure Functions](functions-monitoring.md).

Trwałe rozszerzenie funkcji Azure również emituje *śledzenia zdarzeń* pozwalają śledzić realizację end-to-end aranżacji. Te można znaleźć i wyświetlić przy użyciu [Application Insights Analytics](../application-insights/app-insights-analytics.md) narzędzia w portalu Azure.

### <a name="tracking-data"></a>Dane śledzenia

Każdego zdarzenia cyklu życia wystąpienia aranżacji spowoduje wygenerowanie zdarzenia śledzenia do zapisania **śladów** kolekcji w usłudze Application Insights. To zdarzenie zawiera **customDimensions** ładunku z kilku pól.  Nazwy pól są poprzedzony przez `prop__`.

* **hubName**: nazwą Centrum zadania, w którym jest uruchomiony z orchestrations.
* **appName**: Nazwa aplikacji funkcji. Jest to przydatne, jeśli masz wiele aplikacji funkcji Udostępnianie tego samego wystąpienia usługi Application Insights.
* **slotName**: [miejsce wdrożenia](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) w jest uruchomiona funkcja bieżącej aplikacji. Jest to przydatne, gdy wykorzystanie miejsca wdrożenia do wersji programu orchestrations.
* **functionName**: Nazwa funkcji programu orchestrator lub działania.
* **functiontype —**: typ funkcji, takich jak **Orchestrator** lub **działania**.
* **Identyfikator wystąpienia**: Unikatowy identyfikator wystąpienia aranżacji.
* **Stan**: stan wykonywania cyklu życia wystąpienia. Prawidłowe wartości to:
    * **Zaplanowane**: funkcja zostało zaplanowane do uruchomienia, ale nie zostało rozpoczęte jeszcze uruchomiona.
    * **Rozpoczęto**: funkcja uruchomieniu, ale nie został jeszcze oczekiwane lub ukończona.
    * **Oczekiwane**: orchestrator zostało zaplanowane niektórych pracy i czeka na jej zakończenie.
    * **Nasłuchiwanie**: orchestrator nasłuchuje powiadomienie o zdarzeniu zewnętrznych.
    * **Ukończono**: funkcja została ukończona pomyślnie.
    * **Nie powiodło się**: funkcja nie powiodło się z powodu błędu.
* **Przyczyna**: dodatkowe dane skojarzone ze zdarzeniem śledzenia. Na przykład jeśli wystąpienie oczekuje na powiadomienie o zdarzeniu zewnętrznych, to pole wskazuje nazwę zdarzenia, które oczekuje na. Jeśli funkcja nie powiodła się, to będzie zawierać szczegóły błędu.
* **isReplay**: wartość logiczna określająca, czy zdarzenia śledzenia jest odtwarzany wykonywania.
* **extensionVersion**: wersja rozszerzenia trwałe zadań. Jest to szczególnie ważne dane, gdy raportowaniu usterek możliwych do rozszerzenia. Długotrwałe wystąpień może zgłaszać wiele wersji, jeśli nastąpi aktualizacja jest uruchomiona. 
* **sequenceNumber**: numer sekwencji wykonywania dla zdarzenia. Połączone z sygnatury czasowej pozwala kolejność zdarzenia według czasu wykonywania. *Należy pamiętać, że ta liczba będzie resetowania na zero, jeśli host zostanie uruchomiony ponownie po uruchomieniu wystąpienia tak ważne jest, aby zawsze najpierw sortować sygnatury czasowej, a następnie sequenceNumber.*

Można skonfigurować poziom szczegółowości śledzenia danych wysyłanego do usługi Application Insights w `logger` sekcji `host.json` pliku.

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

Domyślnie wszystkie zdarzenia śledzenia są emitowane. Można zmniejszyć ilość danych przez ustawienie `Host.Triggers.DurableTask` do `"Warning"` lub `"Error"` w takim przypadku śledzenia zdarzeń będzie tylko obliczanie w sytuacjach wyjątkowych.

> [!WARNING]
> Domyślnie telemetrii usługi Application Insights jest próbkowany przez środowisko wykonawcze usługi Azure Functions, aby uniknąć zbyt często emitowanie danych. Może to spowodować, że informacje o śledzeniu za utracone w przypadku wystąpienia wielu zdarzeń cyklu życia w krótkim czasie. [Monitorowania funkcji Azure artykułu](functions-monitoring.md#configure-sampling) opisano sposób skonfigurowania tego zachowania.

### <a name="single-instance-query"></a>Zapytanie pojedynczego wystąpienia

Następujące zapytanie wyświetla dane historyczne śledzenia dla pojedynczego wystąpienia [Hello sekwencji](durable-functions-sequence.md) funkcji aranżacji. Zostaną zapisane przy użyciu [Application Insights zapytania języka (AIQL)](https://docs.loganalytics.io/docs/Language-Reference). Odfiltrowuje go, aby tylko wykonywania powtarzania *logicznej* jest wyświetlana ścieżka wykonywania. Zdarzenia może zostać określona przez sortowanie według `timestamp` i `sequenceNumber` jak pokazano w poniższym zapytaniu: 

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"]) 
| where isReplay == false
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Wynik jest Lista śledzenia zdarzeń, pokazujący ścieżka wykonywania orchestration, w tym wszystkie funkcje działania uporządkowanych według czasu wykonywania w kolejności rosnącej.

![Application Insights zapytania](media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)


### <a name="instance-summary-query"></a>Podsumowanie zapytania wystąpienia

Następujące zapytanie Wyświetla stan wszystkich wystąpień aranżacji, które zostały uruchomione w określonym okresie.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay == false
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```
Wynik jest lista wystąpienia identyfikatorów oraz ich bieżący stan środowiska wykonawczego.

![Application Insights zapytania](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Rejestrowanie

Należy pamiętać o orchestrator powtarzania zachowanie podczas zapisywania dzienników bezpośrednio z funkcji programu orchestrator. Rozważmy na przykład następująca funkcja programu orchestrator:

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```

Wynikowe dane dziennika będzie wyglądać jak poniżej:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Należy pamiętać, że podczas dzienniki oświadczeń wzywania F1, F2 i F3, te funkcje tylko *faktycznie* wywołuje się, zostaną one napotkane po raz pierwszy. Kolejne wywołania, które mają miejsce podczas powtórzeń są pomijane, a dane wyjściowe są odtwarzane logiki orchestrator.

Jeśli chcesz zalogować się wyłącznie na wykonanie powtarzania nie może zapisywać wyrażenie warunkowe do dziennika tylko wtedy, gdy `IsReplaying` jest `false`. Należy wziąć pod uwagę w powyższym przykładzie, ale tym razem z kontroli powtarzania.

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    if (!ctx.IsReplaying) log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    if (!ctx.IsReplaying) log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    if (!ctx.IsReplaying) log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```
Dzięki tej zmianie wpisu w dzienniku jest następujący:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="debugging"></a>Debugowanie

Usługi Azure Functions obsługuje debugowania kodu funkcji bezpośrednio i obsługę tego samego przenosi do przodu do funkcji trwałe, czy działają na platformie Azure lub lokalnie. Istnieje jednak kilka zachowań pod uwagę podczas debugowania:

* **Powtarzania**: funkcje programu Orchestrator regularnie powtarzania po odebraniu nowego danych wejściowych. Oznacza to, że jeden *logicznej* wykonywanie funkcji programu orchestrator może spowodować aktywując punkt przerwania w tym samym wiele razy, zwłaszcza, jeśli jest ustawiona na początku kodu funkcji.
* **Await**: przy każdym `await` jest napotkano daje formantu do dyspozytora trwałe Framework zadań. Jeśli po raz pierwszy w szczególności `await` został Napotkano zadanie skojarzone jest *nigdy nie* wznowione. Ponieważ zadania nigdy nie powraca, wykonywanie krok po kroku *za pośrednictwem* await (F10 w programie Visual Studio) nie jest w rzeczywistości możliwe. Pominięcie działa tylko, gdy zadanie jest jest odtwarzany.
* **Limity czasu wiadomości**: funkcje trwałe wewnętrznie używa wiadomości w kolejce na dysku wykonanie działania funkcji i funkcji programu orchestrator. W środowisku wielu maszyn wirtualnych dzielenie na debugowanie przez dłuższy czas może spowodować inną maszynę Wirtualną do pobrania wiadomości, co powoduje wykonanie zduplikowane. To zachowanie istnieje także regularne wyzwalacza kolejki funkcji, ale ważne jest, aby wskazywanie w tym kontekście, ponieważ kolejki są szczegóły implementacji.

> [!TIP]
> Ustawianie punktów przerwania, jeśli chcesz podzielić tylko na wykonanie bez powtarzania, można ustawić warunkowe punktu przerwania tego podziału tylko wtedy, gdy `IsReplaying` jest `false`.

## <a name="storage"></a>Magazyn

Domyślnie funkcje trwałe zapisuje stan w usłudze Azure Storage. Oznacza to, że możesz sprawdzić stan użytkownika orchestrations za pomocą narzędzi takich jak [Eksploratora usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Zrzut ekranu Eksploratora usługi Storage platformy Azure](media/durable-functions-diagnostics/storage-explorer.png)

Jest to przydatne w przypadku debugowania, ponieważ widzisz dokładnie jakim stanie aranżacji może być. Aby dowiedzieć się, jakie roboczych oczekujących również można zbadać komunikatów w kolejkach (lub zablokowany w niektórych przypadkach).

> [!WARNING]
> Jest wygodną wyświetlić historię wykonywania w magazynie tabel, należy unikać wykonywania żadnych zależności w tej tabeli. Mogą ulec zmianie w miarę rozwoju środowisko rozszerzenie funkcji trwałe.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać czasomierze trwałe](durable-functions-timers.md)