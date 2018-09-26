---
title: Usługa Application Insights dla aplikacji sieci web Java, które znajdują się już na żywo
description: Rozpoczynanie monitorowania aplikacji internetowej, która jest już uruchomiona na serwerze
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/10/2016
ms.author: mbullwin
ms.openlocfilehash: 92b13c27d5f9c110dbcb4aa70aa9b8ddde17531a
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096744"
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Usługa Application Insights dla aplikacji sieci web Java, które znajdują się już na żywo

W przypadku aplikacji sieci web, która jest już uruchomiona na serwerze J2EE można rozpocząć monitorowanie z [usługi Application Insights](app-insights-overview.md) bez konieczności wprowadzania zmian w kodzie lub ponownie skompilować projekt. Po wybraniu tej opcji, możesz uzyskać informacje o żądaniach HTTP wysyłane do serwera, nieobsłużone wyjątki i liczniki wydajności.

Potrzebna będzie subskrypcja platformy [Microsoft Azure](https://azure.com).

> [!NOTE]
> Procedury na tej stronie dodaje zestaw SDK do aplikacji sieci web w czasie wykonywania. Tego środowiska uruchomieniowego Instrumentacji jest przydatne, jeśli nie chcesz zaktualizować lub ponownie skompiluj kod źródłowy. Ale jeśli to możliwe, firma Microsoft zaleca [Dodaj zestaw SDK do kodu źródłowego](app-insights-java-get-started.md) zamiast tego. Który zapewnia więcej opcji takie jak zapisywanie kodu w celu śledzenia działań użytkownika.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Uzyskiwanie klucza instrumentacji usługi Application Insights
1. Zaloguj się do [portalu Microsoft Azure](https://portal.azure.com)
2. Utwórz nowy zasób usługi Application Insights i Ustaw typ aplikacji do aplikacji sieci web Java.
   
    ![Wypełnij nazwę, wybierz aplikację internetową Java i kliknij przycisk Utwórz](./media/app-insights-java-live/02-create.png)

    Zasób jest tworzony w ciągu kilku sekund.

4. Otwórz nowy zasób i pobrać jego klucza instrumentacji. Wkrótce będzie trzeba wkleić ten klucz do projektu kodu.
   
    ![W opisie nowego zasobu kliknij opcję Właściwości i skopiuj klucz instrumentacji](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Pobierz zestaw SDK
1. Pobierz [Zestaw SDK usługi Application Insights dla środowiska Java](https://aka.ms/aijavasdk). 
2. Na serwerze Wyodrębnij zawartość zestawu SDK do katalogu, z którego są ładowane pliki binarne projektu. Jeśli używasz serwera Tomcat ten katalog będzie się zazwyczaj w obszarze `webapps/<your_app_name>/WEB-INF/lib`

Uwaga: należy Powtórz tę czynność dla każdego wystąpienia serwera, a także dla każdej aplikacji.

## <a name="3-add-an-application-insights-xml-file"></a>3. Dodaj plik xml usługi Application Insights
W tym folderze, w którym dodano zestaw SDK, należy utworzyć plik ApplicationInsights.xml. Umieść do niej następujący kod XML.

Zastąp klucz instrumentacji kluczem pobranym z portalu Azure.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

* Klucz instrumentacji jest wysyłany wraz z każdym elementem telemetrii i dzięki temu te elementy mogą być wyświetlane dla odpowiedniego zasobu usługi Application Insights.
* Składnik żądań HTTP jest opcjonalny. Powoduje automatyczne wysyłanie telemetrii dotyczącej żądań i czasów odpowiedzi do portalu.
* Korelacja zdarzeń jest dodatkiem do składnika żądań HTTP. Przypisuje identyfikator do każdego żądania odebranego przez serwer i dodaje go do każdego elementu telemetrii jako właściwość „Operation.Id”. Umożliwia korelowanie telemetrii skojarzonej z każdym żądaniem przez ustawienie filtru [wyszukiwaniu diagnostycznym](app-insights-diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. Dodawanie filtru HTTP
Zlokalizuj i Otwórz plik web.xml w projekcie, a następnie scal poniższy fragment kodu w obszarze węzła web-app, w którym skonfigurowano filtry aplikacji.

Aby uzyskać najbardziej dokładne wyniki, ten filtr powinien być mapowany przed wszystkimi innymi filtrami.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## <a name="5-check-firewall-exceptions"></a>5. Sprawdzanie wyjątków zapory
Konieczne może być [ustawić wyjątki, aby wysyłać dane wychodzące](app-insights-ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. Uruchom ponownie swoją aplikację sieci web
## <a name="7-view-your-telemetry-in-application-insights"></a>7. Wyświetlanie telemetrii w usłudze Application Insights
Wróć do zasobu usługi Application Insights w witrynie [Microsoft Azure Portal](https://portal.azure.com).

Dane telemetryczne dotyczące żądań HTTP pojawią się w bloku przeglądu. (Jeśli ich tam nie ma, odczekaj kilka sekund, a następnie kliknij przycisk Odśwież).

![dane przykładowe](./media/app-insights-java-live/5-results.png)

Klikaj elementy wykresów, aby wyświetlać bardziej szczegółowe metryki. 

![](./media/app-insights-java-live/6-barchart.png)

Podczas wyświetlania właściwości żądania, może uzyskać dostęp i zdarzenia telemetrii, skojarzone z nim, takie jak żądania i wyjątki.

![](./media/app-insights-java-live/7-instance.png)

[Dowiedz się więcej o metrykach.](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>Kolejne kroki
* [Dodaj telemetrię do stron sieci web](app-insights-javascript.md) Aby monitorować wyświetlenia stron i metryki użytkowników.
* [Konfigurowanie testów sieci web](app-insights-monitor-web-app-availability.md) aby upewnić się, że aplikacja stale działa i odpowiada.
* [Przechwytywanie danych śledzenia dziennika](app-insights-java-trace-logs.md)
* [Wyszukiwanie zdarzeń i dzienników](app-insights-diagnostic-search.md) do diagnozowania problemów.
* [Konfigurowanie aplikacji Spring Boot inicjatora](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)
