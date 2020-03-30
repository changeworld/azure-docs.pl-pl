---
title: Filtrowanie danych telemetrycznych usługi Azure Application Insights w aplikacji sieci Web Java
description: Zmniejsz ruch telemetryczny, odfiltrowując zdarzenia, których nie trzeba monitorować.
ms.topic: conceptual
ms.date: 3/14/2019
ms.openlocfilehash: 020e54132e0ca0a9f9ccf0236f94515877015637
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659921"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrowanie danych telemetrycznych w aplikacji java web

Filtry umożliwiają wybranie danych telemetrycznych [wysyłanych przez aplikację Java do usługi Application Insights](java-get-started.md). Istnieje kilka gotowych filtrów, których można użyć, a także można napisać własne filtry niestandardowe.

Gotowe filtry obejmują:

* Śledzenie poziomu ważności
* Określone adresy URL, słowa kluczowe lub kody odpowiedzi
* Szybkie odpowiedzi — czyli żądania, na które aplikacja szybko zareagowała
* Konkretne nazwy zdarzeń

> [!NOTE]
> Filtry pochylają dane aplikacji. Na przykład można zdecydować, że w celu zdiagnozowania powolnych odpowiedzi, można ustawić filtr, aby odrzucić szybki czas odpowiedzi. Należy jednak pamiętać, że średni czas odpowiedzi zgłaszany przez usługa Application Insights będzie wtedy wolniejszy niż rzeczywista szybkość, a liczba żądań będzie mniejsza niż rzeczywista liczba.
> Jeśli jest to problem, należy użyć [próbkowania](../../azure-monitor/app/sampling.md) zamiast.

## <a name="setting-filters"></a>Filtry ustawień

W pliku ApplicationInsights.xml `TelemetryProcessors` dodaj sekcję podobną do tego przykładu:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Sprawdź pełny zestaw wbudowanych procesorów](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Wbudowane filtry

### <a name="metric-telemetry-filter"></a>Filtr Telemetrii metrycznej

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded`- Oddzielona przecinkami lista niestandardowych nazw metryk.


### <a name="page-view-telemetry-filter"></a>Filtr Telemetria widoku strony

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS`- Czas trwania odnosi się do czasu, który został przeładowany przez stronę. Jeśli jest to ustawione, strony, które załadowano szybciej niż ten czas, nie są zgłaszane.
* `NotNeededNames`- Rozdzielona przecinkami lista nazw stron.
* `NotNeededUrls`- Rozdzielona przecinkami lista fragmentów adresów URL. Na przykład `"home"` odfiltruje wszystkie strony, które mają "home" w adresie URL.


### <a name="request-telemetry-filter"></a>Filtr telemetrii żądania


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filtr źródła syntetycznego

Odfiltrowywają wszystkie dane telemetryczne, które mają wartości we właściwości SyntheticSource. Należą do nich żądania od botów, pająków i testów dostępności.

Odfiltruj dane telemetryczne dla wszystkich żądań syntetycznych:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Odfiltrowanie danych telemetrycznych dla określonych źródeł syntetycznych:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded`- Oddzielona przecinkami lista syntetycznych nazw źródeł.

### <a name="telemetry-event-filter"></a>Filtr zdarzeń telemetrii

Filtruje zdarzenia niestandardowe (rejestrowane przy użyciu [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames`- Rozdzielona przecinkami lista nazw zdarzeń.


### <a name="trace-telemetry-filter"></a>Filtr telemetrii śledzenia

Filtry dziennika śladów (rejestrowane przy użyciu [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) lub [moduł zbierający struktury rejestrowania](java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel`prawidłowe wartości to:
  *  OFF - Odfiltruj WSZYSTKIE ślady
  *  TRACE — brak filtrowania. równa się poziomowi śledzenia
  *  INFO - Odfiltruj poziom TRACE
  *  WARN - Odfiltruj TRACE i INFO
  *  BŁĄD - Odfiltruj WARN, INFO, TRACE
  *  CRITICAL - odfiltruj wszystkie, ale KRYTYCZNE


## <a name="custom-filters"></a>Filtry niestandardowe

### <a name="1-code-your-filter"></a>1. Zakoduj swój filtr

W kodzie utwórz klasę, `TelemetryProcessor`która implementuje:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Wywołaj filtr w pliku konfiguracyjnym

W pliku ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3. Wywołaj filtr (Java Spring)

W przypadku aplikacji opartych na platformie Spring niestandardowe procesory telemetryczne muszą być zarejestrowane w głównej klasie aplikacji jako bean. Zostaną one następnie automatycznie okablowane po uruchomieniu aplikacji.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Musisz utworzyć własne parametry filtru `application.properties` i wykorzystać zewnętrzną strukturę konfiguracji Spring Boot, aby przekazać te parametry do filtru niestandardowego. 


## <a name="troubleshooting"></a>Rozwiązywanie problemów

*Mój filtr nie działa.*

* Sprawdź, czy podano prawidłowe wartości parametrów. Na przykład czasy trwania powinny być liczby całkowite. Nieprawidłowe wartości spowodują, że filtr zostanie zignorowany. Jeśli filtr niestandardowy zgłasza wyjątek od konstruktora lub metody zestawu, zostanie on zignorowany.

## <a name="next-steps"></a>Następne kroki

* [Próbkowanie](../../azure-monitor/app/sampling.md) — należy wziąć pod uwagę próbkowanie jako alternatywę, która nie pochyla metryki.
