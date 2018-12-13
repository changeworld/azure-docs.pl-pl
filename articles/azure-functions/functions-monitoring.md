---
title: Monitorowanie usługi Azure Functions
description: Dowiedz się, jak używać usługi Azure Application Insights z usługą Azure Functions na potrzeby monitorowania wykonywania funkcji.
services: functions
author: ggailey777
manager: jeconnoc
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: glenga
ms.openlocfilehash: aba3d9f33d179c09708464975fa2a929a8bb68d0
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876523"
---
# <a name="monitor-azure-functions"></a>Monitorowanie usługi Azure Functions

[Usługa Azure Functions](functions-overview.md) oferuje wbudowaną integrację z [usługi Azure Application Insights](../application-insights/app-insights-overview.md) dla funkcji monitorowania. W tym artykule przedstawiono sposób konfigurowania funkcji do wysyłania plików dziennika generowanych przez system do usługi Application Insights.

![Eksplorator metryk usługi Application Insights](media/functions-monitoring/metrics-explorer.png)

Ma również funkcji [wbudowane funkcje monitorowania, która nie korzysta z usługi Application Insights](#monitoring-without-application-insights). Firma Microsoft zaleca usługi Application Insights, ponieważ oferuje on większej ilości danych i lepsze sposoby analizować dane.

## <a name="application-insights-pricing-and-limits"></a>Limity i ceny usługi Application Insights

Możesz wypróbować integracji usługi Application Insights z aplikacji funkcji za darmo. Jednak istnieje dzienny limit ilości danych, które mogą być przetwarzane za darmo i może napotkać tego limitu podczas testowania. Platforma Azure zapewnia portal i powiadomień e-mail, gdy zbliża się dziennego limitu.  Ale jeśli pominąć te alerty i osiągnięty limit, nowe dzienniki nie będą wyświetlane w zapytania usługi Application Insights. Dlatego należy pamiętać o limit, aby uniknąć niepotrzebnych czas rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Zarządzanie cenami i ilością danych w usłudze Application Insights](../application-insights/app-insights-pricing.md).

## <a name="enable-app-insights-integration"></a>Włączanie integracji usługi App Insights

Dla aplikacji funkcji do przesyłania danych do usługi Application Insights musi ona znasz klucz Instrumentacji zasobu usługi Application Insights. Klucz należy podać w aplikacji, ustawienie o nazwie **APPINSIGHTS_INSTRUMENTATIONKEY**.

Można skonfigurować tego połączenia w [witryny Azure portal](https://portal.azure.com):

* [Automatycznie dla nowej aplikacji funkcji](#new-function-app)
* [Ręcznie połączyć zasobu usługi App Insights](#manually-connect-an-app-insights-resource)

### <a name="new-function-app"></a>Nowa aplikacja funkcji

1. Przejdź do aplikacji funkcji **Utwórz** strony.

1. Ustaw **usługi Application Insights** Przełącz **na**.

1. Wybierz **Lokalizacja usługi Application Insights**. Wybierz region znajdujący się najbliżej regionu aplikację funkcji w [lokalizacja geograficzna platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) którego dane mają być przechowywane.

   ![Włącz usługę Application Insights podczas tworzenia aplikacji funkcji](media/functions-monitoring/enable-ai-new-function-app.png)

1. Wprowadź wymagane informacje, a następnie wybierz pozycję **Utwórz**.

Następnym krokiem jest [wyłączyć wbudowane funkcje rejestrowania](#disable-built-in-logging).

### <a name="manually-connect-an-app-insights-resource"></a>Ręcznie połączyć zasobu usługi App Insights 

1. Utwórz zasób usługi Application Insights. Ustaw typ aplikacji **ogólne**.

   ![Utwórz zasób usługi Application Insights typu ogólnego](media/functions-monitoring/ai-general.png)

1. Skopiuj klucz Instrumentacji z **Essentials** strony zasobu usługi Application Insights. Umieść kursor nad koniec wyświetlane wartości klucza, aby uzyskać **kliknij, aby skopiować** przycisku.

   ![Skopiuj klucz Instrumentacji usługi Application Insights](media/functions-monitoring/copy-ai-key.png)

1. W aplikacji funkcji **ustawienia aplikacji** stronie [Dodaj ustawienie aplikacji](functions-how-to-use-azure-function-app-settings.md#settings) , klikając **Dodaj nowe ustawienie**. Nazwa nowego ustawienia APPINSIGHTS_INSTRUMENTATIONKEY i Wklej klucz Instrumentacji skopiowany.

   ![Dodaj klucz Instrumentacji do pliku ustawień aplikacji](media/functions-monitoring/add-ai-key.png)

1. Kliknij pozycję **Zapisz**.

## <a name="disable-built-in-logging"></a>Wyłącz wbudowane funkcje rejestrowania

Po włączeniu usługi Application Insights, wyłącz [wbudowane funkcje rejestrowania, który używa usługi Azure storage](#logging-to-storage). Wbudowane funkcje rejestrowania jest przydatna przy testowaniu z niewielkich obciążeń, ale nie jest przeznaczona do użytku produkcyjnego duże obciążenie. Do monitorowania produkcji, zaleca się usługi Application Insights. Jeśli wbudowane funkcje rejestrowania jest używany w środowisku produkcyjnym, rekord rejestrowania mogą być niekompletne ze względu na ograniczenie w usłudze Azure Storage.

Aby wyłączyć wbudowane funkcje rejestrowania, należy usunąć `AzureWebJobsDashboard` ustawienia aplikacji. Aby dowiedzieć się, jak usunąć ustawienia aplikacji w witrynie Azure portal, zobacz **ustawienia aplikacji** części [jak Zarządzanie aplikacją funkcji](functions-how-to-use-azure-function-app-settings.md#settings). Przed usunięciem ustawienia aplikacji, upewnij się, że nie istniejących funkcji w tej samej aplikacji funkcji ich używać dla wyzwalaczy usługi Azure Storage lub powiązania.

## <a name="view-telemetry-in-monitor-tab"></a>Wyświetlanie danych telemetrycznych na karcie Monitor

Po skonfigurowaniu integracji usługi Application Insights jak pokazano w poprzednich sekcjach, można wyświetlić dane telemetryczne w **Monitor** kartę.

1. Na stronie aplikacji funkcji, wybierz funkcję, który został uruchomiony przynajmniej raz po usługi Application Insights został skonfigurowany, a następnie wybierz **Monitor** kartę.

   ![Wybierz kartę monitora](media/functions-monitoring/monitor-tab.png)

1. Wybierz **Odśwież** okresowo, dopóki nie zostanie wyświetlona lista wywołania funkcji.

   Może upłynąć do 5 minut na liście, aby wyświetlane ze względu na sposób dane telemetryczne klienta partie w celu przesłania go do serwera. (Nie dotyczy to opóźnienie [Live Stream metryki](../application-insights/app-insights-live-stream.md). Czy usługa łączy się z hosta funkcji podczas ładowania strony, aby dzienniki są przesyłane strumieniowo bezpośrednio do strony.)

   ![Lista wywołań](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Aby wyświetlić dzienniki Aby uzyskać wywołania określonej funkcji, wybierz **data** link kolumny dla tego wywołania.

   ![Szczegóły wywołania link](media/functions-monitoring/invocation-details-link-ai.png)

   W nowej strony pojawi się rejestrowanie danych wyjściowych dla tego wywołania.

   ![Szczegóły wywołania](media/functions-monitoring/invocation-details-ai.png)

Obie strony (wywołanie listy i szczegółów), połącz się z zapytania analizy usługi Application Insights, które pobiera dane:

![Uruchom w usłudze Application Insights](media/functions-monitoring/run-in-ai.png)

![Application Insights — analiza wywołanie listy](media/functions-monitoring/ai-analytics-invocation-list.png)

Z tych zapytań widać, że listy wywołań jest ograniczony do ostatnich 30 dni i nie więcej niż 20 wierszy (`where timestamp > ago(30d) | take 20`) i wywołania Lista szczegółów znajduje się w ciągu ostatnich 30 dni bez ograniczeń.

Aby uzyskać więcej informacji, zobacz [wysyłania zapytań dotyczących danych telemetrii](#query-telemetry-data) w dalszej części tego artykułu.

## <a name="view-telemetry-in-app-insights"></a>Wyświetlanie telemetrii w usłudze App Insights

Aby otworzyć usługę Application Insights z aplikacji funkcji w witrynie Azure portal, wybierz **usługi Application Insights** łącze w **skonfigurowane funkcje** części aplikacji funkcji **—Omówienie** strony.

![Link do szczegółowych informacji aplikacji na stronie przeglądu](media/functions-monitoring/ai-link.png)

Aby uzyskać informacje o tym, jak używać usługi Application Insights, zobacz [dokumentacja usługi Application Insights](https://docs.microsoft.com/azure/application-insights/). W tej sekcji przedstawiono kilka przykładów sposobu wyświetlania danych w usłudze Application Insights. Jeśli już znasz z usługą Application Insights, możesz też przejść bezpośrednio do [sekcje dotyczące konfigurowania i dostosowywania dane telemetryczne](#configure-categories-and-log-levels).

W [Eksploratora metryk](../application-insights/app-insights-metrics-explorer.md), możesz tworzyć wykresy i alerty oparte na metryki, takie jak liczba wywołań funkcji, czas wykonywania i Częstotliwość powodzeń.

![Eksplorator metryk](media/functions-monitoring/metrics-explorer.png)

Na [błędy](../application-insights/app-insights-asp-net-exceptions.md) karty, możesz tworzyć wykresy i alerty oparte na błędy funkcji i serwera, wyjątki. **Nazwy operacji** jest nazwą funkcji. Błędy w zależności nie są wyświetlane, chyba że możesz wdrożyć [niestandardowych danych telemetrycznych](#custom-telemetry-in-c-functions) zależności.

![Awarie](media/functions-monitoring/failures.png)

Na [wydajności](../application-insights/app-insights-performance-counters.md) karcie, można analizować problemy z wydajnością.

![Wydajność](media/functions-monitoring/performance.png)

**Serwerów** karta przedstawia wykorzystanie zasobów i przepływność na serwerze. Może to być przydatne podczas debugowania scenariuszy, w którym funkcje są bogging dół podstawowych zasobów. Serwery są określane jako **wystąpień roli w chmurze**.

![Serwery](media/functions-monitoring/servers.png)

[Live Stream metryki](../application-insights/app-insights-live-stream.md) karta przedstawia dane metryk, ponieważ jest on tworzony w czasie rzeczywistym.

![Transmisja strumieniowa na żywo](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Wykonywanie zapytań dotyczących danych telemetrii

[Analiza usługi Application Insights](../application-insights/app-insights-analytics.md) zapewnia dostęp do wszystkich danych telemetrycznych w formie tabel w bazie danych. Analytics oferuje język zapytań dla wyodrębniania, manipulowanie i wizualizację danych.

![Wybierz usługi Analytics](media/functions-monitoring/select-analytics.png)

![Przykład analizy](media/functions-monitoring/analytics-traces.png)

Poniżej przedstawiono przykład zapytania, który przedstawia rozkład żądania na proces roboczy w ciągu ostatnich 30 minut.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Tabele, które są dostępne są wyświetlane w **schematu** kartę okienka po lewej stronie. Można znaleźć danych generowanych przez wywołania funkcji w poniższych tabelach:

* **ślady** — Dzienniki tworzone przez środowisko uruchomieniowe i kodu funkcji.
* **żądania** — jeden dla każdego wywołania funkcji.
* **Wyjątki** — wyjątki zgłaszane w czasie wykonywania.
* **customMetrics** — Liczba pomyślnie i niepowodzeniem wywołania, Częstotliwość powodzeń, czas trwania.
* **customEvents** -zdarzeń śledzenia w czasie wykonywania, na przykład: Funkcja wyzwalacza żądania HTTP.
* **liczniki wydajności** — informacje na temat wydajności serwerów, które funkcje są uruchomione na.

Inne tabele są przeznaczone dla testów dostępności i telemetria klienta/przeglądarki. Możesz zaimplementować niestandardowej telemetrii, aby dodać dane do nich.

W każdej tabeli, niektóre dane specyficzne dla funkcji jest `customDimensions` pola.  Na przykład, następujące zapytanie pobiera wszystkie ślady, które mają poziom dziennika `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Środowisko wykonawcze zapewnia `customDimensions.LogLevel` i `customDimensions.Category`. Możesz podać dodatkowe pola w dziennikach, napisany w kodzie funkcji. Zobacz [strukturalnego rejestrowania](#structured-logging) w dalszej części tego artykułu.

## <a name="configure-categories-and-log-levels"></a>Konfigurowanie kategorii i poziomy dziennika

Bez żadnej konfiguracji niestandardowych można użyć usługi Application Insights, ale w konfiguracji domyślnej może spowodować duże ilości danych. Jeśli używasz subskrypcji programu Visual Studio na platformie Azure może napotkać limit danych dla usługi Application Insights. W dalszej części tego artykułu pokazuje, jak skonfigurować i dostosować dane, które funkcje wysyłanie do usługi Application Insights.

### <a name="categories"></a>Kategorie

Rejestrator usługi Azure Functions obejmuje *kategorii* dla każdego dziennika. Kategoria wskazuje, która część kodu środowiska uruchomieniowego lub kodu funkcji powstała z jednego dziennika. 

Środowisko uruchomieniowe usługi Functions tworzy dzienniki, które mają kategorii począwszy "Host". Na przykład "Funkcja pracę," "wykonywana funkcja" i "Ukończono działanie funkcji" dzienniki mają kategorii "Host.Executor". 

Jeśli piszesz dzienniki w kodzie funkcji ich kategorii jest "Funkcja".

### <a name="log-levels"></a>Poziomy dziennika

Rejestrator usługi Azure functions obejmuje również *poziom dziennika* przy każdym logowaniu. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) jest wyliczeniem, a kod liczby całkowitej wskazuje względne znaczenie:

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

### <a name="configure-logging-in-hostjson"></a>Konfigurowanie rejestrowania w host.json

*[Host.json](functions-host-json.md)* plik konfiguruje poziom rejestrowania aplikacji funkcji, wysyła do usługi Application Insights. Dla każdej kategorii wskazujesz, minimalny poziom rejestrowania do wysłania. Istnieją dwa przykłady, który jest przeznaczony dla [środowisko uruchomieniowe usługi Functions w wersji 2.x](functions-versions.md#version-2x) (.NET Core), a drugi dla środowisko uruchomieniowe 1.x wersji.

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

1. W dziennikach kategorii `Host.Results` **` or `** `Function`, Wyślij tylko `Error` poziom i nowsze wersje do usługi Application Insights. W dziennikach `Warning` na poziomie i poniżej są ignorowane.
2. W przypadku dzienników z kategorią `Host.Aggregator`, Wyślij wszystkie dzienniki do usługi Application Insights. `Trace` Poziom rejestrowania jest taka sama jak wywołać pewne rejestratorów `Verbose`, ale `Trace` w [host.json](functions-host-json.md) pliku.
3. W przypadku innych dzienników wysyłać tylko `Information` poziom lub nowszym do usługi Application Insights.

Wartości kategorii w [host.json](functions-host-json.md) kontroluje rejestrowanie dla wszystkich kategorii, rozpoczynające się od tej samej wartości. Na przykład `Host` w [host.json](functions-host-json.md) rejestrowanie dla formantów `Host.General`, `Host.Executor`, `Host.Results`, i tak dalej.

Jeśli [host.json](functions-host-json.md) obejmuje wiele kategorii rozpoczynających się od tych samych parametrach dłużej te są dopasowywane. Załóżmy, że chcesz, aby wszystko — od środowiska uruchomieniowego, z wyjątkiem `Host.Aggregator` rejestrowania `Error` poziomu, ale ma `Host.Aggregator` rejestrowania `Information` poziom:

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

Wszystkie te dzienniki są zapisywane w `Information` poziomu, więc jeśli filtru u `Warning` lub powyżej, nie zobaczysz dowolne z tych danych.

### <a name="category-hostaggregator"></a>Category Host.Aggregator

Te dzienniki stanowią liczby i wartości średnich wywołań funkcji za pośrednictwem [konfigurowalne](#configure-the-aggregator) okresu czasu. Domyślny okres to 30 sekund lub 1000 wyników, osiągnięta jako pierwsza. 

Dzienniki są dostępne w **customMetrics** tabeli w usłudze Application Insights. Przykłady to numer uruchomienia, częstotliwości powodzeń i czas trwania.

![Zapytanie customMetrics](media/functions-monitoring/custom-metrics-query.png)

Wszystkie te dzienniki są zapisywane w `Information` poziomu, więc jeśli filtru u `Warning` lub powyżej, nie zobaczysz dowolne z tych danych.

### <a name="other-categories"></a>Inne kategorie

Wszystkie dzienniki dla kategorii innych niż już na liście są dostępne w **ślady** tabeli w usłudze Application Insights.

![Śledzenie zapytań](media/functions-monitoring/analytics-traces.png)

Wszystkie dzienniki z kategoriami, które zaczynają się od "Host" są zapisywane przez środowisko uruchomieniowe usługi Functions. "Funkcja started" i "Funkcja ukończone" dzienniki mają kategorii "Host.Executor". Dotyczącego pomyślnych uruchomień te dzienniki stanową `Information` poziom; wyjątków są rejestrowane w `Error` poziom. Środowisko uruchomieniowe wzrasta, powstaje `Warning` poziom dzienniki, na przykład: wysłanych do kolejki skażone komunikaty w kolejce.

Dzienniki napisane przez kod funkcji mają kategorii "Funkcja" i może być dowolny poziom dziennika.

## <a name="configure-the-aggregator"></a>Konfigurowanie agregatora

Jak wspomniano w poprzedniej sekcji, środowisko uruchomieniowe agreguje dane dotyczące wykonań funkcji w okresie czasu. Domyślny okres to 30 sekund lub 1000 jest uruchamiany w zależności od tego, co nastąpi wcześniej. Można skonfigurować tego ustawienia w [host.json](functions-host-json.md) pliku.  Oto przykład:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurowanie próbkowania

Usługa Application Insights ma [próbkowania](../application-insights/app-insights-sampling.md) funkcji, które można chronić klientów przed tworzenie zbyt dużej ilości danych telemetrycznych dane w czasie szczytowego obciążenia. Liczba przychodzących danych telemetrycznych przekracza określoną wartość progową, Application Insights uruchamia losowo ignorowanie niektórych elementów przychodzących. Domyślne ustawienie maksymalnej liczby elementów na sekundę wynosi 5. Można skonfigurować pobieranie próbek w [host.json](functions-host-json.md).  Oto przykład:

### <a name="version-2x"></a>W wersji 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 5
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
> [Próbkowanie](../application-insights/app-insights-sampling.md) jest domyślnie włączona. Jeśli prawdopodobnie brakuje danych, po prostu może być konieczne dostosowanie ustawień próbkowania odpowiednio do danego scenariusza monitorowania.

## <a name="write-logs-in-c-functions"></a>Zapisują dzienniki w funkcji języka C#

Dzienniki można pisać w kodzie funkcji, które są wyświetlane jako ślady w usłudze Application Insights.

### <a name="ilogger"></a>ILogger

Użyj [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) parametru w funkcji zamiast `TraceWriter` parametru. Dzienniki utworzone za pomocą `TraceWriter` przejdź do usługi Application Insights, ale `ILogger` umożliwia [strukturalnego rejestrowania](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Za pomocą `ILogger` obiektu, należy wywołać `Log<level>` [metody rozszerzenia na ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) do utworzenia dzienników. Na przykład, poniższy kod zapisów `Information` dzienniki z kategorią "Funkcja".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Rejestrowaniem strukturalnym

Kolejność symboli zastępczych, nie ich nazwy, określa, które parametry są używane w komunikatu dziennika. Załóżmy na przykład, poniższy kod:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Jeśli zachować ten sam ciąg wiadomości i odwrócić kolejność parametrów, tekst komunikatu wynikowy musi wartości w niewłaściwej miejsca.

Symbole zastępcze są obsługiwane w ten sposób, dzięki czemu można zrobić rejestrowaniem strukturalnym. Usługa Application Insights są przechowywane pary nazwa wartość parametru oprócz ciąg komunikatu. Powoduje to, że argumenty komunikatu stają się pola, które można tworzyć zapytania w.

Na przykład, jeśli Rejestrator metody wywołania wygląda podobnie jak w poprzednim przykładzie, możesz zbadać pole `customDimensions.prop__rowKey`. `prop__` Prefiks jest dodawany, aby upewnić się, że nie ma żadnych kolizji między polami środowiska uruchomieniowego dodaje i pola Kod funkcji dodaje.

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

### <a name="logging-custom-metrics"></a>Rejestrowanie metryki niestandardowe  

W skrypcie funkcji języka C#, można użyć `LogMetric` metody rozszerzenia `ILogger` utworzyć metryki niestandardowe w usłudze Application Insights. Oto przykładowe wywołanie metody:

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Ten kod jest alternatywą do wywoływania `TrackMetric` przy użyciu [interfejsu API usługi Application Insights dla platformy .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Zapisują dzienniki w funkcji języka JavaScript

W przypadku funkcji środowiska Node.js użyj `context.log` na zapisywanie dzienników. Nie włączono rejestrowaniem strukturalnym.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Rejestrowanie metryki niestandardowe  

W przypadku funkcji środowiska Node.js można użyć `context.log.metric` metodą tworzenia metryki niestandardowe w usłudze Application Insights. Oto przykładowe wywołanie metody:

```javascript
context.log.metric("TestMetric", 1234); 
```

Ten kod jest alternatywą do wywoływania `trackMetric` przy użyciu [zestawu SDK środowiska Node.js dla usługi Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>Telemetria niestandardowa w funkcji języka C#

Możesz użyć [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) pakietu NuGet w celu wysyłania danych niestandardowych danych telemetrycznych do usługi Application Insights. Następujące C# przykładzie użyto [niestandardowego interfejsu API telemetrii](../application-insights/app-insights-api-custom-events-metrics.md). W przykładzie występuje dla biblioteki klas platformy .NET, ale kod usługi Application Insights jest taki sam, aby uzyskać skrypt języka C#.

### <a name="version-2x"></a>W wersji 2.x

Środowisko uruchomieniowe 2.x wersji korzysta z nowszych funkcji w usłudze Application Insights do automatycznie korelowanie danych telemetrycznych z bieżącej operacji. Nie ma potrzeby ręcznego ustawiania operacji `Id`, `ParentId`, lub `Name`.

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
    public static class HttpTrigger2
    {
        // In Functions v2, TelemetryConfiguration.Active is initialized with the InstrumentationKey
        // from APPINSIGHTS_INSTRUMENTATIONKEY. Creating a default TelemetryClient like this will 
        // automatically use that key for all telemetry. It will also enable telemetry correlation
        // with the current operation.
        // If you require a custom TelemetryConfiguration, create it initially with
        // TelemetryConfiguration.CreateDefault() to include this automatic correlation.
        private static TelemetryClient telemetryClient = new TelemetryClient();

        [FunctionName("HttpTrigger2")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
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
            dependency.Context.User.Id = name;
            telemetryClient.TrackDependency(dependency);

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

            // parse query parameter
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
        
        // This correllates all telemetry with the current Function invocation
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

Nie wywołuj `TrackRequest` lub `StartOperation<RequestTelemetry>`, ponieważ zostaną wyświetlone zduplikowane żądania dla wywołania funkcji.  Środowisko uruchomieniowe usługi Functions automatyczne śledzenie żądań.

Nie należy ustawiać `telemetryClient.Context.Operation.Id`. To jest ustawienie globalne i spowoduje nieprawidłowe korelacji, gdy jednocześnie jest uruchomionych wiele funkcji. Zamiast tego Utwórz nowe wystąpienie danych telemetrycznych (`DependencyTelemetry`, `EventTelemetry`) i zmodyfikuj jej `Context` właściwości. Następnie przekaż wystąpienie dane telemetryczne do odpowiednich `Track` metody `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Dzięki temu telemetrią szczegóły korelacji poprawne dla bieżącego wywołania funkcji.

## <a name="custom-telemetry-in-javascript-functions"></a>Telemetria niestandardowa w funkcji języka JavaScript

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

## <a name="known-issues"></a>Znane problemy

### <a name="dependencies"></a>Zależności

Zależności, funkcja używa się do innych usług, które nie będą automatycznie wyświetlane, ale można napisać kod niestandardowy, aby wyświetlić zależności. Przykładowy kod [sekcji niestandardowej telemetrii C#](#custom-telemetry-in-c-functions) pokazuje, jak. Przykładowy kod powoduje *mapy aplikacji* w usłudze Application Insights, który wygląda podobnie do tego:

![Mapa aplikacji](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Zgłaszanie problemów

Aby zgłosić problem z integracją usługi Application Insights w funkcji lub aby wprowadzić sugestię lub żądania, [Utwórz problem w serwisie GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Monitorowanie bez usługi Application Insights

Firma Microsoft zaleca usługę Application Insights dla funkcji monitorowania, ponieważ oferuje on większej ilości danych i lepsze sposoby analizować dane. Jednak jeśli wolisz system wbudowane funkcje rejestrowania, który używa usługi Azure Storage, można nadal używać go.

### <a name="logging-to-storage"></a>Rejestrowanie w magazynie

Wbudowane funkcje rejestrowania używa konta magazynu, określona przez ciąg połączenia w `AzureWebJobsDashboard` ustawienia aplikacji. Na stronie aplikacji funkcji wybierz funkcję, a następnie wybierz **Monitor** kartę i pozostawić ją w widoku klasycznym.

![Przełącz na widok klasyczny](media/functions-monitoring/switch-to-classic-view.png)

 Możesz uzyskać listę wykonań funkcji. Wybierz opcję wykonywania funkcji, aby sprawdzić czas trwania, danych wejściowych, błędów i skojarzone pliki dziennika.

Jeśli wcześniej włączaniu usługi Application Insights, ale możesz teraz przejść do wbudowane funkcje rejestrowania, wyłącz usługę Application Insights ręcznie, a następnie wybierz **Monitor** kartę. Można wyłączyć integracji usługi Application Insights, Usuń ustawienie aplikacji APPINSIGHTS_INSTRUMENTATIONKEY.

Nawet wtedy, gdy **Monitor** karta przedstawia dane usługi Application Insights, możesz sprawdzić dane dziennika w systemie plików, jeśli jeszcze tego nie [wyłączone wbudowane funkcje rejestrowania](#disable-built-in-logging). W zasobie magazynu, przejdź do plików, wybierz usługę plików dla funkcji, a następnie przejdź do `LogFiles > Application > Functions > Function > your_function` można znaleźć w pliku dziennika.

### <a name="real-time-monitoring"></a>Monitorowanie w czasie rzeczywistym

Można przesyłać strumieniowo pliki dziennika w celu sesji wiersza polecenia na lokalnej stacji roboczej za pomocą [interfejsu wiersza polecenia platformy Azure (CLI)](/cli/azure/install-azure-cli) lub [programu Azure PowerShell](/powershell/azure/overview).  

Wiersza polecenia platformy Azure Użyj następujących poleceń, aby zalogować się, wybierz subskrypcję i przesyłanie strumieniowe plików dziennika:

```azurecli
az login
az account list
az account set <subscriptionNameOrId>
az webapp log tail --resource-group <resource group name> --name <function app name>
```

Dla programu Azure PowerShell Użyj następujących poleceń, aby dodać konta platformy Azure, wybierz subskrypcję i przesyłanie strumieniowe plików dziennika:

```powershell
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "<subscription name>" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name <function app name> -Tail
```

Aby uzyskać więcej informacji, zobacz [jak przesyłanie strumieniowe dzienników](../app-service/web-sites-enable-diagnostic-log.md#streamlogs).

### <a name="viewing-log-files-locally"></a>Wyświetlanie plików dziennika lokalnie

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji zawierają następujące zasoby:

* [Application Insights](/azure/application-insights/)
* [Platforma ASP.NET Core rejestrowania](/aspnet/core/fundamentals/logging/)
