---
title: Diagnostyka w Durable Functions — Azure
description: Dowiedz się, jak zdiagnozować problemy przy użyciu rozszerzenia Durable Functions Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 2cc60ee2c73aa6858f68d6b13a895a0188bb5735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098138"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostyka w Durable Functions na platformie Azure

Istnieje kilka opcji diagnozowania problemów z [Durable Functions](durable-functions-overview.md). Niektóre opcje są takie same jak w przypadku funkcji regularnych, a niektóre z nich są unikatowe dla usługi Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) jest zalecanym sposobem przeprowadzania diagnostyki i monitorowania w programie Azure Functions. To samo dotyczy Durable Functions. Aby dowiedzieć się, jak korzystać z Application Insights w aplikacji funkcji, zobacz [Azure Functions monitorowania](../functions-monitoring.md).

Azure Functions trwałego rozszerzenia emituje także *zdarzenia śledzenia* , które umożliwiają śledzenie kompleksowego wykonywania aranżacji. Można je znaleźć i zbadać przy użyciu narzędzia [Application Insights Analytics](../../azure-monitor/app/analytics.md) w Azure Portal.

### <a name="tracking-data"></a>Śledzenie danych

Każde zdarzenie cyklu życia wystąpienia aranżacji powoduje zapisanie zdarzenia śledzenia w kolekcji TRACES w Application Insights. To zdarzenie zawiera ładunek **customDimensions** z kilkoma polami.  Wszystkie nazwy pól są poprzedzone `prop__`.

* **hubName**: Nazwa centrum zadań, w którym są uruchomione aranżacje.
* Nazwa: Nazwa aplikacji funkcji. Jest to przydatne, gdy masz wiele aplikacji funkcji, które współużytkują to samo wystąpienie Application Insights.
* **gniazdoname**: [Miejsce wdrożenia](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) , w którym jest uruchomiona bieżąca aplikacja funkcji. Jest to przydatne w przypadku korzystania z miejsc wdrożenia w celu uzyskania wersji swoich aranżacji.
* **funkcjaname**: Nazwa funkcji programu Orchestrator lub Activity.
* **funkcjatype**: Typ funkcji, na przykład **Orchestrator** lub **Activity**.
* **Identyfikator wystąpienia**: Unikatowy identyfikator wystąpienia aranżacji.
* **stan**: Stan wykonania cyklu życia wystąpienia. Prawidłowe wartości to:
  * **Zaplanowane**: Funkcja została zaplanowana do wykonania, ale jeszcze jej nie uruchomiono.
  * **Uruchomiono**: Funkcja zaczęła działać, ale nie została jeszcze oczekiwana lub ukończona.
  * **Oczekiwane**: Program Orchestrator zaplanował pewną pracę i oczekuje na jego zakończenie.
  * Nasłuchiwanie: Koordynator nasłuchuje powiadomienia o zdarzeniu zewnętrznym.
  * **Ukończono**: Funkcja została ukończona pomyślnie.
  * **Niepowodzenie**: Działanie funkcji nie powiodło się z powodu błędu.
* **Przyczyna**: Dodatkowe dane skojarzone ze zdarzeniem śledzenia. Jeśli na przykład wystąpienie oczekuje na powiadomienie o zdarzeniu zewnętrznym, to pole wskazuje nazwę zdarzenia, dla którego oczekuje. Jeśli funkcja zakończyła się niepowodzeniem, będzie zawierać szczegóły błędu.
* **isreplay**: Wartość logiczna wskazująca, czy zdarzenie śledzenia dotyczy powtarzanego wykonywania.
* **extensionVersion**: Wersja rozszerzenia zadania trwałego. Jest to szczególnie ważne dane podczas raportowania możliwych usterek w rozszerzeniu. Długotrwałe wystąpienia mogą zgłaszać wiele wersji w przypadku, gdy jest ona uruchomiona.
* **sequenceNumber**: Numer sekwencji wykonywania dla zdarzenia. W połączeniu z sygnaturą czasową można zamówić zdarzenia według czasu wykonania. *Należy pamiętać, że ta liczba zostanie zresetowana do wartości zero, Jeśli host zostanie uruchomiony ponownie w trakcie działania wystąpienia, dlatego ważne jest, aby zawsze sortować według sygnatur czasowych, a następnie sequenceNumber.*

Poziom szczegółowości śledzenia danych emitowanych do Application Insights można skonfigurować w `logger` sekcji (Functions 1. x) lub `logging` (Functions 2. `host.json` x) pliku.

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

Domyślnie są emitowane wszystkie zdarzenia śledzenia inne niż powtarzające się. Ilość danych można zmniejszyć przez ustawienie `Host.Triggers.DurableTask` `"Warning"` wartości lub `"Error"` , w przypadku których zdarzenia śledzenia przypadków będą emitowane tylko w sytuacji wyjątkowej.

Aby włączyć emitowanie pełnych zdarzeń powtarzania aranżacji `LogReplayEvents` , można `true` ustawić w `host.json` pliku `durableTask` w sposób pokazany:

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
> Domyślnie dane telemetryczne Application Insights są próbkowane przez środowisko uruchomieniowe Azure Functions, aby uniknąć zbyt częstego emitowania danych. Może to spowodować utratę informacji śledzenia, gdy w krótkim czasie wystąpi wiele zdarzeń cyklu życia. W [artykule monitorowanie Azure Functions](../functions-monitoring.md#configure-sampling) wyjaśniono, jak skonfigurować to zachowanie.

### <a name="single-instance-query"></a>Zapytanie o pojedynczym wystąpieniu

Następujące zapytanie pokazuje historyczne dane śledzenia dla pojedynczego wystąpienia aranżacji funkcji w [sekwencji Hello](durable-functions-sequence.md) . Jest ona zapisywana przy użyciu [języka zapytań Application Insights (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Filtruje wykonywanie powtarzania, tak aby była wyświetlana tylko ścieżka wykonywania logicznego. Zdarzenia mogą być uporządkowane według sortowania `timestamp` według `sequenceNumber` i jak pokazano w poniższym zapytaniu:

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

Wynik jest listą zdarzeń śledzenia, które pokazują ścieżkę wykonywania aranżacji, łącznie z wszystkimi funkcjami działania uporządkowanymi według czasu wykonywania w kolejności rosnącej.

![Zapytanie Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Zapytanie podsumowania wystąpienia

Następujące zapytanie wyświetla stan wszystkich wystąpień aranżacji, które zostały uruchomione w określonym przedziale czasu.

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

Wynikiem jest lista identyfikatorów wystąpień i ich bieżący stan środowiska uruchomieniowego.

![Zapytanie Application Insights](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Rejestrowanie

Ważne jest, aby zachować zachowanie odtwarzania programu Orchestrator podczas pisania dzienników bezpośrednio z poziomu funkcji programu Orchestrator. Rozważmy na przykład następującą funkcję programu Orchestrator:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

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

Wyniki danych dzienników są podobne do następujących:

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
> Należy pamiętać, że podczas gdy usługa Logs wywołuje wywoływanie F1, F2 i F3, te funkcje są tylko *faktycznie* wywoływane przy pierwszym napotkaniu. Kolejne wywołania, które nastąpiły podczas powtarzania są pomijane, a dane wyjściowe są odtwarzane do logiki programu Orchestrator.

Jeśli chcesz tylko zalogować się do wykonywania bez powtarzania, możesz napisać wyrażenie warunkowe do rejestrowania tylko wtedy, `IsReplaying` gdy `false`jest to. Rozważmy przykład powyżej, ale tym razem z testami powtarzania.

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

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

W przypadku tej zmiany dane wyjściowe dziennika są następujące:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Stan niestandardowy

Stan aranżacji niestandardowej pozwala ustawić niestandardową wartość stanu dla funkcji programu Orchestrator. Ten stan jest dostarczany za pośrednictwem interfejsu API zapytania o stan `DurableOrchestrationClient.GetStatusAsync` http lub interfejsu API. Niestandardowy stan aranżacji umożliwia zaawansowane monitorowanie funkcji programu Orchestrator. Na przykład kod funkcji programu Orchestrator może obejmować `DurableOrchestrationContext.SetCustomStatus` wywołania do aktualizowania postępu długotrwałej operacji. Klient, taki jak strona sieci Web lub inny system zewnętrzny, może następnie okresowo badać interfejsy API zapytań o stan HTTP w celu uzyskania bardziej szczegółowych informacji o postępie. Poniżej przedstawiono przykład `DurableOrchestrationContext.SetCustomStatus` użycia:

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

### <a name="javascript-functions-2x-only"></a>JavaScript (tylko funkcje 2. x)

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

Po uruchomieniu aranżacji klienci zewnętrzni mogą pobrać ten stan niestandardowy:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Klienci otrzymają następujące odpowiedzi:

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
> Niestandardowy ładunek stanu jest ograniczony do 16 KB tekstu JSON w formacie UTF-16, ponieważ musi być w stanie zmieścić się w kolumnie Table Storage platformy Azure. Jeśli potrzebujesz większego ładunku, możesz użyć magazynu zewnętrznego.

## <a name="debugging"></a>Debugowanie

Azure Functions obsługuje kod funkcji debugowania bezpośrednio, a ta sama obsługa jest przenoszone do Durable Functions, niezależnie od tego, czy działa na platformie Azure, czy lokalnie. Istnieje jednak kilka zachowań, z którymi należy wiedzieć podczas debugowania:

* **Odtwórz**ponownie: Funkcje programu Orchestrator są regularnie powtarzane, gdy są odbierane nowe dane wejściowe. Oznacza to, że jedno *logiczne* wykonanie funkcji programu Orchestrator może skutkować tym samym punktem przerwania, szczególnie, jeśli jest ono wcześniej ustawione w kodzie funkcji.
* **Oczekiwanie**: `await` Za każdym razem, gdy wystąpi, zwraca kontrolę z powrotem do trwałego dyspozytora struktury zadań. Jeśli jest to pierwszy raz `await` , skojarzone zadanie *nigdy nie* zostanie wznowione. Ponieważ zadanie nigdy nie zostanie wznowione, przechodzenie do oczekiwania (F10 w programie Visual Studio) nie jest *w* rzeczywistości możliwe. Przechodzenie do kolejnych kroków działa tylko wtedy, gdy zadanie jest odtwarzane.
* **Limity czasu obsługi komunikatów**: Durable Functions wewnętrznie używa komunikatów w kolejce do wykonywania zarówno funkcji programu Orchestrator, jak i funkcji działania. W środowisku z obsługą wiele maszyn wirtualnych przerwanie debugowania przez dłuższy czas może spowodować, że inna maszyna wirtualna będzie mogła pobrać komunikat, co spowodowało zduplikowane wykonanie. To zachowanie istnieje dla zwykłych funkcji wyzwalacza kolejki, ale jest ważne, aby wskazać w tym kontekście, ponieważ kolejki są szczegółami implementacji.

> [!TIP]
> Jeśli ustawienia punktów przerwania mają być przerywane tylko przy wykonywaniu niepowtarzania, można ustawić punkt przerwania warunkowego, który działa `IsReplaying` tylko `false`wtedy, gdy jest to.

## <a name="storage"></a>Magazyn

Domyślnie magazyny Durable Functions są przechowywane w usłudze Azure Storage. Oznacza to, że można sprawdzić stan swoich aranżacji przy użyciu narzędzi, takich jak [Eksplorator usługi Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Zrzut ekranu Eksplorator usługi Azure Storage](./media/durable-functions-diagnostics/storage-explorer.png)

Jest to przydatne w przypadku debugowania, ponieważ widzisz dokładnie, z jakim stanem może być aranżacja. Komunikaty w kolejkach można również sprawdzić, aby dowiedzieć się, jaka jest oczekująca (lub w niektórych przypadkach zablokowana).

> [!WARNING]
> Mimo że wygodnie jest wyświetlić historię wykonywania w usłudze Table Storage, unikaj podejmowania jakichkolwiek zależności od tej tabeli. Może ulec zmianie w miarę rozwoju rozszerzenia Durable Functions.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak korzystać z trwałych czasomierzy](durable-functions-timers.md)
