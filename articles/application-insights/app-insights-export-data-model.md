---
title: Model danych usługi Insights aplikacji Azure | Dokumentacja firmy Microsoft
description: Opisuje właściwości wyeksportowane z Eksport ciągły w formacie JSON i używać jako filtrów.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/21/2016
ms.author: mbullwin
ms.openlocfilehash: 841b11421b7198efc7fc9bb3226c7b067fcbbdc4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598831"
---
# <a name="application-insights-export-data-model"></a>Model danych eksportu Insights aplikacji
Poniższa tabela zawiera właściwości danych telemetrycznych wysłanych z [usługi Application Insights](app-insights-overview.md) zestawy SDK do portalu.
Zobaczysz tych właściwości w danych wyjściowych z [eksportu ciągłego](app-insights-export-telemetry.md).
Widoczne są także w filtry właściwości w [Explorer Metryka](app-insights-metrics-explorer.md) i [diagnostycznych wyszukiwania](app-insights-diagnostic-search.md).

Informacje, które należy zwrócić uwagę:

* `[0]` w poniższych tabelach oznacza punkt w ścieżce, jeżeli konieczne jest wstawienie indeksu; ale nie zawsze jest 0.
* Czas trwania znajdują się w dziesiąte mikrosekund, więc 10000000 == 1 sekundę.
* Daty i godziny są w UTC i są podane w formacie ISO `yyyy-MM-DDThh:mm:ss.sssZ`


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
Wszystkie typy telemetrii towarzyszy sekcję kontekstu. Nie wszystkie pola te są przesyłane z każdego punktu danych.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| context.Custom.Dimensions [0] |obiekt] |Wartość parametru właściwości niestandardowe pary klucz wartość ciągu. Maksymalna długość klucza 100, wartości maksymalnej długości 1024. Więcej niż 100 wartości unikatowe, właściwość można przeszukiwać, ale nie można użyć w przypadku segmentacji. Maksymalna liczba 200 kluczy dla ikey. |
| context.Custom.Metrics [0] |obiekt] |Ustaw parametr niestandardowych miar i TrackMetrics pary klucz wartość. Maksymalnej długości klucza 100 wartości może być liczbą. |
| context.data.eventTime |ciąg |UTC |
| context.data.isSynthetic |wartość logiczna |Żądanie zostanie wyświetlony pochodzą z testu bot lub sieci web. |
| context.data.samplingRate |numer |Wartość procentowa telemetrii generowane przez zestaw SDK, który jest wysyłany do portalu. Zakres niż 100,0 0,0. |
| context.Device |obiekt |Urządzenia klienckiego |
| context.Device.Browser |ciąg |IE Chrome... |
| context.device.browserVersion |ciąg |Chrome 48,0... |
| context.device.deviceModel |ciąg | |
| context.device.deviceName |ciąg | |
| context.Device.ID |ciąg | |
| context.Device.Locale |ciąg |de-DE, en-GB... |
| context.Device.Network |ciąg | |
| context.device.oemName |ciąg | |
| context.device.osVersion |ciąg |Systemu operacyjnego hosta |
| context.device.roleInstance |ciąg |Identyfikator serwera hosta |
| context.device.roleName |ciąg | |
| context.Device.Type |ciąg |Komputer PC przeglądarki... |
| context.Location |obiekt |Pochodną clientip. |
| context.Location.City |ciąg |Pochodne clientip, jeśli znane |
| context.Location.ClientIP |ciąg |Ostatni ośmiokąt anonimowy jest 0. |
| context.Location.continent |ciąg | |
| context.Location.country |ciąg | |
| context.Location.Province |ciąg |Województwo |
| context.Operation.ID |ciąg |Elementy, które mają ten sam identyfikator operacji są wyświetlane jako elementy powiązane w portalu. Zazwyczaj identyfikator żądania. |
| context.Operation.name |ciąg |Nazwa adresu URL lub żądania |
| context.operation.parentId |ciąg |Zezwala na zagnieżdżone elementy powiązane. |
| context.Session.ID |ciąg |Identyfikator grupy działań z tego samego źródła. Okres 30 minut bez operacji sygnalizuje koniec sesji. |
| context.session.isFirst |wartość logiczna | |
| context.user.accountAcquisitionDate |ciąg | |
| context.user.anonAcquisitionDate |ciąg | |
| context.user.anonId |ciąg | |
| context.user.authAcquisitionDate |ciąg |[Uwierzytelniony użytkownik](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated |wartość logiczna | |
| internal.data.documentVersion |ciąg | |
| internal.Data.ID |ciąg | Unikatowy identyfikator, gdy element jest pozyskanych przypisany do usługi Application Insights |

## <a name="events"></a>Zdarzenia
Niestandardowe zdarzenia generowane przez [funkcji TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent).

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| Liczba zdarzeń [0] |liczba całkowita |100 / ([próbkowania](app-insights-sampling.md) szybkość). Na przykład 4 =&gt; 25%. |
| Nazwa zdarzenia [0] |ciąg |Nazwa zdarzenia.  Maksymalna długość 250. |
| adres url zdarzenia [0] |ciąg | |
| zdarzenia [0] urlData.base |ciąg | |
| zdarzenia [0] urlData.host |ciąg | |

## <a name="exceptions"></a>Wyjątki
Raporty [wyjątki](app-insights-asp-net-exceptions.md) na serwerze i w przeglądarce.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| zestaw basicException [0] |ciąg | |
| Liczba basicException [0] |liczba całkowita |100 / ([próbkowania](app-insights-sampling.md) szybkość). Na przykład 4 =&gt; 25%. |
| exceptionGroup basicException [0] |ciąg | |
| exceptionType basicException [0] |ciąg | |
| failedUserCodeMethod basicException [0] |ciąg | |
| failedUserCodeAssembly basicException [0] |ciąg | |
| handledAt basicException [0] |ciąg | |
| hasFullStack basicException [0] |wartość logiczna | |
| Identyfikator basicException [0] |ciąg | |
| Metoda basicException [0] |ciąg | |
| komunikat basicException [0] |ciąg |Komunikat o wyjątku. Maksymalna długość 10 tys. |
| outerExceptionMessage basicException [0] |ciąg | |
| outerExceptionThrownAtAssembly basicException [0] |ciąg | |
| outerExceptionThrownAtMethod basicException [0] |ciąg | |
| outerExceptionType basicException [0] |ciąg | |
| outerId basicException [0] |ciąg | |
| basicException [0] [0] parsedStack zestawu |ciąg | |
| Nazwa pliku parsedStack [0] basicException [0] |ciąg | |
| poziom parsedStack [0] basicException [0] |liczba całkowita | |
| basicException [0] [0] parsedStack wiersza |liczba całkowita | |
| Metoda parsedStack [0] basicException [0] |ciąg | |
| stos basicException [0] |ciąg |Maksymalna długość 10k |
| właściwość typeName basicException [0] |ciąg | |

## <a name="trace-messages"></a>Śledź komunikaty
Wysyłany przez [TrackTrace](app-insights-api-custom-events-metrics.md#tracktrace), a [adaptery rejestrowania](app-insights-asp-net-trace-logs.md).

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| Nazwa_rejestratora komunikatu [0] |ciąg | |
| Parametry komunikatu [0] |ciąg | |
| nieprzetworzona komunikatu [0] |ciąg |Komunikat dziennika maksymalna długość 10 tys. |
| poziom ważności komunikatu [0] |ciąg | |

## <a name="remote-dependency"></a>Zależności zdalne
Wysyłane przez TrackDependency. Używany do raportu wydajności i użycia [wywołania zależności](app-insights-asp-net-dependencies.md) na serwerze i wywołania AJAX w przeglądarce.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| asynchroniczne remoteDependency [0] |wartość logiczna | |
| nazwę bazową remoteDependency [0] |ciąg | |
| commandName remoteDependency [0] |ciąg |Na przykład "Strona główna/index" |
| Liczba remoteDependency [0] |liczba całkowita |100 / ([próbkowania](app-insights-sampling.md) szybkość). Na przykład 4 =&gt; 25%. |
| dependencyTypeName remoteDependency [0] |ciąg |HTTP, SQL... |
| durationMetric.value remoteDependency [0] |numer |Czas od wywołania zakończenia odpowiedzi przez zależność |
| Identyfikator remoteDependency [0] |ciąg | |
| Nazwa remoteDependency [0] |ciąg |Adres URL. Maksymalna długość 250. |
| resultCode remoteDependency [0] |ciąg |z zależności HTTP |
| Powodzenie remoteDependency [0] |wartość logiczna | |
| Typ remoteDependency [0] |ciąg |HTTP, Sql... |
| adres url remoteDependency [0] |ciąg |Maksymalna długość 2000 |
| urlData.base remoteDependency [0] |ciąg |Maksymalna długość 2000 |
| urlData.hashTag remoteDependency [0] |ciąg | |
| urlData.host remoteDependency [0] |ciąg |Maksymalna długość 200 |

## <a name="requests"></a>Żądania
Wysyłany przez [TrackRequest](app-insights-api-custom-events-metrics.md#trackrequest). Standardowe moduły umożliwia czas odpowiedzi serwera raportów, na serwerze.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| Liczba żądań [0] |liczba całkowita |100 / ([próbkowania](app-insights-sampling.md) szybkość). Na przykład: 4 =&gt; 25%. |
| durationMetric.value żądania [0] |numer |Czas z żądania przychodzące do odpowiedzi. 1e7 == 1s |
| Identyfikator żądania [0] |ciąg |Identyfikator operacji |
| Nazwa żądania [0] |ciąg |GET/POST + podstawowego adresu url.  Maksymalna długość 250 |
| responseCode żądania [0] |liczba całkowita |Odpowiedź HTTP wysyłane do klienta |
| Powodzenie żądania [0] |wartość logiczna |Domyślna == (responseCode &lt; 400) |
| adres url żądania [0] |ciąg |Wyłączeniem hosta |
| urlData.base żądania [0] |ciąg | |
| urlData.hashTag żądania [0] |ciąg | |
| urlData.host żądania [0] |ciąg | |

## <a name="page-view-performance"></a>Strona Widok wydajności
Wysyłane przez przeglądarkę. Mierzy czas przetwarzania strony, od użytkownika inicjujący żądanie, aby wyświetlić pełną (z wyjątkiem asynchroniczne wywołania AJAX).

Wartości w kontekście Pokaż kliencki system operacyjny i wersja przeglądarki.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| clientProcess.value clientPerformance [0] |liczba całkowita |Czas od końca odbieranie HTML do wyświetlania strony. |
| Nazwa clientPerformance [0] |ciąg | |
| networkConnection.value clientPerformance [0] |liczba całkowita |Czas do ustanowienia połączenia sieciowego. |
| receiveRequest.value clientPerformance [0] |liczba całkowita |Czas od końca wysyłania żądania odbierania HTML w odpowiedzi. |
| sendRequest.value clientPerformance [0] |liczba całkowita |Czas od podjętych można wysłać żądania HTTP. |
| total.value clientPerformance [0] |liczba całkowita |Czas od rozpoczyna wysyłanie żądania do wyświetlania strony. |
| adres url clientPerformance [0] |ciąg |Adres URL tego żądania |
| urlData.base clientPerformance [0] |ciąg | |
| urlData.hashTag clientPerformance [0] |ciąg | |
| urlData.host clientPerformance [0] |ciąg | |
| urlData.protocol clientPerformance [0] |ciąg | |

## <a name="page-views"></a>Wyświetlenia strony
Wysyłany przez trackPageView() lub [stopTrackPage](app-insights-api-custom-events-metrics.md#page-views)

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| Liczba widoku [0] |liczba całkowita |100 / ([próbkowania](app-insights-sampling.md) szybkość). Na przykład 4 =&gt; 25%. |
| durationMetric.value widoku [0] |liczba całkowita |Wartość Opcjonalnie trackPageView() lub startTrackPage() - stopTrackPage(). Nie taka sama jak clientPerformance wartości. |
| Nazwa widoku [0] |ciąg |Tytuł strony.  Maksymalna długość 250 |
| adres url widoku [0] |ciąg | |
| urlData.base widoku [0] |ciąg | |
| urlData.hashTag widoku [0] |ciąg | |
| urlData.host widoku [0] |ciąg | |

## <a name="availability"></a>Dostępność
Raporty [testów sieci web dostępności](app-insights-monitor-web-app-availability.md).

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| availabilityMetric.name dostępności [0] |ciąg |availability |
| availabilityMetric.value dostępności [0] |numer |w wersji 1.0 lub 0,0 |
| Liczba dostępności [0] |liczba całkowita |100 / ([próbkowania](app-insights-sampling.md) szybkość). Na przykład 4 =&gt; 25%. |
| dataSizeMetric.name dostępności [0] |ciąg | |
| dataSizeMetric.value dostępności [0] |liczba całkowita | |
| durationMetric.name dostępności [0] |ciąg | |
| durationMetric.value dostępności [0] |numer |Czas trwania testu. 1e7 == 1s |
| komunikat dostępności [0] |ciąg |Błąd diagnostyki |
| wynik dostępności [0] |ciąg |Przebieg/niepowodzenie |
| runLocation dostępności [0] |ciąg |Liczba żądań http geograficznie źródła |
| Nazwa_testu dostępności [0] |ciąg | |
| testRunId dostępności [0] |ciąg | |
| testTimestamp dostępności [0] |ciąg | |

## <a name="metrics"></a>Metryki
Wygenerowany przez TrackMetric().

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
Wartości metryki, zarówno w raportach metryki i w innych miejscach, są zgłaszane ze strukturą obiektu standardowa. Na przykład:

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

Aktualnie — mimo że to mogą ulec zmianie w przyszłości — wszystkie wartości zgłoszone standardowe moduły SDK `count==1` i tylko `name` i `value` pola są przydatne. Jeśli piszesz wywołania TrackMetric jest tylko wówczas, gdy będą się różnić w którym Ustaw inne parametry.

Celem innych pól jest umożliwienie metryki być łączone w zestawie SDK, aby zmniejszyć ruch do portalu. Na przykład można średni kilka kolejne odczyty przed wysłaniem raportu o każdym metryki. Następnie będzie obliczyć min, max i odchylenie standardowe wartości agregacji (Suma lub średnia) i ustaw liczbę do liczby odczytów reprezentowany przez raport.

W powyższej tabeli mają możemy pominięcia liczba pól rzadko używane, min, max, stdDev i sampledValue.

Zamiast wstępnie agregację metryki, możesz użyć [próbkowania](app-insights-sampling.md) Jeśli musisz zmniejszyć ilość danych telemetrycznych.

### <a name="durations"></a>Czas trwania
Z wyjątkiem przypadków, gdy zaznaczono inaczej czas trwania są reprezentowane w dziesiąte mikrosekund tak, aby 10000000.0 oznacza 1 sekundę.

## <a name="see-also"></a>Zobacz także
* [Application Insights](app-insights-overview.md)
* [Eksport ciągły](app-insights-export-telemetry.md)
* [Przykłady kodu](app-insights-export-telemetry.md#code-samples)
