---
title: Monitorowanie usługi Azure Functions
description: Dowiedz się, jak używać usługi Azure Application Insights z usługą Azure Functions można monitorować wykonywania funkcji.
services: functions
author: ggailey777
manager: jeconnoc
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: fecf6759dd7b277dda10fa2656e6ae9407490370
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922965"
---
# <a name="monitor-azure-functions"></a>Monitorowanie usługi Azure Functions

[Usługa Azure Functions](functions-overview.md) oferuje wbudowaną integrację z [usługi Azure Application Insights](../azure-monitor/app/app-insights-overview.md) do monitorowania funkcji. W tym artykule przedstawiono sposób konfigurowania usługi Azure Functions do wysyłania plików dziennika generowanych przez system do usługi Application Insights.

Firma Microsoft zaleca, za pomocą usługi Application Insights, ponieważ zbiera dzienniki, wydajności i danych o błędach. Automatycznie wykrywa anomalie wydajność i obejmują zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów i aby zrozumieć, jak są używane funkcje. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań. Możesz nawet Application Insights mogą służyć podczas tworzenia projektu aplikacji funkcji lokalnej. Aby uzyskać więcej informacji, zobacz [co to jest usługa Application Insights?](../azure-monitor/app/app-insights-overview.md).

Jak wymagane Instrumentacji usługi Application Insights jest wbudowana w usłudze Azure Functions, wszystko co potrzebne jest kluczem Instrumentacji prawidłowe połączyć swoją aplikację funkcji do zasobu usługi Application Insights.

## <a name="application-insights-pricing-and-limits"></a>Limity i ceny usługi Application Insights

Możesz wypróbować integracji usługi Application Insights z aplikacji funkcji za darmo. Brak dzienny limit ilości danych, które mogą być przetwarzane za darmo. Może napotkać tego limitu podczas testowania. Platforma Azure zapewnia portal i powiadomień e-mail, gdy zbliża się dziennego limitu. Jeśli umknęło alertów i osiągnięty limit nowe dzienniki pojawiać się w zapytaniach usługi Application Insights. Należy pamiętać o limit, aby uniknąć niepotrzebnych czas rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Zarządzanie cenami i ilością danych w usłudze Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Włączanie integracji usługi Application Insights

Dla aplikacji funkcji do przesyłania danych do usługi Application Insights musi ona znasz klucz Instrumentacji zasobu usługi Application Insights. Klucz należy podać w aplikacji, ustawienie o nazwie **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Nową aplikację funkcji w portalu

Po użytkownik [utworzyć aplikację funkcji w witrynie Azure portal](functions-create-first-azure-function.md), Integracja usługi Application Insights jest domyślnie włączona. Zasób usługi Application Insights ma taką samą nazwę jak aplikacja funkcji i zostanie utworzony w tym samym regionie lub w najbliższym regionie.

Aby zapoznać się z zasobem usługi Application Insights, trwa tworzenie, wybierz ją, aby rozwinąć **usługi Application Insights** okna. Możesz zmienić **Nowa nazwa zasobu** lub wybrać inną **lokalizacji** w [lokalizacja geograficzna platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) potrzebne do przechowywania danych.

![Włącz usługę Application Insights podczas tworzenia aplikacji funkcji](media/functions-monitoring/enable-ai-new-function-app.png)

Po wybraniu **Utwórz**, zasób usługi Application Insights jest tworzony za pomocą aplikacji funkcji, która ma `APPINSIGHTS_INSTRUMENTATIONKEY` w ustawieniach aplikacji. Wszystko jest gotowe.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Dodawanie do istniejącej aplikacji funkcji 

Po utworzeniu aplikacji funkcji przy użyciu [wiersza polecenia platformy Azure](functions-create-first-azure-function-azure-cli.md), [programu Visual Studio](functions-create-your-first-function-visual-studio.md), lub [programu Visual Studio Code](functions-create-first-function-vs-code.md), należy utworzyć zasób usługi Application Insights. Następnie można dodać klucza Instrumentacji z tego zasobu jako ustawienie aplikacji w aplikacji funkcji.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Wcześniejsze wersje funkcji używany wbudowany monitoring, który nie jest już zalecany. Podczas włączania Integracja usługi Application Insights dla aplikacji funkcji, musisz także [wyłączyć wbudowane funkcje rejestrowania](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Wyświetlanie danych telemetrycznych na karcie Monitor

Za pomocą [Integracja usługi Application Insights włączoną](#enable-application-insights-integration), można wyświetlić dane telemetryczne w **Monitor** kartę.

1. Na stronie aplikacji funkcji wybierz funkcję, która zostało uruchomione co najmniej raz, po skonfigurowaniu usługi Application Insights. Następnie wybierz pozycję **Monitor** kartę.

   ![Wybierz kartę monitora](media/functions-monitoring/monitor-tab.png)

1. Wybierz **Odśwież** okresowo, dopóki nie zostanie wyświetlona lista wywołania funkcji.

   Może upłynąć do pięciu minut listy są wyświetlane, gdy klient telemetrii partii danych do przesłania do serwera. (Opóźnienie nie ma zastosowania do [Live Stream metryki](../azure-monitor/app/live-stream.md). Czy usługa łączy się z hosta funkcji podczas ładowania strony, aby dzienniki są przesyłane strumieniowo bezpośrednio do strony.)

   ![Lista wywołań](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Aby wyświetlić dzienniki Aby uzyskać wywołania określonej funkcji, wybierz **data** link kolumny dla tego wywołania.

   ![Szczegóły wywołania link](media/functions-monitoring/invocation-details-link-ai.png)

   W nowej strony pojawi się rejestrowanie danych wyjściowych dla tego wywołania.

   ![Szczegóły wywołania](media/functions-monitoring/invocation-details-ai.png)

Widać, że obie strony mają **Uruchom w usłudze Application Insights** łącze do zapytania analizy usługi Application Insights, które pobiera dane.

![Uruchom w usłudze Application Insights](media/functions-monitoring/run-in-ai.png)

Następujące zapytanie zostanie wyświetlone. Widać, że listy wywołań jest ograniczony do ostatnich 30 dni. Lista zawiera nie więcej niż 20 wierszy (`where timestamp > ago(30d) | take 20`). Lista szczegółów wywołanie jest w ciągu ostatnich 30 dni bez ograniczeń.

![Application Insights — analiza wywołanie listy](media/functions-monitoring/ai-analytics-invocation-list.png)

Aby uzyskać więcej informacji, zobacz [wysyłania zapytań dotyczących danych telemetrii](#query-telemetry-data) w dalszej części tego artykułu.

## <a name="view-telemetry-in-application-insights"></a>Wyświetlanie telemetrii w usłudze Application Insights

Aby otworzyć usługę Application Insights z aplikacji funkcji w witrynie Azure portal, przejdź do aplikacji funkcji **Przegląd** strony. W obszarze **skonfigurowane funkcje**, wybierz opcję **usługi Application Insights**.

![Otwórz usługę Application Insights na stronie funkcji aplikacji — omówienie](media/functions-monitoring/ai-link.png)

Aby uzyskać informacje o tym, jak używać usługi Application Insights, zobacz [dokumentacja usługi Application Insights](https://docs.microsoft.com/azure/application-insights/). W tej sekcji przedstawiono kilka przykładów sposobu wyświetlania danych w usłudze Application Insights. Jeśli już znasz z usługi Application Insights, możesz przejść bezpośrednio do [sekcje dotyczące sposobu konfigurowania i dostosowywania dane telemetryczne](#configure-categories-and-log-levels).

![Karta Przegląd szczegółowych informacji w aplikacji](media/functions-monitoring/metrics-explorer.png)

Następujące obszary usługi Application Insights może być przydatne podczas obliczania zachowanie, wydajności i błędów w funkcji:

| Tab | Opis |
| ---- | ----------- |
| **[błędy](../azure-monitor/app/asp-net-exceptions.md)** |  Tworzenie wykresów i alertów na podstawie funkcji błędy i wyjątki serwera. **Nazwy operacji** jest nazwą funkcji. Błędy w zależności nie są wyświetlane, chyba że implementacji niestandardowej telemetrii zależności. |
| **[Wydajność](../azure-monitor/app/performance-counters.md)** | Analizuj problemy z wydajnością. |
| **Serwery** | Widok wykorzystania zasobów i przepływność na serwerze. Może to być przydatne podczas debugowania scenariuszy, w którym funkcje są bogging dół podstawowych zasobów. Serwery są określane jako **wystąpień roli w chmurze**. |
| **[Metryki](../azure-monitor/app/metrics-explorer.md)** | Tworzenie wykresów i alertów, które są oparte na metryki. Metryki obejmują liczbę wywołań funkcji, czas wykonywania i współczynniki powodzenia. |
| **[Transmisja strumieniowa metryk na żywo](../azure-monitor/app/live-stream.md)** | Wyświetlanie danych metryk utworzonemu w czasie rzeczywistym. |

## <a name="query-telemetry-data"></a>Wykonywanie zapytań dotyczących danych telemetrii

[Analiza usługi Application Insights](../azure-monitor/app/analytics.md) zapewnia dostęp do wszystkich danych telemetrycznych w formie tabel w bazie danych. Analytics oferuje język zapytań dla wyodrębniania, manipulowanie i wizualizację danych.

![Wybierz usługi Analytics](media/functions-monitoring/select-analytics.png)

![Przykład analizy](media/functions-monitoring/analytics-traces.png)

Poniżej przedstawiono przykład zapytania, który przedstawia rozkład żądania na proces roboczy w ciągu ostatnich 30 minut.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Tabele, które są dostępne są wyświetlane w **schematu** karty po lewej stronie. Można znaleźć danych generowanych przez wywołania funkcji w poniższych tabelach:

| Tabela | Opis |
| ----- | ----------- |
| **ślady** | Dzienniki utworzone przez środowisko uruchomieniowe i kodu funkcji. |
| **requests** | Jedno żądanie dla każdego wywołania funkcji. |
| **Wyjątki** | Wyjątki zgłaszane w czasie wykonywania. |
| **customMetrics** | Liczba wywołań zakończonych powodzeniem i niepowodzeniem, Częstotliwość powodzeń i czas trwania. |
| **customEvents** | Zdarzenia śledzenia w czasie wykonywania, na przykład: Żądania HTTP, które mogą powodować funkcji. |
| **Liczniki wydajności** | Informacje o wydajności z serwerów, które funkcje są uruchomione na. |

Inne tabele są przeznaczone dla testów dostępności i dane telemetryczne dotyczące klienta i przeglądarki. Możesz zaimplementować niestandardowej telemetrii, aby dodać dane do nich.

W każdej tabeli, niektóre dane specyficzne dla funkcji jest `customDimensions` pola.  Na przykład, następujące zapytanie pobiera wszystkie ślady, które mają poziom dziennika `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Środowisko wykonawcze zapewnia `customDimensions.LogLevel` i `customDimensions.Category` pola. Możesz podać dodatkowe pola w dziennikach, które piszesz w kodzie funkcji. Zobacz [strukturalnego rejestrowania](#structured-logging) w dalszej części tego artykułu.

## <a name="configure-categories-and-log-levels"></a>Konfigurowanie kategorii i poziomy dziennika

Za pomocą usługi Application Insights bez żadnej konfiguracji niestandardowej. Domyślna konfiguracja może spowodować duże ilości danych. Jeśli używasz subskrypcji programu Visual Studio na platformie Azure może napotkać limit danych dla usługi Application Insights. W dalszej części tego artykułu dowiesz się, jak skonfigurować i dostosować dane, które funkcje wysyłanie do usługi Application Insights. W przypadku aplikacji funkcji rejestrowania jest konfigurowana w [host.json] pliku.

### <a name="categories"></a>Categories

Rejestrator usługi Azure Functions obejmuje *kategorii* dla każdego dziennika. Kategoria wskazuje, która część kodu środowiska uruchomieniowego lub kodu funkcji powstała z jednego dziennika. 

Środowisko uruchomieniowe usługi Functions tworzy dzienniki z kategorii, które zaczynają się od "Host". "Funkcja pracę," "wykonywana funkcja", a dzienniki "Ukończono działanie funkcji" należą do kategorii "Host.Executor." 

Jeśli piszesz dzienniki w kodzie funkcji ich kategorii jest "Funkcja".

### <a name="log-levels"></a>Poziomy dziennika

Rejestrator usługi Azure Functions obejmuje również *poziom dziennika* przy każdym logowaniu. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) jest wyliczeniem, a kod liczby całkowitej wskazuje względne znaczenie:

|LogLevel    |Kod|
|------------|---|
|Ślad       | 0 |
|Debugowanie       | 1 |
|Informacje | 2 |
|Ostrzeżenie     | 3 |
|Błąd       | 4 |
|Krytyczny    | 5 |
|Brak        | 6 |

Poziom dziennika `None` zostało wyjaśnione w kolejnej sekcji. 

### <a name="log-configuration-in-hostjson"></a>Konfiguracja dziennika w host.json

[Host.json] plik konfiguruje poziom rejestrowania aplikacji funkcji, wysyła do usługi Application Insights. Dla każdej kategorii wskazujesz, minimalny poziom rejestrowania do wysłania. Istnieją dwa przykłady: pierwszy cele przykład [środowisko uruchomieniowe usługi Functions w wersji 2.x](functions-versions.md#version-2x) (.NET Core), a drugi przykład jest przez środowisko uruchomieniowe 1.x wersji.

### <a name="version-2x"></a>W wersji 2.x

Środowisko uruchomieniowe v2.x używa [hierarchia filtrów rejestrowania platformy .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>W wersji 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

Ten przykład konfiguruje następujące reguły:

* W dziennikach kategorii `Host.Results` lub `Function`, Wyślij tylko `Error` poziom i nowsze wersje do usługi Application Insights. W dziennikach `Warning` na poziomie i poniżej są ignorowane.
* W przypadku dzienników z kategorią `Host.Aggregator`, Wyślij wszystkie dzienniki do usługi Application Insights. `Trace` Poziom rejestrowania jest taka sama jak wywołać pewne rejestratorów `Verbose`, ale `Trace` w [host.json] pliku.
* W przypadku innych dzienników wysyłać tylko `Information` poziom lub nowszym do usługi Application Insights.

Wartości kategorii w [host.json] kontroluje rejestrowanie dla wszystkich kategorii, rozpoczynające się od tej samej wartości. `Host` w [host.json] rejestrowanie dla formantów `Host.General`, `Host.Executor`, `Host.Results`i tak dalej.

Jeśli [host.json] obejmuje wiele kategorii rozpoczynających się od tych samych parametrach dłużej te są dopasowywane. Załóżmy, że chcesz, aby wszystko — od środowiska uruchomieniowego, z wyjątkiem `Host.Aggregator` rejestrowania `Error` poziomu, ale ma `Host.Aggregator` rejestrowania `Information` poziom:

### <a name="version-2x"></a>W wersji 2.x 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>W wersji 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Aby pominąć wszystkie dzienniki dla kategorii, należy użyć poziom dziennika `None`. Nie dzienniki są zapisywane do tej kategorii i nie ma żadnych poziom dziennika nad nim.

W poniższych sekcjach opisano główne kategorie dzienników, które tworzy środowiska uruchomieniowego. 

### <a name="category-hostresults"></a>Kategoria Host.Results

Te dzienniki są wyświetlane jako "requests" w usłudze Application Insights. Wskazują, powodzenia lub niepowodzenia funkcji.

![Wykres dotyczący żądań](media/functions-monitoring/requests-chart.png)

Wszystkie te dzienniki są zapisywane w `Information` poziom. Jeśli filtru u `Warning` lub powyżej, nie zobaczysz dowolne z tych danych.

### <a name="category-hostaggregator"></a>Category Host.Aggregator

Te dzienniki stanowią liczby i wartości średnich wywołań funkcji za pośrednictwem [konfigurowalne](#configure-the-aggregator) okresu czasu. Domyślny okres to 30 sekund lub 1000 wyników, osiągnięta jako pierwsza. 

Dzienniki są dostępne w **customMetrics** tabeli w usłudze Application Insights. Przykłady to numer uruchomienia, częstotliwości powodzeń i czas trwania.

![Zapytanie customMetrics](media/functions-monitoring/custom-metrics-query.png)

Wszystkie te dzienniki są zapisywane w `Information` poziom. Jeśli filtru u `Warning` lub powyżej, nie zobaczysz dowolne z tych danych.

### <a name="other-categories"></a>Inne kategorie

Wszystkie dzienniki dla kategorii innych niż już na liście są dostępne w **ślady** tabeli w usłudze Application Insights.

![Śledzenie zapytań](media/functions-monitoring/analytics-traces.png)

Wszystkie dzienniki z kategoriami, które zaczynają się od `Host` są zapisywane przez środowisko uruchomieniowe usługi Functions. "Funkcja started" i "Ukończono działanie funkcji" dzienniki mają kategorii `Host.Executor`. Dotyczącego pomyślnych uruchomień te dzienniki stanową `Information` poziom. Wyjątki są rejestrowane w `Error` poziom. Środowisko uruchomieniowe wzrasta, powstaje `Warning` poziom dzienniki, na przykład: wysłanych do kolejki skażone komunikaty w kolejce.

Dzienniki napisane przez kod funkcji mają kategorii `Function` i może być dowolny poziom dziennika.

## <a name="configure-the-aggregator"></a>Konfigurowanie agregatora

Jak wspomniano w poprzedniej sekcji, środowisko uruchomieniowe agreguje dane dotyczące wykonań funkcji w okresie czasu. Domyślny okres to 30 sekund lub 1000 jest uruchamiany w zależności od tego, co nastąpi wcześniej. Można skonfigurować tego ustawienia w [host.json] pliku.  Oto przykład:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurowanie próbkowania

Usługa Application Insights ma [próbkowania](../azure-monitor/app/sampling.md) funkcja, która może chronić klientów przed tworzenie zbyt dużej ilości danych telemetrycznych na ukończone wykonaniami w czasie szczytowego obciążenia. Liczba wykonań przychodzący przekroczy określoną wartość progową, Application Insights uruchamia losowo ignoruje część wykonania przychodzących. Domyślnym ustawieniem maksymalnej liczby wykonań na sekundę wynosi 20 (5 w wersji 1.x). Można skonfigurować pobieranie próbek w [host.json].  Oto przykład:

### <a name="version-2x"></a>W wersji 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>W wersji 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [Próbkowanie](../azure-monitor/app/sampling.md) jest domyślnie włączona. Prawdopodobnie brakuje danych, może być konieczne dostosowanie ustawień próbkowania odpowiednio do danego scenariusza monitorowania.

## <a name="write-logs-in-c-functions"></a>Zapisują dzienniki w funkcji języka C#

Dzienniki można pisać w kodzie funkcji, które są wyświetlane jako ślady w usłudze Application Insights.

### <a name="ilogger"></a>ILogger

Użyj [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) parametru w funkcji zamiast `TraceWriter` parametru. Dzienniki utworzone za pomocą `TraceWriter` przejdź do usługi Application Insights, ale `ILogger` umożliwia [strukturalnego rejestrowania](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Za pomocą `ILogger` obiektu, należy wywołać `Log<level>` [metody rozszerzenia na ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) do utworzenia dzienników. Poniższy kod zapisów `Information` dzienniki z kategorią "Funkcja".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Rejestrowaniem strukturalnym

Kolejność symboli zastępczych, nie ich nazwy, określa, które parametry są używane w komunikatu dziennika. Załóżmy, że masz następujący kod:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Jeśli zachować ten sam ciąg wiadomości i odwrócić kolejność parametrów, tekst komunikatu wynikowy musi wartości w niewłaściwej miejsca.

Symbole zastępcze są obsługiwane w ten sposób, dzięki czemu można zrobić rejestrowaniem strukturalnym. Usługa Application Insights przechowuje pary nazwa wartość parametru i ciąg komunikatu. Powoduje to, że argumenty komunikatu stają się pola, które można tworzyć zapytania w.

Jeśli Rejestrator metody wywołania wygląda podobnie jak w poprzednim przykładzie, można tworzyć zapytania pole `customDimensions.prop__rowKey`. `prop__` Prefiks jest dodawany, aby upewnić się, nie ma żadnych kolizji między polami środowiska uruchomieniowego dodaje i pola Kod funkcji dodaje.

Możesz także zbadać na oryginalny ciąg komunikatu, odwołując się do pola `customDimensions.prop__{OriginalFormat}`.  

Poniżej przedstawiono przykładowe reprezentacja JSON `customDimensions` danych:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Rejestrowanie metryki niestandardowe

W skrypcie funkcji języka C#, można użyć `LogMetric` metody rozszerzenia `ILogger` utworzyć metryki niestandardowe w usłudze Application Insights. Oto przykładowe wywołanie metody:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Ten kod jest alternatywą do wywoływania `TrackMetric` przy użyciu interfejsu API usługi Application Insights dla platformy .NET.

## <a name="write-logs-in-javascript-functions"></a>Zapisują dzienniki w funkcji języka JavaScript

W przypadku funkcji środowiska Node.js użyj `context.log` na zapisywanie dzienników. Nie włączono rejestrowaniem strukturalnym.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Rejestrowanie metryki niestandardowe

Jeśli uruchamiasz [wersji 1.x](functions-versions.md#creating-1x-apps) funkcje środowiska uruchomieniowego, można użyć funkcji Node.js `context.log.metric` metodą tworzenia metryki niestandardowe w usłudze Application Insights. Ta metoda nie jest obecnie obsługiwane w wersji 2.x. Oto przykładowe wywołanie metody:

```javascript
context.log.metric("TestMetric", 1234);
```

Ten kod jest alternatywą do wywoływania `trackMetric` przy użyciu zestawu SDK środowiska Node.js dla usługi Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Zaloguj się niestandardowych danych telemetrycznych C# funkcji

Możesz użyć [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) pakietu NuGet w celu wysyłania danych niestandardowych danych telemetrycznych do usługi Application Insights. Następujące C# przykładzie użyto [niestandardowego interfejsu API telemetrii](../azure-monitor/app/api-custom-events-metrics.md). W przykładzie występuje dla biblioteki klas platformy .NET, ale kod usługi Application Insights jest taki sam, aby uzyskać skrypt języka C#.

### <a name="version-2x"></a>W wersji 2.x

Środowisko uruchomieniowe 2.x wersji korzysta z nowszych funkcji w usłudze Application Insights do automatycznie korelowanie danych telemetrycznych z bieżącej operacji. Nie ma potrzeby ręcznego ustawiania operacji `Id`, `ParentId`, lub `Name` pola.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>W wersji 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Nie wywołuj `TrackRequest` lub `StartOperation<RequestTelemetry>` ponieważ zostaną wyświetlone zduplikowane żądania dla wywołania funkcji.  Środowisko uruchomieniowe usługi Functions automatyczne śledzenie żądań.

Nie należy ustawiać `telemetryClient.Context.Operation.Id`. Tego ustawienia globalnego powoduje nieprawidłowe korelacji, gdy jednocześnie jest uruchomionych wiele funkcji. Zamiast tego Utwórz nowe wystąpienie danych telemetrycznych (`DependencyTelemetry`, `EventTelemetry`) i zmodyfikuj jej `Context` właściwości. Następnie przekaż wystąpienie dane telemetryczne do odpowiednich `Track` metody `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Ta metoda zapewnia telemetrią szczegóły korelacji poprawne dla bieżącego wywołania funkcji.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Dziennik niestandardowych danych telemetrycznych w funkcji języka JavaScript

[Zestawu SDK środowiska Node.js usługi Application Insights](https://www.npmjs.com/package/applicationinsights) jest obecnie dostępna w wersji beta. Poniżej przedstawiono przykładowy kod, który wysyła niestandardowych danych telemetrycznych do usługi Application Insights:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

`tagOverrides` Zestawów parametrów `operation_Id` identyfikatora wywołania funkcji To ustawienie umożliwia korelowanie wszystkie automatycznie generowane i niestandardowych danych telemetrycznych dla wywołania danej funkcji.

## <a name="dependencies"></a>Zależności

Funkcje w wersji 2 automatycznie zbiera zależności dla żądań HTTP, magistrali usług i SQL.

Można napisać kod niestandardowy, aby wyświetlić zależności. Przykłady można znaleźć w przykładowym kodzie w [ C# sekcji niestandardowej telemetrii](#log-custom-telemetry-in-c-functions). Przykładowy kod powoduje *mapy aplikacji* w usłudze Application Insights, który wygląda podobnie do poniższej ilustracji:

![Mapa aplikacji](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Zgłaszanie problemów

Aby zgłosić problem z integracją usługi Application Insights w funkcji lub aby wprowadzić sugestię lub żądania, [Utwórz problem w serwisie GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Dzienniki przesyłania strumieniowego

Podczas tworzenia aplikacji, jest często warto wyświetlać informacje rejestrowania w czasie niemal rzeczywistym. Można wyświetlić strumienia plików dzienników generowanych przez funkcje w witrynie Azure portal lub w sesji wiersza polecenia na komputerze lokalnym.

Jest to równoważne z danych wyjściowych widoczny podczas debugowania funkcji podczas [rozwoju lokalnego](functions-develop-local.md). Aby uzyskać więcej informacji, zobacz [jak przesyłanie strumieniowe dzienników](../app-service/troubleshoot-diagnostic-logs.md#streamlogs).

> [!NOTE]
> Dzienniki przesyłania strumieniowego obsługuje tylko jedno wystąpienie hosta funkcji. W przypadku funkcji skalowania do wielu wystąpień, dane z innych wystąpień nie są wyświetlane w strumień dziennika. [Live Stream metryki](../azure-monitor/app/live-stream.md) w usłudze Application Insights obsługuje wiele wystąpień. A także w niemal w czasie rzeczywistym analizy przesyłania strumieniowego są również na podstawie [próbce danych](#configure-sampling).

### <a name="portal"></a>Portal

Aby wyświetlić dzienniki przesyłania strumieniowego w portalu, wybierz **funkcje platformy** kartę w aplikacji funkcji. Następnie w obszarze **monitorowanie**, wybierz **przesyłanie strumieniowe dzienników**.

![Włącz dzienniki przesyłania strumieniowego w portalu](./media/functions-monitoring/enable-streaming-logs-portal.png)

Ta aplikacja łączy się z dzienników przesyłania strumieniowego usługi i dzienniki aplikacji są wyświetlane w oknie. Można przełączać się między **Dzienniki aplikacji** i **dzienników serwera w sieci Web**.  

![Wyświetl dzienniki przesyłania strumieniowego w portalu](./media/functions-monitoring/streaming-logs-window.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Dzienniki przesyłania strumieniowego można włączyć za pomocą [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Aby zalogować się, wybierz subskrypcję i przesyłanie strumieniowe plików dziennika, użyj następujących poleceń:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Dzienniki przesyłania strumieniowego można włączyć za pomocą [programu Azure PowerShell](/powershell/azure/overview). W przypadku programu PowerShell Użyj następujących poleceń, aby dodać konta platformy Azure, wybierz subskrypcję i przesyłanie strumieniowe plików dziennika:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Wyłącz wbudowane funkcje rejestrowania

Po włączeniu usługi Application Insights, należy wyłączyć wbudowane funkcje rejestrowania, który używa usługi Azure Storage. Wbudowane funkcje rejestrowania jest przydatna przy testowaniu z niewielkich obciążeń, ale nie jest przeznaczony do użytku produkcyjnego duże obciążenie. Do monitorowania produkcji, firma Microsoft zaleca usługę Application Insights. Jeśli wbudowane funkcje rejestrowania jest używany w środowisku produkcyjnym, rekord rejestrowania może być niekompletna ze względu na ograniczenia przepustowości w usłudze Azure Storage.

Aby wyłączyć wbudowane funkcje rejestrowania, należy usunąć `AzureWebJobsDashboard` ustawienia aplikacji. Aby dowiedzieć się, jak usunąć ustawienia aplikacji w witrynie Azure portal, zobacz **ustawienia aplikacji** części [jak Zarządzanie aplikacją funkcji](functions-how-to-use-azure-function-app-settings.md#settings). Zanim usuniesz ustawienia aplikacji, upewnij się, że żadne istniejące funkcje w tej samej aplikacji funkcji, użyj ustawienia dla wyzwalaczy usługi Azure Storage lub powiązania.

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji zawierają następujące zasoby:

* [Application Insights](/azure/application-insights/)
* [Platforma ASP.NET Core rejestrowania](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
