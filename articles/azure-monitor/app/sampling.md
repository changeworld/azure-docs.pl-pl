---
title: Próbkowanie danych telemetrycznych w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Jak zapewnić ilości danych telemetrycznych pod kontrolą.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/02/2018
ms.reviewer: vitalyg
ms.author: mbullwin
ms.openlocfilehash: 7b25495973502f1b28aedf8b99c84ca45a5ac898
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028496"
---
# <a name="sampling-in-application-insights"></a>Próbkowanie w usłudze Application Insights


Próbkowanie to funkcja [usługi Azure Application Insights](../../application-insights/app-insights-overview.md). Jest to zalecany sposób zmniejszenia ruchu telemetrycznego i Magazyn, przy jednoczesnym zachowaniu statystycznie prawidłowej analizy danych aplikacji. Filtr wybiera elementy, które są powiązane, dzięki czemu możesz przechodzić między elementami w trakcie badania diagnostyczne.
Gdy liczby metryk jest dostępny w portalu, są one renormalized w celu uwzględnienia próbkowania w celu zminimalizowania wpływu na dane statystyczne.

Próbkowania zmniejsza koszty ruchu sieciowego i danych i pomaga uniknąć ograniczania przepustowości.

## <a name="in-brief"></a>W skrócie:
* Próbkowanie zachowuje 1 w *n* rejestruje i odrzuca wszystkie pozostałe. Na przykład mogą go zachować zdarzenia 1 do 5, częstotliwość próbkowania w wysokości 20%. 
* Próbkowanie odbywa się automatycznie, jeśli Twoja aplikacja przesyła wiele danych telemetrycznych, w aplikacjach serwer sieci web platformy ASP.NET.
* Można również ustawić ręcznie próbkowania, albo w portalu na użycie i szacowane koszty strony. lub w zestawie SDK platformy ASP.NET w pliku Config. lub zestawu SDK języka Java w pliku ApplicationInsights.xml do również zmniejszenie ruchu w sieci.
* Jeśli rejestrować zdarzenia niestandardowe i chcesz upewnić się, że zestaw zdarzeń jest zatrzymany lub odrzucone ze sobą, upewnij się, że mają taką samą wartość OperationId.
* Dzielnik próbkowania *n* jest zgłaszany w każdym rekordzie we właściwości `itemCount`, w polu wyszukiwania widocznego w obszarze przyjazna nazwa "licznik żądanie" lub "liczba zdarzeń". Podczas pobierania próbek nie znajduje się w operacji `itemCount==1`.
* Jeśli piszesz zapytania analityczne, wykonaj następujące czynności [uwzględnienia próbkowania](../../azure-monitor/log-query/aggregations.md). W szczególności, a nie po prostu wciąż dochodzą nowe rekordy, należy użyć `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Typy próbkowania
Istnieją trzy metody alternatywnej próbkowania:

* **Próbkowanie adaptacyjne** automatycznie dostosowuje ilości telemetrii wysyłanych z zestawu SDK w aplikacji ASP.NET. Począwszy od zestawu SDK v 2.0.0-beta3 jest domyślna metoda pobierania próbek. Próbkowanie adaptacyjne jest obecnie dostępna tylko dla telemetrii po stronie serwera ASP.NET. Przeznaczanie dla pełnej aplikacji platformy Asp.NET Core próbkowanie adaptacyjne jest dostępna w wersji 1.0.0 Microsoft.ApplicationInsights.AspNetCore SDK. W przypadku aplikacji platformy Asp.NET Core przeznaczone dla NetCore próbkowanie adaptacyjne jest dostępne z 2.2.0-beta1 Microsoft.ApplicationInsights.AspNetCore zestawu SDK.

* **Próbkowanie stałej stawki** powoduje zmniejszenie ilości telemetrii wysyłanych z Twojej platformy ASP.NET lub Java serwera i z przeglądarek użytkowników. Możesz ustawić częstotliwość. Klient i serwer będzie synchronizować ich próbkowania tak, w wyszukiwania, możesz przechodzić między żądań i wyświetleń stron powiązane.
* **Próbkowanie fragmentaryczne** działa w witrynie Azure portal. Powoduje odrzucenie niektórych danych telemetrycznych przychodzący z Twoją aplikację, częstotliwość próbkowania, który został ustawiony. Nie zmniejszenia ruchu telemetrycznego, wysyłanych z aplikacji, ale pomaga zagwarantować bezpieczeństwo w ramach miesięcznego limitu przydziału. Główną zaletą próbkowanie fragmentaryczne jest, że możesz ustawić częstotliwość próbkowania, bez konieczności ponownego wdrażania aplikacji i jednolicie działa w przypadku wszystkich serwerów i klientów. 

Jeśli operacja adaptacyjną lub stała szybkość pobierania próbek, próbkowanie fragmentaryczne jest wyłączona.

## <a name="ingestion-sampling"></a>Próbkowanie fragmentaryczne
Ta forma próbkowania działa w punkcie, w którym dane telemetryczne z serwera sieci web, przeglądarek i urządzeń osiąga punkt końcowy usługi Application Insights. Chociaż nie redukują ruch danych telemetrycznych wysyłanych z aplikacji, to zmniejszyć ilość przetwarzane i przechowywane (i opłata za) przez usługę Application Insights.

Użyj tego typu próbkowania, jeśli aplikacja często przechodzi przez osiągnięto miesięczny limit przydziału, a nie ma możliwości przy użyciu zestawu SDK rodzajów próbkowania. 

Ustaw częstotliwość próbkowania w użycie i szacowane koszty strony:

![Z poziomu bloku Przegląd aplikacji kliknij pozycję Ustawienia, limit przydziału, próbek, a następnie wybierz częstotliwość próbkowania, a następnie kliknij przycisk aktualizacji.](./media/sampling/04.png)

Podobnie jak inne rodzaje próbkowania algorytm zachowuje elementy powiązane dane telemetryczne. Na przykład gdy masz sprawdzanie danych telemetrycznych w polu wyszukiwania, będziesz mieć możliwość odnaleźć żądania powiązaną z określonym wyjątkiem. Zlicza metryki, takie jak liczba żądań i częstotliwość występowania wyjątków poprawnie zostaną zachowane.

Punktów danych, które są odrzucane przez pobieranie próbek nie są dostępne w dowolnej usługi Application Insights funkcji takich jak [eksportu ciągłego](../../azure-monitor/app/export-telemetry.md).

Próbkowanie fragmentaryczne nie działają podczas SDK próbkowanie adaptacyjne lub stałej stawki. Należy pamiętać, że próbkowanie adaptacyjne jest domyślnie włączona, zestawu SDK platformy ASP.NET jest włączona w programie Visual Studio lub za pomocą Monitora stanu, gdy próbkowanie fragmentaryczne jest wyłączona. Jeśli częstotliwość próbkowania w zestawie SDK jest mniejsza niż 100%, częstotliwość próbkowania pozyskiwania, który został ustawiony jest ignorowana.

> [!WARNING]
> Wartości wyświetlane na kafelku wskazuje wartość, która zostanie ustawiona próbkowanie fragmentaryczne. Nie on reprezentować rzeczywiste próbkowania, jeśli trwa próbkowanie zestawu SDK.
> 
> 

## <a name="adaptive-sampling-at-your-web-server"></a>Próbkowanie adaptacyjne na serwerze sieci web
Próbkowanie adaptacyjne jest dostępna dla zestaw Application Insights SDK dla platformy ASP.NET v 2.0.0-beta3 lub nowszego i jest domyślnie włączona. 

Próbkowanie adaptacyjne wpływa na ilość danych telemetrycznych wysyłanych z aplikacji serwera sieci web z punktem końcowym usługi Application Insights. Wolumin jest automatycznie dostosowywany do zachowania w ramach określonego maksymalną szybkość ruchu.

Go nie działa w niskiej ilości danych telemetrycznych, więc w debugowaniu aplikacji lub witryny o niskim użyciu nie ulegnie zmianie.

Aby osiągnąć woluminu docelowego, niektóre wygenerowane dane telemetryczne zostaną odrzucone. Ale takie jak inne rodzaje próbkowania algorytm zachowuje elementy powiązane dane telemetryczne. Na przykład gdy masz sprawdzanie danych telemetrycznych w polu wyszukiwania, będziesz mieć możliwość odnaleźć żądania powiązaną z określonym wyjątkiem. 

Zlicza metryki, takie jak liczba żądań i częstotliwość występowania wyjątków są dostosowywane do wyrównania częstotliwość próbkowania, tak aby pokazywały około poprawne wartości w Eksploratorze metryk.

### <a name="update-nuget-packages"></a>Aktualizowanie pakietów NuGet ###

Pakiety NuGet projektu aktualizacji do najnowszej wersji *wersji wstępnej* wersję usługi Application Insights. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, wybierz polecenie Zarządzaj pakietami NuGet Sprawdź **Uwzględnij wersję wstępną** i wyszukaj Microsoft.ApplicationInsights.Web. 

### <a name="configuring-adaptive-sampling"></a>Konfigurowanie próbkowanie adaptacyjne ###

W [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), możesz dostosować kilka parametrów w `AdaptiveSamplingTelemetryProcessor` węzła. Dane liczbowe, wyświetlane są wartości domyślne:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Szybkość docelowej adaptacyjne algorytm ma dla **na każdym hoście serwera**. Jeśli Twoja aplikacja sieci web jest uruchamiana na wielu hostach, zmniejsz tę wartość w ten sposób, w ramach stawka docelowych ruchu w portalu usługi Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Interwał, w którym ponowna ocena jest to bieżąca liczba danych telemetrycznych. Ocena jest wykonywane to ruchoma średnia. Można skrócić interwał ten przypadku ponosić odpowiedzialności wobec nagłe wzrosty telemetrii.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Podczas pobierania próbek zmiany wartość procentową, po jakim czasie po firma Microsoft mogą niższe wartości procentowej próbkowania ponownie, aby przechwycić mniejszej ilości danych.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Podczas pobierania próbek zmiany wartość procentową, jak najszybciej po firma Microsoft mogą zwiększyć wartości procentowej próbkowania ponownie, aby przechwycić więcej danych.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Jako wartości procentowej próbkowania różni się co to jest minimalna wartość, którą firma Microsoft mogła ustawić.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Jako wartości procentowej próbkowania różni się co to jest maksymalna wartość, którą firma Microsoft mogła ustawić.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Do obliczania średniej ruchomej wagę przypisane do najnowszych wartości. Użyj wartości równe lub mniejsze niż 1. Mniejsze wartości wprowadź algorytm mniej reaktywnych na nagłe zmiany.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Wartość przypisana w momencie aplikacja właśnie została uruchomiona. Nie zmniejsza to podczas debugowania. 

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Rozdzielana średnikami lista typów, które mają być pobrane. Rozpoznane typy to: Śledzenie zdarzeń, wyjątków, widok strony, żądania, zależności. Wszystkie wystąpienia określonych typów są przekazywane; typy, które nie zostały określone są próbkowane.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Rozdzielana średnikami lista typów, które mają być pobrane. Rozpoznane typy to: Śledzenie zdarzeń, wyjątków, widok strony, żądania, zależności. Określonych typów są próbkowane; wszystkie wystąpienia innych typów są zawsze przesyłane.


**Wyłączanie** adaptacyjnego próbkowania, Usuń węźle AdaptiveSamplingTelemetryProcessor z konfiguracji dotycząca usługi Application insights.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatywa: Konfigurowanie próbkowania adaptacyjnego w kodzie
Zamiast ustawiać parametru próbkowania w pliku .config, można programowo ustawić te wartości. Dzięki temu można określić funkcji wywołania zwrotnego, które jest wywoływane zawsze wtedy, gdy częstotliwość próbkowania ponowna ocena. Można to, na przykład, aby dowiedzieć się, jakie częstotliwość próbkowania jest używany.

Usuń `AdaptiveSamplingTelemetryProcessor` węzeł z pliku Config.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Dowiedz się więcej o telemetrii procesorów](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

<a name="other-web-pages"></a>

## <a name="sampling-for-web-pages-with-javascript"></a>Próbkowania dla stron sieci web za pomocą języka JavaScript
Można skonfigurować stron sieci web do próbkowania szybkościami transmisji z dowolnego serwera. 

Gdy użytkownik [Konfigurowanie stron sieci web usługi Application Insights](../../azure-monitor/app/javascript.md), zmodyfikować fragment kodu JavaScript, który można pobrać z portalu usługi Application Insights. (W aplikacjach ASP.NET, fragment kodu zwykle znajduje się w _Layout.cshtml.)  Wstaw wiersz, takie jak `samplingPercentage: 10,` przed klucz Instrumentacji:

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

Dla wartości procentowej pobierania próbek wybierz wartość procentowa jest bliskie 100/N, gdzie N to liczba całkowita.  Próbkowanie aktualnie nie obsługuje inne wartości.

Włączenie próbkowania szybkościami transmisji na serwerze, klientami a serwerem będzie synchronizować tak, w wyszukiwania, możesz przechodzić między żądań i wyświetleń stron powiązane.

## <a name="fixed-rate-sampling-for-aspnet-and-java-web-sites"></a>Próbkowanie stałej stawki dla witryn sieci web platformy ASP.NET i Java
Stałą stawkę próbkowania powoduje zmniejszenie ruchu wysyłane z serwera sieci web i przeglądarek sieci web. W przeciwieństwie do próbkowania adaptacyjnego zmniejsza telemetrii według stałej stawki ustalanej postanowiła przez użytkownika. Także synchronizuje klienta i serwera próbkowania tak, aby powiązane elementy są zachowywane — na przykład, kiedy wyświetlasz widok strony, w polu wyszukiwania można znaleźć jego powiązanego żądania.

Algorytm próbkowania zachowuje powiązanych elementów. Dla każdego żądania HTTP zdarzenia, żądania i jej powiązane zdarzenia są odrzucane lub przekazywane ze sobą. 

W Eksploratorze metryk kursy, takie jak liczby żądań i wyjątków są pomnożonej przez współczynnik celu kompensacji częstotliwość próbkowania, aby były one około poprawne.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Konfigurowanie próbkowania stałej stawki na platformie ASP.NET ###

1. **Aktualizowanie pakietów NuGet projektu** do najnowszej wersji *wersji wstępnej* wersję usługi Application Insights. W programie Visual Studio, kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań, wybierz polecenie Zarządzaj pakietami NuGet Sprawdź **Uwzględnij wersję wstępną** i wyszukaj Microsoft.ApplicationInsights.Web. 
2. **Wyłącz próbkowanie adaptacyjne**: W [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), usunąć lub skomentować `AdaptiveSamplingTelemetryProcessor` węzła.
   
    ```xml
   
    <TelemetryProcessors>
   
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->

    ```

3. **Włącz moduł próbkowania szybkościami transmisji.** Dodaj następujący fragment do [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Konfigurowanie stałej częstotliwość próbkowania w języku JAVA ###

1. Pobieranie i konfigurowanie aplikacji sieci web za pomocą najnowszej [aplikacji insights java SDK](../../azure-monitor/app/java-get-started.md)

2. **Włącz moduł próbkowania stałej stawki** , dodając poniższy fragment kodu do pliku ApplicationInsights.xml.

```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type = "FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name = "SamplingPercentage" value = "50" />
            </Processor>
        </BuiltInProcessors>
    <TelemetryProcessors/>
```

3. Możesz dołączyć lub wykluczyć określone rodzaje danych telemetrycznych z próbkowania przy użyciu następujących tagów, wewnątrz tagu procesora "FixedRateSamplingTelemetryProcessor"
```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
```
Dostępne są następujące typy telemetrii, które mogą być dołączone lub wykluczone z próbkowania: Zależności, zdarzeń, wyjątków, widok strony, żądania i śledzenia.

> [!NOTE]
> Dla wartości procentowej pobierania próbek wybierz wartość procentowa jest bliskie 100/N, gdzie N to liczba całkowita.  Próbkowanie aktualnie nie obsługuje inne wartości.
> 
> 

### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternatywa: włączenia próbkowania szybkościami transmisji w kodzie serwera
Zamiast ustawiać parametru próbkowania w pliku .config, można programowo ustawić te wartości. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Dowiedz się więcej o telemetrii procesorów](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

## <a name="when-to-use-sampling"></a>Kiedy należy używać próbkowania?
Jeśli używasz wersji 2.0.0-beta3 zestawu SDK platformy ASP.NET jest automatycznie włączone próbkowanie adaptacyjne lub nowszej. Niezależnie od tego, która wersja zestawu SDK, możesz użyć można włączyć próbkowanie fragmentaryczne umożliwia przykładowe zbierane dane usługi Application Insights.

Próbkowanie nie jest domyślnie włączona w języku Java SDK. Obecnie obsługuje on tylko stały częstotliwość próbkowania. Próbkowanie adaptacyjne nie jest obsługiwana w zestawie SDK języka Java.

Ogólnie rzecz biorąc w przypadku większości aplikacji małych i średnich próbkowanie nie jest potrzebny. Najbardziej użyteczne informacje diagnostyczne i najdokładniejszych statystyki są uzyskiwane przez zbieranie danych dla wszystkich działań użytkownika. 

Dostępne są następujące główne zalety próbkowania:

* Aplikacji Insights usługi spadnie ("ograniczenia") punktów danych, gdy aplikacja wysyła bardzo wysoki stopień telemetrii w skrócie time interwału. 
* Aby zachować w ramach [przydziału](../../azure-monitor/app/pricing.md) punktów danych w warstwie cenowej. 
* Aby zmniejszyć ruch sieciowy z kolekcji danych telemetrycznych. 

### <a name="which-type-of-sampling-should-i-use"></a>Jakiego typu próbkowania należy używać?
**Użyj pozyskiwania próbkowania, jeśli:**

* Często można przejść przez miesięczny limit danych telemetrycznych.
* Używasz wersji zestawu SDK, który nie obsługuje pobierania próbek — na przykład ASP.NET wersji wcześniejszej niż 2.
* Otrzymujesz wiele danych telemetrycznych z przeglądarek użytkowników.

**Użyj stałej stawki próbkowania, jeśli:**

* Używasz zestawu SDK usługi Application Insights dla platformy ASP.NET sieci web services w wersji 2.0.0 lub nowszej lub zestawu SDK Java v2.0.1 lub nowszym, oraz
* Chcesz próbkowanie zsynchronizowane między klientem i serwerem, tak, że jeśli analizujesz zdarzenia w [wyszukiwania](../../azure-monitor/app/diagnostic-search.md), można nawigować między powiązanymi zdarzeniami na kliencie i serwerze, na przykład wyświetlenia stron i żądań http.
* Upewnieniu się, wartości procentowej próbkowania odpowiednie dla twojej aplikacji. Powinien być odpowiednio wysoka, aby uzyskać dokładne metryki, ale poniżej stawki przekraczający cen limitu przydziału i limity ograniczania. 

**Użyj próbkowanie adaptacyjne:**

Jeśli warunki na użycie innych metod próbkowanie nie mają zastosowania, firma Microsoft zaleca próbkowanie adaptacyjne. Ta opcja jest włączona domyślnie w zestaw ASP.NET server SDK, wersji 2.0.0-beta3 lub nowszej. Go nie zmniejsza ruch aż do osiągnięcia niektóre minimalne wskaźnik, w związku z tym, niskie użycie witryny nie zostaną zmienione.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Jak sprawdzić, czy trwa próbkowanie?
Aby odnaleźć rzeczywiste próbkowania niezależnie od tego, gdzie zostały zastosowane, należy użyć [zapytania usługi Analytics](../../azure-monitor/app/analytics.md) takich jak to:

```
union * 
| where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h), itemType
| render timechart 
```

W każdym przechowywane rekordu, `itemCount` wskazuje liczbę oryginalnego rekordy, które reprezentuje on równą 1 + Liczba poprzednich odrzuconych rekordów. 

## <a name="how-does-sampling-work"></a>Jak działa próbkowanie?
Próbkowanie szybkościami transmisji funkcji zestawu SDK w wersji platformy ASP.NET z 2.0.0 oraz zestaw Java SDK w wersji 2.0.1 i lub nowszy. Próbkowanie adaptacyjne to funkcja zestawu SDK w wersji platformy ASP.NET w wersji 2.0.0. Próbkowanie fragmentaryczne jest funkcją usługi Application Insights i może być w operacji, jeśli zestaw SDK nie wykonuje próbkowania. 

Algorytm próbkowania decyduje, elementy danych telemetrycznych, które można usunąć, a które zapewnienie (czy w zestawie SDK lub w usłudze Application Insights). Decyzja próbkowania opiera się na kilka reguł, które Staraj się zachować wszystkie punkty danych powiązane ze sobą bez zmian, utrzymywanie diagnostycznymi w usłudze Application Insights jest praktyczna i niezawodny, nawet w przypadku ograniczonego zestawu danych. Na przykład jeśli żądanie nie powiodło się Twoja aplikacja wysyła dodatkową telemetrię elementów (na przykład wyjątku i ślady zarejestrowane z tym żądaniem), próbkowanie nie zostanie podzielona, to żądanie i inną telemetrią. Ją zachowuje lub porzuca ich wszystko ze sobą. W rezultacie sprawdzając szczegóły żądania w usłudze Application Insights, należy zawsze możesz zobaczyć żądanie, wraz z jego elementów skojarzonych danych telemetrycznych. 

Decyzja próbkowania opiera się na identyfikator operacji żądania, co oznacza, że wszystkie elementy danych telemetrycznych należących do określonej operacji jest zachowywana lub porzucony. Elementy danych telemetrycznych, które nie mają operacji próbkowania zestawie (przykładowe elementy dane telemetryczne zgłoszone asynchronicznego wątków przy użyciu Brak kontekstu http) identyfikator przechwytuje po prostu procent elementów telemetrii każdego typu. Przed 2.5.0-beta2 zestawu SDK platformy .NET i 2.2.0-beta3 zestawu SDK dla platformy ASP.NET Core, decyzja próbkowania zależała od wyznaczania wartości skrótu identyfikatora użytkownika dla aplikacji, które definiują "user" (czyli najbardziej typowych aplikacjach sieci web). Dla typów aplikacji, które nie zostały zdefiniowane użytkownikom (np. usługi sieci web) decyzji próbkowania zależała od identyfikator operacji żądania.

Podczas wyświetlania danych telemetrycznych do Ciebie, usługa Application Insights dostosowywaniu metryk w tej samej wartości procentowej próbkowania, który został użyty w czasie zbierania, aby zrekompensować Brak punktów danych. W związku z tym przeglądając dane telemetryczne w usłudze Application Insights, użytkownicy są widoczne statystycznie prawidłowej przybliżeń, które znajdują się bardzo blisko liczb rzeczywistych.

Dokładność zbliżenia w dużym stopniu zależy od wartości procentowej próbkowania skonfigurowany. Dokładność zwiększa się także, w przypadku aplikacji, które obsługują duże obciążenie ogólnie podobne żądania z wielu użytkowników. Z drugiej strony w przypadku aplikacji, które nie działają z znaczne obciążenie próbkowanie nie jest potrzebna jak te aplikacje zwykle wysłać ich dane telemetryczne jednocześnie nie przekraczając limit przydziału, nie powodując utraty danych z ograniczenia przepustowości. 

> [!WARNING]
> Usługa Application Insights nie przykładowe typy telemetrii metryk i sesji. Zmniejszenie dokładność może być wysoce niepożądane dla tych typów danych telemetrycznych.
> 

### <a name="adaptive-sampling"></a>Próbkowanie adaptacyjne
Próbkowanie adaptacyjne dodaje składnik, który monitoruje bieżącej szybkości transmisji z zestawu SDK i dopasowuje wartości procentowej próbkowania w celu pozostania w docelowym maksymalna szybkość. Dostosowania są przeliczane w regularnych odstępach czasu i opiera się na ruchomą średnią szybkość transmisji wychodzących.

## <a name="sampling-and-the-javascript-sdk"></a>Pobieranie próbek i zestaw SDK języka JavaScript
Po stronie klienta (JavaScript) zestawu SDK uczestniczy w stałej stawki próbkowania w połączeniu z zestawu SDK po stronie serwera. Instrumentowane stron wysyła dane telemetryczne po stronie klienta z tych samych użytkowników, dla których po stronie serwera wprowadzone swojej decyzji "sample w". Tę logikę zaprojektowano w celu zachowania integralności sesji użytkownika na stronach klienta i serwera. W związku z dowolnego elementu określonego telemetrii w usłudze Application Insights można znaleźć wszystkie pozostałe elementy danych telemetrycznych dla tego użytkownika lub sesję. 

*Moje klienta i dane telemetryczne po stronie serwera nie pokazuj skoordynowanego przykładów, jak opisano powyżej.*

* Sprawdź czy jest włączony próbkowania stałej stawki zarówno na serwerze i kliencie.
* Upewnij się, że wersja zestawu SDK jest w wersji 2.0 lub nowszej.
* Sprawdź, można ustawić tej samej wartości procentowej próbkowania w klienta i serwera.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
*Dlaczego nie jest próbkowanie proste "zbieranie X procent każdego typu danych telemetrycznych"?*

* Gdy ta metoda pobierania próbek zapewni o bardzo dużej dokładności w przybliżeń metryki, zaburzyłaby możliwość skorelowania danych diagnostycznych na użytkowników, sesji i żądań, co ma kluczowe znaczenie dla diagnostyki. W związku z tym, działa próbkowanie lepiej za pomocą "zbieranie danych telemetrycznych elementy dla X procent użytkowników aplikacji" lub "Zbieraj wszystkie dane telemetryczne dla X Procent żądań aplikacji" Logic Apps. W przypadku elementów telemetrii nie jest skojarzona z żądania (na przykład asynchronicznego przetwarzania w tle), bazowy jest "zbieranie X procent wszystkich elementów, dla każdego typu danych telemetrycznych." 

*Mogą ulec zmianie wartości procentowej próbkowania?*

* Tak, próbkowanie adaptacyjne stopniowo zmienia wartości procentowej próbkowania, zgodnie z aktualnie obserwowanych ilością danych telemetrycznych.

*Użycie stałej częstotliwość próbkowania, skąd mam wiedzieć, które próbkowania procent będzie najlepsza dla mojej aplikacji?*

* Jednym ze sposobów zaczynać adaptacyjnego próbkowania, Dowiedz się, co oceń rozliczy na (zobacz powyższe pytanie), a następnie przejdź do stałej szybkości próbkowanie przy użyciu tego kursu. 
  
    W przeciwnym razie mieć Odgadywanie. Analizowanie bieżące użycie telemetrii w usłudze Application Insights, obserwować, wszelkie ograniczenia przepustowości występuje i oszacowania ilości zbieranych danych telemetrycznych. Te trzy danych wejściowych, wraz z wybranej warstwy cenowej, sugestie, ile możesz chcieć ograniczyć ilość zbieranych danych telemetrycznych. Jednak zwiększenie liczby użytkowników lub innych przesunięcia w ilości danych telemetrycznych może unieważnić szacowania.

*Co się stanie, jeśli mam skonfigurowane zbyt niskiej wartości procentowej pobierania próbek?*

* Procent próbkowania zbyt niski (próbkowanie agresywne) powoduje zmniejszenie dokładności przybliżeń, podczas próby wyrównania wizualizacji danych redukcji woluminu danych usługi Application Insights. Ponadto doświadczenie diagnostyczne może być obniżona, jako część żądania rzadko, ile niepowodzeniem lub wolne próbkować można się.

*Co się stanie, jeśli mam skonfigurowane zbyt wysokiej wartości procentowej pobierania próbek?*

* Konfigurowanie próbkowania zbyt wysoki procent (nie agresywne wystarczająco) powoduje niewystarczające redukcję ilości zbieranych danych telemetrycznych. Nadal może wystąpić utrata danych dane telemetryczne dotyczące ograniczania przepustowości, a koszt za pomocą usługi Application Insights może być większa niż należy zaplanować z powodu opłaty za użycie nadwyżkowe.

*Na jakich platformach można użyć próbkowania?*

* Próbkowanie fragmentaryczne mogą być wykonywane automatycznie dla żadnych danych telemetrycznych powyżej niektórych woluminu, jeśli zestaw SDK nie wykonuje próbkowania. To będzie działać, na przykład, jeśli używasz starszej wersji zestawu SDK platformy ASP.NET lub starszą wersję SDK(1.0.10 or before) języka Java.
* Jeśli używasz zestawu SDK platformy ASP.NET w wersji 2.0.0 lub nowszym (w obsługiwanych na platformie Azure lub na własnym serwerze), można pobrać adaptacyjnego próbkowania domyślnie, ale możesz przełączyć się do stałej stawki, zgodnie z powyższym opisem. Z włączonym próbkowaniem szybkościami transmisji, przeglądarki, zestaw SDK automatycznie synchronizuje próbkowania powiązanych zdarzeń. 
* Jeśli używasz zestawu SDK Java wersji 2.0.1 lub nowszych można skonfigurować plik ApplicationInsights.xml do włączenia stały częstotliwość próbkowania. Próbkowanie jest domyślnie wyłączona. Z włączonym próbkowaniem szybkościami transmisji, przeglądarki, zestaw SDK automatycznie synchronizuje próbkowania powiązanych zdarzeń.

*Brak niektórych rzadkich zdarzeń, które można zawsze mają być wyświetlane. Jak można je poza moduł próbkowania?*

* Zainicjuj osobne wystąpienie TelemetryClient za pomocą nowego TelemetryConfiguration (nie domyślnie aktywna). Używać, aby wysyłać zdarzenia rzadkie.

## <a name="next-steps"></a>Kolejne kroki
* [Filtrowanie](../../azure-monitor/app/api-filtering-sampling.md) może zapewnić więcej ścisłej kontroli wysyła zestawu SDK.

