---
title: Diagnostyka w trwałych funkcjach — Azure
description: Dowiedz się, jak zdiagnozować problemy z rozszerzeniem funkcje trwałe dla usługi Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4cb832f8fe11ac2581e97d9cdcc777eaff702ee9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278196"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostyka w usłudze Durable Functions na platformie Azure

Istnieje kilka opcji diagnozowania problemów z [funkcjami trwałymi.](durable-functions-overview.md) Niektóre opcje są takie same jak w przypadku funkcji regularnych, a niektóre z nich są unikatowe dla usługi Durable Functions.

## <a name="application-insights"></a>Application Insights

[Usługa Application Insights](../../azure-monitor/app/app-insights-overview.md) to zalecany sposób diagnostyki i monitorowania w usłudze Azure Functions. To samo dotyczy funkcji trwałych. Aby zapoznać się z omówieniem sposobu wykorzystania usługi Application Insights w aplikacji funkcji, zobacz [Monitorowanie funkcji platformy Azure](../functions-monitoring.md).

Rozszerzenie trwałe usługi Azure Functions emituje również *zdarzenia śledzenia,* które umożliwiają śledzenie end-to-end wykonanie aranżacji. Te zdarzenia śledzenia można znaleźć i zbadać za pomocą narzędzia [analizy usługi Application Insights](../../azure-monitor/app/analytics.md) w witrynie Azure portal.

### <a name="tracking-data"></a>Dane śledzenia

Każde zdarzenie cyklu życia wystąpienia aranżacji powoduje, że zdarzenie śledzenia mają być zapisywane do **kolekcji śledzenia** w usłudze Application Insights. To zdarzenie zawiera **niestandardowyłasieć ładunku** z kilku pól.  Nazwy pól są poprzedzone `prop__`.

* **hubName:** Nazwa centrum zadań, w którym są uruchomione aranżacji.
* **appName**: Nazwa aplikacji funkcji. To pole jest przydatne, gdy masz wiele aplikacji funkcji współużytkuje to samo wystąpienie usługi Application Insights.
* **slotName:** [Gniazdo wdrażania,](../functions-deployment-slots.md) w którym jest uruchomiona bieżąca aplikacja funkcji. To pole jest przydatne, gdy wykorzystujesz gniazda wdrażania do wersji aranżacji.
* **functionName**: Nazwa funkcji koordynatora lub działania.
* **functionType**: Typ funkcji, taki jak **Koordynator** lub **Działanie**.
* **instanceId**: unikatowy identyfikator wystąpienia aranżacji.
* **stan:** Stan wykonywania cyklu życia wystąpienia. Prawidłowe wartości to:
  * **Zaplanowane:** Funkcja została zaplanowana do wykonania, ale nie została jeszcze uruchomiona.
  * **Rozpoczęto:** Funkcja została uruchomiona, ale nie została jeszcze oczekiwana lub ukończona.
  * **Oczekiwano:** Orkiestrator zaplanował trochę pracy i czeka na jej zakończenie.
  * **Słuchanie:** Koordynator nasłuchuje powiadomienia o wydarzeniu zewnętrznym.
  * **Ukończono**: Funkcja została ukończona pomyślnie.
  * **Nie powiodło się:** Funkcja nie powiodła się z powodu błędu.
* **przyczyna:** Dodatkowe dane skojarzone ze zdarzeniem śledzenia. Na przykład jeśli wystąpienie oczekuje na powiadomienie o zdarzeniu zewnętrznym, to pole wskazuje nazwę zdarzenia, na które oczekuje. Jeśli funkcja nie powiodła się, to pole będzie zawierać szczegóły błędu.
* **isReplay**: Wartość logiczna wskazująca, czy zdarzenie śledzenia jest dla powtarzane wykonanie.
* **extensionVersion**: Wersja rozszerzenia trwałe zadanie. Informacje o wersji są szczególnie ważne podczas zgłaszania możliwych błędów w rozszerzeniu. Długotrwałe wystąpienia mogą zgłaszać wiele wersji, jeśli aktualizacja występuje podczas jej uruchamiania.
* **sequenceNumber**: Numer sekwencyjny wykonania zdarzenia. W połączeniu z sygnaturą czasowa pomaga uporządkować zdarzenia według czasu wykonania. *Należy zauważyć, że ta liczba zostanie zresetowana do zera, jeśli host zostanie ponownie uruchomiony, gdy wystąpienie jest uruchomione, dlatego ważne jest, aby zawsze sortować według sygnatury czasowej, a następnie sequenceNumber.*

Szczegółowość danych śledzenia emitowanych do usługi Application Insights można `logger` skonfigurować w sekcji `logging` (Funkcje 1.x) lub `host.json` (Funkcje 2.0) pliku.

#### <a name="functions-10"></a>Funkcje 1.0

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

#### <a name="functions-20"></a>Funkcje 2.0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Domyślnie emitowane są wszystkie zdarzenia śledzenia bez powtarzania. Ilość danych można zmniejszyć, `Host.Triggers.DurableTask` `"Warning"` ustawiając `"Error"` lub w którym to przypadku zdarzenia śledzenia będą emitowane tylko w wyjątkowych sytuacjach.

Aby włączyć emitowanie pełnych zdarzeń powtórki `LogReplayEvents` aranżacji, `host.json` można `durableTask` ustawić `true` w pliku w obszarze, jak pokazano:

#### <a name="functions-10"></a>Funkcje 1.0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Funkcje 2.0

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
> Domyślnie dane telemetryczne usługi Application Insights są próbkowania przez środowisko uruchomieniowe usługi Azure Functions, aby uniknąć emitowania danych zbyt często. Może to spowodować utratę informacji śledzenia, gdy wiele zdarzeń cyklu życia występuje w krótkim okresie czasu. W [artykule Monitorowanie funkcji platformy Azure](../functions-monitoring.md#configure-sampling) wyjaśniono, jak skonfigurować to zachowanie.

### <a name="single-instance-query"></a>Zapytanie pojedynczego wystąpienia

Poniższa kwerenda przedstawia historyczne dane śledzenia dla pojedynczego wystąpienia aranżacji funkcji [Hello Sequence.](durable-functions-sequence.md) Jest napisany przy użyciu [języka zapytań aplikacji Insights (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Odfiltruje wykonanie powtarzania, tak aby *wyświetlana* jest tylko ścieżka logicznego wykonywania. Zdarzenia można zamówić, sortując według `timestamp` i `sequenceNumber` jak pokazano w poniższej kwerendzie:

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

Wynikiem jest lista zdarzeń śledzenia, który pokazuje ścieżkę wykonywania aranżacji, w tym wszystkie funkcje działania uporządkowane przez czas wykonywania w kolejności rosnącej.

![Zapytanie usługi Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Kwerenda zbiorcza wystąpienia

W poniższej kwerendzie jest wyświetlany stan wszystkich wystąpień aranżacji, które zostały uruchomione w określonym zakresie czasu.

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

Wynikiem jest lista identyfikatorów wystąpień i ich bieżącego stanu środowiska uruchomieniowego.

![Zapytanie usługi Application Insights](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Rejestrowanie

Ważne jest, aby zachować zachowanie powtórki orkiestry na uwadze podczas pisania dzienników bezpośrednio z funkcji orkiestratora. Rozważmy na przykład następującą funkcję koordynatora:

### <a name="precompiled-c"></a>Wstępnie skompilowany C #

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
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

### <a name="c-script"></a>Skrypt języka C#

```csharp
public static async Task Run(
    IDurableOrchestrationContext context,
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

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2.0)

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

Wynikowe dane dziennika będzie wyglądać mniej więcej jak następujące dane wyjściowe przykładu:

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
> Należy pamiętać, że podczas gdy dzienniki twierdzą, że dzwoni f1, F2 i F3, te funkcje są *faktycznie* nazywane tylko po raz pierwszy są one napotkane. Kolejne wywołania, które się podczas odtwarzania są pomijane i wyjścia są odtwarzane do logiki orchestrator.

Jeśli chcesz tylko zalogować się do wykonywania bez powtarzania, możesz `IsReplaying` napisać `false`wyrażenie warunkowe do dziennika tylko wtedy, gdy jest . Rozważmy powyższy przykład, ale tym razem z sprawdzania powtórek.

#### <a name="precompiled-c"></a>Wstępnie skompilowany C #

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
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

#### <a name="c"></a>C#

```cs
public static async Task Run(
    IDurableOrchestrationContext context,
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

#### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2.0)

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

Począwszy od funkcji trwałych 2.0, .NET orchestrator `ILogger` funkcje mają również możliwość utworzenia, który automatycznie filtruje instrukcje dziennika podczas odtwarzania. To automatyczne filtrowanie odbywa `IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)` się za pomocą interfejsu API.

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

W związku z wyżej wymienionymi zmianami dane wyjściowe dziennika są następujące:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Poprzednie przykłady języka C# są dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

## <a name="custom-status"></a>Stan niestandardowy

Stan aranżacji niestandardowej umożliwia ustawienie niestandardowej wartości stanu dla funkcji koordynatora. Ten stan jest dostarczany za pośrednictwem `IDurableOrchestrationClient.GetStatusAsync` interfejsu API kwerendy stanu HTTP lub interfejsu API. Stan aranżacji niestandardowej umożliwia bogatsze monitorowanie funkcji aranżatora. Na przykład kod funkcji koordynatora `IDurableOrchestrationContext.SetCustomStatus` może zawierać wywołania aktualizacji postępu dla długotrwałej operacji. Klient, taki jak strona sieci web lub inny system zewnętrzny, może następnie okresowo wysyłać zapytania do interfejsów API kwerendy o stanie HTTP w celu uzyskania bogatszych informacji o postępie. Poniżej `IDurableOrchestrationContext.SetCustomStatus` przedstawiono przykładowe użycie:

### <a name="precompiled-c"></a>Wstępnie skompilowany C #

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> Poprzedni przykład języka C# jest dla funkcji trwałych 2.x. W przypadku funkcji trwałych 1.x należy używać `DurableOrchestrationContext` zamiast `IDurableOrchestrationContext`. Aby uzyskać więcej informacji na temat różnic między wersjami, zobacz [wersje funkcji trwałych](durable-functions-versions.md) artykułu.

### <a name="javascript-functions-20-only"></a>JavaScript (tylko funkcje 2.0)

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

Gdy aranżacja jest uruchomiona, klienci zewnętrzni mogą pobrać ten stan niestandardowy:

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
> Ładunek stanu niestandardowego jest ograniczony do 16 KB tekstu JSON UTF-16, ponieważ musi być w stanie zmieścić się w kolumnie usługi Azure Table Storage. Można użyć magazynu zewnętrznego, jeśli potrzebujesz większego ładunku.

## <a name="debugging"></a>Debugowanie

Usługa Azure Functions obsługuje debugowanie kodu funkcji bezpośrednio i tej samej pomocy technicznej przenosi do funkcji trwałych, czy uruchomiony na platformie Azure lub lokalnie. Istnieje jednak kilka zachowań, o których należy pamiętać podczas debugowania:

* **Powtórka:** Funkcje Orchestrator regularnie [powtarzają się](durable-functions-orchestrations.md#reliability) po odebraniu nowych danych wejściowych. To zachowanie oznacza, że *pojedyncze logiczne* wykonanie funkcji koordynatora może spowodować trafienie tego samego punktu przerwania wiele razy, zwłaszcza jeśli jest ustawiona na wczesnym etapie kodu funkcji.
* **Await:** Gdy `await` napotka się w funkcji koordynatora, daje kontrolę z powrotem do dyspozytora trwałe task framework. Jeśli po raz pierwszy `await` napotkano konkretne zadanie, skojarzone zadanie *nigdy nie* zostanie wznowione. Ponieważ zadanie nigdy nie zostanie wznowione, przechodzenie *między* oczekującymi (F10 w programie Visual Studio) nie jest możliwe. Przechodzenie między tylko działa, gdy zadanie jest odtwarzane.
* **Limity czasu obsługi**: Funkcje trwałe wewnętrznie używa komunikatów kolejki do kierowania wykonywanie funkcji koordynatora, działania i jednostki. W środowisku wielu maszyn wirtualnych włamanie do debugowania przez dłuższy czas może spowodować, że inna maszyna wirtualna odbierze wiadomość, co spowoduje wykonanie duplikatów. To zachowanie istnieje również dla regularnych funkcji wyzwalacza kolejki, ale jest ważne, aby wskazać w tym kontekście, ponieważ kolejki są szczegóły implementacji.
* **Zatrzymywanie i uruchamianie:** Wiadomości w trwałe funkcje utrzymują się między sesjami debugowania. Jeśli przestaniesz debugowania i zakończyć proces hosta lokalnego podczas wykonywania funkcji trwałe, funkcja ta może ponownie wykonać automatycznie w przyszłej sesji debugowania. To zachowanie może być mylące, gdy nie oczekuje. Wyczyszczenie wszystkich wiadomości z [kolejek magazynu wewnętrznego](durable-functions-perf-and-scale.md#internal-queue-triggers) między sesjami debugowania jest jedną z technik, aby uniknąć tego zachowania.

> [!TIP]
> Podczas ustawiania punktów przerwania w funkcjach programu orchestrator, jeśli chcesz tylko przerwać wykonywanie bez `IsReplaying` powtarzania, można ustawić warunkowy punkt przerwania, który przerywa tylko wtedy, gdy jest `false`.

## <a name="storage"></a>Magazyn

Domyślnie funkcje trwałe przechowuje stan w usłudze Azure Storage. To zachowanie oznacza, że można sprawdzić stan aranżacji za pomocą narzędzi, takich jak [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Zrzut ekranu przedstawiający Eksploratora usługi Azure Storage](./media/durable-functions-diagnostics/storage-explorer.png)

Jest to przydatne do debugowania, ponieważ widzisz dokładnie, w jakim stanie może znajdować się aranżacja. Wiadomości w kolejkach można również zbadać, aby dowiedzieć się, co praca jest w toku (lub zatrzymany w niektórych przypadkach).

> [!WARNING]
> Chociaż jest to wygodne, aby wyświetlić historię wykonywania w magazynie tabel, należy unikać podejmowania zależności w tej tabeli. Może się zmienić w miarę rozwoju rozszerzenia Funkcje trwałe.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o monitorowaniu w usłudze Azure Functions](../functions-monitoring.md)
