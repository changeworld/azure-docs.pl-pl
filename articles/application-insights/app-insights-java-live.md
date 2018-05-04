---
title: Application Insights dla aplikacji sieci web Java, które znajdują się już na żywo
description: Rozpocznij monitorowanie aplikacji sieci web, która jest już uruchomiona na serwerze
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: mbullwin
ms.openlocfilehash: b8ddd43fb19b3800840180c458268301b5ec26f6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Application Insights dla aplikacji sieci web Java, które znajdują się już na żywo


Jeśli aplikacja sieci web, która jest już uruchomiona na serwerze J2EE, można rozpocząć monitorowanie za pomocą [usługi Application Insights](app-insights-overview.md) bez konieczności zmiany kodu lub ponownie skompilować projekt. Po wybraniu tej opcji możesz uzyskać informacje o żądaniach HTTP wysyłane do serwera, nieobsługiwanych wyjątków i liczniki wydajności.

Potrzebna będzie subskrypcja platformy [Microsoft Azure](https://azure.com).

> [!NOTE]
> Procedury na tej stronie dodaje zestaw SDK do aplikacji sieci web w czasie wykonywania. Instrumentacja to środowisko uruchomieniowe jest przydatne, jeśli nie chcesz zaktualizować lub ponownie skompiluj kod źródłowy. Ale jeśli to możliwe, zalecamy [Dodaj zestaw SDK do kodu źródłowego](app-insights-java-get-started.md) zamiast tego. Zapewniającą więcej opcji przykład pisania kodu, aby śledzić aktywność użytkownika.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Uzyskiwanie klucza instrumentacji usługi Application Insights
1. Zaloguj się do [portalu Microsoft Azure](https://portal.azure.com)
2. Utwórz nowy zasób usługi Application Insights i Ustaw typ aplikacji do aplikacji sieci web Java.
   
    ![Wypełnij nazwę, wybierz aplikację sieci Web Java i kliknij przycisk Utwórz](./media/app-insights-java-live/02-create.png)

    Zasób jest tworzony w ciągu kilku sekund.

4. Otwórz nowy zasób i uzyskać jego klucza instrumentacji. Wkrótce będzie trzeba wkleić ten klucz do projektu kodu.
   
    ![W opisie nowego zasobu kliknij opcję Właściwości i skopiuj klucz instrumentacji](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Pobierz zestaw SDK
1. Pobierz [Zestaw SDK usługi Application Insights dla środowiska Java](https://aka.ms/aijavasdk). 
2. Na serwerze Wyodrębnij zawartość zestawu SDK do katalogu, z którego są załadowane dane binarne Twojego projektu. Jeśli używasz Tomcat ten katalog będzie zazwyczaj w obszarze `webapps/<your_app_name>/WEB-INF/lib`

Należy pamiętać, że trzeba Powtórz te czynności dla każdego wystąpienia serwera, a także dla każdej aplikacji.

## <a name="3-add-an-application-insights-xml-file"></a>3. Dodaj plik xml usługi Application Insights
Utwórz ApplicationInsights.xml w folderze, w którym dodano zestawu SDK. Umieść w nim następujące XML.

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
* Korelacja zdarzeń jest dodatkiem do składnika żądań HTTP. Przypisuje identyfikator do każdego żądania odebranego przez serwer i dodaje go do każdego elementu telemetrii jako właściwość „Operation.Id”. Umożliwia korelowanie telemetrycznych skojarzonych z każdym żądaniem przez ustawienie filtru [diagnostycznych wyszukiwania](app-insights-diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. Dodawanie filtru HTTP
Zlokalizuj i Otwórz plik web.xml w projekcie i scal poniższy fragment kodu w węźle aplikacji sieci web, gdzie są skonfigurowane filtry aplikacji.

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

## <a name="5-check-firewall-exceptions"></a>5. Sprawdź wyjątki zapory
Konieczne może być [ustawić wyjątki, aby wysłać dane wychodzące](app-insights-ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. Uruchom ponownie aplikację sieci web
## <a name="7-view-your-telemetry-in-application-insights"></a>7. Wyświetlanie telemetrii w usłudze Application Insights
Wróć do zasobu usługi Application Insights w witrynie [Microsoft Azure Portal](https://portal.azure.com).

W bloku omówienie pojawi się dane telemetryczne dotyczące żądania HTTP. (Jeśli ich tam nie ma, odczekaj kilka sekund, a następnie kliknij przycisk Odśwież).

![dane przykładowe](./media/app-insights-java-live/5-results.png)

Klikaj elementy wykresów, aby wyświetlać bardziej szczegółowe metryki. 

![](./media/app-insights-java-live/6-barchart.png)

Podczas przeglądania właściwości żądania, może uzyskać zdarzenia telemetrii skojarzonych z nim, takich jak żądania i wyjątków i.

![](./media/app-insights-java-live/7-instance.png)

[Dowiedz się więcej o metrykach.](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>Kolejne kroki
* [Dodaj telemetrię do stron sieci web](app-insights-javascript.md) wyświetleń strony monitora i metryki użytkownika.
* [Ustawianie testów sieci web](app-insights-monitor-web-app-availability.md) aby upewnić się, aplikacja pozostaje na żywo i szybko reagowały.
* [Śledzenie dziennika](app-insights-java-trace-logs.md)
* [Wyszukiwanie zdarzeń i dzienniki](app-insights-diagnostic-search.md) do diagnozowania problemów.

