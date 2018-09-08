---
title: Diagnostyka funkcje trwałe - Azure
description: Dowiedz się, jak diagnozować problemy z rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: e1211241ec3a2b32647260d1a5c7dc561019cfdf
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092245"
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Diagnostyka funkcje trwałe (usługa Azure Functions)

Dostępnych jest kilka opcji dotyczących diagnozowania problemów z [funkcje trwałe](durable-functions-overview.md). Niektóre opcje są takie same w przypadku funkcji regularnych, a niektóre z nich są unikatowe dla funkcje trwałe.

## <a name="application-insights"></a>Application Insights

[Usługa Application Insights](../application-insights/app-insights-overview.md) jest zalecanym sposobem czy Diagnostyka i monitorowanie w usłudze Azure Functions. Funkcje trwałe to samo dotyczy. Aby uzyskać omówienie jak korzystać z usługi Application Insights w aplikacji funkcji, zobacz [monitora usługi Azure Functions](functions-monitoring.md).

Trwałe rozszerzenie funkcji platformy Azure również emituje *zdarzenia śledzenia* umożliwiającą Śledź wykonywanie end-to-end aranżacji. Te można znaleźć i wyświetlić przy użyciu [analizy usługi Application Insights](../application-insights/app-insights-analytics.md) narzędzie w witrynie Azure portal.

### <a name="tracking-data"></a>Dane śledzenia

Każde zdarzenie cyklu życia wystąpienia aranżacji wygenerowanie zdarzenia śledzenia są zapisywane w **ślady** kolekcji w usłudze Application Insights. To zdarzenie zawiera **tabeli customDimensions** ładunek z kilku pól.  Nazwy pól są poprzedzonej ciągiem `prop__`.

* **hubName**: Nazwa Centrum zadania, w którym są uruchomione usługi aranżacji.
* **appName**: Nazwa aplikacji funkcji. Jest to przydatne, jeśli masz wiele aplikacji funkcyjnych, udostępnianie tego samego wystąpienia usługi Application Insights.
* **slotName**: [miejsce wdrożenia](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) w bieżącej aplikacji funkcji, który działa. Jest to przydatne, gdy korzystanie z mechanizmów miejsc wdrożenia do wersji.
* **functionName**: Nazwa funkcji programu orchestrator lub działania.
* **functiontype —**: typ funkcji, takich jak **Orchestrator** lub **działania**.
* **Identyfikator instanceId**: Unikatowy identyfikator wystąpienia aranżacji.
* **Stan**: stan wykonywania cyklu życia wystąpienia. Prawidłowe wartości to:
    * **Zaplanowane**: funkcja została zaplanowana do wykonania, ale nie zostało rozpoczęte jeszcze uruchomiona.
    * **Pracę**: funkcja rozpoczęło działanie, ale nie został jeszcze oczekiwane lub ukończone.
    * **Oczekiwane**: koordynatora zaplanowano jakąś pracę i czeka na jego zakończenie.
    * **Nasłuchiwanie**: koordynatora nasłuchuje powiadomień o zdarzeniach zewnętrznych.
    * **Ukończono**: funkcja została ukończona pomyślnie.
    * **Nie powiodło się**: funkcja nie powiodło się z powodu błędu.
* **Przyczyna**: dodatkowe dane skojarzone ze zdarzeniem śledzenia. Na przykład jeśli wystąpienie jest oczekiwanie na powiadomienie o zdarzeniu zewnętrznych, to pole wskazuje nazwę zdarzenia, które oczekuje na. Jeśli funkcja zakończyło się niepowodzeniem, to będzie zawierać szczegóły błędu.
* **isReplay**: wartość logiczną wskazującą, czy zdarzenie śledzenia jest odtwarzany wykonywania.
* **extensionVersion**: wersja rozszerzenia zadań trwałe. Jest to szczególnie ważne dane, gdy raportowanie możliwych usterek w rozszerzeniu. Długo działających wystąpień mogą zgłaszać wiele wersji, jeśli nastąpi aktualizacja jest uruchomiona. 
* **sequenceNumber**: numer sekwencyjny wykonania dla zdarzenia. W połączeniu z sygnatury czasowej ułatwia kolejności zdarzeń według czasu wykonywania. *Należy pamiętać, że ta liczba będzie Resetowanie na zero, jeśli host powoduje ponowne uruchomienie wystąpienia jest uruchomiona, dlatego ważne jest, aby zawsze najpierw sortowanie według sygnatur czasowych, a następnie sequenceNumber.*

Poziom szczegółowości śledzenia emitowane do usługi Application Insights można skonfigurować w `logger` części `host.json` pliku.

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

Domyślnie wszystkie inne niż powtarzania śledzenia zdarzeń jest emitowanych. Ilość danych można zmniejszyć, ustawiając `Host.Triggers.DurableTask` do `"Warning"` lub `"Error"` w którym to przypadku śledzenia zdarzeń będzie tylko obliczanie w sytuacjach wyjątkowych.

Umożliwia emitowanie zdarzeń powtarzania pełne aranżacji, `LogReplayEvents` można ustawić `true` w `host.json` plik `durableTask` jak pokazano:

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

> [!NOTE]
> Domyślnie telemetria usługi Application Insights są próbkowane tak, przez środowisko uruchomieniowe usługi Azure Functions, aby uniknąć emitowanie dane zbyt często. Może to spowodować, że informacje ze śledzenia zostanie utracone w przypadku wystąpienia wielu zdarzeń cyklu życia w krótkim czasie. [Monitorowania usługi Azure Functions artykułu](functions-monitoring.md#configure-sampling) wyjaśnia, jak w celu skonfigurowania tego zachowania.

### <a name="single-instance-query"></a>Pojedyncze wystąpienie zapytania

Następujące zapytanie wyświetla dane historyczne śledzenia dla pojedynczego wystąpienia [Hello sekwencji](durable-functions-sequence.md) funkcji aranżacji. Jest ona zapisywana przy użyciu [Application Insights zapytanie języka (AIQL)](https://docs.loganalytics.io/docs/Language-Reference). Filtruje się powtarzania wykonywania, aby tylko *logiczne* jest wyświetlana ścieżka wykonywania. Zdarzenia może zostać określona przez sortowanie według `timestamp` i `sequenceNumber` jak pokazano w poniższym zapytaniu: 

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
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Wynik jest Lista śledzenia zdarzeń, która wyświetla ścieżki wykonywania aranżacji, w tym wszystkie funkcje działania uporządkowane według czasu wykonywania w kolejności rosnącej.

![Zapytanie usługi Application Insights](media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)


### <a name="instance-summary-query"></a>Podsumowanie zapytania wystąpienia

Następujące zapytanie Wyświetla stan wszystkich wystąpień aranżacji, które zostały uruchomione w określonym zakresie czasu.

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
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```
Wynikiem jest lista identyfikatorów wystąpień oraz ich bieżący stan środowiska uruchomieniowego.

![Zapytanie usługi Application Insights](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Rejestrowanie

Należy pamiętać o koordynatora powtarzania zachowanie podczas zapisywania dzienników bezpośrednio z poziomu funkcji orkiestratora. Na przykład rozważmy następującą funkcję programu orchestrator:

#### <a name="c"></a>C#

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivityAsync("F1");
    context.log("Calling F2.");
    yield context.df.callActivityAsync("F2");
    context.log("Calling F3.");
    yield context.df.callActivityAsync("F3");
    context.log("Done!");
});
```

Wynikowe dane dziennika będzie wyglądać następująco:

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
> Należy pamiętać, że gdy dzienniki oświadczenie do wywoływania, F1, F2 i F3, te funkcje są tylko *faktycznie* o nazwie ich napotkaniu po raz pierwszy. Kolejne wywołania, które odbywa się podczas ponownego odtwarzania zostaną pominięte, a dane wyjściowe są odtwarzane logiki programu orchestrator.

Jeśli chcesz zalogować się wyłącznie na wykonanie bez powtarzania wyrażenie warunkowe można zapisywać do dziennika tylko wtedy, gdy `IsReplaying` jest `false`. Należy wziąć pod uwagę w powyższym przykładzie, ale tym razem z kontroli powtarzania.

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
Dzięki tej zmianie dane wyjściowe dziennika jest następująca:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> `IsReplaying` Właściwość nie jest jeszcze dostępna w języku JavaScript.

## <a name="custom-status"></a>Stan niestandardowego

Stan niestandardowej aranżacji pozwala ustawić wartość stanu niestandardowych dla funkcji programu orchestrator. Ten stan jest oferowana w ramach kwerendy stanu HTTP interfejsu API lub `DurableOrchestrationClient.GetStatusAsync` interfejsu API. Stan niestandardowej aranżacji umożliwia bogatszych monitorowania dla funkcji programu orchestrator. Na przykład, kod funkcji programu orchestrator mogą zawierać `DurableOrchestrationContext.SetCustomStatus` wywołania do zaktualizowania postępu dla operacji długotrwałej. Klienta, takich jak strony sieci web lub innych systemu zewnętrznego, następnie można będzie okresowo uruchamiać kwerendę kwerendy stanu HTTP interfejsów API bogatsze informacje o postępie. Na przykład za pośrednictwem `DurableOrchestrationContext.SetCustomStatus` znajduje się poniżej:

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    ctx.SetCustomStatus(customStatus);

    // ...do more work...
}
```

Po uruchomieniu aranżacji klientów zewnętrznych można pobrać ten stan niestandardowy:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Klienci otrzymają następującą odpowiedź: 

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
>  Stan niestandardowy ładunek jest ograniczony do 16 KB tekstu JSON UTF-16, ponieważ wymagane jest, aby mieściły się w kolumnie Azure Table Storage. Można użyć magazynu zewnętrznego, jeśli potrzebujesz większej ładunku.

## <a name="debugging"></a>Debugowanie

Usługi Azure Functions obsługuje debugowanie kodu funkcji bezpośrednio i które obsługują ten sam niesie ze sobą do przodu do funkcje trwałe, czy działających na platformie Azure lub lokalnie. Istnieje jednak kilka zachowań pod uwagę podczas debugowania:

* **Oparte na metodzie powtórzeń**: funkcje programu Orchestrator regularnie oparte na metodzie powtórzeń po odebraniu nowych danych wejściowych. Oznacza to, że jeden *logiczne* wykonania funkcji orkiestratora może spowodować osiągnięcia tego samego punktu przerwania wielokrotnie, zwłaszcza, jeśli jest ustawiona na wczesnym etapie kodu funkcji.
* **Await**: zawsze, gdy `await` jest napotkane, przekazuje sterowanie do dyspozytora trwałe Framework zadania. Jeśli po raz pierwszy, w szczególności `await` został napotkany, jest skojarzone zadanie *nigdy nie* wznowione. Ponieważ nigdy nie powraca do zadania, wykonywanie krokowe *za pośrednictwem* await (F10 w programie Visual Studio) nie jest faktycznie możliwe. Pominięcie działa tylko, gdy zadanie jest jest odtwarzany.
* **Komunikaty przekroczeń limitu czasu**: funkcje trwałe wewnętrznie używa komunikatów w kolejce do wykonania dysku działania funkcji i funkcji programu orchestrator. W środowisku z wieloma Maszynami wirtualnymi podzielenie na debugowanie przez dłuższy czas, może spowodować innej maszyny Wirtualnej wczytać komunikat, co powoduje wykonanie zduplikowane. To zachowanie istnieje również funkcji regularnych wyzwalacz kolejki, ale ważne jest, aby wspomnieć w tym kontekście, ponieważ kolejki są szczegółowo opisuje implementacja.

> [!TIP]
> Podczas ustawiania punktów przerwania, jeśli chcesz tylko przerywał działanie w przypadku wykonywania bez powtarzania, można ustawić warunkowego punktu przerwania tego podziału tylko wtedy, gdy `IsReplaying` jest `false`.

## <a name="storage"></a>Magazyn

Domyślnie funkcje trwałe przechowuje stan w usłudze Azure Storage. Oznacza to, że można sprawdzić stan usługi aranżacji przy użyciu narzędzi takich jak [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Zrzut ekranu Eksploratora usługi Storage platformy Azure](media/durable-functions-diagnostics/storage-explorer.png)

Jest to przydatne podczas debugowania, ponieważ widać dokładnie w jakim stanie aranżacji może znajdować się w. Aby dowiedzieć się więcej pracy jest w stanie oczekiwania również można zbadać komunikaty w kolejkach (lub zablokowane w niektórych przypadkach).

> [!WARNING]
> Mimo że jest wygodne wyświetlić historię wykonywania w usłudze table storage, należy unikać wykonywania wszelkich zależności w tej tabeli. Go mogą ulec zmianie w miarę rozwoju rozszerzenia funkcji trwałych.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak i używaj czasomierzy trwałe](durable-functions-timers.md)
