---
title: Filtrowanie telemetrii usługi Azure Application Insights w aplikacji sieci web Java | Dokumentacja firmy Microsoft
description: Zmniejszenia ruchu telemetrycznego przez filtrowanie zdarzeń, które nie jest potrzebny do monitorowania.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 3/14/2019
ms.author: mbullwin
ms.openlocfilehash: 9cf939b241da01be55c1b2ba5f00a5131ab94c06
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061162"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrowanie danych telemetrycznych w aplikacji sieci web Java

Filtry umożliwiają wybieranie danych telemetrycznych, Twoje [aplikacji sieci web Java wysyła do usługi Application Insights](java-get-started.md). Istnieją niektóre filtry out-of--box, które są dostępne, a można również napisać własne niestandardowe filtry.

Dostępne są następujące filtry out-of--box:

* Poziom ważności śledzenia
* Określone adresy URL, słowa kluczowe lub kody odpowiedzi
* Szybkie uzyskiwanie odpowiedzi z — czyli żądań, do których aplikacji wypełniona szybko
* Nazwy określone zdarzenie

> [!NOTE]
> Filtry pochylanie metryki aplikacji. Na przykład można zdecydować, że, aby zdiagnozować powolne odpowiedzi, ustawisz filtr, aby odrzucić krótszych czasów reakcji. Jednak należy pamiętać, że średniego czasu odpowiedzi zgłoszony przez usługę Application Insights będzie mniejsza niż szybkość wartość true, a liczba żądań będzie mniejsza niż liczba rzeczywistych.
> Jeśli jest to niepożądane, użyj [próbkowania](../../azure-monitor/app/sampling.md) zamiast tego.

## <a name="setting-filters"></a>Filtry

W ApplicationInsights.xml, Dodaj `TelemetryProcessors` sekcji podobną do przykładowej:


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




[Zbadaj pełny zestaw wbudowanych procesorów](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Wbudowane filtry

### <a name="metric-telemetry-filter"></a>Filtra metryki danych Telemetrycznych

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` — Rozdzielana przecinkami lista nazw metryki niestandardowe.


### <a name="page-view-telemetry-filter"></a>Filtr danych Telemetrycznych widoku strony

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` -Czas trwania odnosi się do czas ładowania strony. Jeśli ta opcja jest ustawiona, stron, które są ładowane szybciej niż w tej chwili nie są zgłaszane.
* `NotNeededNames` — Rozdzielana przecinkami lista nazw stron.
* `NotNeededUrls` — Fragmenty rozdzielana przecinkami lista adresów URL. Na przykład `"home"` odfiltrowuje wszystkie strony, które mają "Strona główna" w adresie URL.


### <a name="request-telemetry-filter"></a>Żądanie Telemetrii filtru


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Syntetyczne Filtr źródła

Odfiltrowuje wszystkie dane telemetryczne, które wartości właściwości SyntheticSource. Obejmują one żądania od roboty i przeszukiwarki testów dostępności.

Filtrowanie danych telemetrycznych dla wszystkich żądań syntetycznych:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Filtrowanie danych telemetrycznych dla określonych źródeł syntetycznych:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` — Rozdzielana przecinkami lista nazw syntetycznego źródła.

### <a name="telemetry-event-filter"></a>Filtr zdarzeń telemetrii

Filtruje zdarzenia niestandardowe (rejestrowane przy użyciu [poleceń TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` — Rozdzielana przecinkami lista nazw zdarzeń.


### <a name="trace-telemetry-filter"></a>Filtr Telemetrii śledzenia

Ślady dzienników filtry (rejestrowane przy użyciu [metody TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) lub [modułu zbierającego framework rejestrowania](java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` Prawidłowe wartości to:
  *  WYŁĄCZONE — odfiltrować wszystkie ślady
  *  TRACE — bez filtrowania. poziom śledzenia jest równa
  *  Informacje o — odfiltrowywanie poziom śledzenia
  *  WARN — filtr śledzenia i informacji
  *  Błąd — odfiltrowywanie OSTRZEGAJ, INFO, śledzenia
  *  KRYTYCZNY - odfiltrowywanie wszystkich pól poza krytyczne


## <a name="custom-filters"></a>Filtry niestandardowe

### <a name="1-code-your-filter"></a>1. Kod z filtrem

W kodzie, należy utworzyć klasę, która implementuje `TelemetryProcessor`:

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


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Wywołania z filtrem w pliku konfiguracji

W ApplicationInsights.xml:

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

### <a name="3-invoke-your-filter-java-spring"></a>3. Wywołania z filtrem (Java Spring)

W przypadku aplikacji Spring framework w oparciu procesorów niestandardowych danych telemetrycznych musi być zarejestrowana w klasie aplikacji głównej jako bean. Zostaną autowired podczas uruchamiania aplikacji.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Musisz utworzyć własne parametry filtru w `application.properties` i korzystać z aplikacji Spring Boot framework zewnętrznych konfiguracji do przekazania parametrów do niestandardowego filtru. 


## <a name="troubleshooting"></a>Rozwiązywanie problemów

*Moje filtru nie działa.*

* Sprawdź, czy podane prawidłowymi wartościami parametrów. Na przykład czasy trwania powinny być liczbami całkowitymi. Nieprawidłowe wartości spowoduje, że filtr, które mają być ignorowane. Niestandardowy filtr zgłasza wyjątek z konstruktora lub metody set, zostaną zignorowane.

## <a name="next-steps"></a>Kolejne kroki

* [Próbkowanie](../../azure-monitor/app/sampling.md) — należy wziąć pod uwagę próbkowania jako alternatywę, który nie pochylanie metryk.
