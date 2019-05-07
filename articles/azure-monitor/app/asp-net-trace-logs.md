---
title: Eksplorowanie dzienników śledzenia .NET w usłudze Application Insights
description: Wyszukaj dzienniki generowane przez śledzenia, NLog i Log4Net.
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
ms.openlocfilehash: 74cb1b3ec4e0570aa4316e6f45e99719f36815d1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150714"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Poznaj podstawowe.NET/.NET dzienniki śledzenia w usłudze Application Insights

Wysyłanie dzienników śledzenia diagnostycznego dla aplikacji ASP.NET/ASP.NET Core z ILogger, NLog, log4Net lub System.Diagnostics.Trace, aby [usługi Azure Application Insights][start]. Następnie możesz eksplorować i ich wyszukiwanie. Te dzienniki są scalane z innymi plikami dzienników z aplikacji, dzięki czemu można zidentyfikować śladów, które są skojarzone z każdego żądania użytkownika i skoreluj je z innymi zdarzeń i raporty dotyczące wyjątków.

> [!NOTE]
> Czy potrzebujesz modułu Przechwytywanie dziennika? Jest przydatne karty rejestratorów innych firm. Ale jeśli nie są już używane, System.Diagnostics.Trace, NLog i log4Net, należy wziąć pod uwagę tylko wywoływania [ **Application Insights z metody TrackTrace()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) bezpośrednio.
>
>
## <a name="install-logging-on-your-app"></a>Zainstaluj logowanie w swojej aplikacji
Zainstaluj preferowanej struktury rejestrowania wybrany w projekcie, co powinno spowodować wpis w pliku app.config lub web.config.

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
[Dodaj usługę Application Insights do projektu](../../azure-monitor/app/asp-net.md) Jeśli użytkownik jeszcze nie. Zobaczysz opcję uwzględniania dziennika modułu zbierającego.

Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, aby **Konfiguruj usługę Application Insights**. Wybierz **Konfigurowanie zbierania śladu** opcji.

> [!NOTE]
> Nie opcji Application Insights menu lub dziennik modułu zbierającego? Spróbuj [Rozwiązywanie problemów z](#troubleshooting).

## <a name="manual-installation"></a>Instalacja ręczna
Ta metoda swój typ projektu nie jest obsługiwana przez Instalatora usługi Application Insights (na przykład projektu pulpitu Windows).

1. Jeśli planujesz użyć log4Net i NLog, należy go zainstalować w projekcie.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz **Zarządzaj pakietami NuGet**.
3. Wyszukaj "Application Insights".
4. Wybierz jedną z następujących pakietów:

   - Aby uzyskać ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Aby uzyskać NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Aby uzyskać Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Aby uzyskać System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

Pakiet NuGet instaluje konieczne zestawy, a także modyfikuje plik web.config lub app.config, jeśli jest to konieczne.

## <a name="ilogger"></a>ILogger

Aby uzyskać przykłady przy użyciu implementacji Application Insights ILogger z aplikacji konsoli i ASP.NET Core, zobacz [dzienniki ApplicationInsightsLoggerProvider dla platformy .NET Core ILogger](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Wstawianie wywołań dziennik diagnostyczny
Jeśli używasz System.Diagnostics.Trace, będą się typowe wywołanie:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Jeśli wolisz, log4net i NLog, należy użyć:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Korzystanie ze zdarzeń EventSource
Można skonfigurować [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) zdarzenia do wysłania do usługi Application Insights jako ślady. Najpierw zainstaluj `Microsoft.ApplicationInsights.EventSourceListener` pakietu NuGet. Następnie Edytuj `TelemetryModules` części [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pliku.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Dla każdego źródła można ustawić następujące parametry:
 * **Nazwa** Określa nazwę źródła zdarzeń do zbierania.
 * **Poziom** określa poziom rejestrowania, aby zebrać: *Krytyczne*, *błąd*, *informacyjny*, *LogAlways*, *pełne*, lub *ostrzeżenie*.
 * **Słowa kluczowe** (opcjonalnie) określ wartość całkowitą kombinacje — słowo kluczowe do użycia.

## <a name="use-diagnosticsource-events"></a>Korzystanie ze zdarzeń DiagnosticSource
Można skonfigurować [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) zdarzenia do wysłania do usługi Application Insights jako ślady. Najpierw zainstaluj [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) pakietu NuGet. Następnie przeprowadź edycję sekcji "TelemetryModules" [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pliku.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Dla każdego DiagnosticSource do śledzenia, Dodaj wpis z **nazwa** atrybut ustawiony na nazwę Twojej DiagnosticSource.

## <a name="use-etw-events"></a>Korzystanie ze zdarzeń ETW.
Można skonfigurować śledzenie zdarzeń dla Windows (ETW) zdarzenia do wysłania do usługi Application Insights jako ślady. Najpierw zainstaluj `Microsoft.ApplicationInsights.EtwCollector` pakietu NuGet. Następnie przeprowadź edycję sekcji "TelemetryModules" [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pliku.

> [!NOTE] 
> Zdarzenia ETW mogą być zbierane tylko, jeśli proces, który hostuje zestaw SDK jest uruchomiony przy użyciu tożsamości, który jest elementem członkowskim Użytkownicy dziennika wydajności lub Administratorzy.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Dla każdego źródła można ustawić następujące parametry:
 * **ProviderName** jest nazwą dostawcy funkcji ETW, aby zebrać.
 * **Identyfikator_guid_dostawcy** Określa identyfikator GUID dostawcy funkcji ETW, aby zebrać. Mogą być używane zamiast `ProviderName`.
 * **Poziom** ustawia poziom rejestrowania, aby zebrać. Może być *krytyczny*, *błąd*, *komunikat o charakterze informacyjnym*, *LogAlways*, *pełne*, lub *Ostrzeżenie*.
 * **Słowa kluczowe** (opcjonalnie) wartość całkowitą kombinacje — słowo kluczowe do użycia.

## <a name="use-the-trace-api-directly"></a>Używany jest tryb ślad interfejsu API bezpośrednio
Śledzenia usługi Application Insights API można wywoływać bezpośrednio. Rejestrowanie kart używać tego interfejsu API.

Na przykład:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Zaletą TrackTrace jest umieszczenie stosunkowo długo dane w komunikacie. Na przykład możesz zakodować dane POST.

Poziom ważności można również dodać do komunikatu. I podobnie jak inne dane telemetryczne, można dodać wartości właściwości w celu filtrowania lub wyszukiwania dla różnych zestawów danych śledzenia. Na przykład:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Łatwe filtrowanie w umożliwi [wyszukiwania] [ diagnostic] wszystkie komunikaty o określonej ważności, które odnoszą się do określonej bazy danych.

## <a name="explore-your-logs"></a>Zapoznaj się z dzienników
Uruchom aplikację w trybie debugowania, albo wdrożyć go na żywo.

W okienku przeglądu swojej aplikacji w [portalu usługi Application Insights][portal], wybierz opcję [wyszukiwania][diagnostic].

Możliwe, na przykład:

* Filtrowanie danych śledzenia dziennika lub w elementach z określonymi właściwościami.
* Sprawdź, czy konkretny element szczegółowo.
* Znajdź inne dane dziennika systemu, które odnoszą się do tego samego żądania użytkownika (ma taki sam identyfikator OperationId).
* Zapisz konfigurację strony jako ulubiony.

> [!NOTE]
>Jeśli Twoja aplikacja wysyła dużo danych, a używasz zestawu SDK Application Insights dla platformy ASP.NET w wersji 2.0.0-beta3 lub nowszej, *próbkowanie adaptacyjne* funkcja może działać i wysłać tylko część danych telemetrycznych. [Dowiedz się więcej na temat próbkowania.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="how-do-i-do-this-for-java"></a>Jak to zrobić to dla języka Java?
Użyj [kart dziennika Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Nie ma żadnej opcji usługi Application Insights z menu kontekstowego projektu
* Upewnij się, że narzędzia usługi Application Insights jest zainstalowany na komputerze deweloperskim. W programie Visual Studio **narzędzia** > **rozszerzenia i aktualizacje**, poszukaj **Application Insights Tools**. Jeśli nie jest na **zainstalowane** otwartą kartą **Online** kartę i zainstaluj go.
* Może to być typ projektu, który nie obsługuje narzędzia usługi Application Insights. Użyj [Instalacja ręczna](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Nie ma żadnej opcji karty dziennika w narzędziu konfiguracji
* Najpierw zainstaluj struktury rejestrowania.
* Jeśli używasz System.Diagnostics.Trace, upewnij się, że jest używana [skonfigurowane w *web.config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Upewnij się, że masz najnowszą wersję usługi Application Insights. W programie Visual Studio, przejdź do **narzędzia** > **rozszerzenia i aktualizacje**, a następnie otwórz **aktualizacje** kartę. Jeśli **Developer Analytics Tools** jest tam, wybierz ją, aby go zaktualizować.

### <a name="emptykey"></a>Otrzymuję komunikat o błędzie "klucz Instrumentacji nie może być pusty"
Pakiet Nuget karty rejestrowania jest prawdopodobnie zainstalowane bez instalowania usługi Application Insights. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy *plik ApplicationInsights.config*i wybierz **aktualizacji Application Insights**. Zostanie wyświetlony monit logowanie do platformy Azure i Utwórz zasób usługi Application Insights lub ponownego użycia istniejącej. Który powinno rozwiązać problem.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Mogę zobaczyć dane śledzenia, ale nie innych zdarzeń w wyszukiwaniu diagnostycznym
Może upłynąć trochę czasu, zanim wszystkie zdarzenia i żądania, które można pobrać za pośrednictwem potoku.

### <a name="limits"></a>Jak dużo danych jest zachowywana?
Kilka czynników wpływa na ilość danych, które są przechowywane. Aby uzyskać więcej informacji, zobacz [limity](../../azure-monitor/app/api-custom-events-metrics.md#limits) części odbiorcy zdarzeń metryki.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Nie widzę niektórych oczekiwanych wpisów dziennika
Jeśli Twoja aplikacja przesyła obszernych ilości danych, a używasz zestawu SDK Application Insights dla platformy ASP.NET w wersji 2.0.0-beta3 lub nowszej, funkcja adaptacyjnego próbkowania może działać i wysłać tylko część danych telemetrycznych. [Dowiedz się więcej na temat próbkowania.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Następne kroki

* [Diagnozowanie awarii i wyjątków w programie ASP.NET:][exceptions]
* [Dowiedz się więcej na temat wyszukiwania][diagnostic]
* [Konfigurowanie dostępności i czasu odpowiedzi testów][availability]
* [Rozwiązywanie problemów][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md