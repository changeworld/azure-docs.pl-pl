---
title: Strumień metryk niestandardowych metryk i diagnostyki w usłudze Azure Application Insights na żywo | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji sieci web w czasie rzeczywistym z metryki niestandardowe i diagnozowanie problemów z błędów, ślady i wydarzeń na żywo źródło danych.
services: application-insights
documentationcenter: ''
author: SoubhagyaDash
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: mbullwin
ms.openlocfilehash: f0338642ab99af2fd5ec4f6432bbb8d626daea29
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Strumień na żywo metryki: Monitor & Diagnozuj z opóźnieniem 1 sekundę 

Sonda Puls wznowiony aplikacji sieci web na żywo, w środowisku produkcyjnym za pomocą strumień na żywo metryki z [usługi Application Insights](app-insights-overview.md). Wybierz i filtrować liczniki wydajności i metryk, które należy obserwować w czasie rzeczywistym, bez jakichkolwiek zakłóceń w usłudze. Sprawdź dane śledzenia stosu z żądań próbki nie powiodło się i wyjątki. Razem z [profilera](app-insights-profiler.md), [debugera migawki](app-insights-snapshot-debugger.md), i [testowania wydajności](app-insights-monitor-web-app-availability.md#performance-tests), strumień na żywo metryki udostępnia zaawansowane i nieinwazyjna narzędzie diagnostyczne dla sieci web na żywo lokacja.

Strumień na żywo metryki można:

* Sprawdź poprawność poprawkę podczas jego zwolnienia obserwując liczniki wydajności i niepowodzenie.
* Obejrzyj wpływu testu obciążenia i diagnozowanie problemów na żywo. 
* Skupić się na sesje poszczególnego testu lub odfiltrować znanych problemów, wybierając i filtrowanie metryk, które chcesz obejrzeć.
* Pobierz dane śledzenia wyjątków, po ich wprowadzeniu.
* Poeksperymentuj z filtrów, aby znaleźć najbardziej odpowiednie kluczowych wskaźników wydajności.
* Monitorowanie oknami wydajności licznika na żywo.
* Łatwo zidentyfikować serwera, na którym występują problemy i filtr, który wszystkich kluczowych wskaźników wydajności/live źródła danych tylko z tym serwerem.

[![Metryki strumienia wideo na żywo](./media/app-insights-live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Strumień na żywo metryki jest obecnie dostępna w aplikacji ASP.NET uruchomionych lokalnie lub w chmurze. 

## <a name="get-started"></a>Rozpoczęcie pracy

1. Jeśli nie jest jeszcze [zainstalowane usługi Application Insights](app-insights-asp-net.md) w aplikacji sieci web ASP.NET lub [aplikacji dla systemu Windows server](app-insights-windows-services.md), zrób to teraz. 
2. **Aktualizacja do najnowszej wersji** pakietu usługi Application Insights. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt i wybierz polecenie **pakiety zarządzania pakietami Nuget**. Otwórz **aktualizacje** karcie wyboru **Uwzględnij wersję wstępną**i wybrać wszystkie pakiety Microsoft.ApplicationInsights.*.

    Ponownie wdróż aplikację.

3. W [portalu Azure](https://portal.azure.com), otwórz zasobu usługi Application Insights dla aplikacji, a następnie strumień na żywo.

4. [Bezpieczny kanał kontrolny](#secure-the-control-channel) Jeśli poufnych danych, takich jak nazwy klienta można użyć w filtry.


![W bloku Przegląd kliknij strumień na żywo](./media/app-insights-live-stream/live-stream-2.png)

### <a name="no-data-check-your-server-firewall"></a>Brak danych? Sprawdź zapory serwera

Sprawdź [wychodzące portów dla strumień na żywo metryki](app-insights-ip-addresses.md#outgoing-ports) są otwarte w zaporze serwerów. 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Jaka jest różnica strumień na żywo metryki z Eksploratora metryk i analiza?

| |Transmisja strumieniowa na żywo | Eksploratora metryk i analiza |
|---|---|---|
|Opóźnienie|Dane wyświetlane w ciągu sekundy|Zagregowane w ciągu minut|
|Brak okresu przechowywania|Danych będzie nadal występować, gdy jest na wykresie, a następnie zostaje odrzucone|[Dane przechowywane przez 90 dni](app-insights-data-retention-privacy.md#how-long-is-the-data-kept)|
|Na żądanie|Dane przesyłane strumieniowo podczas otwierania metryki na żywo|Dane są przesyłane, gdy zestaw SDK jest zainstalowany i włączony|
|Bezpłatna|Bezpłatne strumień na żywo danych nie istnieje|Warunkiem [ceny](app-insights-pricing.md)
|Próbkowanie|Wszystkie wybrane metryki i liczniki są przesyłane. Błędy i ślady stosu są próbkowane. TelemetryProcessors nie są stosowane.|Zdarzenia mogą być [próbkowany](app-insights-api-filtering-sampling.md)|
|Kanał kontrolny|Sygnały formant filtru są wysyłane do zestawu SDK. Firma Microsoft zaleca [bezpiecznego kanału](#secure-channel).|Komunikacja jest jednokierunkowa do portalu|


## <a name="select-and-filter-your-metrics"></a>Wybierz i filtrowanie Twoje metryki

(Dostępne w klasycznej aplikacji ASP.NET przy użyciu najnowszej wersji zestawu SDK).

Niestandardowe wskaźnik KPI na żywo można monitorować, stosując filtry dowolnego na dowolnym telemetrii usługi Application Insights z portalu. Kliknij formant filtru, który pokazuje, kiedy użytkownik myszą żadnego z wykresami. Poniższy schemat jest kreślenia niestandardowych liczbę żądań wskaźnika KPI z filtrami na adres URL i czas trwania atrybutów. Sprawdź poprawność filtry z sekcji podglądu strumienia, który zawiera źródło danych na żywo dane telemetryczne, który spełnia kryteria określone w dowolnym momencie w czasie. 

![Żądanie niestandardowego wskaźnika KPI](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Można monitorować wartość inna niż liczba. Opcje zależą od typu stream, który może być dowolnym telemetrii usługi Application Insights: żądań, zależności, wyjątki, śledzenie, zdarzenia lub metryki. Może być własną [niestandardowych miar](app-insights-api-custom-events-metrics.md#properties):

![Wartość opcji](./media/app-insights-live-stream/live-stream-valueoptions.png)

Oprócz telemetrii usługi Application Insights można również monitorować żadnych licznika wydajności systemu Windows, zaznaczając który opcje strumienia i podanie nazwy licznika wydajności.

Metryki na żywo są agregowane w dwóch miejscach: lokalnie na każdym serwerze, a następnie na wszystkich serwerach. Możesz zmienić domyślną w zaznaczając inne opcje w odpowiednich listach rozwijanych.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Przykładowe dane telemetryczne: Niestandardowych zdarzeń diagnostycznych na żywo
Domyślnie na żywo źródła zdarzeń zawiera przykłady żądań zakończonych niepowodzeniem wywołania zależności, wyjątki, zdarzeń i śledzenia. Kliknij ikonę filtru, aby wyświetlić kryteria zastosowane w dowolnym momencie w czasie. 

![Źródła danych na żywo domyślne](./media/app-insights-live-stream/live-stream-eventsdefault.png)

Jako o metryki, można określić żadnych kryteriów dowolne typy telemetrii usługi Application Insights. W tym przykładzie mamy wybierania określone żądanie błędów, ślady i zdarzeń. Możemy również wybierania wszystkie zależności błędy i wyjątki.

![Niestandardowego źródła danych na żywo](./media/app-insights-live-stream/live-stream-events.png)

Uwaga: Obecnie kryteriów na podstawie komunikatu wyjątku używać komunikat o wyjątku najbardziej zewnętrznego. W powyższym przykładzie filtrowanie niegroźne wyjątek z komunikatem o wyjątku wewnętrznego (następuje "<--" ogranicznika) "klient rozłączył się." Użyj wiadomości nie zawiera kryteriów "Błąd podczas odczytu treści żądania".

Zobacz szczegóły elementów w źródle danych na żywo, klikając go. Źródła danych można wstrzymać albo klikając **wstrzymać** lub po prostu przewijania w dół lub klikając element. Źródła danych na żywo zostanie wznowiona po przewiń do góry lub przez kliknięcie przycisku licznik elementów zebranych podczas została wstrzymana.

![Próbkowany awarii na żywo](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtruj według wystąpienia serwera

Jeśli chcesz monitorować wystąpienia roli konkretnego serwera, można filtrować według serwera.

![Próbkowany awarii na żywo](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Wymagania dotyczące zestawu SDK
Niestandardowe strumień na żywo metryki jest dostępna z wersji 2.4.0-beta2 lub nowszej programu [zestaw SDK usługi Application Insights dla sieci web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Pamiętaj wybrać opcję "Uwzględnij wydanie wstępne" z Menedżera pakietów NuGet.

## <a name="secure-the-control-channel"></a>Bezpieczny kanał kontrolny
Filtry niestandardowe podane kryteria są odsyłane do składnika metryki na żywo w zestaw SDK usługi Application Insights. Filtry potencjalnie mogą zawierać poufne informacje, takie jak customerIDs. Kanał można zabezpieczyć przy użyciu tajnego klucza interfejsu API, oprócz klucza instrumentacji.
### <a name="create-an-api-key"></a>Utwórz klucz interfejsu API

![Utwórz klucz interfejsu api](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Dodaj klucz interfejsu API do konfiguracji

# <a name="net-standardtabnet-standard"></a>[.NET Standard](#tab/.net-standard)

W pliku applicationinsights.config dodać AuthenticationApiKey QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
Lub w kodzie, ustaw go w QuickPulseTelemetryModule:

``` C#

    module.AuthenticationApiKey = "YOUR-API-KEY-HERE";

```
# <a name="net-core-tabnet-core"></a>[.NET Core] (#tab/.net-core)

Modyfikowanie pliku startup.cs w następujący sposób:

Najpierw dodaj

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;
```

Następnie w obszarze metody konfiguracji należy dodać:

``` C#
  QuickPulseTelemetryModule dep;
            var modules = app.ApplicationServices.GetServices<ITelemetryModule>();
            foreach (var module in modules)
            {
                if (module is QuickPulseTelemetryModule)
                {
                    dep = module as QuickPulseTelemetryModule;
                    dep.AuthenticationApiKey = "YOUR-API-KEY-HERE";
                    dep.Initialize(TelemetryConfiguration.Active);
                }
            }
```

---

Jednak jeśli rozpoznaje i zaufania połączonych serwerów, możesz spróbować filtry niestandardowe bez uwierzytelnionego kanału. Ta opcja jest dostępna przez sześć miesięcy. To zastąpienie jest wymagana raz na nowej sesji, lub gdy nowy serwer przejściu do trybu online.

![Opcje uwierzytelniania metryki na żywo](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>Zdecydowanie zaleca się skonfigurowanie kanału uwierzytelnionego przed wprowadzeniem potencjalnie wrażliwe informacje, takie jak CustomerID w kryteriach filtrowania.
>

## <a name="generating-a-performance-test-load"></a>Generowanie wydajności testu obciążenia

Jeśli chcesz obserwować wpływ wzrostu obciążenia, użyj bloku testu wydajności. Symuluje ona żądań z liczba równoczesnych użytkowników. Można uruchomić albo "testy ręczne" (ping testy) pojedynczego adresu URL, lub można je uruchomić [testu wydajności sieci web wieloetapowych](app-insights-monitor-web-app-availability.md#multi-step-web-tests) przekazywanego (w taki sam sposób jak test dostępności).

> [!TIP]
> Po utworzeniu testu wydajności, otwórz testu i bloku strumień na żywo w oddzielnych okien. W tym samym czasie widoczne podczas uruchamiania testu wydajności umieszczonych w kolejce i obejrzyj strumień na żywo.
>


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Brak danych? Jeśli aplikacja znajduje się w sieci chronionej: strumień na żywo metryki korzysta z innych adresów IP, niż inne telemetrii usługi Application Insights. Upewnij się, że [adresów IP](app-insights-ip-addresses.md) w zaporze są otwarte.



## <a name="next-steps"></a>Kolejne kroki
* [Monitorowanie użycia za pomocą usługi Application Insights](app-insights-web-track-usage.md)
* [W wyszukiwaniu diagnostycznych](app-insights-diagnostic-search.md)
* [Profiler](app-insights-profiler.md)
* [Debuger migawki](app-insights-snapshot-debugger.md)
