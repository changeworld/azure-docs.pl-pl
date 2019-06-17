---
title: Live Stream metryki niestandardowe metryki i Diagnostyka w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitorowanie w czasie rzeczywistym za pomocą metryk niestandardowych aplikacji sieci web i diagnozowanie problemów z Kanał informacyjny na żywo błędów, ślady i zdarzenia.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/22/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: bc85de0c8ec89ea88d2bae8e3f226da7d3163f53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721095"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Stream metryki na żywo: Monitorowanie i diagnozowanie z opóźnieniem 1 sekundę

Sonda serce interwałów pulsu aplikacji sieci web na żywo, w środowisku produkcyjnym za pomocą Live Stream metryk z [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md). Wybierania i filtrowania liczniki wydajności i metryki, aby obejrzeć w czasie rzeczywistym, bez żadnych zakłóceń w usłudze. Sprawdź, czy ślady stosu z przykładowych nie powiodło się żądań i wyjątków. Wraz z [Profiler](../../azure-monitor/app/profiler.md), [rozszerzenia Snapshot debugger](../../azure-monitor/app/snapshot-debugger.md), i [testowania wydajnościowego](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests), Live Stream metryk udostępnia zaawansowane i nieinwazyjny narzędzie diagnostyczne dla sieci web na żywo witryna.

Za pomocą Live Stream metryki możesz wykonywać następujące czynności:

* Sprawdzanie poprawności poprawkę, gdy jest on zwalniany, obserwując liczniki wydajności i niepowodzeń.
* Obejrzyj wpływu testu ładowania i diagnozować problemy na żywo. 
* Skup się na sesji dla określonego testu lub odfiltrować znanych problemów, wybieranie i filtrowanie metryk, które chcesz obejrzeć.
* Pobierz dane śledzenia wyjątków, po ich wprowadzeniu.
* Poeksperymentuj z filtry, aby znaleźć najbardziej odpowiedniego kluczowe wskaźniki wydajności.
* Monitoruj wszystkie Windows wydajności licznik na żywo.
* Łatwo zidentyfikować serwerze, na którym występują problemy i filtr, który wszystkich KPI/live źródła danych z tym serwerem, po prostu.

[![Wideo na żywo Stream metryki](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Metryki na żywo są obecnie obsługiwane w przypadku aplikacji ASP.NET, ASP.NET Core, usługi Azure Functions, Java i Node.js.

## <a name="get-started"></a>Rozpoczęcie pracy

1. Jeśli nie zostały jeszcze [Zainstaluj usługę Application Insights](../../azure-monitor/azure-monitor-app-hub.md) w aplikacji sieci web, zrób to teraz.
2. Oprócz standardowych pakietów usługi Application Insights [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) jest wymagany do włączenia Live Metrics stream.
3. **Aktualizacja do najnowszej wersji** pakietu usługi Application Insights. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami Nuget**. Otwórz **aktualizacje** kartę, a następnie wybierz pozycję wszystkie pakiety Microsoft.ApplicationInsights.*.

    Ponownie wdróż aplikację.

3. W [witryny Azure portal](https://portal.azure.com), otwórz zasób usługi Application Insights dla aplikacji, a następnie otwórz Live Stream.

4. [Bezpieczny kanał kontrolny](#secure-the-control-channel) Jeśli poufnych danych, takich jak nazwy klienta można użyć w filtry.

### <a name="nodejs"></a>Node.js

Metryki na żywo za pomocą środowiska Node.js, należy zaktualizować do wersji 1,30 lub nowszej zestawu SDK. Domyślnie metryki na żywo jest wyłączone w zestawie SDK środowiska Node.js. Aby włączyć metryki na żywo należy dodać `setSendLiveMetrics(true)` do Twojej [metody konfiguracji](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) jak zainicjować zestaw SDK.

### <a name="no-data-check-your-server-firewall"></a>Brak danych? Sprawdź Zapora serwera

Sprawdź [porty wychodzące dla Live Stream metryki](../../azure-monitor/app/ip-addresses.md#outgoing-ports) są otwarte w zaporze serwerów. 

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Czym różni się Live Stream metryk z Eksploratora metryk i analiza?

| |Transmisja strumieniowa na żywo | Eksplorator metryk i analiza |
|---|---|---|
|Opóźnienie|Dane wyświetlane w ciągu sekundy|Zagregowane w ciągu minut|
|Nie przechowywania|Danych będzie nadal występować, gdy znajduje się na wykresie, a następnie zostaje odrzucone|[Dane przechowywane przez 90 dni](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|Na żądanie|Dane są przesyłane strumieniowo, gdy otworzysz metryki na żywo|Dane są wysyłane zawsze, gdy zestaw SDK jest zainstalowane i włączone|
|Wolne|Nie ma opłat dla danych Live Stream.|Podlegają [ceny](../../azure-monitor/app/pricing.md)
|Próbkowanie|Wszystkie wybrane metryk i liczników są przesyłane. Błędy i ślady stosu są próbkowane. TelemetryProcessors nie są stosowane.|Zdarzenia mogą być [próbkowania](../../azure-monitor/app/api-filtering-sampling.md)|
|Kanał kontrolny|Sygnały formant filtru są wysyłane do zestawu SDK. Zaleca się, że zabezpieczenia tego kanału.|Komunikacja jest jednokierunkowe do portalu|

## <a name="select-and-filter-your-metrics"></a>Wybierania i filtrowania metryk

(Dostępne za pomocą programu ASP.NET, ASP.NET Core i usługi Azure Functions (v2)).

Stosowanie filtrów dowolnego na żadnych danych telemetrycznych usługi Application Insights z poziomu portalu można monitorować na żywo niestandardowe kluczowego wskaźnika wydajności. Kliknij formant filtru, który pokazuje, kiedy użytkownik wskaźnika myszy nad dowolne wykresy. Poniższy wykres jest wykreślania niestandardowe liczby żądań kluczowy wskaźnik wydajności z filtrami w atrybutach adresu URL i czas trwania. Sprawdź poprawność filtry z sekcją Stream (wersja zapoznawcza), który pokazuje Kanał informacyjny na żywo danych telemetrycznych, który spełnia kryteria, które określono w dowolnym momencie w czasie. 

![Żądanie niestandardowego wskaźnika KPI](./media/live-stream/live-stream-filteredMetric.png)

Można monitorować wartość różni się od liczby. Opcje zależą od rodzaju strumienia, który może być żadnych danych telemetrycznych usługi Application Insights: żądań, zależności, wyjątki, ślady, zdarzenia i metryki. Może być własne [niestandardowej miary](../../azure-monitor/app/api-custom-events-metrics.md#properties):

![Opcje wartości](./media/live-stream/live-stream-valueoptions.png)

Oprócz telemetrii usługi Application Insights można również monitorować wszelkie licznika wydajności Windows, wybierając z opcji strumienia i podając nazwę licznika wydajności.

Metryki na żywo są agregowane w dwóch miejscach: lokalnie na każdym serwerze, a następnie na wszystkich serwerach. Można zmienić domyślny na poziomie, wybierając inne opcje w odpowiednich listach rozwijanych.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Przykładowe dane telemetryczne: Diagnostyczne wydarzeń na żywo niestandardowe
Domyślnie Kanał informacyjny na żywo zdarzeń zawiera przykłady żądań zakończonych niepowodzeniem i wywołań zależności, wyjątki, zdarzenia i dane śledzenia. Kliknij ikonę filtru, aby wyświetlić kryteria zastosowanych w dowolnym momencie w czasie. 

![Kanał informacyjny na żywo domyślne](./media/live-stream/live-stream-eventsdefault.png)

Za pomocą metryk, możesz określić dowolnego kryterium dowolnego do dowolnego typu danych telemetrycznych usługi Application Insights. W tym przykładzie wybieramy błędów określonych żądań, ślady i zdarzenia. Również wybieramy wszystkie wyjątki i błędy zależności.

![Niestandardowy Kanał informacyjny na żywo](./media/live-stream/live-stream-events.png)

Uwaga: Obecnie dla kryteriów opartych na komunikat o wyjątku, należy używać komunikat o wyjątku najbardziej zewnętrznej. W poprzednim przykładzie, aby odfiltrować niegroźne wyjątek z komunikatem o wyjątku wewnętrznego (następuje "<--" ogranicznika) "klient rozłączył się." Użyj komunikatu nie zawiera kryteria "Błąd podczas odczytu treści żądania".

Zobacz szczegóły dotyczące elementu w źródle danych na żywo, klikając go. Źródło danych można wstrzymać, klikając przycisk **wstrzymać** lub po prostu przewijając w dół lub klikając element. Kanał informacyjny na żywo zostanie wznowiona po podczas przewijania do góry lub klikając licznik elementów zbierane, gdy została wstrzymana.

![Próbkowane awarii na żywo](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtruj według wystąpienia serwera

Jeśli chcesz monitorować wystąpienia roli serwera określonego, można filtrować przez serwer.

![Próbkowane awarii na żywo](./media/live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Wymagania dotyczące zestawu SDK
Niestandardowe Stream metryk na żywo jest dostępne przy użyciu wersji 2.4.0-beta2 lub nowszej z [zestawu SDK usługi Application Insights dla sieci web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Pamiętaj wybrać opcję "Uwzględnij wydania wstępne" z Menedżera pakietów NuGet.

## <a name="secure-the-control-channel"></a>Bezpieczny kanał kontrolny
Filtry niestandardowe określonych kryteriów są wysyłane do składnika metryki na żywo w zestaw SDK usługi Application Insights. Filtry potencjalnie mogą zawierać poufne informacje, takie jak customerIDs. Kanał można zabezpieczyć przy użyciu klucza tajnego klucza interfejsu API, oprócz klucz instrumentacji.
### <a name="create-an-api-key"></a>Utwórz klucz interfejsu API

![Utwórz klucz interfejsu API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Dodaj klucz interfejsu API do konfiguracji

### <a name="classic-aspnet"></a>Klasycznej platformy ASP.NET

W pliku applicationinsights.config należy dodać AuthenticationApiKey do QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Lub w kodzie, należy ustawić go na QuickPulseTelemetryModule:

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

Dla aplikacji funkcji platformy Azure (wersja 2) zabezpieczanie kanału z interfejsem API key można osiągnąć za pomocą zmiennej środowiskowej. 

Tworzenie klucza interfejsu API, w ramach zasobu usługi Application Insights i przejdź do **ustawienia aplikacji** dla aplikacji funkcji. Wybierz **Dodaj nowe ustawienie** i wprowadź nazwę `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` i wartość, która odnosi się do klucza interfejsu API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>Platforma ASP.NET Core (wymaga platformy ASP.NET Core SDK usługi Application Insights 2.3.0-beta lub nowszej)

Zmodyfikuj plik startup.cs w następujący sposób:

Najpierw dodaj

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Następnie w metodzie ConfigureServices należy dodać:

``` C#
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Jednak jeśli rozpoznaje i zaufania połączonych serwerów, możesz spróbować filtry niestandardowe bez uwierzytelnionych kanałów. Ta opcja jest dostępna przez sześć miesięcy. To zastąpienie jest wymagany, raz na nowej sesji lub gdy nowy serwer przejściu do trybu online.

![Na żywo opcje uwierzytelniania metryki](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Zdecydowanie zaleca się skonfigurowanie kanału uwierzytelnionego przed wejściem potencjalnie poufne informacje, takie jak CustomerID w kryteriach filtrowania.
>

## <a name="generating-a-performance-test-load"></a>Generowanie wydajności testu obciążenia

Jeśli chcesz zobaczyć efekt zwiększenia obciążenia, należy użyć bloku Test wydajności. Symuluje ona żądania pochodzące z wielu równoczesnych użytkowników. Można uruchomić, albo "testy ręczne" (ping testy) z pojedynczego adresu URL lub można uruchomić [wieloetapowego testu sieci web wydajności](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) przekazane (w taki sam sposób jak test dostępności).

> [!TIP]
> Po utworzeniu testu wydajności, otwórz test i bloku Stream na żywo w oddzielnych okien. Po uruchomieniu testu wydajności umieszczonych w kolejce i obejrzyj transmisji strumieniowej na żywo widoczne w tym samym czasie.
>


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Brak danych? Jeśli aplikacja znajduje się w chronionej sieci: Live Stream metryki korzysta z różnymi adresami IP niż inne telemetria usługi Application Insights. Upewnij się, że [te adresy IP](../../azure-monitor/app/ip-addresses.md) są otwarte w zaporze.



## <a name="next-steps"></a>Kolejne kroki
* [Monitorowanie użycia za pomocą usługi Application Insights](../../azure-monitor/app/usage-overview.md)
* [Za pomocą wyszukiwanie diagnostyczne](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Rozszerzenie Snapshot debugger](../../azure-monitor/app/snapshot-debugger.md)
