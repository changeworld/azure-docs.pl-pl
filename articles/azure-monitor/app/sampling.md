---
title: Próbkowanie danych telemetrycznych na platformie Azure Application Insights | Microsoft Docs
description: Jak zachować ilość danych telemetrycznych pod kontrolą.
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
ms.openlocfilehash: 83243ba7df48db5cd7757a464f0818ef69c4559e
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372566"
---
# <a name="sampling-in-application-insights"></a>Próbkowanie w usłudze Application Insights

Próbkowanie to funkcja [platformy Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Zalecanym sposobem jest zmniejszenie ilości danych telemetrycznych i magazynu przy zachowaniu statystycznie prawidłowej analiz w przypadku aplikacji. Filtr wybiera elementy, które są powiązane, dzięki czemu można przechodzić między elementami podczas przeprowadzania badań diagnostycznych.
Gdy liczba metryk jest prezentowana w portalu, zostaną one rozznormalizowane w celu uwzględnienia próbkowania. W ten sposób minimalizuje wszystkie efekty dotyczące statystyk.

Próbkowanie powoduje zmniejszenie kosztów ruchu i danych oraz pozwala uniknąć ograniczania przepustowości.

## <a name="in-brief"></a>W skrócie:

* Próbkowanie zachowuje 1 w *n* rekordach i odrzuca resztę. Na przykład może zachować jeden w pięciu zdarzeniach, częstotliwość próbkowania 20%. 
* Próbkowanie adaptacyjne jest domyślnie włączone we wszystkich najnowszych wersjach programu ASP.NET i ASP.NET Core Software Development Kit (SDK).
* Możesz również ustawić próbkowanie ręcznie. Tę konfigurację można skonfigurować w portalu na *stronie użycie i szacowane koszty*w zestawie SDK ASP.NET w pliku ApplicationInsights. config, w zestawie SDK ASP.NET Core za pomocą kodu lub zestawu Java SDK w pliku ApplicationInsights. XML.
* Jeśli rejestrujesz zdarzenia niestandardowe i chcesz się upewnić, że zestaw zdarzeń jest zachowywany lub odrzucony, zdarzenia muszą mieć tę samą wartość OperationId.
* W każdym rekordzie w właściwości `itemCount` jest raportowany dzielnik próbkowania *n* , który w polu wyszukiwania pojawia się pod przyjazną nazwą "liczba żądań" lub "liczba zdarzeń". próbkowanie @no__t 0when nie jest w trakcie operacji.
* W przypadku pisania zapytań analitycznych należy [wziąć pod uwagę próbkowanie](../../azure-monitor/log-query/aggregations.md). W szczególności, a nie do zwykłego zliczania rekordów, należy używać `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Typy próbkowania

Istnieją trzy alternatywne metody próbkowania:

* **Próbkowanie adaptacyjne** automatycznie dostosowuje ilość danych telemetrycznych wysyłanych z zestawu SDK w aplikacji ASP.NET/ASP.NET Core. Jest to domyślne próbkowanie z ASP.NET Web SDK v 2.0.0-beta3 i Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-beta1.  Próbkowanie adaptacyjne jest obecnie dostępne tylko dla telemetrii po stronie serwera ASP.NET.

* **Próbkowanie o stałej szybkości** zmniejsza ilość danych telemetrycznych wysyłanych z serwera ASP.NET lub ASP.NET Core lub Java oraz z przeglądarek użytkowników. Należy ustawić stawkę. Klient i serwer zsynchronizują swoje próbkowanie, aby w programie Search można było nawigować między widokami i żądaniami stron pokrewnych.

* **Pobieranie próbek** Działa w Azure Portal. Odrzuci niektóre dane telemetryczne, które nadeszły z Twojej aplikacji, przy ustawionej częstotliwości próbkowania. Nie zmniejsza to ruchu telemetrii wysyłanego z aplikacji, ale ułatwia utrzymywanie się w ramach miesięcznego limitu przydziału. Główną zaletą pobierania próbek jest możliwość ustawienia częstotliwości próbkowania bez ponownego wdrażania aplikacji. Próbkowanie do pozyskiwania działa jednolicie dla wszystkich serwerów i klientów.

W przypadku pobierania próbek z adaptacyjną lub stałą szybkością próbkowanie pozyskiwania jest wyłączone.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Próbkowanie adaptacyjne w aplikacjach sieci Web ASP.NET/ASP.NET Core

Próbkowanie adaptacyjne jest dostępne dla Application Insights SDK dla ASP.NET v 2.0.0-beta3 i nowszych, Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-beta1 i nowszych, i jest domyślnie włączone.

Próbkowanie adaptacyjne wpływa na ilość danych telemetrycznych wysyłanych z aplikacji serwera sieci Web do punktu końcowego usługi Application Insights. Wolumin jest dostosowywany automatycznie, aby zachować w ramach określonej maksymalnej szybkości ruchu, i jest kontrolowany przez ustawienie `MaxTelemetryItemsPerSecond`. Jeśli aplikacja tworzy niską ilość danych telemetrycznych, na przykład podczas debugowania lub z powodu niskiego użycia, elementy nie zostaną porzucone przez procesor próbkowania, o ile ilość poniżej `MaxTelemetryItemsPerSecond`. W miarę wzrostu ilości danych telemetrycznych częstotliwość próbkowania jest korygowana, aby osiągnąć wolumin docelowy.

Aby osiągnąć wolumin docelowy, niektóre z wygenerowanych danych telemetrycznych są odrzucane. Podobnie jak w przypadku innych typów próbkowania, algorytm zachowuje powiązane elementy telemetrii. Na przykład podczas przeprowadzania inspekcji danych telemetrycznych w programie Search będziesz mieć możliwość znalezienia żądania związanego z określonym wyjątkiem.

Liczby metryk, takie jak częstotliwość żądań i częstotliwość wyjątków, są dostosowywane w celu zrekompensowania współczynnika próbkowania, dzięki czemu pokazują one w Eksploratorze metryk w mniej prawidłowych wartościach.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurowanie próbkowania adaptacyjnego dla aplikacji ASP.NET

[Dowiedz się więcej](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) o konfigurowaniu próbkowania adaptacyjnego dla aplikacji ASP.NET Core. 

W [pliku ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)można dostosować kilka parametrów w węźle `AdaptiveSamplingTelemetryProcessor`. Wyświetlane wartości są wartościami domyślnymi:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Szybkość docelowa, którą algorytm adaptacyjny ma dla **każdego hosta serwera**. Jeśli aplikacja sieci Web jest uruchamiana na wielu hostach, Zmniejsz tę wartość tak, aby pozostawała w docelowym wskaźniku ruchu w portalu Application Insights.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Interwał, w którym bieżąca stawka danych telemetrycznych jest przeszacowana. Obliczanie jest wykonywane jako średnia przenoszona. Możesz chcieć skrócić ten interwał, jeśli dane telemetryczne są odpowiedzialne za nagłe rozerwania.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    W przypadku zmiany wartości procentowej próbkowania, jak wkrótce będzie można ponownie obniżyć wartość procentową próbkowania, aby przechwycić mniejszą ilość danych.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    W przypadku zmiany wartości procentowej próbkowania, jak wkrótce będzie możliwe zwiększenie wartości procentowej próbkowania w celu przechwycenia większej ilości danych.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    W miarę jak procent próbkowania różni się od wartości minimalnej, którą można ustawić.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    W miarę jak procent próbkowania różni się od wartości maksymalnej, którą można ustawić.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Przy obliczaniu średniej przenoszonej waga przypisana do najnowszej wartości. Użyj wartości równej lub mniejszej od 1. Mniejsze wartości sprawiają, że algorytm jest mniej aktywny w nagłych zmianach.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Wartość przypisana, gdy aplikacja została właśnie uruchomiona. Nie Redukuj wartości podczas debugowania.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Rozdzielana średnikami lista typów, które nie mają być próbkowane. Rozpoznawane typy to: zależność, zdarzenie, wyjątek, PageView, żądanie, śledzenie. Wszystkie wystąpienia określonych typów są przesyłane; typy, które nie są określone, są próbkowane.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Rozdzielana średnikami lista typów, które mają być próbkowane. Rozpoznawane typy to: zależność, zdarzenie, wyjątek, PageView, żądanie, śledzenie. Określone typy są próbkowane; wszystkie wystąpienia innych typów będą zawsze przesyłane.


**Aby wyłączyć** próbkowanie adaptacyjne, Usuń węzły AdaptiveSamplingTelemetryProcessor z ApplicationInsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatywa: Konfigurowanie próbkowania adaptacyjnego w kodzie

Zamiast ustawiać parametr próbkowania w pliku config, można programowo ustawić te wartości.

1. Usuń wszystkie węzły `AdaptiveSamplingTelemetryProcessor` z pliku. config.
2. Użyj poniższego fragmentu kodu, aby skonfigurować próbkowanie adaptacyjne.

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

([Dowiedz się więcej o procesorach telemetrii](../../azure-monitor/app/api-filtering-sampling.md#filtering)).

Możesz również dostosować częstotliwość próbkowania osobno dla każdego typu telemetrii lub nawet wykluczyć określone typy z próbkowania. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurowanie próbkowania adaptacyjnego dla aplikacji ASP.NET Core.

Nie ma `ApplicationInsights.Config` dla aplikacji ASP.NET Core, więc każda konfiguracja odbywa się za pośrednictwem kodu.
Próbkowanie adaptacyjne jest domyślnie włączone dla wszystkich aplikacji ASP.NET Core. Można wyłączyć lub dostosować zachowanie pobierania próbek.

### <a name="turning-off-adaptive-sampling"></a>Wyłączanie próbkowania adaptacyjnego

Domyślną funkcję próbkowania można wyłączyć podczas dodawania usługi Application Insights, w metodzie ```ConfigureServices```, przy użyciu ```ApplicationInsightsServiceOptions``` w pliku `Startup.cs`:

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

Powyższy kod spowoduje wyłączenie funkcji próbkowania. Wykonaj poniższe kroki, aby dodać próbkowanie z większą liczbą opcji dostosowywania.

### <a name="configure-sampling-settings"></a>Konfigurowanie ustawień próbkowania

Użyj metod rozszerzających ```TelemetryProcessorChainBuilder```, jak pokazano poniżej, aby dostosować zachowanie próbkowania.

> [!IMPORTANT]
> Jeśli używasz tej metody do konfigurowania próbkowania, upewnij się, że używasz aiOptions. EnableAdaptiveSampling = false; ustawienia z AddApplicationInsightsTelemetry ().

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

**W przypadku używania powyższej metody w celu skonfigurowania próbkowania upewnij się, że używasz ustawień ```aiOptions.EnableAdaptiveSampling = false;``` z AddApplicationInsightsTelemetry ().**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-java-websites-and-python-applications"></a>Próbkowanie z ustaloną szybkością dla ASP.NET, ASP.NET Core, witryny sieci Web Java i aplikacje Python

Próbkowanie ustalonej stawki zmniejsza ruch wysyłany z serwera sieci Web i przeglądarek sieci Web. W przeciwieństwie do próbkowania adaptacyjnego, zmniejsza ona dane telemetryczne według stałej stawki ustalonej przez użytkownika. Synchronizuje także próbkowanie klienta i serwera, aby powiązane elementy były zachowywane — na przykład podczas przeglądania widoku strony w usłudze Search można znaleźć powiązane żądanie.

Podobnie jak w przypadku innych technik próbkowania, zachowuje także powiązane elementy. Dla każdego zdarzenia żądania HTTP żądanie i powiązane z nim zdarzenia zostały odrzucone lub przesłane ze sobą.

W Eksplorator metryk stawki, takie jak żądania i liczby wyjątków, są mnożone przez współczynnik, aby zrekompensować częstotliwość próbkowania, dzięki czemu są one mniej poprawne.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Konfigurowanie próbkowania o stałej szybkości w ASP.NET

1. **Wyłącz próbkowanie adaptacyjne**: w [pliku ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)Usuń węzeł `AdaptiveSamplingTelemetryProcessor` lub Dodaj do niego komentarz.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Włącz moduł próbkowania stałego.** Dodaj następujący fragment kodu do [pliku ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternatywa: Włącz próbkowanie o stałej szybkości w kodzie serwera
    
    Zamiast ustawiać parametr próbkowania w pliku config, można programowo ustawić te wartości. 

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
([Dowiedz się więcej o procesorach telemetrii](../../azure-monitor/app/api-filtering-sampling.md#filtering)).

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Konfigurowanie próbkowania o stałej szybkości w ASP.NET Core

1. **Wyłącz próbkowanie adaptacyjne**: zmiany można wprowadzać w metodzie ```ConfigureServices```, używając ```ApplicationInsightsServiceOptions```:

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

2. **Włącz moduł próbkowania stałego.** Zmiany można wprowadzać w metodzie ```Configure```, jak pokazano w poniższym fragmencie kodu:

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

### <a name="configuring-fixed-rate-sampling-in-java"></a>Konfigurowanie próbkowania o stałej szybkości w języku JAVA ###

1. Pobieranie i Konfigurowanie aplikacji sieci Web przy użyciu najnowszego [zestawu Java SDK usługi Application Insights](../../azure-monitor/app/java-get-started.md)

2. **Włącz moduł próbkowania ustalonego** przez dodanie następującego fragmentu kodu do pliku ApplicationInsights. XML.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        </TelemetryProcessors>
    ```

3. Można dołączać lub wykluczać określone typy danych telemetrycznych z próbkowania przy użyciu następujących tagów w tagu procesora "FixedRateSamplingTelemetryProcessor"
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

Typy telemetrii, które mogą być dołączone lub wykluczone z próbkowania, to: zależność, zdarzenie, wyjątek, PageView, żądanie i śledzenie.

> [!NOTE]
> W polu procent próbkowania wybierz wartość procentową zbliżoną do 100/N, gdzie N jest liczbą całkowitą.  Obecnie próbkowanie nie obsługuje innych wartości.
> 
> 

<a name="other-web-pages"></a>

### <a name="configuring-fixed-rate-sampling-in-opencensus-python"></a>Konfigurowanie próbkowania o stałej szybkości w OpenCensus Python ###

1. Instrumentacja aplikacji przy użyciu najnowszych [OpenCensusch eksportujących Azure monitor](../../azure-monitor/app/opencensus-python.md).

> [!NOTE]
> Próbkowanie z ustaloną szybkością jest dostępne tylko przy użyciu funkcji eksportowania śladów. Oznacza to, że żądania przychodzące i wychodzące są jedynymi typami danych telemetrycznych, w przypadku których można skonfigurować próbkowanie.
> 
> 

2. Możesz określić element `sampler` w ramach konfiguracji elementu `Tracer`. Jeśli nie zostanie podany jawny próbnik, ProbabilitySampler będzie używany domyślnie. ProbabilitySampler będzie używać stawki 1/10000 domyślnie, co oznacza, że jeden z żądań 10000 zostanie wysłany do Application Insights. Jeśli chcesz określić częstotliwość próbkowania, zobacz instrukcje poniżej.

3. Podczas określania próbnika upewnij się, że element `Tracer` określa próbnik z częstotliwością próbkowania z zakresu od 0,0 do 1,0 włącznie. Częstotliwość próbkowania 1,0 reprezentuje 100%, co oznacza, że wszystkie żądania będą wysyłane jako dane telemetryczne do Application Insights.

    ```python
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
        ),
        sampler=ProbabilitySampler(1.0),
    )
    ```

## <a name="ingestion-sampling"></a>Pobieranie próbek

Ta forma próbkowania działa w punkcie, w którym dane telemetryczne z serwera sieci Web, przeglądarek i urządzeń docierają do punktu końcowego usługi Application Insights. Chociaż nie zmniejsza to ruchu telemetrii wysyłanego z aplikacji, zmniejsza ilość przetworzonych i zachowywanych (i naliczanych przez program) przez Application Insights.

Użyj tego typu próbkowania, jeśli aplikacja często przechodzi przez swój miesięczny limit przydziału i nie masz możliwości użycia jednego z typów próbkowania opartych na zestawie SDK. 

Ustaw częstotliwość próbkowania na stronie użycie i szacowane koszty:

![W bloku przegląd aplikacji kliknij pozycję Ustawienia, przydziały, próbki, a następnie wybierz pozycję częstotliwość próbkowania, a następnie kliknij pozycję Aktualizuj.](./media/sampling/04.png)

Podobnie jak w przypadku innych typów próbkowania, algorytm zachowuje powiązane elementy telemetrii. Na przykład podczas przeprowadzania inspekcji danych telemetrycznych w programie Search będziesz mieć możliwość znalezienia żądania związanego z określonym wyjątkiem. Liczby metryk, takie jak częstotliwość żądań i częstotliwość wyjątków, są prawidłowo zachowywane.

Punkty danych, które są odrzucane przez próbkowanie, nie są dostępne w żadnej funkcji Application Insights, takiej jak [eksport ciągły](../../azure-monitor/app/export-telemetry.md).

Próbkowanie do pozyskiwania nie działa, gdy jest wykonywane próbkowanie lub naprawiona stawka z zestawem SDK. Próbkowanie adaptacyjne jest domyślnie włączone, gdy ASP.NET/ASP.NET rdzeń SDK jest włączony w Visual Studio lub włączone w rozszerzeniach aplikacji sieci Web platformy Azure lub przy użyciu monitor stanu, a próbkowanie pozyskiwania jest wyłączone. Jeśli częstotliwość próbkowania w zestawie SDK jest mniejsza niż 100% (tj. elementy są próbkowane), a następnie wybrana częstotliwość próbkowania pozyskiwania jest ignorowana.

> [!WARNING]
> Wartość wyświetlana na kafelku wskazuje wartość ustawioną dla pobierania próbek. Nie reprezentuje rzeczywistej częstotliwości próbkowania, jeśli trwa próbkowanie zestawu SDK.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>Próbkowanie dla stron sieci Web za pomocą języka JavaScript
Na stronie sieci Web można skonfigurować stałe próbkowanie z dowolnego serwera. 

Podczas [konfigurowania stron sieci Web dla Application Insights](../../azure-monitor/app/javascript.md)należy zmodyfikować fragment kodu JavaScript uzyskany w portalu Application Insights. (W aplikacjach ASP.NET fragment kodu zwykle znajduje się w _Layout. cshtml).  Wstaw wiersz podobny do `samplingPercentage: 10,` przed kluczem Instrumentacji:

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

W polu procent próbkowania wybierz wartość procentową zbliżoną do 100/N, gdzie N jest liczbą całkowitą.  Obecnie próbkowanie nie obsługuje innych wartości.

Jeśli włączysz również próbkowanie o stałej szybkości na serwerze, klienci i serwer zostaną zsynchronizowane w taki sposób, aby w obszarze wyszukiwania można przechodzić między widokami i żądaniami stron.

## <a name="when-to-use-sampling"></a>Kiedy używać próbkowania?

Próbkowanie adaptacyjne jest automatycznie włączane w najnowszych zestawach .NET i .NET Core SDK. Niezależnie od używanej wersji zestawu SDK możesz włączyć próbkowanie pozyskiwania, aby umożliwić Application Insights próbkowanie zebranych danych.

Domyślnie żadne próbkowanie nie jest włączone w zestawie Java SDK. Obecnie obsługuje tylko próbkowanie o stałej szybkości. Próbkowanie adaptacyjne nie jest obsługiwane w zestawie Java SDK.

Ogólnie rzecz biorąc, w przypadku aplikacji o małych i średnich rozmiarach nie jest wymagane próbkowanie. Najbardziej przydatne informacje diagnostyczne i najbardziej dokładne statystyki są uzyskiwane przez zbieranie danych na wszystkich działaniach użytkownika. 

Główne zalety pobierania próbek są następujące:

* Usługa Application Insights odrzuca punkty danych ("dławienia"), gdy aplikacja wysyła bardzo wysokie dane telemetryczne w krótkim czasie. 
* Aby zachować [limit przydziału](../../azure-monitor/app/pricing.md) punktów danych w warstwie cenowej. 
* Aby zmniejszyć ruch sieciowy z kolekcji telemetrii. 

### <a name="which-type-of-sampling-should-i-use"></a>Jakiego typu próbkowanie należy użyć?

**Użyj próbkowania pozyskiwania, jeśli:**

* Często przechodzisz przez miesięczny przydział danych telemetrycznych.
* Używasz wersji zestawu SDK, która nie obsługuje próbkowania — na przykład ASP.NET wersje wcześniejsze niż 2.
* Używasz zbyt dużej ilości danych telemetrycznych z przeglądarek sieci Web użytkowników.

**Użyj próbkowania stałego, jeśli:**

* Używasz zestawu SDK Application Insights dla ASP.NET Web Services w wersji 2.0.0 lub nowszej lub Java SDK v 2.0.1 lub nowszej.
* Należy synchronizować próbkowanie między klientem a serwerem, aby podczas badania zdarzeń w usłudze [Search](../../azure-monitor/app/diagnostic-search.md)można przechodzić między powiązanymi zdarzeniami na kliencie i serwerze, takimi jak widoki stron i żądania HTTP.
* Masz pewność, że dla aplikacji jest pobierana odpowiednia wartość procentowa. Powinien być wystarczająco duży, aby uzyskać dokładne metryki, ale poniżej stawki, która przekracza limit przydziału cen i limity ograniczania. 

**Użyj próbkowania adaptacyjnego:**

Jeśli warunki używania innych form próbkowania nie mają zastosowania, zalecamy próbkowanie adaptacyjne. To ustawienie jest domyślnie włączone w ASP.NET/ASP.NET Core Server SDK. Nie zmniejszy to ruchu do momentu osiągnięcia pewnej minimalnej stawki, w związku z czym nie będzie to miało zastosowania.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Jak mogę sprawdzić, czy próbkowanie jest w trakcie operacji?

Aby wykryć rzeczywistą częstotliwość próbkowania niezależnie od tego, gdzie została ona zastosowana, należy użyć [zapytania analitycznego](../../azure-monitor/app/analytics.md) , takiego jak:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Jeśli RetainedPercentage dla dowolnego typu jest mniejsza niż 100, ten element jest próbkowany.

**Application Insights nie próbuje próbkować typów danych telemetrycznych, metryk i liczników wydajności w każdej techniki próbkowania opisanej powyżej. Te typy są zawsze wyłączone z próbkowania, ponieważ zmniejszenie dokładności może być wysoce niepożądane dla tych typów telemetrii**

## <a name="how-does-sampling-work"></a>Jak działa próbkowanie?

Funkcja próbkowania o stałej szybkości zestawu SDK w wersjach ASP.NET z 2.0.0 i Java SDK w wersji 2.0.1 i nowszych. Adaptacyjne próbkowanie jest funkcją zestawu SDK w wersjach ASP.NET z 2.0.0 lub nowszym. Próbkowanie do pozyskiwania jest funkcją usługi Application Insights i może działać, jeśli zestaw SDK nie wykonuje próbkowania.

Algorytm próbkowania decyduje o tym, które elementy telemetrii mają być porzucane i które mają być zachowane (czy jest on w zestawie SDK lub w usłudze Application Insights). Decyzja o próbkowaniu jest oparta na kilku regułach, które mają na celu zachowanie wszystkich powiązanych punktów danych bez zmian, dzięki objęciu diagnostyki w Application Insights, które są bardziej funkcjonalne i niezawodne nawet przy użyciu zredukowanego zestawu danych. Na przykład jeśli dla żądania zakończonego niepowodzeniem aplikacja wyśle dodatkowe elementy telemetrii (na przykład wyjątek i ślady zarejestrowane z tego żądania), próbkowanie nie spowoduje podzielenia tego żądania i innych danych telemetrycznych. Utrzymuje lub porzuca wszystkie jednocześnie. W związku z tym, gdy zobaczysz szczegóły żądania w Application Insights, możesz zawsze zobaczyć żądanie wraz ze skojarzonymi z nim elementami telemetrii. 

Decyzja o próbkowaniu jest określana na podstawie identyfikatora operacji żądania, co oznacza, że wszystkie elementy telemetrii należące do określonej operacji są zachowywane lub porzucane. W przypadku elementów telemetrycznych, które nie mają ustawionego identyfikatora operacji (na przykład elementy telemetrii zgłaszane z wątków asynchronicznych bez kontekstu http), po prostu przechwytuje procent elementów telemetrii każdego typu. Przed 2.5.0-beta2 zestawu .NET SDK i 2.2.0-beta3 zestawu ASP.NET Core SDK decyzja o próbkowaniu była oparta na skrócie identyfikatora użytkownika dla aplikacji, które definiują "użytkownika" (to znaczy najpopularniejsze aplikacje sieci Web). W przypadku typów aplikacji, które nie definiują użytkowników (takich jak usługi sieci Web), decyzja o próbkowaniu była oparta na IDENTYFIKATORze operacji żądania.

Podczas przedstawiania danych telemetrycznych z powrotem do Ciebie Usługa Application Insights dostosowuje metryki o ten sam procent próbkowania, który był używany w czasie zbierania, aby skompensować brakujące punkty danych. W związku z tym podczas wyszukiwania danych telemetrycznych w Application Insights użytkownicy widzą statystycznie prawidłowe przybliżenia, które są blisko liczb rzeczywistych.

Dokładność przybliżania zależy od skonfigurowanej wartości procentowej próbkowania. Ponadto dokładność zwiększa się w przypadku aplikacji, które obsługują dużą liczbę zwykle podobnych żądań od wielu użytkowników. Z drugiej strony, w przypadku aplikacji, które nie współpracują z znaczącym obciążeniem, pobieranie próbek nie jest potrzebne, ponieważ te aplikacje zazwyczaj mogą wysyłać wszystkie dane telemetryczne w ramach limitu przydziału bez powodowania utraty danych. 

> [!WARNING]
> Application Insights nie próbkuje typów telemetrii metryk i sesji. Zmniejszenie dokładności może być wysoce niepożądane dla tych typów telemetrii.
> 

### <a name="adaptive-sampling"></a>Adaptacyjne próbkowanie

Adaptacyjne próbkowanie dodaje składnik monitorujący bieżącą szybkość transmisji z zestawu SDK, a następnie dostosowuje wartość procentową próbkowania w celu pozostawania w ramach docelowej stawki maksymalnej. Korekta jest przeliczana w regularnych odstępach czasu i opiera się na średniej liczbie wychodzącej szybkości transmisji.

## <a name="sampling-and-the-javascript-sdk"></a>Próbkowanie i zestaw SDK języka JavaScript

Zestaw SDK po stronie klienta (JavaScript) w połączeniu z zestawem SDK po stronie serwera ma udział w próbkach o stałej szybkości. Strony z instrumentami będą wysyłać dane telemetryczne po stronie klienta z tych samych użytkowników, dla których po stronie serwera podjęto decyzję o "przykładzie". Ta logika została zaprojektowana w celu utrzymania integralności sesji użytkownika na klientach i serwerach. W związku z tym z dowolnego określonego elementu telemetrii w Application Insights można znaleźć wszystkie inne elementy telemetrii dla tego użytkownika lub sesji. 

*Moje dane telemetryczne po stronie klienta i serwera nie wyświetlają skoordynowanych próbek, jak opisano powyżej.*

* Sprawdź, czy włączono próbkowanie ustalone na serwerze i kliencie.
* Upewnij się, że wersja zestawu SDK to 2,0 lub nowsza.
* Upewnij się, że ustawiono ten sam procent próbkowania zarówno na kliencie, jak i na serwerze.

### <a name="sampling-in-azure-functions"></a>Próbkowanie w Azure Functions

Postępuj zgodnie z instrukcjami podanymi w [tym](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) temacie, aby skonfigurować próbkowanie dla aplikacji działających w Azure Functions.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

*Co to jest domyślne zachowanie podczas próbkowania w ASP.NET i zestawie SDK ASP.NET Core?*

* Jeśli używasz jednej z najnowszych wersji powyższego zestawu SDK, adaptacyjne próbkowanie jest domyślnie włączone z pięciu elementów telemetrii na sekundę.
  Domyślnie dodano 2 AdaptiveSamplingTelemetryProcessors, a jeden z nich zawiera typ zdarzenia w próbkach, a inne wyklucza typ zdarzenia z próbkowania. Ta konfiguracja oznacza, że zestaw SDK spróbuje ograniczyć liczbę elementów telemetrycznych do pięciu elementów telemetrii typów zdarzeń i pięć elementów telemetrii wszystkich innych typów, dzięki czemu można próbkować zdarzenia oddzielnie od innych typów telemetrii. Zdarzenia są zwykle używane w przypadku telemetrii biznesowej i najprawdopodobniej nie powinny mieć wpływ na woluminy telemetrii diagnostyki.
  
  Poniżej przedstawiono domyślny wygenerowany plik ApplicationInsights. config. Jak opisano, istnieją dwa osobne węzły AdaptiveSamplingTelemetryProcessor, z wyjątkiem typów zdarzeń i innych. W ASP.NET Core dokładne zachowanie domyślne jest włączone w kodzie. Użyj przykładów z wcześniejszej sekcji dokumentu, aby zmienić to zachowanie domyślne.

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

*Czy dane telemetryczne mogą być próbkowane więcej niż raz?*

* Nie. SamplingTelemetryProcessors Ignoruj elementy z przykładów próbkowania, jeśli element jest już próbkowany. To samo jest prawdziwe w przypadku pobierania próbek, które nie zastosują próbkowania do tych elementów, które są już próbkowane w samym zestawie SDK.

*Dlaczego próbkowanie jest proste "zbieranie X procent każdego typu telemetrii"?*

* Chociaż takie podejście do próbkowania zapewni wysoki poziom precyzji w przybliżeniu pomiarowym, może przerwać możliwość skorelowania danych diagnostycznych na użytkownika, sesję i żądanie, co jest niezwykle ważne dla celów diagnostycznych. W związku z tym próbkowanie działa lepiej z opcją "Zbierz wszystkie elementy telemetrii dla X procent użytkowników aplikacji" lub "Zbierz wszystkie dane telemetryczne dla X procent żądań aplikacji". W przypadku elementów telemetrycznych, które nie są skojarzone z żądaniami (na przykład asynchroniczne przetwarzanie w tle), rezerwa jest wartość "Zbierz X procent wszystkich elementów dla każdego typu telemetrii". 

*Czy wartość procentowa próbkowania może ulec zmianie z upływem czasu?*

* Tak, próbkowanie adaptacyjne stopniowo zmienia procent próbkowania na podstawie aktualnie zaobserwowanej ilości danych telemetrycznych.

*Jeśli korzystam z próbkowania o stałej szybkości, skąd mam wiedzieć, który procent próbkowania będzie działał najlepiej dla mojej aplikacji?*

* Jednym ze sposobów jest rozpoczęcie od adaptacyjnego próbkowania, Dowiedz się, jaka stawka jest naliczana (patrz powyższe pytanie), a następnie przejdź do próbkowania ustalonego przy użyciu tej stawki. 
  
    W przeciwnym razie musisz się przygadnąć. Analizuj bieżące użycie telemetrii w Application Insights, obserwuj wszystkie występujące ograniczenia przepustowości i Szacuj wolumen zebranych danych telemetrycznych. Te trzy dane wejściowe wraz z wybraną warstwą cenową sugerują, jak dużo można zmniejszyć wolumin zbieranych danych telemetrycznych. Jednak zwiększenie liczby użytkowników lub innych zmian w ilości danych telemetrycznych może spowodować unieważnienie oszacowania.

*Co się stanie, jeśli skonfigurujesz procent próbkowania za mało?*

* Zbyt niski procent próbkowania (nadmiernie agresywne próbkowanie) zmniejsza dokładność przybliżeń, gdy Application Insights próbuje kompensować wizualizację danych w celu zmniejszenia ilości danych. Ponadto może to mieć negatywny wpływ na środowisko diagnostyczne, ponieważ niektóre rzadko występujące błędy lub powolne żądania mogą być próbkowane.

*Co się stanie, jeśli skonfigurujesz procent próbkowania zbyt wysoki?*

* Konfigurowanie zbyt dużej ilości próbek próbkowania (niedostatecznie agresywne) powoduje niewystarczającą redukcję woluminu zebranej telemetrii. Nadal może wystąpić utrata danych telemetrycznych związana z ograniczeniami, a koszt korzystania z Application Insights może być wyższy niż planowany ze względu na opłaty za nadwyżkowe użycie.

*Na jakich platformach można używać próbkowania?*

* Próbkowanie do pozyskiwania może odbywać się automatycznie dla każdej telemetrii powyżej określonego woluminu, jeśli zestaw SDK nie wykonuje próbkowania. Ta konfiguracja będzie działała, na przykład jeśli używasz starszej wersji zestawu ASP.NET SDK lub poprzedniej wersji zestawu Java SDK (1.0.10 lub before).
* Jeśli używasz zestawu ASP.NET SDK w wersji 2.0.0 lub nowszej lub ASP.NET CORE SDK w wersji 2.2.0 i nowszej (hostowanej na platformie Azure lub na własnym serwerze), domyślnie otrzymujesz próbkowanie adaptacyjne, ale możesz przełączyć się na stałą stawkę, jak opisano powyżej. W przypadku próbkowania o stałej szybkości zestaw SDK przeglądarki automatycznie synchronizuje się z przykładowymi zdarzeniami powiązanymi. 
* Jeśli używasz zestawu Java SDK w wersji 2.0.1 lub nowszej, możesz skonfigurować ApplicationInsights. XML, aby włączyć próbkowanie o stałym wskaźniku. Próbkowanie jest domyślnie wyłączone. W przypadku próbkowania o stałej szybkości zestaw SDK przeglądarki automatycznie synchronizuje się z przykładowymi zdarzeniami powiązanymi.

*Istnieją pewne rzadkie zdarzenia, które zawsze chcę zobaczyć. Jak mogę uzyskać dostęp do modułu próbkowania?*

* Najlepszym sposobem osiągnięcia tego celu jest napisanie niestandardowych [TelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer), które ustawia `SamplingPercentage` na 100 w elemencie telemetrii, który ma zostać zachowany, jak pokazano poniżej. Ponieważ inicjatory są gwarantowane do uruchomienia przed procesorami danych telemetrycznych (w tym próbkowanie), zapewnia to, że wszystkie techniki próbkowania zignorują ten element z wszelkich zagadnień związanych z próbkami.

```csharp
     public class MyTelemetryInitializer : ITelemetryInitializer
      {
         public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)item).SamplingPercentage = 100;
            }
        }
      }
```

## <a name="next-steps"></a>Następne kroki

* [Filtrowanie](../../azure-monitor/app/api-filtering-sampling.md) może zapewnić bardziej rygorystyczną kontrolę nad tym, co wysyła zestaw SDK.
* Zapoznaj się z artykułem dotyczącym sieci deweloperów [Optymalizacja danych telemetrycznych z Application Insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
