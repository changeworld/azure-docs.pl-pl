---
title: Eksploruj dzienniki śledzenia platformy .NET w usłudze Application Insights
description: Wyszukaj dzienniki generowane z śledzenia, NLog i Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 5ffb758fe5fa42be6323de06afbfb38068ae1926
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296173"
---
# <a name="explore-net-trace-logs-in-application-insights"></a>Eksploruj dzienniki śledzenia platformy .NET w usłudze Application Insights
Jeśli używasz NLog, log4Net lub System.Diagnostics.Trace do śledzenia diagnostycznego w aplikacji programu ASP.NET może mieć dzienniki wysyłane do [Azure Application Insights][start], gdzie można eksplorować i ich wyszukiwania. Dzienniki zostaną scalone z innymi telemetrii pochodzące z aplikacji, aby zidentyfikować dane śledzenia skojarzone z obsługi każdego żądania użytkownika i skorelowania je za pomocą innych zdarzeń i raporty wyjątek.

> [!NOTE]
> Potrzebujesz modułu przechwytywania dziennika? Jest to przydatne karta dla firm 3rd rejestratorów, ale jeśli nie używasz już NLog, log4Net lub System.Diagnostics.Trace, należy wziąć pod uwagę tylko wywołania [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) bezpośrednio.
>
>

## <a name="install-logging-on-your-app"></a>Zainstaluj logowania aplikacji
Zainstaluj strukturę z wybranym rejestrowania w projekcie. Powinno to spowodować, że wpis w pliku app.config lub web.config.

Jeśli używasz System.Diagnostics.Trace, należy dodać wpis do pliku web.config:

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener"
             type="System.Diagnostics.TextWriterTraceListener"
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```
## <a name="configure-application-insights-to-collect-logs"></a>Konfiguruj usługę Application Insights do zbierania dzienników
**[Dodawanie usługi Application Insights do projektu](app-insights-asp-net.md)**  Jeśli możesz jeszcze nie. Zobaczysz opcję uwzględniania modułu zbierającego dzienniki.

Lub **Konfiguruj usługę Application Insights** przez kliknięcie prawym przyciskiem myszy projekt w Eksploratorze rozwiązań. Wybierz opcję **Konfigurowanie zbierania danych śledzenia**.

*Brak usługi Application Insights menu lub dziennika modułu zbierającego opcji?* Spróbuj [Rozwiązywanie problemów z](#troubleshooting).

## <a name="manual-installation"></a>Instalacja ręczna
Użyj tej metody, jeśli typ swojego projektu nie jest obsługiwany przez Instalatora usługi Application Insights (na przykład projekt pulpitu systemu Windows).

1. Jeśli planujesz użyć log4Net, NLog, należy go zainstalować w projekcie.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet**.
3. Wyszukaj „Application Insights”
4. Wybierz odpowiedni pakiet — jeden z:

   * Microsoft.ApplicationInsights.TraceListener (do przechwytywania System.Diagnostics.Trace wywołań)
   * Microsoft.ApplicationInsights.EventSourceListener (do przechwytywania zdarzeń EventSource)
   * Microsoft.ApplicationInsights.EtwListener (do przechwytywania zdarzeń ETW)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

Pakiet NuGet instaluje konieczne zestawy, a także modyfikuje plik web.config lub app.config.

## <a name="insert-diagnostic-log-calls"></a>Wstawianie wywołania dziennik diagnostyczny
Jeśli używasz System.Diagnostics.Trace, będzie Typowe wywołania:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Jeśli wolisz, log4net lub NLog:

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>W przypadku używania zdarzeń źródła zdarzeń
Można skonfigurować [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) zdarzenia mają być wysyłane do usługi Application Insights jako dane śledzenia. Najpierw zainstaluj `Microsoft.ApplicationInsights.EventSourceListener` pakietu NuGet. Następnie Edytuj `TelemetryModules` sekcji [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) pliku.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Dla każdego źródła można ustawić następujące parametry:
 * `Name` Określa nazwę elementu EventSource do zbierania.
 * `Level` Określa poziom rejestrowania do zbierania. Może być jednym z `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Opcjonalnie) określa wartość całkowita kombinacji słowa kluczowe do użycia.

## <a name="using-diagnosticsource-events"></a>W przypadku używania DiagnosticSource zdarzeń
Można skonfigurować [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) zdarzenia mają być wysyłane do usługi Application Insights jako dane śledzenia. Najpierw zainstaluj [ `Microsoft.ApplicationInsights.DiagnosticSourceListener` ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) pakietu NuGet. Następnie Edytuj `TelemetryModules` sekcji [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) pliku.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Dla każdego DiagnosticSource do śledzenia, Dodaj wpis z `Name` atrybutu Ustaw nazwę sieci DiagnosticSource.

## <a name="using-etw-events"></a>W przypadku używania zdarzeń ETW.
Można skonfigurować zdarzenia ETW mają być wysyłane do usługi Application Insights jako dane śledzenia. Najpierw zainstaluj `Microsoft.ApplicationInsights.EtwCollector` pakietu NuGet. Następnie Edytuj `TelemetryModules` sekcji [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) pliku.

> [!NOTE] 
> Zdarzenia ETW mogą być zbierane tylko, jeśli proces obsługujący zestawu SDK jest uruchomiona w ramach tożsamości, który jest członkiem grupy "Użytkownicy dzienników wydajności" lub Administratorzy.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Dla każdego źródła można ustawić następujące parametry:
 * `ProviderName` jest nazwą dostawcy ETW do zbierania.
 * `ProviderGuid` Określa identyfikator GUID dostawców ETW, aby zebrać, można użyć zamiast `ProviderName`.
 * `Level` Ustawia poziom rejestrowania do zbierania. Może być jednym z `Critical`, `Error`, `Informational`, `LogAlways`, `Verbose`, `Warning`.
 * `Keywords` (Opcjonalnie) ustawia całkowitą kombinacji — słowo kluczowe do użycia.

## <a name="using-the-trace-api-directly"></a>Bezpośrednio za pomocą śledzenia interfejsu API
Śledzenia usługi Application Insights interfejsu API można wywołać bezpośrednio. Adaptery rejestrowania użycia tego interfejsu API.

Na przykład:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Zaletą TrackTrace jest umieszczenie danych stosunkowo długo w komunikacie. Na przykład można zakodować danych POST.

Ponadto można dodać poziomu ważności do wiadomości. I, podobnie jak inne dane telemetryczne, można dodać wartości właściwości, które można użyć filtru lub wyszukiwania dla różnych zestawów danych śledzenia. Na przykład:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

To, czy włączyć w [wyszukiwania][diagnostic], aby łatwo filtrować komunikaty poziom ważności określonego odnoszących się do określonej bazy danych.

## <a name="explore-your-logs"></a>Eksploruj dzienniki
Uruchamianie aplikacji, albo w trybie debugowania, albo wdrożyć go na żywo.

W bloku Przegląd aplikacji w [portalu Application Insights][portal], wybierz [wyszukiwania][diagnostic].

![W usłudze Application Insights wybierz wyszukiwania](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![Wyszukiwanie](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

Można na przykład:

* Odfiltrować ślady dziennika lub elementy o określonej właściwości
* Sprawdź, czy konkretny element szczegółowo.
* Znajdź inne dane telemetryczne odnoszących się do tego samego żądania użytkownika (czyli z tego samego OperationId)
* Zapisywanie konfiguracji tej strony jako ulubione

> [!NOTE]
> **Próbkowania.** Jeśli aplikacja wysyła dużo danych, a używasz zestawu SDK usługi Application Insights dla technologii ASP.NET w wersji 2.0.0-beta3 lub nowszej, może działać funkcja adaptacyjnego próbkowania, powodując wysyłanie tylko ułamka telemetrii. [Dowiedz się więcej na temat próbkowania.](app-insights-sampling.md)
>
>

## <a name="next-steps"></a>Kolejne kroki
[Diagnozowanie błędów i wyjątków w programie ASP.NET][exceptions]

[Dowiedz się więcej na temat wyszukiwania][diagnostic].

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="how-do-i-do-this-for-java"></a>Jak to zrobić to dla języka Java?
Użyj [kart dziennika Java](app-insights-java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Nie ma opcji menu kontekstowego projektu usługi Application Insights
* Sprawdź usługę Application Insights tools jest zainstalowany na tym komputerze deweloperskim. W programie Visual Studio menu Narzędzia, rozszerzenia i aktualizacje poszukaj Application Insights Tools. Jeśli nie jest zainstalowana karta, otwórz kartę Online, a następnie zainstaluj go.
* Może to być typ projektu nie jest obsługiwane przez narzędzia Application Insights. Użyj [Instalacja ręczna](#manual-installation).

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>Brak opcji karty dziennika narzędzia konfiguracji
* Należy najpierw zainstalować struktury rejestrowania.
* Jeśli używasz System.Diagnostics.Trace, upewnij się, że [skonfigurowany w `web.config` ](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Możesz znaleźć najnowszej wersji usługi Application Insights? W programie Visual Studio **narzędzia** menu, wybierz **rozszerzenia i aktualizacje**i Otwórz **aktualizacje** kartę. W przypadku narzędzia Developer Analytics, kliknij, aby go zaktualizować.

### <a name="emptykey"></a>Otrzymuję komunikat o błędzie "klucza Instrumentacji nie może być pusta"
Prawdopodobnie zainstalowano pakiet Nuget adapter rejestrowania bez instalowania usługi Application Insights.

W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy `ApplicationInsights.config` i wybierz polecenie **usługi Application Insights dla aktualizacji**. Okno dialogowe, które umożliwiające logowanie do platformy Azure, zostanie wyświetlony i Utwórz zasobu usługi Application Insights lub ponownego użycia istniejącej. Który powinno rozwiązać go.

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>Wyświetlane dane śledzenia diagnostycznego wyszukiwania, ale nie innych zdarzeń
Czasami może upłynąć trochę czasu zanim wszystkie zdarzenia i żądania można uzyskać za pośrednictwem potoku.

### <a name="limits"></a>Jak dużo danych jest zachowywana?
Istnieje kilka możliwych wpływ na ilość danych przechowywane. Zobacz [limity](app-insights-api-custom-events-metrics.md#limits) części strony metryki zdarzenia klienta, aby uzyskać więcej informacji. 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>Nie widzę niektórych oczekiwanych wpisów dziennika
Jeśli aplikacja wysyła dużo danych, a używasz zestawu SDK usługi Application Insights dla technologii ASP.NET w wersji 2.0.0-beta3 lub nowszej, może działać funkcja adaptacyjnego próbkowania, powodując wysyłanie tylko ułamka telemetrii. [Dowiedz się więcej na temat próbkowania.](app-insights-sampling.md)

## <a name="add"></a>Następne kroki
* [Konfigurowanie dostępności i testy czasu odpowiedzi][availability]
* [Rozwiązywanie problemów][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
