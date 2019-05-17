---
title: Próbkowanie danych telemetrycznych w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Jak zapewnić ilości danych telemetrycznych pod kontrolą.
services: application-insights
documentationcenter: windows
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.author: cithomas
ms.openlocfilehash: c11eeb84fe4ed1ded93cb8de7ff54b756fd36749
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779909"
---
# <a name="sampling-in-application-insights"></a>Próbkowanie w usłudze Application Insights

Próbkowanie to funkcja [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Jest to zalecany sposób zmniejszenia ruchu telemetrycznego i Magazyn, przy jednoczesnym zachowaniu statystycznie prawidłowej analizy danych aplikacji. Filtr wybiera elementy, które są powiązane, dzięki czemu możesz przechodzić między elementami w trakcie badania diagnostyczne.
Metryki liczby umieszczeniem w portalu są one renormalized wziąć pod uwagę próbkowania. Ten sposób minimalizuje żadnego wpływu na dane statystyczne.

Próbkowania zmniejsza koszty ruchu sieciowego i danych i pomaga uniknąć ograniczania przepustowości.

## <a name="in-brief"></a>W skrócie:

* Próbkowanie zachowuje 1 w *n* rejestruje i odrzuca wszystkie pozostałe. Na przykład mogą go zachować jednego w pięciu zdarzenia, częstotliwość próbkowania w wysokości 20%. 
* Próbkowanie adaptacyjne jest domyślnie włączone w najnowszej wersji programu ASP.NET i ASP.NET Core Software Development Kit (SDK).
* Można również ustawić próbkowanie ręcznie. Można to skonfigurować w portalu na *użycie i szacowane koszty strony*, w zestawie SDK platformy ASP.NET w pliku ApplicationInsights.config w ASP.NET Core SDK, za pomocą kodu lub zestaw SDK języka Java w ApplicationInsights.xml pliku.
* Jeśli umieścisz niestandardowych zdarzeń i upewnij się, że zestaw zdarzeń jest zatrzymany lub odrzucone ze sobą potrzebę zdarzeń musi mieć taką samą wartość OperationId.
* Dzielnik próbkowania *n* jest zgłaszany w każdym rekordzie we właściwości `itemCount`, w polu wyszukiwania widocznego w obszarze przyjazna nazwa "licznik żądanie" lub "liczba zdarzeń". `itemCount==1`podczas próbkowania nie jest w operacji.
* Jeśli piszesz zapytania analityczne, wykonaj następujące czynności [uwzględnienia próbkowania](../../azure-monitor/log-query/aggregations.md). W szczególności, a nie po prostu wciąż dochodzą nowe rekordy, należy użyć `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Typy próbkowania

Istnieją trzy metody alternatywnej próbkowania:

* **Próbkowanie adaptacyjne** automatycznie dostosowuje ilości telemetrii wysyłanych z zestawu SDK w aplikacji ASP.NET/ASP.NET Core. Jest to próbkowania domyślnej wersji 2.0.0-beta3 v zestawu SDK sieci Web platformy ASP.NET i 2.2.0-beta1 v Microsoft.ApplicationInsights.AspNetCore zestawu SDK i nowszych wersjach.  Próbkowanie adaptacyjne jest obecnie dostępna tylko dla telemetrii po stronie serwera ASP.NET.

* **Próbkowanie stałej stawki** powoduje zmniejszenie ilości telemetrii wysyłanych z Twojej platformy ASP.NET lub ASP.NET Core lub Java serwera i z przeglądarek użytkowników. Możesz ustawić częstotliwość. Klient i serwer będzie synchronizować ich próbkowania tak, w wyszukiwania, możesz przechodzić między żądań i wyświetleń stron powiązane.

* **Próbkowanie fragmentaryczne** działa w witrynie Azure portal. Powoduje odrzucenie niektórych danych telemetrycznych przychodzący z Twoją aplikację, częstotliwość próbkowania, który został ustawiony. Nie zmniejszenia ruchu telemetrycznego, wysyłanych z aplikacji, ale pomaga zagwarantować bezpieczeństwo w ramach miesięcznego limitu przydziału. Główną zaletą próbkowanie fragmentaryczne jest, że można skonfigurować częstotliwość próbkowania, bez konieczności ponownego wdrażania aplikacji. Próbkowanie fragmentaryczne równomiernie działa w przypadku wszystkich serwerów i klientów.

Jeśli operacja adaptacyjną lub stała szybkość pobierania próbek, próbkowanie fragmentaryczne jest wyłączona.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Próbkowanie adaptacyjne w aplikacjach sieci Web Core ASP.NET/ASP.NET

Próbkowanie adaptacyjne jest dostępna dla zestawu SDK Application Insights dla platformy ASP.NET v 2.0.0-beta3 i nowszych 2.2.0-beta1 v Microsoft.ApplicationInsights.AspNetCore SDK lub nowszy i jest domyślnie włączona.

Próbkowanie adaptacyjne wpływa na ilość danych telemetrycznych wysyłanych z aplikacji serwera sieci web z punktem końcowym usługi Application Insights. Wolumin jest automatycznie dostosowywany do zachowania w ramach określonego maksymalną szybkość ruchu i jest kontrolowana przez ustawienia `MaxTelemetryItemsPerSecond`. Jeśli aplikacja generuje małą ilością danych telemetrycznych, takich jak podczas debugowania lub z powodu o niskim użyciu elementy nie uzyskać próbkowane tak długo, jak wolumin jest poniżej `MaxTelemetryItemsPerSecond`. Jako wolumin rośnie telemetrię częstotliwość próbkowania jest ustawiony tak, aby osiągnąć woluminu docelowego.

Aby osiągnąć woluminu docelowego, niektóre wygenerowane dane telemetryczne zostaną odrzucone. Ale takie jak inne rodzaje próbkowania algorytm zachowuje elementy powiązane dane telemetryczne. Na przykład gdy masz sprawdzanie danych telemetrycznych w polu wyszukiwania, będziesz mieć możliwość odnaleźć żądania powiązaną z określonym wyjątkiem.

Zlicza metryki, takie jak liczba żądań i częstotliwość występowania wyjątków są dostosowywane do wyrównania częstotliwość próbkowania, tak aby pokazywały około poprawne wartości w Eksploratorze metryk.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurowanie próbkowanie adaptacyjne dla aplikacji ASP.NET

[Dowiedz się,](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) o konfigurowaniu próbkowanie adaptacyjne dla aplikacji programu ASP.NET Core. 

W [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), możesz dostosować kilka parametrów w `AdaptiveSamplingTelemetryProcessor` węzła. Dane liczbowe, wyświetlane są wartości domyślne:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Szybkość docelowej adaptacyjne algorytm ma dla **na każdym hoście serwera**. Jeśli Twoja aplikacja sieci web jest uruchamiana na wielu hostach, zmniejsz tę wartość w ten sposób, w ramach stawka docelowych ruchu w portalu usługi Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Interwał, w którym bieżąca liczba telemetrii jest ponownie oceniane. Ocena jest wykonywane to ruchoma średnia. Można skrócić interwał ten przypadku ponosić odpowiedzialności wobec nagłe wzrosty telemetrii.
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
  
    Wartość przypisana w momencie aplikacja właśnie została uruchomiona. Nie należy zmniejszyć wartość, którą debugujesz.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Rozdzielana średnikami lista typów, które mają być pobrane. Rozpoznane typy to: Śledzenie zdarzeń, wyjątków, widok strony, żądania, zależności. Wszystkie wystąpienia określonych typów są przekazywane; typy, które nie zostały określone są próbkowane.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Rozdzielana średnikami lista typów, które mają być pobrane. Rozpoznane typy to: Śledzenie zdarzeń, wyjątków, widok strony, żądania, zależności. Określonych typów są próbkowane; wszystkie wystąpienia innych typów są zawsze przesyłane.


**Aby wyłączyć** adaptacyjnego próbkowania, usuwania węzłów AdaptiveSamplingTelemetryProcessor applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatywa: Konfigurowanie próbkowania adaptacyjnego w kodzie

Zamiast ustawiać parametru próbkowania w pliku .config, można programowo ustawić te wartości.

1. Usuń wszystkie `AdaptiveSamplingTelemetryProcessor` węzły z pliku Config.
2. Aby skonfigurować próbkowania adaptacyjnego, należy użyć następującego fragmentu kodu.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Dowiedz się więcej o telemetrii procesorów](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Można również dostosować częstotliwość próbkowania dla każdego typu danych Telemetrycznych indywidualnie lub nawet można wykluczyć określonych typów z próbkowane w ogóle. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurowanie próbkowanie adaptacyjne dla aplikacji programu ASP.NET Core.

Istnieje nie `ApplicationInsights.Config` w przypadku aplikacji ASP.NET Core, więc każdej konfiguracji jest realizowana za pośrednictwem kodu.
Próbkowanie adaptacyjne jest domyślnie włączone dla wszystkich aplikacji platformy ASP.NET Core. Można wyłączyć lub dostosować zachowanie pobierania próbek.

### <a name="turning-off-adaptive-sampling"></a>Wyłączenie próbkowania adaptacyjnego

Funkcję próbkowania domyślne można wyłączyć podczas dodawania usługi Application Insights w metodzie ```ConfigureServices```przy użyciu ```ApplicationInsightsServiceOptions``` w ramach `Startup.cs` pliku:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
    //...
}
```

Powyższy kod spowoduje wyłączenie funkcji pobierania próbek. Wykonaj poniższe kroki, aby dodać próbkowanie z innych opcji dostosowywania.

### <a name="configure-sampling-settings"></a>Skonfiguruj ustawienia próbkowania

Należy użyć metod rozszerzenia ```TelemetryProcessorChainBuilder``` jak pokazano poniżej, aby dostosować zachowanie pobierania próbek.

> [!IMPORTANT]
> Jeśli używasz tej metody do skonfigurowania próbkowania, upewnij się, że używasz aiOptions.EnableAdaptiveSampling = false;. ustawienia z AddApplicationInsightsTelemetry().

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**Jeśli przy użyciu powyższej metody konfigurowania próbkowania, upewnij się użyć ```aiOptions.EnableAdaptiveSampling = false;``` ustawienia z AddApplicationInsightsTelemetry().**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites"></a>Próbkowanie stałej stawki dla witryn sieci Web platformy ASP.NET, ASP.NET Core i Java

Stałą stawkę próbkowania powoduje zmniejszenie ruchu wysyłane z serwera sieci web i przeglądarek sieci web. W przeciwieństwie do próbkowania adaptacyjnego zmniejsza telemetrii według stałej stawki ustalanej postanowiła przez użytkownika. Także synchronizuje klienta i serwera próbkowania tak, aby powiązane elementy są zachowywane — na przykład, kiedy wyświetlasz widok strony, w polu wyszukiwania można znaleźć jego powiązanego żądania.

Podobnie jak inne techniki próbkowania również zachowuje powiązanych elementów. Dla każdego żądania HTTP zdarzenia, żądania i jej powiązane zdarzenia są odrzucane lub przekazywane ze sobą.

W Eksploratorze metryk kursy, takie jak liczby żądań i wyjątków są pomnożonej przez współczynnik celu kompensacji częstotliwość próbkowania, aby były one około poprawne.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Konfigurowanie próbkowania stałej stawki na platformie ASP.NET

1. **Wyłącz próbkowanie adaptacyjne**: W [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), usunąć lub skomentować `AdaptiveSamplingTelemetryProcessor` węzła.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Włącz moduł próbkowania szybkościami transmisji.** Dodaj następujący fragment do [plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternatywa: włączenia próbkowania szybkościami transmisji w kodzie serwera
    
    Zamiast ustawiać parametru próbkowania w pliku .config, można programowo ustawić te wartości. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([Dowiedz się więcej o telemetrii procesorów](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Konfigurowanie stałej częstotliwość próbkowania w programie ASP.NET Core

1. **Wyłącz próbkowanie adaptacyjne**:  Można wprowadzić zmiany w metodzie ```ConfigureServices```przy użyciu ```ApplicationInsightsServiceOptions```:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    //...
    }
    ```

2. **Włącz moduł próbkowania szybkościami transmisji.** Można wprowadzić zmiany w metodzie ```Configure``` w następujący fragment kodu:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

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

<a name="other-web-pages"></a>



## <a name="ingestion-sampling"></a>Próbkowanie fragmentaryczne

Ta forma próbkowania działa w punkcie, w którym dane telemetryczne z serwera sieci web, przeglądarek i urządzeń osiąga punkt końcowy usługi Application Insights. Chociaż nie redukują ruch danych telemetrycznych wysyłanych z aplikacji, to zmniejszyć ilość przetwarzane i przechowywane (i opłata za) przez usługę Application Insights.

Użyj tego typu próbkowania, jeśli aplikacja często przechodzi przez osiągnięto miesięczny limit przydziału, a nie ma możliwości przy użyciu zestawu SDK rodzajów próbkowania. 

Ustaw częstotliwość próbkowania w użycie i szacowane koszty strony:

![Z poziomu bloku Przegląd aplikacji kliknij pozycję Ustawienia, limit przydziału, próbek, a następnie wybierz częstotliwość próbkowania, a następnie kliknij przycisk aktualizacji.](./media/sampling/04.png)

Podobnie jak inne rodzaje próbkowania algorytm zachowuje elementy powiązane dane telemetryczne. Na przykład gdy masz sprawdzanie danych telemetrycznych w polu wyszukiwania, będziesz mieć możliwość odnaleźć żądania powiązaną z określonym wyjątkiem. Zlicza metryki, takie jak liczba żądań i częstotliwość występowania wyjątków poprawnie zostaną zachowane.

Punktów danych, które są odrzucane przez pobieranie próbek nie są dostępne w dowolnej usługi Application Insights funkcji takich jak [eksportu ciągłego](../../azure-monitor/app/export-telemetry.md).

Próbkowanie fragmentaryczne nie działają podczas SDK próbkowanie adaptacyjne lub stałej stawki. Próbkowanie adaptacyjne jest domyślnie włączona, gdy ASP.NET/ASP.NET Core SDK jest włączona w programie Visual Studio, czy włączono rozszerzenia aplikacji sieci Web platformy Azure lub za pomocą Monitora stanu i próbkowanie fragmentaryczne jest wyłączona. Jeśli częstotliwość próbkowania w zestawie SDK jest mniejsza niż 100% (tj.) elementy są próbkowane), a następnie częstotliwość próbkowania pozyskiwania, który został ustawiony jest ignorowana.

> [!WARNING]
> Wartości wyświetlane na kafelku wskazuje wartość, która zostanie ustawiona próbkowanie fragmentaryczne. Nie on reprezentować rzeczywiste próbkowania, jeśli trwa próbkowanie zestawu SDK.
>
>
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

## <a name="when-to-use-sampling"></a>Kiedy należy używać próbkowania?

Próbkowanie adaptacyjne jest włączana automatycznie w najnowszych .NET i .NET Core SDK. Niezależnie od tego, która wersja zestawu SDK, możesz użyć można włączyć próbkowanie fragmentaryczne umożliwia przykładowe zbierane dane usługi Application Insights.

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
* Otrzymujesz zbyt dużej ilości danych telemetrycznych z przeglądarek użytkowników.

**Użyj stałej stawki próbkowania, jeśli:**

* Używasz zestawu SDK usługi Application Insights dla platformy ASP.NET sieci web services w wersji 2.0.0 lub nowszej lub zestawu SDK Java v2.0.1 lub nowszym, oraz
* Chcesz próbkowanie zsynchronizowane między klientem i serwerem, tak, że jeśli analizujesz zdarzenia w [wyszukiwania](../../azure-monitor/app/diagnostic-search.md), można nawigować między powiązanymi zdarzeniami na kliencie i serwerze, na przykład wyświetlenia stron i żądań http.
* Upewnieniu się, wartości procentowej próbkowania odpowiednie dla twojej aplikacji. Powinien być odpowiednio wysoka, aby uzyskać dokładne metryki, ale poniżej stawki przekraczający cen limitu przydziału i limity ograniczania. 

**Użyj próbkowanie adaptacyjne:**

Jeśli warunki na użycie innych metod próbkowanie nie mają zastosowania, firma Microsoft zaleca próbkowanie adaptacyjne. To ustawienie jest włączone domyślnie na serwerze ASP.NET/ASP.NET Core SDK. Go nie zmniejsza ruch aż do osiągnięcia niektóre minimalne wskaźnik, w związku z tym, niskie użycie witryny nie zostaną zmienione.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Jak sprawdzić, czy trwa próbkowanie?

Aby odnaleźć rzeczywiste próbkowania niezależnie od tego, gdzie zostały zastosowane, należy użyć [zapytania usługi Analytics](../../azure-monitor/app/analytics.md) takich jak to:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Jeśli RetainedPercentage dla dowolnego typu, jest mniejsza niż 100 tego elementu są próbkowane.

**Usługa Application Insights nie przykładowe sesji, metryki i typy telemetrii w dowolnym opisane powyżej metody pobierania próbek liczników wydajności. Te typy są zawsze wykluczeni z próbkowania, jako zmniejszenie dokładności może być wysoce niepożądane dla tych typów danych telemetrycznych**

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

*Co to jest domyślne zachowanie pobieranie próbek w ASP.NET i ASP.NET Core SDK?*

* Jeśli używasz jednej z najnowszych wersji zestawu SDK powyżej próbkowanie adaptacyjne jest domyślnie włączona, przy użyciu pięć elementów danych telemetrycznych na sekundę.
  Istnieją 2 AdaptiveSamplingTelemetryProcessors dodawany domyślnie i jeden zawiera typ zdarzenia podczas próbkowania, a pomija drugi typ zdarzenia próbkowania. Ta konfiguracja oznacza, że zestaw SDK podejmie próbę ograniczyć liczbę elementów danych telemetrycznych do pięć elementów danych telemetrycznych typów zdarzeń i pięć elementów danych telemetrycznych wszystkich innych typów w połączeniu, zapewniając, że zdarzenia są pobrane oddzielnie od innych typów danych Telemetrycznych. Zdarzenia są zwykle używane do telemetrii biznesowych i najprawdopodobniej nie powinny zależeć przez woluminy diagnostyczne dane telemetryczne.
  
  Poniższy kod przedstawia domyślny plik ApplicationInsights.Config wygenerowany. Zgodnie z opisem, istnieją dwa osobne węzły AdaptiveSamplingTelemetryProcessor dodane, z wyjątkiem typów zdarzeń, a druga uwzględniania go. W programie ASP.NET Core dokładnie takie samo zachowanie domyślne jest włączona w kodzie. Użyj przykłady we wcześniejszej sekcji dokumentu, aby zmienić to zachowanie domyślne.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*Telemetria może być próbkowany więcej niż jeden raz?*

* Nie. SamplingTelemetryProcessors zignorować elementów z próbkowanie zagadnienia, jeśli element jest już pobrane. To samo dotyczy próbkowanie fragmentaryczne jako, które nie dotyczą próbkowania te elementy, które już są próbkowane w zestawie SDK, sama. "

*Dlaczego nie jest próbkowanie proste "zbieranie X procent każdego typu danych telemetrycznych"?*

* Gdy ta metoda pobierania próbek zapewni wysoki poziom dokładności przybliżeń metryki, zaburzyłaby możliwość skorelowania danych diagnostycznych na użytkowników, sesji i żądań, co ma kluczowe znaczenie dla diagnostyki. W związku z tym, działa próbkowanie lepiej za pomocą "zbieranie danych telemetrycznych elementy dla X procent użytkowników aplikacji" lub "Zbieraj wszystkie dane telemetryczne dla X Procent żądań aplikacji" Logic Apps. W przypadku elementów telemetrii nie jest skojarzona z żądania (na przykład asynchronicznego przetwarzania w tle), plan awaryjny jest "zbieranie X procent wszystkich elementów, dla każdego typu danych telemetrycznych." 

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

* Próbkowanie fragmentaryczne mogą być wykonywane automatycznie dla żadnych danych telemetrycznych powyżej niektórych woluminu, jeśli zestaw SDK nie wykonuje próbkowania. Ta konfiguracja będzie działać, na przykład, jeśli używasz starszej wersji zestawu SDK platformy ASP.NET lub starszą wersję SDK(1.0.10 or before) języka Java.
* Jeśli używasz zestawu SDK platformy ASP.NET w wersji 2.0.0 i nowszych lub zestawu SDK platformy ASP.NET CORE w wersji 2.2.0 i nowsze (hostowane na platformie Azure lub na własnym serwerze), można pobrać adaptacyjnego próbkowania domyślnie, ale możesz przełączyć się do stałej stawki, zgodnie z powyższym opisem. Z włączonym próbkowaniem szybkościami transmisji, przeglądarki, zestaw SDK automatycznie synchronizuje próbkowania powiązanych zdarzeń. 
* Jeśli używasz zestawu SDK Java wersji 2.0.1 lub nowszym można skonfigurować plik ApplicationInsights.xml do włączenia stały częstotliwość próbkowania. Próbkowanie jest domyślnie wyłączona. Z włączonym próbkowaniem szybkościami transmisji, przeglądarki, zestaw SDK automatycznie synchronizuje próbkowania powiązanych zdarzeń.

*Brak niektórych rzadkich zdarzeń, które można zawsze mają być wyświetlane. Jak można je poza moduł próbkowania?*

* Najlepszym sposobem osiągnięcia tego jest napisanie niestandardowego [TelemetryProcessor](../../azure-monitor/app/api-filtering-sampling.md#filtering), który konfiguruje `SamplingPercentage` 100 dla elementu telemetrii chcesz zachowane, jak pokazano poniżej. Daje to gwarancję, że wszystkie metody pobierania próbek zignoruje ten element od dowolnego zagadnienia dotyczące pobierania próbek.

```csharp
    if(somecondition)
    {
        ((ISupportSampling)item).SamplingPercentage = 100;
    }
```

## <a name="next-steps"></a>Kolejne kroki

* [Filtrowanie](../../azure-monitor/app/api-filtering-sampling.md) może zapewnić więcej ścisłej kontroli wysyła zestawu SDK.
