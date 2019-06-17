---
title: Model danych szczegółowych informacji w aplikacji platformy Azure | Dokumentacja firmy Microsoft
description: Opisuje właściwości wyeksportowane z Eksport ciągły w formacie JSON i używane jako filtry.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: mbullwin
ms.openlocfilehash: 4f8fd0b317c17f142664d22291c23442dd49f970
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053293"
---
# <a name="application-insights-export-data-model"></a>Model danych eksportu Insights aplikacji
Poniższa tabela zawiera listę właściwości telemetrii wysyłanych z [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md) zestawów SDK do portalu.
Zobaczysz tych właściwości w danymi wyjściowymi [eksportu ciągłego](export-telemetry.md).
Widoczne są także w filtry właściwości w [Eksploratora metryk](../../azure-monitor/app/metrics-explorer.md) i [wyszukiwaniu diagnostycznym](../../azure-monitor/app/diagnostic-search.md).

Informacje, które należy zwrócić uwagę:

* `[0]` w poniższych tabelach oznacza punkt w ścieżce, gdzie trzeba wstawić indeksu; ale nie zawsze jest 0.
* Czasów trwania są w dziesiątych mikrosekund, więc 10000000 == 1 sekundę.
* Daty i godziny są czasem UTC i są podane w formacie ISO `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Przykład
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Kontekst
Wszystkie rodzaje danych telemetrycznych towarzyszą sekcję kontekstu. Nie wszystkie te pola są przesyłane przy użyciu każdego punktu danych.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| context.Custom.Dimensions [0] |obiekt] |Pary klucz wartość ciągu jest ustawiony za pomocą parametru właściwości niestandardowe. Maksymalna długość 100, wartości maksymalna długość klucza 1024. Więcej niż 100 wartości unikatowych, właściwości mogą być wyszukiwane, ale nie można używać do segmentacji. 200 maksymalna liczba kluczy na klucz instrumentacji. |
| context.Custom.Metrics [0] |obiekt] |Ustaw parametr pomiary niestandardowe i TrackMetrics pary klucz wartość. Maksymalny długości klucza 100 wartości może być liczbą. |
| context.data.eventTime |string |UTC |
| context.data.isSynthetic |wartość logiczna |Pojawia się, że żądania pochodzą z testów sieci web lub bot. |
| context.data.samplingRate |numer |Procent telemetrii wygenerowanej przez zestaw SDK, które są wysyłane do portalu. Należeć do zakresu od 0,0 100,0. |
| context.device |obiekt |Urządzenie klienckie |
| context.device.browser |string |IE, Chrome, ... |
| context.device.browserVersion |string |Chrome 48.0, ... |
| context.device.deviceModel |string | |
| context.device.deviceName |string | |
| context.device.id |string | |
| context.device.locale |string |de-DE, en-GB... |
| context.device.network |string | |
| context.device.oemName |string | |
| context.device.os |string | |
| context.device.osVersion |string |System operacyjny hosta |
| context.device.roleInstance |string |Identyfikator serwera hosta |
| context.device.roleName |string | |
| context.device.screenResolution |string | |
| context.device.type |string |Komputer PC, przeglądarce... |
| context.location |obiekt |Pochodnej ClientIP. |
| context.location.city |string |Pochodnej ClientIP, jeśli są znane |
| context.location.clientip |string |Ostatni ośmiokątem są anonimowe na 0. |
| context.location.continent |string | |
| context.location.country |string | |
| context.location.province |string |Województwo |
| context.Operation.ID |string |Elementy, które mają ten sam identyfikator operacji są wyświetlane jako elementy powiązane w portalu. Zazwyczaj identyfikator żądania. |
| context.Operation.name |string |Nazwa adresu URL lub żądanie |
| context.operation.parentId |string |Zezwala na zagnieżdżone elementy powiązane. |
| context.session.id |string |Identyfikator grupy działań z tego samego źródła. Okres 30 minut bez operacji sygnalizuje koniec sesji. |
| context.session.isFirst |wartość logiczna | |
| context.user.accountAcquisitionDate |string | |
| context.user.accountId |string | |
| context.user.anonAcquisitionDate |string | |
| context.user.anonId |string | |
| context.user.authAcquisitionDate |string |[Uwierzytelniony użytkownik](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |string | |
| context.user.isAuthenticated |wartość logiczna | |
| context.user.storeRegion |string | |
| internal.data.documentVersion |string | |
| internal.data.id |string | Unikatowy identyfikator, który jest przypisywany podczas element pobieranym do usługi Application Insights |

## <a name="events"></a>Events
Niestandardowe zdarzenia generowane przez [poleceń TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| Liczba zdarzeń [0] |liczba całkowita |100 / ([próbkowania](../../azure-monitor/app/sampling.md) współczynnik). Na przykład 4 =&gt; 25%. |
| Nazwa zdarzenia [0] |string |Nazwa zdarzenia.  Maksymalna długość 250. |
| adres url zdarzenia [0] |string | |
| event [0] urlData.base |string | |
| event [0] urlData.host |string | |

## <a name="exceptions"></a>Wyjątki
Raporty [wyjątki](../../azure-monitor/app/asp-net-exceptions.md) na serwerze i w przeglądarce.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| zestaw basicException [0] |string | |
| Liczba basicException [0] |liczba całkowita |100 / ([próbkowania](../../azure-monitor/app/sampling.md) współczynnik). Na przykład 4 =&gt; 25%. |
| exceptionGroup basicException [0] |string | |
| Typ basicException [0] |string | |
| failedUserCodeMethod basicException [0] |string | |
| failedUserCodeAssembly basicException [0] |string | |
| handledAt basicException [0] |string | |
| hasFullStack basicException [0] |wartość logiczna | |
| Identyfikator basicException [0] |string | |
| Metoda basicException [0] |string | |
| komunikat basicException [0] |string |Komunikat o wyjątku. Maksymalna długość wynosi 10 tys. |
| outerExceptionMessage basicException [0] |string | |
| outerExceptionThrownAtAssembly basicException [0] |string | |
| outerExceptionThrownAtMethod basicException [0] |string | |
| outerExceptionType basicException [0] |string | |
| outerId basicException [0] |string | |
| zestaw parsedStack [0] basicException [0] |string | |
| basicException [0], nazwa_pliku parsedStack [0] |string | |
| poziom parsedStack [0] basicException [0] |liczba całkowita | |
| wiersz parsedStack [0] basicException [0] |liczba całkowita | |
| Metoda parsedStack [0] basicException [0] |string | |
| stos basicException [0] |string |Maksymalna długość wynosi 10k |
| typeName basicException [0] |string | |

## <a name="trace-messages"></a>Komunikaty śledzenia
Wysyłany przez [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)i [kart rejestrowania](../../azure-monitor/app/asp-net-trace-logs.md).

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| Nazwa_rejestratora komunikatu [0] |string | |
| Parametry komunikatu [0] |string | |
| komunikat [0] nieprzetworzone |string |Komunikat dziennika maksymalna długość 10 tys. |
| severityLevel komunikatu [0] |string | |

## <a name="remote-dependency"></a>Zależności zdalne
Wysyłany przez TrackDependency. Używany do raportu wydajności i użycia [wywołania zależności](../../azure-monitor/app/asp-net-dependencies.md) na serwerze i wywołań AJAX w przeglądarce.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| asynchroniczne remoteDependency [0] |wartość logiczna | |
| remoteDependency [0] baseName |string | |
| commandName remoteDependency [0] |string |Na przykład "home/index" |
| Liczba remoteDependency [0] |liczba całkowita |100 / ([próbkowania](../../azure-monitor/app/sampling.md) współczynnik). Na przykład 4 =&gt; 25%. |
| remoteDependency [0] dependencyTypeName |string |HTTP, SQL, ... |
| remoteDependency [0] durationMetric.value |numer |Czas od wywołania uzupełniania odpowiedzi przez zależność |
| Identyfikator remoteDependency [0] |string | |
| Nazwa remoteDependency [0] |string |Adres URL. Maksymalna długość 250. |
| Kod wyniku remoteDependency [0] |string |z zależności HTTP |
| Powodzenie remoteDependency [0] |wartość logiczna | |
| Typ remoteDependency [0] |string |Protokół HTTP Sql... |
| adres url remoteDependency [0] |string |Maksymalna długość 2000 |
| remoteDependency [0] urlData.base |string |Maksymalna długość 2000 |
| remoteDependency [0] urlData.hashTag |string | |
| remoteDependency [0] urlData.host |string |Maksymalna długość wynosi 200 |

## <a name="requests"></a>Żądania
Wysyłany przez [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Standardowe moduły umożliwia czas odpowiedzi serwera raportów, mierzona na serwerze.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| Liczba żądań [0] |liczba całkowita |100 / ([próbkowania](../../azure-monitor/app/sampling.md) współczynnik). Na przykład: 4 =&gt; 25%. |
| request [0] durationMetric.value |numer |Czas od żądania przychodzące do odpowiedzi. 1e7 == 1s |
| Identyfikator żądania [0] |string |Identyfikator operacji |
| Nazwa żądania [0] |string |GET/POST + baza adresów url.  Maksymalna długość 250 |
| responseCode żądania [0] |liczba całkowita |Odpowiedzi HTTP wysłanej do klienta |
| żądanie powiodło się [0] |wartość logiczna |Domyślne == (responseCode &lt; 400) |
| adres url żądania [0] |string |Nie wliczając hosta |
| urlData.base żądania [0] |string | |
| request [0] urlData.hashTag |string | |
| request [0] urlData.host |string | |

## <a name="page-view-performance"></a>Wydajność widoku strony
Wysyłane przez przeglądarkę. Pomiar czasu przetwarzania strony, użytkownik inicjuje żądanie, aby wyświetlić pełną (z wyjątkiem asynchroniczne wywołania AJAX).

Wartości w kontekście pokazują kliencki system operacyjny i wersję przeglądarki.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |liczba całkowita |Czas od końca odbieranie HTML do wyświetlania strony. |
| Nazwa clientPerformance [0] |string | |
| clientPerformance [0] networkConnection.value |liczba całkowita |Czas poświęcony na ustanowienia połączenia sieciowego. |
| clientPerformance [0] receiveRequest.value |liczba całkowita |Czas od końca wysyła żądanie do odbierania w odpowiedzi HTML. |
| clientPerformance [0] sendRequest.value |liczba całkowita |Czas od podjęte w celu wysyłania żądań HTTP. |
| clientPerformance [0] total.value |liczba całkowita |Czas od rozpoczyna wysyłanie żądań do wyświetlania strony. |
| adres url clientPerformance [0] |string |Adres URL tego żądania |
| clientPerformance [0] urlData.base |string | |
| clientPerformance [0] urlData.hashTag |string | |
| clientPerformance [0] urlData.host |string | |
| urlData.protocol clientPerformance [0] |string | |

## <a name="page-views"></a>Wyświetlenia strony
Wysyłany przez trackPageView() lub [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| Liczba wyświetleń [0] |liczba całkowita |100 / ([próbkowania](../../azure-monitor/app/sampling.md) współczynnik). Na przykład 4 =&gt; 25%. |
| Wyświetl durationMetric.value [0] |liczba całkowita |Wartość Opcjonalnie trackPageView() lub startTrackPage() - stopTrackPage(). Nie taka sama jak clientPerformance wartości. |
| Nazwa widoku [0] |string |Tytuł strony.  Maksymalna długość 250 |
| adres url widoku [0] |string | |
| Wyświetl urlData.base [0] |string | |
| Wyświetl urlData.hashTag [0] |string | |
| Wyświetl urlData.host [0] |string | |

## <a name="availability"></a>Dostępność
Raporty [testy sieci web dostępności](../../azure-monitor/app/monitor-web-app-availability.md).

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| availabilityMetric.name dostępności [0] |string |availability |
| availabilityMetric.value dostępności [0] |numer |w wersji 1.0 lub 0.0 |
| Liczba dostępności [0] |liczba całkowita |100 / ([próbkowania](../../azure-monitor/app/sampling.md) współczynnik). Na przykład 4 =&gt; 25%. |
| dataSizeMetric.name dostępności [0] |string | |
| dataSizeMetric.value dostępności [0] |liczba całkowita | |
| durationMetric.name dostępności [0] |string | |
| durationMetric.value dostępności [0] |numer |Czas trwania testu. 1e7==1s |
| komunikat o dostępności [0] |string |Błąd diagnostyki |
| wynik dostępności [0] |string |Powodzenie/niepowodzenie |
| runLocation dostępności [0] |string |Źródło geograficznie liczba żądań http |
| Nazwa_testu dostępności [0] |string | |
| Identyfikator przebiegu testu dostępności [0] |string | |
| testTimestamp dostępności [0] |string | |

## <a name="metrics"></a>Metryki
Generowane przez TrackMetric().

Wartość metryki znajduje się w context.custom.metrics[0]

Na przykład:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Wartości metryk — informacje
Wartości metryk, zarówno w raportach metryki i w innych miejscach, są zgłaszane ze strukturą obiektu standardowego. Na przykład:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Aktualnie — mimo że to mogą ulec zmianie w przyszłości — wszystkie wartości zgłoszone standardowe moduły SDK `count==1` i tylko `name` i `value` pola są przydatne. Będzie tylko wówczas, gdy będą się różnić, jeśli piszesz wywołania TrackMetric w którym zestawie innych parametrów.

Celem innych pól jest umożliwiające metryki agregowania w zestawie SDK w celu zredukowania ruchu w portalu. Można na przykład średnia kilka kolejne odczyty, przed wysłaniem raportu o każdym metryki. Następnie może obliczyć min, max, odchylenie standardowe i wartości zagregowanej (Suma lub średnia) i ustaw liczbę do liczby odczytów reprezentowany przez raport.

W powyższej tabeli pominięto rzadko używane pola liczą, min, max, stdDev i sampledValue.

Zamiast wstępnie agregacji metryk, możesz użyć [próbkowania](../../azure-monitor/app/sampling.md) Jeśli musisz ograniczyć ilość danych telemetrii.

### <a name="durations"></a>Czas trwania
Z wyjątkiem sytuacji, gdy inaczej czasu trwania są reprezentowane w liczba dziesiątych części mikrosekund tak, aby 10000000.0 oznacza, że 1 sekundę.

## <a name="see-also"></a>Zobacz także
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Eksport ciągły](export-telemetry.md)
* [Przykłady kodu](export-telemetry.md#code-samples)
