---
title: Eksplorowanie dzienników śledzenia .NET w usłudze Application Insights
description: Wyszukaj dzienniki wygenerowane za pomocą śledzenia, NLog i Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mbullwin
ms.openlocfilehash: 8c722eb0db3022620ba03e02dd2ae00f97a78f28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691159"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Poznaj podstawowe.NET/.NET dzienniki śledzenia w usłudze Application Insights

Jeśli używasz ILogger, NLog, log4Net lub System.Diagnostics.Trace do śledzenia diagnostycznego w aplikacji ASP.NET/ASP.NET podstawowej, może mieć dzienniki wysyłane do [usługi Azure Application Insights][start], gdzie możesz można eksplorować i ich wyszukiwanie. Dzienniki zostaną scalone z innych danych telemetrycznych pochodzących z aplikacji, tak, aby zidentyfikować dane śledzenia skojarzony z obsługi każdego żądania użytkownika i skoreluj je z innymi zdarzeń i raporty o wyjątkach.

> [!NOTE]
> Czy potrzebujesz modułu przechwytywania dziennika? Jest przydatne karty rejestratorów firm 3, ale jeśli nie są już używane, NLog, log4Net lub System.Diagnostics.Trace, należy wziąć pod uwagę tylko wywoływania [Application Insights z metody TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) bezpośrednio.
>
>

## <a name="install-logging-on-your-app"></a>Zainstaluj logowanie w swojej aplikacji
Zainstaluj preferowanej struktury rejestrowania wybrany w projekcie. Powinno to spowodować, że wpis w pliku app.config lub web.config.

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Skonfiguruj usługę Application Insights do zbierania dzienników
**[Dodaj usługę Application Insights do projektu](../../azure-monitor/app/asp-net.md)**  Jeśli użytkownik jeszcze nie. Zobaczysz opcję uwzględniania dziennika modułu zbierającego.

Lub **Konfiguruj usługę Application Insights** klikając prawym przyciskiem myszy projekt w Eksploratorze rozwiązań. Wybierz opcję, aby **Konfigurowanie zbierania śladu**.

*Nie opcji Application Insights menu lub dziennik modułu zbierającego?* Spróbuj [Rozwiązywanie problemów z](#troubleshooting).

## <a name="manual-installation"></a>Instalacja ręczna
Ta metoda swój typ projektu nie jest obsługiwana przez Instalatora usługi Application Insights (na przykład projektu pulpitu Windows).

1. Jeśli planujesz użyć log4Net i NLog, należy go zainstalować w projekcie.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.
3. Wyszukaj „Application Insights”
4. Wybierz jedną z następujących pakietów:

   - Aby uzyskać ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Aby uzyskać NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Aby uzyskać Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Aby uzyskać System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Pakiet NuGet instaluje konieczne zestawy, a jeśli ma to zastosowanie modyfikuje plik web.config lub app.config.

## <a name="ilogger"></a>ILogger

Przykłady użycia aplikacji ILogger Insights wdrożenia za pomocą aplikacji konsoli i ASP.NET Core, zapoznaj się z tą [artykułu](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Wstawianie wywołań dziennik diagnostyczny
Jeśli używasz System.Diagnostics.Trace, będą się typowe wywołanie:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Jeśli wolisz, log4net i NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>Używanie zdarzeń EventSource
Można skonfigurować [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) zdarzenia do wysłania do usługi Application Insights jako ślady. Najpierw zainstaluj `Microsoft.ApplicationInsights.EventSourceListener` pakietu NuGet. Następnie Edytuj `TelemetryModules` części [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pliku.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Dla każdego źródła można ustawić następujące parametry:
 * `Name` Określa nazwę źródła zdarzeń do zbierania.
 * `Level` Określa poziom rejestrowania, aby zebrać. Może być jednym z `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Opcjonalnie) określa wartość całkowitą kombinacje słów kluczowych do użycia.

## <a name="using-diagnosticsource-events"></a>Przy użyciu DiagnosticSource zdarzeń
Można skonfigurować [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) zdarzenia do wysłania do usługi Application Insights jako ślady. Najpierw zainstaluj [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) pakietu NuGet. Następnie Edytuj `TelemetryModules` części [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pliku.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Dla każdego DiagnosticSource do śledzenia, Dodaj wpis z `Name` atrybut ustawiony na nazwę Twojej DiagnosticSource.

## <a name="using-etw-events"></a>Używanie zdarzeń ETW.
Można skonfigurować zdarzenia ETW do wysłania do usługi Application Insights jako ślady. Najpierw zainstaluj `Microsoft.ApplicationInsights.EtwCollector` pakietu NuGet. Następnie Edytuj `TelemetryModules` części [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pliku.

> [!NOTE] 
> Zdarzenia ETW mogą być zbierane tylko, jeśli proces obsługujący zestawu SDK jest uruchomiona z tożsamością, który jest elementem członkowskim "Użytkownicy dziennika wydajności" lub Administratorzy.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Dla każdego źródła można ustawić następujące parametry:
 * `ProviderName` jest nazwą dostawcy funkcji ETW, aby zebrać.
 * `ProviderGuid` Określa identyfikator GUID dostawcy funkcji ETW, aby zebrać, można użyć zamiast `ProviderName`.
 * `Level` Ustawia poziom rejestrowania, aby zebrać. Może być jednym z `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Opcjonalnie) ustawia wartość całkowitą kombinacje — słowo kluczowe do użycia.

## <a name="using-the-trace-api-directly"></a>Bezpośrednio za pomocą śledzenia interfejsu API
Śledzenia usługi Application Insights API można wywoływać bezpośrednio. Rejestrowanie kart używać tego interfejsu API.

Na przykład:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Zaletą TrackTrace jest umieszczenie stosunkowo długo dane w komunikacie. Na przykład można zakodować danych POST.

Ponadto można dodać jej poziom ważności do wiadomości. I podobnie jak inne dane telemetryczne, można dodać wartości właściwości, które można użyć w celu filtrowania lub wyszukiwania dla różnych zestawów danych śledzenia. Na przykład:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

To, czy włączyć w [wyszukiwania][diagnostic], aby łatwo odfiltrować wszystkie komunikaty o określonej ważności, odnoszące się do określonej bazy danych.

## <a name="explore-your-logs"></a>Zapoznaj się z dzienników
Uruchom aplikację, albo w trybie debugowania, albo wdrożyć go na żywo.

W bloku przeglądu aplikacji w [portalu usługi Application Insights][portal], wybierz [wyszukiwania][diagnostic].

Możliwe, na przykład:

* Filtrowanie danych śledzenia dziennika lub elementy z określonymi właściwościami
* Sprawdź, czy konkretny element szczegółowo.
* Znajdź inne telemetrię odnoszące się do tego samego żądania użytkownika (oznacza to, z tym samym identyfikatorem OperationId)
* Zapisz konfigurację tę stronę jako ulubione

> [!NOTE]
> **Pobieranie próbek.** Jeśli aplikacja wysyła dużo danych, a używasz zestawu SDK usługi Application Insights dla technologii ASP.NET w wersji 2.0.0-beta3 lub nowszej, może działać funkcja adaptacyjnego próbkowania, powodując wysyłanie tylko ułamka telemetrii. [Dowiedz się więcej na temat próbkowania.](../../azure-monitor/app/sampling.md)
>
>

## <a name="next-steps"></a>Kolejne kroki
[Diagnozowanie awarii i wyjątków w programie ASP.NET:][exceptions]

[Dowiedz się więcej na temat wyszukiwania][diagnostic].

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="how-do-i-do-this-for-java"></a>Jak to zrobić to dla języka Java?
Użyj [kart dziennika Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Nie ma żadnej opcji usługi Application Insights z menu kontekstowego projektu
* Sprawdź, czy narzędzi usługi Application Insights są zainstalowane na tym komputerze deweloperskim. W Visual Studio menu Narzędzia, rozszerzenia i aktualizacje Wyszukaj narzędzia usługi Application Insights. Jeśli nie ma go na karcie zainstalowany, otwórz kartę Online i zainstaluj go.
* Może to być typ projektu, które nie są obsługiwane przez narzędzia Application Insights. Użyj [Instalacja ręczna](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Nie opcji karty dziennika w narzędziu konfiguracji
* Należy najpierw zainstalować struktury rejestrowania.
* Jeśli używasz System.Diagnostics.Trace, upewnij się, że [skonfigurowany w `web.config` ](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Masz masz najnowszą wersję usługi Application Insights? W programie Visual Studio **narzędzia** menu, wybierz **rozszerzenia i aktualizacje**, a następnie otwórz **aktualizacje** kartę. Jeśli istnieją narzędzia Developer Analytics kliknij, aby go zaktualizować.

### <a name="emptykey"></a>Otrzymuję komunikat o błędzie "klucz Instrumentacji nie może być pusty"
Wygląda na to zainstalowany pakiet Nuget karta Rejestrowanie bez konieczności instalowania usługi Application Insights.

W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy `ApplicationInsights.config` i wybierz polecenie **aktualizacji Application Insights**. Zobaczysz okno dialogowe, które umożliwiające logowanie do platformy Azure, a następnie utwórz zasób usługi Application Insights lub ponowne użycie istniejącej. Który powinno rozwiązać go.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Mogę zobaczyć śladów w wyszukiwaniu diagnostycznym, ale nie innych zdarzeń
Czasami może potrwać trochę czasu, zanim wszystkie zdarzenia i żądania, które można pobrać za pośrednictwem potoku.

### <a name="limits"></a>Jak dużo danych jest zachowywana?
Kilka czynników wpływa na ilość danych zachowywanych. Zobacz [limity](../../azure-monitor/app/api-custom-events-metrics.md#limits) sekcji na stronie metryki zdarzenia klienta, aby uzyskać więcej informacji. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Nie widzę niektórych wpisy dziennika, które mogę oczekiwać
Jeśli aplikacja wysyła dużo danych, a używasz zestawu SDK usługi Application Insights dla technologii ASP.NET w wersji 2.0.0-beta3 lub nowszej, może działać funkcja adaptacyjnego próbkowania, powodując wysyłanie tylko ułamka telemetrii. [Dowiedz się więcej na temat próbkowania.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Następne kroki
* [Konfigurowanie dostępności i czasu odpowiedzi testów][availability]
* [Rozwiązywanie problemów][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
