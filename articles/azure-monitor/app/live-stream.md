---
title: Live Metrics Stream z niestandardowymi metrykami i diagnostyką na platformie Azure Application Insights | Microsoft Docs
description: Monitoruj swoją aplikację sieci Web w czasie rzeczywistym za pomocą metryk niestandardowych i Diagnozuj problemy z dynamicznym źródłem błędów, śladów i zdarzeń.
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
ms.openlocfilehash: f8203cade1d2e34a9852e945df03dc2fddc1fbe5
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359419"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream: Monitorowanie & diagnozowanie przy użyciu 1-sekundowego opóźnienia

Przesondowanie pulsu aktywnej, działającej aplikacji sieci Web przy użyciu Live Metrics Stream z [Application Insights](../../azure-monitor/app/app-insights-overview.md). Wybieranie i filtrowanie metryk i liczników wydajności w czasie rzeczywistym, bez żadnych zakłóceń usługi. Sprawdzanie śladów stosu z przykładowych nieudanych żądań i wyjątków. Za pomocą narzędzia [Profiler](../../azure-monitor/app/profiler.md)i [debugera migawek](../../azure-monitor/app/snapshot-debugger.md). Live Metrics Stream zapewnia zaawansowane i nieinwazyjne narzędzie diagnostyczne dla aktywnej witryny sieci Web.

Za pomocą Live Metrics Stream można:

* Sprawdź poprawność poprawki, gdy zostanie ona wydana, obserwując liczniki wydajności i niepowodzeń.
* Obserwuj efekt ładowania testów i Diagnozuj problemy na żywo. 
* Skup się na konkretnych sesjach testowych lub odfiltruj znane problemy, wybierając i filtrując metryki, które mają być obserwowane.
* Pobierz ślady wyjątków w miarę ich występowania.
* Eksperymentuj z filtrami, aby znaleźć najbardziej odpowiednie wskaźniki KPI.
* Monitoruj dowolny licznik wydajności systemu Windows na żywo.
* Łatwo Zidentyfikuj serwer, który ma problemy, i przefiltruj wszystkie wskaźniki KPI/kanał Aktualności na tylko ten serwer.

[![Film wideo Live Metrics Stream](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Metryki na żywo są obecnie obsługiwane w przypadku aplikacji ASP.NET, ASP.NET Core, Azure Functions, Java i Node. js.

## <a name="get-started"></a>Wprowadzenie

1. Jeśli jeszcze nie zainstalowano [Application Insights](../../azure-monitor/azure-monitor-app-hub.md) w aplikacji sieci Web, zrób to teraz.
2. Oprócz standardowych pakietów Application Insights [Microsoft. ApplicationInsights. PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) jest wymagany do włączenia strumienia metryk na żywo.
3. **Zaktualizuj do najnowszej wersji** pakietu Application Insights. W programie Visual Studio kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet**. Otwórz kartę **aktualizacje** i wybierz wszystkie pakiety Microsoft. ApplicationInsights. *.

    Ponownie wdróż aplikację.

3. W [Azure Portal](https://portal.azure.com)otwórz zasób Application Insights dla aplikacji, a następnie otwórz Live Stream.

4. [Zabezpiecz kanał kontroli,](#secure-the-control-channel) Jeśli możesz użyć poufnych danych, takich jak nazwy klientów w filtrach.

### <a name="nodejs"></a>Node.js

Aby korzystać z metryk na żywo w programie Node. js, należy zaktualizować program do wersji 1,30 lub nowszej zestawu SDK. Domyślnie metryki na żywo są wyłączone w zestawie SDK środowiska Node. js. Aby włączyć metryki na `setSendLiveMetrics(true)` żywo, Dodaj do Twoich [metod konfiguracji](https://github.com/Microsoft/ApplicationInsights-node.js#configuration) podczas inicjowania zestawu SDK.

### <a name="no-data-check-your-server-firewall"></a>Brak danych? Sprawdź zaporę serwera

Sprawdź, czy [porty wychodzące Live Metrics Stream](../../azure-monitor/app/ip-addresses.md#outgoing-ports) są otwarte w zaporze serwerów. 

## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Jak Live Metrics Stream różnić się od Eksplorator metryk i analiz?

| |Live Stream | Eksplorator metryk i analiza |
|---|---|---|
|Opóźnienie|Dane wyświetlane w jednej sekundzie|Zagregowane w ciągu minut|
|Brak przechowywania|Dane są przechowywane na wykresie, a następnie są odrzucane|[Dane przechowywane przez 90 dni](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|Na żądanie|Dane są przesyłane strumieniowo podczas otwierania metryk na żywo|Dane są wysyłane za każdym razem, gdy zestaw SDK jest zainstalowany i włączony|
|Wolne|Za dane Live Stream nie są naliczane opłaty|Podlega [cennikowi](../../azure-monitor/app/pricing.md)
|Próbkowanie|Wszystkie wybrane metryki i liczniki są przesyłane. Błędy i ślady stosu są próbkowane. TelemetryProcessors nie są stosowane.|Zdarzenia mogą być [próbkowane](../../azure-monitor/app/api-filtering-sampling.md)|
|Kanał kontrolny|Sygnały kontroli filtru są wysyłane do zestawu SDK. Zalecamy zabezpieczenie tego kanału.|Komunikacja jest jednokierunkowa w portalu|

## <a name="select-and-filter-your-metrics"></a>Wybieranie i filtrowanie metryk

(Dostępne w ASP.NET, ASP.NET Core i Azure Functions (v2).)

Możesz monitorować niestandardowy kluczowy wskaźnik wydajności, stosując dowolne filtry dla dowolnej Application Insights telemetrii w portalu. Kliknij kontrolkę filtr, która jest wyświetlana po umieszczeniu wskaźnika myszy na dowolnym z wykresów. Poniższy wykres przedstawia niestandardowy wskaźnik KPI liczby żądań z filtrami dla atrybutów adresu URL i czasu trwania. Sprawdź poprawność filtrów za pomocą sekcji Podgląd strumienia, która zawiera dynamiczne źródło danych telemetrycznych spełniające kryteria określone w dowolnym momencie. 

![Niestandardowy wskaźnik wydajności żądania](./media/live-stream/live-stream-filteredMetric.png)

Można monitorować wartość różną od Count. Opcje są zależne od typu strumienia, który może być dowolną Application Insights telemetrii: żądania, zależności, wyjątki, ślady, zdarzenia lub metryki. Może to być własny [niestandardowy pomiar](../../azure-monitor/app/api-custom-events-metrics.md#properties):

![Opcje wartości](./media/live-stream/live-stream-valueoptions.png)

Oprócz Application Insights telemetrii można także monitorować dowolny licznik wydajności systemu Windows, wybierając go z opcji strumienia i podając nazwę licznika wydajności.

Metryki na żywo są agregowane w dwóch punktach: lokalnie na każdym serwerze, a następnie na wszystkich serwerach. Wartość domyślną można zmienić, wybierając opcję inne opcje z odpowiednich list rozwijanych.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Przykładowa Telemetria: Niestandardowe zdarzenia diagnostyczne na żywo
Domyślnie dynamiczne źródło zdarzeń zawiera przykłady żądań zakończonych niepowodzeniem oraz wywołania zależności, wyjątki, zdarzenia i ślady. Kliknij ikonę filtru, aby wyświetlić zastosowane kryteria w dowolnym momencie. 

![Domyślne kanały informacyjne na żywo](./media/live-stream/live-stream-eventsdefault.png)

Podobnie jak w przypadku metryk, można określić dowolne dowolne kryterium dla dowolnych typów Application Insights telemetrii. W tym przykładzie wybieramy konkretne błędy żądań, ślady i zdarzenia. Wybieramy również wszystkie wyjątki i awarie zależności.

![Niestandardowe kanały informacyjne na żywo](./media/live-stream/live-stream-events.png)

Uwaga: Obecnie w przypadku kryteriów opartych na komunikatach o wyjątkach należy użyć najbardziej zewnętrznego komunikatu o wyjątku. W poprzednim przykładzie, aby odfiltrować wyjątek niegroźny z wewnętrznym komunikatem wyjątku (następuje "<--" ogranicznik) "klient został odłączony". Użyj komunikatu niezawierającego kryterium "błąd podczas odczytu zawartości żądania".

Zobacz szczegóły elementu w kanale dynamicznym, klikając go. Możesz wstrzymać kanał informacyjny przez kliknięcie  przycisku Wstrzymaj lub po prostu przewinięcie lub kliknięcie elementu. Kanały informacyjne na żywo zostaną wznowione po przeprowadzeniu przewijania do góry lub przez kliknięcie licznika zebranych elementów podczas wstrzymania.

![Próbkowane błędy na żywo](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtruj według wystąpienia serwera

Jeśli chcesz monitorować konkretne wystąpienie roli serwera, możesz filtrować według serwera.

![Próbkowane błędy na żywo](./media/live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Wymagania dotyczące zestawu SDK
Niestandardowy Live Metrics Stream jest dostępny w wersji 2.4.0-beta2 lub nowszej [zestawu SDK Application Insights dla sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Pamiętaj, aby wybrać opcję "Uwzględnij wersję wstępną" z Menedżera pakietów NuGet.

## <a name="secure-the-control-channel"></a>Zabezpieczanie kanału kontroli
Określone kryteria filtrów niestandardowych są wysyłane z powrotem do składnika metryki na żywo w zestawie Application Insights SDK. Filtry mogą potencjalnie zawierać informacje poufne, takie jak customerID. Kanał można zabezpieczyć przy użyciu klucza tajnego interfejsu API oprócz klucza Instrumentacji.
### <a name="create-an-api-key"></a>Tworzenie klucza interfejsu API

![Tworzenie klucza interfejsu API](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Dodaj klucz interfejsu API do konfiguracji

### <a name="classic-aspnet"></a>Klasyczny ASP.NET

W pliku ApplicationInsights. config Dodaj AuthenticationApiKey do QuickPulseTelemetryModule:
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

W przypadku aplikacji funkcji platformy Azure (v2) Zabezpieczanie kanału za pomocą klucza interfejsu API można wykonać przy użyciu zmiennej środowiskowej. 

Utwórz klucz interfejsu API z poziomu zasobu Application Insights i przejdź do pozycji **Ustawienia aplikacji** dla aplikacja funkcji. Wybierz pozycję **Dodaj nowe ustawienie** i wprowadź nazwę `APPINSIGHTS_QUICKPULSEAUTHAPIKEY` i wartość odpowiadającą kluczowi interfejsu API.

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (wymaga Application Insights ASP.NET Core SDK 2.3.0 — wersja beta lub nowsza)

Zmodyfikuj plik startup.cs w następujący sposób:

Najpierw Dodaj

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

Następnie w ramach metody ConfigureServices Dodaj:

```csharp
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```

Jeśli jednak rozpoznasz i ufasz wszystkim połączonym serwerom, możesz wypróbować filtry niestandardowe bez uwierzytelnionego kanału. Ta opcja jest dostępna przez sześć miesięcy. To przesłonięcie jest wymagane po każdej nowej sesji lub gdy nowy serwer przejdzie w tryb online.

![Opcje uwierzytelniania metryk na żywo](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>Zdecydowanie zalecamy skonfigurowanie kanału uwierzytelnionego przed wprowadzeniem potencjalnie poufnych informacji, takich jak CustomerID w kryteriach filtrowania.
>

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Brak danych? Jeśli aplikacja należy do sieci chronionej: Live Metrics Stream używa różnych adresów IP niż inne dane telemetryczne Application Insights. Upewnij się, że [te adresy IP](../../azure-monitor/app/ip-addresses.md) są otwarte w zaporze.



## <a name="next-steps"></a>Kolejne kroki
* [Monitorowanie użycia za pomocą Application Insights](../../azure-monitor/app/usage-overview.md)
* [Korzystanie z wyszukiwania diagnostycznego](../../azure-monitor/app/diagnostic-search.md)
* [Profiler](../../azure-monitor/app/profiler.md)
* [Debuger migawek](../../azure-monitor/app/snapshot-debugger.md)
