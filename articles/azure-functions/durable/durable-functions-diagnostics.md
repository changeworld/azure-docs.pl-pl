---
title: Diagnostyka funkcje trwałe - Azure
description: Dowiedz się, jak diagnozować problemy z rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 167f697d4928d88114a30739a1d39a576c87ac84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126665"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostyka funkcje trwałe na platformie Azure

Dostępnych jest kilka opcji dotyczących diagnozowania problemów z [funkcje trwałe](durable-functions-overview.md). Niektóre opcje są takie same jak w przypadku funkcji regularnych, a niektóre z nich są unikatowe dla usługi Durable Functions.

## <a name="application-insights"></a>Application Insights

[Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) jest zalecanym sposobem czy Diagnostyka i monitorowanie w usłudze Azure Functions. Funkcje trwałe to samo dotyczy. Aby uzyskać omówienie jak korzystać z usługi Application Insights w aplikacji funkcji, zobacz [monitora usługi Azure Functions](../functions-monitoring.md).

Trwałe rozszerzenie funkcji platformy Azure również emituje *zdarzenia śledzenia* umożliwiającą Śledź wykonywanie end-to-end aranżacji. Te można znaleźć i wyświetlić przy użyciu [analizy usługi Application Insights](../../azure-monitor/app/analytics.md) narzędzie w witrynie Azure portal.

### <a name="tracking-data"></a>Dane śledzenia

Każde zdarzenie cyklu życia wystąpienia aranżacji wygenerowanie zdarzenia śledzenia są zapisywane w **ślady** kolekcji w usłudze Application Insights. To zdarzenie zawiera **tabeli customDimensions** ładunek z kilku pól.  Nazwy pól są poprzedzonej ciągiem `prop__`.

* **hubName**: Nazwa koncentratora zadania, w którym są uruchomione usługi aranżacji.
* **Nazwa aplikacji**: Nazwa aplikacji funkcji. Jest to przydatne, jeśli masz wiele aplikacji funkcyjnych, udostępnianie tego samego wystąpienia usługi Application Insights.
* **slotName**: [Miejsce wdrożenia](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) w bieżącej aplikacji funkcji, który działa. Jest to przydatne, gdy korzystanie z mechanizmów miejsc wdrożenia do wersji.
* **functionName**: Nazwa funkcji programu orchestrator lub działania.
* **functiontype —** : Typ funkcji, takich jak **Orchestrator** lub **działania**.
* **instanceId**: Unikatowy identyfikator wystąpienia aranżacji.
* **Stan**: Stan wykonywania cyklu życia wystąpienia. Prawidłowe wartości to:
  * **Zaplanowane**: Funkcja została zaplanowana do wykonania, ale nie zostało rozpoczęte jeszcze uruchomiona.
  * **Pracę**: Funkcja rozpoczęło działanie, ale nie został jeszcze oczekiwane lub ukończone.
  * **Oczekiwane**: Koordynatora zaplanowano jakąś pracę i czeka na jego zakończenie.
  * **Nasłuchiwanie**: Koordynatora nasłuchuje powiadomień o zdarzeniach zewnętrznych.
  * **Ukończono**: Funkcja została pomyślnie ukończona.
  * **Niepowodzenie**: Funkcja nie powiodło się z powodu błędu.
* **Przyczyna**: Dodatkowe dane skojarzone ze zdarzeniem śledzenia. Na przykład jeśli wystąpienie jest oczekiwanie na powiadomienie o zdarzeniu zewnętrznych, to pole wskazuje nazwę zdarzenia, które oczekuje na. Jeśli funkcja zakończyło się niepowodzeniem, to będzie zawierać szczegóły błędu.
* **isReplay**: Wartość logiczna wskazująca, czy zdarzenie śledzenia jest powtórzonym wykonywania.
* **extensionVersion**: Wersja rozszerzenia zadań trwałe. Jest to szczególnie ważne dane, gdy raportowanie możliwych usterek w rozszerzeniu. Długo działających wystąpień mogą zgłaszać wiele wersji, jeśli nastąpi aktualizacja jest uruchomiona.
* **sequenceNumber**: Numer sekwencyjny wykonania dla zdarzenia. W połączeniu z sygnatury czasowej ułatwia kolejności zdarzeń według czasu wykonywania. *Należy pamiętać, że ta liczba będzie Resetowanie na zero, jeśli host powoduje ponowne uruchomienie wystąpienia jest uruchomiona, dlatego ważne jest, aby zawsze najpierw sortowanie według sygnatur czasowych, a następnie sequenceNumber.*

Poziom szczegółowości śledzenia emitowane do usługi Application Insights można skonfigurować w `logger` (funkcje 1.x) lub `logging` (funkcje 2.x) sekcji `host.json` pliku.

#### <a name="functions-1x"></a>Functions w wersji 1.x

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

#### <a name="functions-2x"></a>Functions w wersji 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Domyślnie wszystkie inne niż powtarzania śledzenia zdarzeń jest emitowanych. Ilość danych można zmniejszyć, ustawiając `Host.Triggers.DurableTask` do `"Warning"` lub `"Error"` w którym to przypadku śledzenia zdarzeń będzie tylko obliczanie w sytuacjach wyjątkowych.

Umożliwia emitowanie zdarzeń powtarzania pełne aranżacji, `LogReplayEvents` można ustawić `true` w `host.json` plik `durableTask` jak pokazano:

#### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Functions w wersji 2.x

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Domyślnie telemetria usługi Application Insights są próbkowane tak, przez środowisko uruchomieniowe usługi Azure Functions, aby uniknąć emitowanie dane zbyt często. Może to spowodować, że informacje ze śledzenia zostanie utracone w przypadku wystąpienia wielu zdarzeń cyklu życia w krótkim czasie. [Monitorowania usługi Azure Functions artykułu](../functions-monitoring.md#configure-sampling) wyjaśnia, jak w celu skonfigurowania tego zachowania.

### <a name="single-instance-query"></a>Pojedyncze wystąpienie zapytania

Następujące zapytanie wyświetla dane historyczne śledzenia dla pojedynczego wystąpienia [Hello sekwencji](durable-functions-sequence.md) funkcji aranżacji. Jest ona zapisywana przy użyciu [Application Insights zapytanie języka (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Filtruje się powtarzania wykonywania, aby tylko *logiczne* jest wyświetlana ścieżka wykonywania. Zdarzenia może zostać określona przez sortowanie według `timestamp` i `sequenceNumber` jak pokazano w poniższym zapytaniu:

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

![Zapytanie usługi Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

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

![Zapytanie usługi Application Insights](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Rejestrowanie

Należy pamiętać o koordynatora powtarzania zachowanie podczas zapisywania dzienników bezpośrednio z poziomu funkcji orkiestratora. Na przykład rozważmy następującą funkcję programu orchestrator:

### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
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

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Dzięki tej zmianie dane wyjściowe dziennika jest następująca:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Stan niestandardowego

Stan niestandardowej aranżacji pozwala ustawić wartość stanu niestandardowych dla funkcji programu orchestrator. Ten stan jest oferowana w ramach kwerendy stanu HTTP interfejsu API lub `DurableOrchestrationClient.GetStatusAsync` interfejsu API. Stan niestandardowej aranżacji umożliwia bogatszych monitorowania dla funkcji programu orchestrator. Na przykład, kod funkcji programu orchestrator mogą zawierać `DurableOrchestrationContext.SetCustomStatus` wywołania do zaktualizowania postępu dla operacji długotrwałej. Klienta, takich jak strony sieci web lub innych systemu zewnętrznego, następnie można będzie okresowo uruchamiać kwerendę kwerendy stanu HTTP interfejsów API bogatsze informacje o postępie. Na przykład za pośrednictwem `DurableOrchestrationContext.SetCustomStatus` znajduje się poniżej:

### <a name="c"></a>C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (działa tylko 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
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
> Stan niestandardowy ładunek jest ograniczony do 16 KB tekstu JSON UTF-16, ponieważ wymagane jest, aby mieściły się w kolumnie Azure Table Storage. Można użyć magazynu zewnętrznego, jeśli potrzebujesz większej ładunku.

## <a name="debugging"></a>Debugowanie

Usługi Azure Functions obsługuje debugowanie kodu funkcji bezpośrednio i które obsługują ten sam niesie ze sobą do przodu do funkcje trwałe, czy działających na platformie Azure lub lokalnie. Istnieje jednak kilka zachowań pod uwagę podczas debugowania:

* **Powtarzanie**: Funkcje programu orchestrator regularnie oparte na metodzie powtórzeń po odebraniu nowych danych wejściowych. Oznacza to, że jeden *logiczne* wykonania funkcji orkiestratora może spowodować osiągnięcia tego samego punktu przerwania wielokrotnie, zwłaszcza, jeśli jest ustawiona na wczesnym etapie kodu funkcji.
* **Await**: Zawsze, gdy `await` jest napotkane, przekazuje sterowanie do dyspozytora trwałe Framework zadania. Jeśli po raz pierwszy, w szczególności `await` został napotkany, jest skojarzone zadanie *nigdy nie* wznowione. Ponieważ nigdy nie powraca do zadania, wykonywanie krokowe *za pośrednictwem* await (F10 w programie Visual Studio) nie jest faktycznie możliwe. Pominięcie działa tylko, gdy zadanie jest jest odtwarzany.
* **Komunikaty przekroczeń limitu czasu**: Trwałe funkcje wewnętrznie używa komunikatów w kolejce do wykonania dysku działania funkcji i funkcji programu orchestrator. W środowisku z wieloma Maszynami wirtualnymi podzielenie na debugowanie przez dłuższy czas, może spowodować innej maszyny Wirtualnej wczytać komunikat, co powoduje wykonanie zduplikowane. To zachowanie istnieje również funkcji regularnych wyzwalacz kolejki, ale ważne jest, aby wspomnieć w tym kontekście, ponieważ kolejki są szczegółowo opisuje implementacja.

> [!TIP]
> Podczas ustawiania punktów przerwania, jeśli chcesz tylko przerywał działanie w przypadku wykonywania bez powtarzania, można ustawić warunkowego punktu przerwania tego podziału tylko wtedy, gdy `IsReplaying` jest `false`.

## <a name="storage"></a>Magazyn

Domyślnie funkcje trwałe przechowuje stan w usłudze Azure Storage. Oznacza to, że można sprawdzić stan usługi aranżacji przy użyciu narzędzi takich jak [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Usługa Azure Storage Explorer zrzut ekranu](./media/durable-functions-diagnostics/storage-explorer.png)

Jest to przydatne podczas debugowania, ponieważ widać dokładnie w jakim stanie aranżacji może znajdować się w. Aby dowiedzieć się więcej pracy jest w stanie oczekiwania również można zbadać komunikaty w kolejkach (lub zablokowane w niektórych przypadkach).

> [!WARNING]
> Mimo że jest wygodne wyświetlić historię wykonywania w usłudze table storage, należy unikać wykonywania wszelkich zależności w tej tabeli. Go mogą ulec zmianie w miarę rozwoju rozszerzenia funkcji trwałych.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak i używaj czasomierzy trwałe](durable-functions-timers.md)
