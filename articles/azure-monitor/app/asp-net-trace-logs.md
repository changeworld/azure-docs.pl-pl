---
title: Eksplorowanie dzienników śledzenia platformy .NET w usłudze Application Insights
description: Dzienniki wyszukiwania generowane przez Trace, NLog lub Log4Net.
ms.topic: conceptual
ms.date: 05/08/2019
ms.openlocfilehash: 273d5a2f4e1155541e159332312bdaa68aa175d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276272"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Eksploruj dzienniki śledzenia .NET/.NET Core i Python w usłudze Application Insights

Wysyłaj dzienniki śledzenia diagnostycznego dla aplikacji ASP.NET/ASP.NET Core z aplikacji ILogger, NLog, log4Net lub System.Diagnostics.Trace do [usługi Azure Application Insights][start]. W przypadku aplikacji języka Python należy wysyłać dzienniki śledzenia diagnostycznego przy użyciu usługi AzureLogHandler w usłudze OpenCensus Python for Azure Monitor. Następnie można je eksplorować i przeszukiwać. Te dzienniki są scalane z innymi plikami dziennika z aplikacji, dzięki czemu można zidentyfikować ślady, które są skojarzone z każdym żądaniem użytkownika i skorelować je z innymi zdarzeniami i raportami wyjątków.

> [!NOTE]
> Czy potrzebny jest moduł przechwytywania dzienników? Jest to przydatna karta dla rejestratorów innych firm. Ale jeśli nie używasz już NLog, log4Net lub System.Diagnostics.Trace, należy rozważyć tylko [**wywołanie Application Insights TrackTrace()**](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) bezpośrednio.
>
>
## <a name="install-logging-on-your-app"></a>Instalowanie logowania w aplikacji
Zainstaluj wybraną platformę rejestrowania w projekcie, co powinno spowodować wpis w pliku app.config lub web.config.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Konfigurowanie usługi Application Insights do zbierania dzienników
[Dodaj usługa Application Insights do projektu,](../../azure-monitor/app/asp-net.md) jeśli jeszcze tego nie zrobiono. Zostanie wyświetlenie opcji dołączania modułu zbierającego dzienniki.

Możesz też kliknąć prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, aby **skonfigurować aplikację Insights**. Wybierz opcję **Konfiguruj kolekcję śledzenia.**

> [!NOTE]
> Brak menu aplikacji insights lub opcji modułu zbierającego dzienniki? Spróbuj [rozwiązać problem](#troubleshooting).

## <a name="manual-installation"></a>Instalacja ręczna
Tej metody należy użyć, jeśli typ projektu nie jest obsługiwany przez instalator usługi Application Insights (na przykład projekt pulpitu systemu Windows).

1. Jeśli planujesz używać log4Net lub NLog, zainstaluj go w projekcie.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz pozycję **Zarządzaj pakietami NuGet**.
3. Wyszukaj hasło "Usługa Application Insights".
4. Wybierz jeden z następujących pakietów:

   - Dla ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Dla NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Dla Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Dla system.diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Pakiet NuGet instaluje niezbędne zestawy i modyfikuje web.config lub app.config, jeśli ma to zastosowanie.

## <a name="ilogger"></a>ILogger

Przykłady użycia implementacji ILogger usługi Application Insights z aplikacjami konsoli i ASP.NET Core można znaleźć w [aplikacji ApplicationInsightsLoggerProvider dla dzienników iloggera .NET Core ILogger](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Wstawianie wywołań dziennika diagnostycznego
Jeśli używasz System.Diagnostics.Trace, typowe wywołanie będzie:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Jeśli wolisz log4net lub NLog, użyj:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Używanie zdarzeń EventSource
Zdarzenia [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) mają być wysyłane do usługi Application Insights jako ślady. Najpierw zainstaluj `Microsoft.ApplicationInsights.EventSourceListener` pakiet NuGet. Następnie edytuj `TelemetryModules` sekcję pliku [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Dla każdego źródła można ustawić następujące parametry:
 * **Nazwa** określa nazwę EventSource do zbierania.
 * **Poziom** określa poziom rejestrowania do zebrania: *Krytyczny*, *Błąd*, *Informacje*, *LogAlways*, *Pełne*lub *Ostrzeżenie*.
 * **Słowa kluczowe** (opcjonalnie) określają wartość całkowitą kombinacji słów kluczowych do użycia.

## <a name="use-diagnosticsource-events"></a>Użyj zdarzeń DiagnosticSource
Zdarzenia [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) mają być wysyłane do usługi Application Insights jako ślady. Najpierw zainstaluj [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) pakiet NuGet. Następnie edytuj sekcję "TelemetryModules" pliku [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Dla każdego DiagnosticSource, które chcesz śledzić, dodaj wpis z atrybutem **Nazwa** ustawionym na nazwę źródła diagnostycznego.

## <a name="use-etw-events"></a>Korzystanie ze zdarzeń ETW
Zdarzenia śledzenia zdarzeń dla systemu Windows (ETW) mają być wysyłane do usługi Application Insights jako ślady. Najpierw zainstaluj `Microsoft.ApplicationInsights.EtwCollector` pakiet NuGet. Następnie edytuj sekcję "TelemetryModules" pliku [ApplicationInsights.config.](../../azure-monitor/app/configuration-with-applicationinsights-config.md)

> [!NOTE] 
> Zdarzenia ETW mogą być zbierane tylko wtedy, gdy proces, który obsługuje SDK działa pod tożsamością, która jest członkiem użytkowników dziennika wydajności lub administratorów.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Dla każdego źródła można ustawić następujące parametry:
 * **ProviderName** to nazwa dostawcy ETW do zebrania.
 * **ProviderGuid** określa identyfikator GUID dostawcy ETW do zbierania. Może być używany zamiast `ProviderName`.
 * **Poziom** ustawia poziom rejestrowania do zbierania. Może to być *krytyczny*, *błąd*, *informacyjny*, *LogAlways*, *pełne*lub *ostrzeżenie*.
 * **Słowa kluczowe** (opcjonalnie) ustawiają wartość całkowitą kombinacji słów kluczowych do użycia.

## <a name="use-the-trace-api-directly"></a>Bezpośrednio użyj interfejsu API śledzenia
Można wywołać interfejs API śledzenia usługi Application Insights bezpośrednio. Karty rejestrowania używają tego interfejsu API.

Przykład:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Zaletą TrackTrace jest to, że można umieścić stosunkowo długie dane w wiadomości. Na przykład można tam zakodować dane POST.

Można również dodać poziom ważności do wiadomości. Podobnie jak inne dane telemetryczne, można dodać wartości właściwości, aby ułatwić filtrowanie lub wyszukiwanie różnych zestawów śladów. Przykład:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Umożliwi to łatwe odfiltrowanie w [search][diagnostic] wszystkie komunikaty o określonym poziomie ważności, które odnoszą się do określonej bazy danych.

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler dla OpenCensus Python
Program obsługi dzienników usługi Azure Monitor umożliwia eksportowanie dzienników języka Python do usługi Azure Monitor.

Instrument aplikacji za pomocą [opencensus Python SDK](../../azure-monitor/app/opencensus-python.md) dla usługi Azure Monitor.

W tym przykładzie pokazano, jak wysłać dziennik poziomu ostrzeżenia do usługi Azure Monitor.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>Eksploruj swoje dzienniki
Uruchom aplikację w trybie debugowania lub wdrożyć ją na żywo.

W okienku przeglądu aplikacji w [portalu Usługi Application Insights][portal]wybierz pozycję [Wyszukaj][diagnostic].

Można na przykład:

* Filtruj ślady dziennika lub na elementach o określonych właściwościach.
* Sprawdź szczegółowo określony element.
* Znajdź inne dane dziennika systemu, który odnosi się do tego samego żądania użytkownika (ma ten sam OperationId).
* Zapisz konfigurację strony jako ulubioną.

> [!NOTE]
>Jeśli aplikacja wysyła dużo danych i używasz SDK usługi Application Insights dla ASP.NET wersji 2.0.0-beta3 lub nowszej, *adaptacyjna funkcja próbkowania* może działać i wysyłać tylko część danych telemetrycznych. [Dowiedz się więcej na temat próbkowania.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="how-do-i-do-this-for-java"></a>Jak to zrobić w przypadku oprogramowania Java?
Użyj [kart dziennika Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>W menu kontekstowym projektu nie ma opcji usługi Application Insights
* Upewnij się, że narzędzia do analizy deweloperów są zainstalowane na komputerze deweloperskim. W witrynie Visual Studio **Tools** > **Extensions and Updates**szukaj narzędzi do analizy dla **deweloperów**. Jeśli nie ma go na karcie **Zainstalowane,** otwórz kartę **Online** i zainstaluj ją.
* Może to być typ projektu, który devloper Analytics Tools nie obsługuje. Użyj [instalacji ręcznej](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>W narzędziu konfiguracyjnym nie ma opcji karty dziennika
* Najpierw zainstaluj strukturę rejestrowania.
* Jeśli używasz System.Diagnostics.Trace, upewnij się, że masz go [skonfigurowany w *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Upewnij się, że masz najnowszą wersję usługi Application Insights. W programie Visual Studio przejdź do**rozszerzenia i aktualizacje** **narzędzi** > i otwórz kartę **Aktualizacje.** Jeśli **narzędzia do analizy dla deweloperów** są dostępne, wybierz je, aby je zaktualizować.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>Pojawia się komunikat o błędzie "Klucz instrumentacji nie może być pusty"
Prawdopodobnie zainstalowano pakiet Nuget karty rejestrowania bez instalowania usługi Application Insights. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy *pozycję ApplicationInsights.config*i wybierz polecenie **Aktualizuj wgląd w aplikacje**. Zostanie wyświetlony monit o zalogowanie się na platformie Azure i utworzenie zasobu usługi Application Insights lub ponowne użycie istniejącego. To powinno rozwiązać problem.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Widzę ślady, ale nie inne zdarzenia w wyszukiwaniu diagnostycznym
Może upłynąć trochę czasu dla wszystkich zdarzeń i żądań, aby przejść przez potok.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Ile danych jest zachowywanych?
Kilka czynników wpływa na ilość danych, które są przechowywane. Aby uzyskać więcej informacji, zobacz sekcję [limity](../../azure-monitor/app/api-custom-events-metrics.md#limits) na stronie metryki zdarzeń klienta.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Nie widzę niektórych wpisów dziennika, których oczekiwałem
Jeśli aplikacja wysyła obszerne ilości danych i używasz zestawu SDK usługi Application Insights dla ASP.NET wersji 2.0.0-beta3 lub nowszej, adaptacyjna funkcja próbkowania może działać i wysyłać tylko część danych telemetrycznych. [Dowiedz się więcej na temat próbkowania.](../../azure-monitor/app/sampling.md)

## <a name="next-steps"></a><a name="add"></a>Następne kroki

* [Diagnozowanie błędów i wyjątków w ASP.NET][exceptions]
* [Dowiedz się więcej o wyszukiwaniu][diagnostic]
* [Konfigurowanie testów dostępności i reakcji][availability]
* [Rozwiązywanie problemów][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
