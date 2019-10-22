---
title: Model danych usługi Azure Application Insights | Microsoft Docs
description: Opisuje właściwości eksportowane z eksportu ciągłego w formacie JSON i używane jako filtry.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/08/2019
ms.openlocfilehash: 8f84e3179a6f949e4a322a2218736fc9ebe60442
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677908"
---
# <a name="application-insights-export-data-model"></a>Application Insights eksportowanie modelu danych
Ta tabela zawiera listę właściwości telemetrii wysyłanych z zestawów SDK [Application Insights](../../azure-monitor/app/app-insights-overview.md) do portalu.
Te właściwości będą widoczne w danych wyjściowych z [eksportu ciągłego](export-telemetry.md).
Są one również wyświetlane w filtrach właściwości w [Eksploratorze metryk](../../azure-monitor/app/metrics-explorer.md) i w [przeszukiwaniu diagnostycznym](../../azure-monitor/app/diagnostic-search.md).

Punkty do uwagi:

* `[0]` w tych tabelach oznacza punkt w ścieżce, w którym należy wstawić indeks; ale nie zawsze jest równa 0.
* Czas trwania jest częścią mikrosekundowych, więc 10000000 = = 1 sekunda.
* Daty i godziny są UTC i są podawane w formacie ISO `yyyy-MM-DDThh:mm:ss.sssZ`


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
Wszystkie typy danych telemetrycznych są dołączone do sekcji kontekstowej. Nie wszystkie z tych pól są przesyłane do każdego punktu danych.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| Context. Custom. Dimensions [0] |Obiekt [] |Pary ciągów klucz-wartość ustawione przez parametr właściwości niestandardowych. Maksymalna długość klucza 100, Maksymalna długość wartości 1024. Więcej niż 100 wartości unikatowych, właściwość można wyszukać, ale nie można jej użyć do segmentacji. Maksymalna 200 kluczy na iKey. |
| Context. Custom. Metrics [0] |Obiekt [] |Pary klucz-wartość są ustawiane za pomocą parametru pomiarów niestandardowych i TrackMetrics. Maksymalna długość klucza 100, wartość może być wartością numeryczną. |
| Context. Data. eventTime |string |UTC |
| Context. Data. issyntetyczne |wartość logiczna |Żądanie prawdopodobnie pochodzi z testu bot lub sieci Web. |
| Context. Data. samplingRate |numer |Procent danych telemetrycznych generowanych przez zestaw SDK, który jest wysyłany do portalu. Zakres 0.0-100,0. |
| Context. Device |obiekt |Urządzenie klienckie |
| Context. Device. Browser |string |IE, Chrome,... |
| Context. Device. browserVersion |string |Chrome 48,0,... |
| Context. Device. deviceModel |string | |
| Context. Device. DeviceName |string | |
| context.device.id |string | |
| Context. Device. locale |string |pl GB, de-DE,... |
| Context. Device. Network |string | |
| Context. Device. oemName |string | |
| Context. Device. OS |string | |
| Context. Device. osVersion |string |System operacyjny hosta |
| Context. Device. roleInstance |string |Identyfikator hosta serwera |
| Context. Device. rolename |string | |
| Context. Device. screenResolution |string | |
| Context. Device. Type |string |KOMPUTER, przeglądarka,... |
| Context. Location |obiekt |Pochodny od ClientIP. |
| Context. Location. City |string |Pochodny od ClientIP, jeśli jest znany |
| Context. Location. ClientIP |string |Ostatni ośmiokątem jest anonimowe na 0. |
| Context. Location. kontynent |string | |
| Context. Location. Country |string | |
| Context. Location. Prowincja |string |Województwo |
| context.operation.id |string |Elementy, które mają ten sam Identyfikator operacji, są wyświetlane jako elementy pokrewne w portalu. Zazwyczaj identyfikator żądania. |
| context.operation.name |string |adres URL lub nazwa żądania |
| Context. Operation. parentId |string |Zezwala na zagnieżdżone elementy pokrewne. |
| context.session.id |string |Identyfikator grupy operacji z tego samego źródła. Okres 30 minut bez operacji sygnalizuje zakończenie sesji. |
| Context. Session. isfirst |wartość logiczna | |
| Context. User. accountAcquisitionDate |string | |
| Context. User. accountId |string | |
| Context. User. anonAcquisitionDate |string | |
| Context. User. anonId |string | |
| Context. User. authAcquisitionDate |string |[Uwierzytelniony użytkownik](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| Context. User. authId |string | |
| Context. User. IsAuthenticated |wartość logiczna | |
| Context. User. storeRegion |string | |
| Internal. Data. documentVersion |string | |
| internal.data.id |string | Unikatowy identyfikator, który jest przypisywany podczas pozyskiwania elementu do Application Insights |

## <a name="events"></a>Wydarzenia
Zdarzenia niestandardowe wygenerowane przez [poleceń trackEvent ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| liczba zdarzeń [0] |liczba całkowita |100/(częstotliwość[próbkowania](../../azure-monitor/app/sampling.md) ). Na przykład 4 = &gt; 25%. |
| Nazwa zdarzenia [0] |string |Nazwa zdarzenia.  Maksymalna długość 250. |
| adres URL zdarzenia [0] |string | |
| zdarzenie [0] urlData. Base |string | |
| zdarzenie [0] urlData. host |string | |

## <a name="exceptions"></a>Wyjątki
Zgłasza [wyjątki](../../azure-monitor/app/asp-net-exceptions.md) na serwerze i w przeglądarce.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| zestaw basicexception [0] |string | |
| Licznik basicexception [0] |liczba całkowita |100/(częstotliwość[próbkowania](../../azure-monitor/app/sampling.md) ). Na przykład 4 = &gt; 25%. |
| wyjątek basicexception [0] |string | |
| wyjątek basicexception [0] |string | |
| podstawowaexception [0] failedUserCodeMethod |string | |
| podstawowaexception [0] failedUserCodeAssembly |string | |
| podstawowaexception [0] handledAt |string | |
| podstawowaexception [0] hasFullStack |wartość logiczna | |
| Identyfikator podstawowegoexception [0] |string | |
| Metoda basicexception [0] |string | |
| komunikat podstawowyexception [0] |string |Komunikat o wyjątku. Maksymalna długość 10 znaków. |
| podstawowaexception [0] outerExceptionMessage |string | |
| podstawowaexception [0] outerExceptionThrownAtAssembly |string | |
| podstawowaexception [0] outerExceptionThrownAtMethod |string | |
| podstawowaexception [0] outerExceptionType |string | |
| podstawowaexception [0] outerId |string | |
| zestaw basicexception [0] parsedStack [0] |string | |
| podstawowaexception [0] parsedStack [0] fileName |string | |
| podstawowyexception [0] parsedStack [0] |liczba całkowita | |
| Linia podstawowa [0] parsedStack [0] |liczba całkowita | |
| Podstawowa metoda [0] parsedStack [0] |string | |
| stosexception [0] |string |Maksymalna długość 10 tys. |
| podstawowaexception [0], typeName |string | |

## <a name="trace-messages"></a>Komunikaty śledzenia
Wysyłane przez [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)i [karty rejestrowania](../../azure-monitor/app/asp-net-trace-logs.md).

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| komunikat [0] rejestratorname |string | |
| komunikat [0] parametry |string | |
| komunikat [0] RAW |string |Komunikat dziennika, Maksymalna długość 10 tys. |
| komunikat [0] severityLevel |string | |

## <a name="remote-dependency"></a>Zależność zdalna
Wysłane przez TrackDependency. Służy do zgłaszania wydajności i użycia [wywołań do zależności](../../azure-monitor/app/asp-net-dependencies.md) na serwerze i wywołań AJAX w przeglądarce.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| remoteDependency [0] Async |wartość logiczna | |
| remoteDependency [0] basename |string | |
| remoteDependency [0] CommandName |string |Na przykład "Strona główna/Indeks" |
| remoteDependency [0] liczba |liczba całkowita |100/(częstotliwość[próbkowania](../../azure-monitor/app/sampling.md) ). Na przykład 4 = &gt; 25%. |
| remoteDependency [0] dependencyTypeName |string |HTTP, SQL,... |
| remoteDependency [0] durationMetric. Value |numer |Czas od wywołania do ukończenia odpowiedzi według zależności |
| Identyfikator remoteDependency [0] |string | |
| remoteDependency [0] nazwa |string |Adres URL. Maksymalna długość 250. |
| remoteDependency [0] resultCode |string |z zależności HTTP |
| remoteDependency [0] sukces |wartość logiczna | |
| remoteDependency [0] typ |string |Http,,... SQL |
| adres URL remoteDependency [0] |string |Maksymalna długość 2000 |
| remoteDependency [0] urlData. Base |string |Maksymalna długość 2000 |
| remoteDependency [0] urlData. hasztagów |string | |
| remoteDependency [0] urlData. host |string |Maksymalna długość 200 |

## <a name="requests"></a>Żądania
Wysłane przez [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Moduły standardowe używają tego do raportowania czasu odpowiedzi serwera, mierzoną na serwerze.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| Liczba żądań [0] |liczba całkowita |100/(częstotliwość[próbkowania](../../azure-monitor/app/sampling.md) ). Na przykład: 4 = &gt; 25%. |
| żądanie [0] durationMetric. Value |numer |Czas od żądania do odpowiedzi. 1e7 = = 1S |
| Identyfikator żądania [0] |string |Identyfikator operacji |
| Nazwa żądania [0] |string |GET/POST + URL Base.  Maksymalna długość 250 |
| żądanie [0] responseCode |liczba całkowita |Odpowiedź HTTP wysłana do klienta |
| żądanie [0] zakończyło się pomyślnie |wartość logiczna |Wartość domyślna = = (responseCode &lt; 400) |
| adres URL żądania [0] |string |Nie dołączaj hosta |
| żądanie [0] urlData. Base |string | |
| żądanie [0] urlData. hasztagów |string | |
| żądanie [0] urlData. host |string | |

## <a name="page-view-performance"></a>Wydajność wyświetlania stron
Wysyłane przez przeglądarkę. Mierzy czas przetwarzania strony od użytkownika inicjującego żądanie wyświetlenia zakończone (z wyłączeniem asynchronicznych wywołań AJAX).

Wartości kontekstu pokazują system operacyjny klienta i wersję przeglądarki.

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| clientPerformance [0] clientProcess. Value |liczba całkowita |Czas od końca odebrania kodu HTML w celu wyświetlenia strony. |
| clientPerformance [0] nazwa |string | |
| clientPerformance [0] networkConnection. Value |liczba całkowita |Czas nawiązywania połączenia sieciowego. |
| clientPerformance [0] receiveRequest. Value |liczba całkowita |Czas od końca wysłania żądania do odebrania kodu HTML w odpowiedzi. |
| clientPerformance [0] sendRequest. Value |liczba całkowita |Czas od wykonania w celu wysłania żądania HTTP. |
| clientPerformance [0] suma. wartość |liczba całkowita |Czas od rozpoczęcia wysłania żądania w celu wyświetlenia strony. |
| adres URL clientPerformance [0] |string |Adres URL tego żądania |
| clientPerformance [0] urlData. Base |string | |
| clientPerformance [0] urlData. hasztagów |string | |
| clientPerformance [0] urlData. host |string | |
| clientPerformance [0] urlData. Protocol |string | |

## <a name="page-views"></a>Wyświetlenia stron
Wysłane przez trackPageView () lub [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| Widok [0] |liczba całkowita |100/(częstotliwość[próbkowania](../../azure-monitor/app/sampling.md) ). Na przykład 4 = &gt; 25%. |
| Widok [0] durationMetric. Value |liczba całkowita |Wartość Opcjonalnie ustawiona w trackPageView () lub przez startTrackPage ()-stopTrackPage (). Nie są takie same jak wartości clientPerformance. |
| Widok [0] nazwa |string |Tytuł strony.  Maksymalna długość 250 |
| Wyświetl [0] URL |string | |
| Widok [0] urlData. Base |string | |
| Widok [0] urlData. hasztagów |string | |
| Widok [0] urlData. host |string | |

## <a name="availability"></a>Dostępność
Raportuje [testy sieci Web dostępności](../../azure-monitor/app/monitor-web-app-availability.md).

| Ścieżka | Typ | Uwagi |
| --- | --- | --- |
| Dostępność [0] availabilityMetric.name |string |availability |
| Dostępność [0] availabilityMetric. Value |numer |1,0 lub 0,0 |
| Dostępność [0] |liczba całkowita |100/(częstotliwość[próbkowania](../../azure-monitor/app/sampling.md) ). Na przykład 4 = &gt; 25%. |
| Dostępność [0] dataSizeMetric.name |string | |
| Dostępność [0] dataSizeMetric. Value |liczba całkowita | |
| Dostępność [0] durationMetric.name |string | |
| Dostępność [0] durationMetric. Value |numer |Czas trwania testu. 1e7 = = 1S |
| komunikat o dostępności [0] |string |Diagnostyka niepowodzeń |
| Dostępność [0] wynik |string |Przebieg/niepowodzenie |
| Dostępność [0] runLocation |string |Źródło geograficzne żądania http |
| Dostępność [0] TestName |string | |
| Dostępność [0] testRunId |string | |
| Dostępność [0] testTimestamp |string | |

## <a name="metrics"></a>Metryki
Wygenerowano przez TrackMetric ().

Wartość metryki znajduje się w kontekście. Custom. Metrics [0]

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

## <a name="about-metric-values"></a>Informacje o wartościach metryk
Wartości metryk, zarówno w raportach metryk, jak i w innym miejscu, są raportowane przy użyciu standardowej struktury obiektów. Na przykład:

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

Obecnie ta sytuacja może ulec zmianie w przyszłości — wszystkie wartości raportowane przez standardowe moduły SDK `count==1` i tylko pola `name` i `value`. Jedyną sytuacją, w której byłyby różne, byłoby napisanie własnych wywołań TrackMetric, w których są ustawiane inne parametry.

Celem innych pól jest umożliwienie agregowania metryk w zestawie SDK w celu zmniejszenia ruchu do portalu. Na przykład można obliczyć kilka kolejnych odczytów przed wysłaniem poszczególnych raportów metryk. Następnie można obliczyć wartości minimalną, maksymalną, odchylenie standardowe i wartość zagregowaną (sum lub Average) i ustawić liczbę odczytów reprezentowanych przez raport.

W powyższych tabelach pominięto liczbę rzadko używanych pól, minimalną, maksymalną, stdDev i sampledValue.

Zamiast wstępnie agregowanych metryk można użyć [próbkowania](../../azure-monitor/app/sampling.md) , jeśli zachodzi potrzeba zmniejszenia ilości danych telemetrycznych.

### <a name="durations"></a>Czasów trwania
O ile nie zaznaczono inaczej, czasy trwania są reprezentowane w dziesiątce mikrosekundowych, więc 10000000,0 oznacza 1 sekundę.

## <a name="see-also"></a>Zobacz także
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Eksport ciągły](export-telemetry.md)
* [Przykłady kodu](export-telemetry.md#code-samples)
