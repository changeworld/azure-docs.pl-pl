---
title: Diagnozowanie za pomocą strumienia metryk na żywo — usługa Azure Application Insights
description: Monitoruj aplikację sieci web w czasie rzeczywistym za pomocą niestandardowych metryk i diagnozuj problemy z transmisją na żywo błędów, śladów i zdarzeń.
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.openlocfilehash: ea0d786d0b8b96941d791bcc8e92fad9a869c5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670104"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Strumień metryk na żywo: monitor & diagnozowanie z 1-sekundowym opóźnieniem

Sonduj bijące serce twojej żywej, produkcyjnej aplikacji internetowej przy użyciu strumienia metryk na żywo z [usługi Application Insights.](../../azure-monitor/app/app-insights-overview.md) Wybieraj i filtruj dane i liczniki wydajności do oglądania w czasie rzeczywistym, bez żadnych zakłóceń w usłudze. Sprawdź ślady stosu z próbkowanych żądań i wyjątków. Wraz z [profilera](../../azure-monitor/app/profiler.md), [debuger migawki](../../azure-monitor/app/snapshot-debugger.md). Live Metrics Stream zapewnia zaawansowane i nieinwazyjne narzędzie diagnostyczne dla twojej witryny sieci Web na żywo.

Dzięki live metrics stream możesz:

* Sprawdź poprawność poprawki, gdy jest zwolniony, obserwując wydajność i liczbę błędów.
* Obserwuj efekt obciążeń testowych i diagnozuj problemy na żywo.
* Skoncentruj się na poszczególnych sesjach testowych lub odfiltruj znane problemy, wybierając i filtrując metryki, które chcesz oglądać.
* Pobierz ślady wyjątków, jak się zdarzyć.
* Eksperymentuj z filtrami, aby znaleźć najbardziej odpowiednie kluczowe wskaźniki wydajności.
* Monitoruj dowolny licznik wydajności systemu Windows na żywo.
* Łatwo zidentyfikować serwer, który ma problemy, i filtrować wszystkie KPI / live feed tylko do tego serwera.

[![Transmisja wideo na żywo](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Metryki na żywo są obecnie obsługiwane dla aplikacji ASP.NET, ASP.NET Core, Azure Functions, Java i Node.js.

## <a name="get-started"></a>Wprowadzenie

1. Jeśli usługa Application Insights nie została jeszcze [zainstalowana](../../azure-monitor/azure-monitor-app-hub.yml) w aplikacji sieci Web, zrób to teraz.
2. Oprócz standardowych pakietów usługi Application Insights [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) jest wymagany do włączenia strumienia metryk na żywo.
3. **Zaktualizuj do najnowszej wersji** pakietu usługi Application Insights. W programie Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami Nuget**. Otwórz kartę **Aktualizacje** i wybierz wszystkie pakiety Microsoft.ApplicationInsights.*

    Ponownie wdróż aplikację.

3. W [witrynie Azure portal](https://portal.azure.com)otwórz zasób usługi Application Insights dla aplikacji, a następnie otwórz usługę Live Stream.

4. [Zabezpiecz kanał sterowania,](#secure-the-control-channel) jeśli możesz używać poufnych danych, takich jak nazwy klientów w filtrach.

### <a name="no-data-check-your-server-firewall"></a>Brak danych? Sprawdzanie zapory serwera

Sprawdź, [czy porty wychodzące dla strumienia metryk na żywo](../../azure-monitor/app/ip-addresses.md#outgoing-ports) są otwarte w zaporze serwerów.

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Czym różni się strumień metryk na żywo od Eksploratora metryk i Analytics?

| |Transmisja strumieniowa na żywo | Eksplorator metryk i analiza |
|---|---|---|
|Opóźnienie|Dane wyświetlane w ciągu jednej sekundy|Zagregowane w ciągu minut|
|Brak retencji|Dane pozostają na wykresie, a następnie są odrzucane|[Dane przechowywane przez 90 dni](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|Na żądanie|Dane są przesyłane strumieniowo podczas otwierania metryk na żywo|Dane są wysyłane za każdym razem, gdy zestaw SDK jest zainstalowany i włączony|
|Bezpłatna|Nie ma żadnych opłat za transmisję danych na żywo|Z [zastrzeżeniem cen](../../azure-monitor/app/pricing.md)
|Próbkowanie|Wszystkie wybrane dane i liczniki są przesyłane. Próbkowania błędów i śladów stosu. TelemetryProcessors nie są stosowane.|Zdarzenia mogą być [próbkowanie](../../azure-monitor/app/api-filtering-sampling.md)|
|Kanał sterowania|Sygnały sterujące filtrem są wysyłane do SDK. Zalecamy zabezpieczenie tego kanału.|Komunikacja jest jednym ze sposobów, do portalu|

## <a name="select-and-filter-your-metrics"></a>Wybieranie i filtrowanie danych

(Dostępne z ASP.NET, ASP.NET Core i Azure Functions (w wersji 2).)

Niestandardowy wskaźnik KPI można monitorować na żywo, stosując dowolne filtry w dowolnej telemetrii usługi Application Insights z portalu. Kliknij kontrolkę filtru, która pokazuje, kiedy najedziesz kursorem myszy na którykolwiek z wykresów. Na poniższym wykresie jest kreślenie niestandardowego wskaźnika KPI liczby żądań z filtrami atrybutów URL i duration. Sprawdź poprawność filtrów za pomocą sekcji Podgląd strumienia, która pokazuje transmisję danych na żywo danych telemetrycznych, która spełnia kryteria określone w dowolnym momencie.

![Niestandardowy wskaźnik KPI żądania](./media/live-stream/live-stream-filteredMetric.png)

Można monitorować wartość inną niż Count. Opcje zależą od typu strumienia, który może być dowolną telemetrią usługi Application Insights: żądania, zależności, wyjątki, ślady, zdarzenia lub metryki. Może to być twój własny [pomiar niestandardowy:](../../azure-monitor/app/api-custom-events-metrics.md#properties)

![Opcje wartości](./media/live-stream/live-stream-valueoptions.png)

Oprócz danych telemetrycznych usługi Application Insights można również monitorować dowolny licznik wydajności systemu Windows, wybierając go z opcji strumienia i podając nazwę licznika wydajności.

Metryki na żywo są agregowane w dwóch punktach: lokalnie na każdym serwerze, a następnie na wszystkich serwerach. Wartość domyślną można zmienić, wybierając inne opcje w odpowiednich elementach rozwijanych.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Przykładowa telemetria: niestandardowe zdarzenia diagnostyczne na żywo
Domyślnie źródło na żywo zdarzeń pokazuje próbki nieudanych żądań i wywołań zależności, wyjątków, zdarzeń i śladów. Kliknij ikonę filtru, aby wyświetlić zastosowane kryteria w dowolnym momencie. 

![Domyślny kanał na żywo](./media/live-stream/live-stream-eventsdefault.png)

Podobnie jak w przypadku metryk, można określić dowolne kryteria dowolnego typu danych telemetrycznych usługi Application Insights. W tym przykładzie wybieramy określone błędy żądania, ślady i zdarzenia. Wybieramy również wszystkie wyjątki i błędy zależności.

![Niestandardowy kanał na żywo](./media/live-stream/live-stream-events.png)

Uwaga: Obecnie w przypadku kryteriów opartych na wiadomości exception należy użyć najbardziej zewnętrznego komunikatu wyjątku. W poprzednim przykładzie, aby odfiltrować łagodny wyjątek z wewnętrznym komunikatem wyjątku (następuje ogranicznik "< --") "Klient rozłączony". użyj komunikatu nie zawiera kryteriów "Zawartość żądania odczytu błędu".

Kliknij go w szczegóły elementu w kanale na żywo. Możesz wstrzymać kanał informacyjny, klikając **przycisk Wstrzymaj,** po prostu przewijając w dół lub klikając element. Transmisja na żywo zostanie wznowiona po przewinięciu z powrotem na górę lub kliknięciu licznika elementów zebranych podczas jego wstrzymania.

![Próbkowanie błędów na żywo](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrowanie według wystąpienia serwera

Jeśli chcesz monitorować wystąpienie roli określonego serwera, możesz filtrować według serwera.

![Próbkowanie błędów na żywo](./media/live-stream/live-stream-filter.png)

## <a name="secure-the-control-channel"></a>Zabezpiecz kanał sterowania
Określone kryteria filtrów niestandardowych są wysyłane z powrotem do składnika Aktywne metryki w zestawie SDK usługi Application Insights. Filtry mogą potencjalnie zawierać poufne informacje, takie jak identyfikatory identyfikatorów customerI. Oprócz klucza instrumentacji można zabezpieczyć kanał za pomocą tajnego klucza interfejsu API.
### <a name="create-an-api-key"></a>Tworzenie klucza interfejsu API

![Utwórz klucz interfejsu API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Dodawanie klucza interfejsu API do konfiguracji

### <a name="classic-aspnet"></a>klasyczna ASP.NET

W pliku applicationinsights.config dodaj authenticationApiKey do quickpulsetelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Lub w kodzie, ustaw go na QuickPulseTelemetryModule:

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="azure-function-apps"></a>Aplikacje funkcji platformy Azure

W przypadku aplikacji usługi Azure Function Apps (wersja 2) zabezpieczenie kanału za pomocą klucza interfejsu API można wykonać za pomocą zmiennej środowiskowej.

Utwórz klucz interfejsu API z poziomu zasobu usługi Application Insights i przejdź do **ustawień aplikacji** dla aplikacji funkcji. Wybierz **pozycję dodaj nowe** ustawienie `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` i wprowadź nazwę i wartość odpowiadającą kluczowi interfejsu API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-or-greater"></a>ASP.NET Core (wymaga wglądu w aplikacje ASP.NET core SDK 2.3.0 lub nowszym)

Zmodyfikuj plik startup.cs w następujący sposób:

Pierwsze dodanie

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Następnie w ramach metody ConfigureServices dodaj:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Jeśli jednak rozpoznajesz i ufasz wszystkim połączonym serwerom, możesz wypróbować filtry niestandardowe bez uwierzytelnionego kanału. Ta opcja jest dostępna przez sześć miesięcy. To zastąpienie jest wymagane raz na każdą nową sesję lub gdy nowy serwer jest w trybie online.

![Opcje Auth Metryki na żywo](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Zdecydowanie zaleca się skonfigurowanie uwierzytelnionego kanału przed wprowadzeniem potencjalnie poufnych informacji, takich jak CustomerID w kryteriach filtrowania.
>

## <a name="supported-features-table"></a>Tabela obsługiwanych funkcji

| Język                         | Podstawowe metryki       | Metryki wydajności | Filtrowanie niestandardowe    | Przykładowa telemetria    | Podział procesora według procesu |
|----------------------------------|:--------------------|:--------------------|:--------------------|:--------------------|:---------------------|
| .NET                             | Obsługiwane (V2.7.2+) | Obsługiwane (V2.7.2+) | Obsługiwane (V2.7.2+) | Obsługiwane (V2.7.2+) | Obsługiwane (V2.7.2+)  |
| .NET Core (target=.NET Framework)| Obsługiwane (V2.4.1+) | Obsługiwane (V2.4.1+) | Obsługiwane (V2.4.1+) | Obsługiwane (V2.4.1+) | Obsługiwane (V2.4.1+)  |
| .NET Core (target=.NET Core)     | Obsługiwane (V2.4.1+) | Obsługiwane*          | Obsługiwane (V2.4.1+) | Obsługiwane (V2.4.1+) | **Nie obsługiwane**    |
| Usługi Azure w wersji 2               | Obsługiwane           | Obsługiwane           | Obsługiwane           | Obsługiwane           | **Nie obsługiwane**    |
| Java                             | Obsługiwane (V2.0.0+) | Obsługiwane (V2.0.0+) | **Nie obsługiwane**   | **Nie obsługiwane**   | **Nie obsługiwane**    |
| Node.js                          | Obsługiwane (V1.3.0+) | Obsługiwane (V1.3.0+) | **Nie obsługiwane**   | Obsługiwane (V1.3.0+) | **Nie obsługiwane**    |

Podstawowe metryki obejmują żądanie, zależność i częstotliwość wyjątków. Metryki wydajności (liczniki wydajności) obejmują pamięć i procesor. Przykładowa telemetria pokazuje strumień szczegółowych informacji o nieudanych żądaniach i zależnościach, wyjątkach, zdarzeniach i śladach.

 \*Obsługa perfCounters różni się nieznacznie w różnych wersjach programu .NET Core, które nie są przeznaczone dla programu .NET Framework:

- Metryki PerfCounters są obsługiwane podczas uruchamiania w usłudze Azure App Service dla systemu Windows. (AspNetCore SDK w wersji 2.4.1 lub nowszej)
- PerfCounters są obsługiwane, gdy aplikacja jest uruchomiona w dowolnym komputerach z systemem Windows (VM lub Usługa w chmurze lub on-prem itp.) (AspNetCore SDK w wersji 2.7.1 lub nowszej), ale dla aplikacji przeznaczonych dla .NET Core 2.0 lub nowszego.
- PerfCounters są obsługiwane, gdy aplikacja jest uruchomiona w dowolnym miejscu (Linux, Windows, usługa aplikacji dla Linuksa, kontenerów itp.) w najnowszej wersji beta (tj. AspNetCore SDK w wersji 2.8.0-beta1 lub nowszej), ale dla aplikacji przeznaczonych dla .NET Core 2.0 lub nowszego.

Domyślnie Metryki na żywo jest wyłączona w node.js SDK. Aby włączyć metryki `setSendLiveMetrics(true)` na żywo, dodaj do [metod konfiguracji](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) podczas inicjowania sdk.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Brak danych? Jeśli aplikacja znajduje się w sieci chronionej: Live Metrics Stream używa innych adresów IP niż inne dane telemetryczne usługi Application Insights. Upewnij się, że [te adresy IP](../../azure-monitor/app/ip-addresses.md) są otwarte w zaporze.

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie użycia za pomocą usługi Application Insights](../../azure-monitor/app/usage-overview.md)
* [Korzystanie z wyszukiwania diagnostycznego](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Debuger migawek](../../azure-monitor/app/snapshot-debugger.md)
