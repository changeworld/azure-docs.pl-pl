---
title: Metryki oparte na dziennikach usługi Azure Application Insights | Dokumenty firmy Microsoft
description: W tym artykule wymieniono metryki usługi Azure Application Insights z obsługiwanymi agregacjami i wymiarami. Szczegółowe informacje na temat metryk opartych na dzienniku obejmują podstawowe instrukcje zapytania Kusto.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664919"
---
# <a name="application-insights-log-based-metrics"></a>Metryki oparte na dziennikach usługi Application Insights

Metryki oparte na dzienniku usługi Application Insights umożliwiają analizowanie kondycji monitorowanych aplikacji, tworzenie zaawansowanych pulpitów nawigacyjnych i konfigurowanie alertów. Istnieją dwa rodzaje metryk:

* [Metryki oparte na dziennikach](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) za sceną są tłumaczone na [zapytania Kusto](https://docs.microsoft.com/azure/kusto/query/) z przechowywanych zdarzeń.
* [Standardowe metryki](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) są przechowywane jako wstępnie zagregowane serie czasowe.

Ponieważ *standardowe metryki* są wstępnie agregowane podczas zbierania, mają lepszą wydajność w czasie kwerendy. To sprawia, że są one lepszym wyborem do pulpitu nawigacyjnego i w czasie rzeczywistym alertów. *Metryki oparte na dzienniku* mają więcej wymiarów, co czyni je doskonałą opcją analizy danych i diagnostyki ad hoc. Użyj [selektora obszaru nazw,](metrics-getting-started.md#create-your-first-metric-chart) aby przełączać się między metrykami opartymi na dzienniku a standardowymi w [Eksploratorze metryk](metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Interpretowanie i używanie zapytań z tego artykułu

W tym artykule wymieniono metryki z obsługiwanymi agregacjami i wymiarami. Szczegółowe informacje na temat metryk opartych na dzienniku obejmują podstawowe instrukcje zapytania Kusto. Dla wygody każda kwerenda używa wartości domyślnych dla szczegółowości czasu, typu wykresu, a czasami dzielenia wymiaru, co upraszcza korzystanie z kwerendy w usłudze Log Analytics bez konieczności modyfikowania.

Podczas drukowania tej samej metryki w [Eksploratorze metryk](metrics-getting-started.md)nie ma żadnych wartości domyślnych — kwerenda jest dostosowywana dynamicznie na podstawie ustawień wykresu:

- Wybrany **zakres czasu** jest tłumaczony na dodatkowe, *gdzie sygnatura czasowa...* klauzula, aby wybrać tylko zdarzenia z wybranego zakresu czasu. Na przykład wykres przedstawiający dane z ostatnich 24 godzin, zapytanie zawiera *| gdzie sygnatura czasowa > temu(24 h)*.

- Wybrana **szczegółowość czasu** jest umieszczana w ostatecznej *podsumować ... przez klauzulę bin(timestamp, [time grain]).*

- Wszystkie wybrane wymiary **filtru** są tłumaczone na dodatkowe *klauzule gdzie.*

- Wybrany wymiar **wykresu podziału** jest tłumaczony na dodatkową właściwość podsumowania. Na przykład, jeśli wykres zostanie podzielony według *lokalizacji*i wydruku przy użyciu 5-minutowej szczegółowości czasu, *klauzula podsumowania* zostanie podsumowana *... przez bin(sygnatura czasowa, 5 m), lokalizacja*.

> [!NOTE]
> Jeśli jesteś nowy w języku zapytań Kusto, zaczynasz od kopiowania i wklejania instrukcji Kusto do okienka zapytań usługi Log Analytics bez wprowadzania żadnych modyfikacji. Kliknij **przycisk Uruchom,** aby wyświetlić wykres podstawowy. Jak zacząć rozumieć składnię języka zapytań, można rozpocząć wprowadzanie małych zmian i zobaczyć wpływ zmiany. Eksplorowanie własnych danych to świetny sposób na rozpoczęcie realizacji pełnej mocy [usługi Log Analytics](../../azure-monitor/log-query/get-started-portal.md) i usługi Azure [Monitor.](../../azure-monitor/overview.md)

## <a name="availability-metrics"></a>Metryki dostępności

Metryki w kategorii Dostępność umożliwiają wyświetlanie kondycji aplikacji sieci web obserwowanej z punktów na całym świecie. [Skonfiguruj testy dostępności,](../../azure-monitor/app/monitor-web-app-availability.md) aby rozpocząć korzystanie z dowolnych metryk z tej kategorii.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Dostępność (dostępnośćWyniki/dostępnośćProcent)
Metryka *Dostępność* pokazuje procent przebiegów testu sieci web, które nie wykryły żadnych problemów. Najniższa możliwa wartość wynosi 0, co oznacza, że wszystkie przebiegi testu sieci web nie powiodły się. Wartość 100 oznacza, że wszystkie przebiegi testu sieci web przeszły kryteria sprawdzania poprawności.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|---|---|---|
|Procentowe|Średnia|Uruchom lokalizację, nazwę testu|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Czas trwania testu dostępności (dostępnośćWyniki/czas trwania)

Metryka *czasu trwania testu dostępności* pokazuje, ile czasu zajęło uruchomienie testu sieci web. W przypadku [wieloetapowych testów sieci web](../../azure-monitor/app/availability-multistep.md)metryka odzwierciedla całkowity czas wykonywania wszystkich kroków.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|---|---|---|
|Milisekund|Średnia, Min, Max|Uruchom lokalizację, nazwę testu, wynik testu

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Testy dostępności (dostępnośćWyniki/liczba)

Metryka *Testy dostępności* odzwierciedla liczbę testów sieci web uruchamianych przez usługę Azure Monitor.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|---|---|---|
|Liczba|Liczba|Uruchom lokalizację, nazwę testu, wynik testu|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Dane przeglądarki

Metryki przeglądarki są zbierane przez SDK JavaScript aplikacji Insights z rzeczywistych przeglądarek użytkowników końcowych. Zapewniają one doskonały wgląd w środowisko użytkowników z aplikacją internetową. Metryki przeglądarki zazwyczaj nie są próbkowane, co oznacza, że zapewniają większą dokładność liczb użycia w porównaniu do metryk po stronie serwera, które mogą być wypaczone przez próbkowanie.

> [!NOTE]
> Aby zbierać dane przeglądarki, aplikacja musi być instrumentowana za pomocą [SDK JavaScript aplikacji Insights](../../azure-monitor/app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Czas ładowania strony przeglądarki (przeglądarkaTimings/totalDuration)

|Jednostka miary|Obsługiwane agregacje|Wstępnie zagregowane wymiary|
|---|---|---|
|Milisekund|Średnia, Min, Max|Brak|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Czas przetwarzania klienta (przeglądarkaTiming/processingDuration)

|Jednostka miary|Obsługiwane agregacje|Wstępnie zagregowane wymiary|
|---|---|---|
|Milisekund|Średnia, Min, Max|Brak|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Czas ładowania strony sieciowej (przeglądarkaTimings/networkDuration)

|Jednostka miary|Obsługiwane agregacje|Wstępnie zagregowane wymiary|
|---|---|---|
|Milisekund|Średnia, Min, Max|Brak|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Odbieranie czasu odpowiedzi (przeglądarkaTimings/receiveDuration)

|Jednostka miary|Obsługiwane agregacje|Wstępnie zagregowane wymiary|
|---|---|---|
|Milisekund|Średnia, Min, Max|Brak|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Wyślij czas żądania (przeglądarkaTimings/sendDuration)

|Jednostka miary|Obsługiwane agregacje|Wstępnie zagregowane wymiary|
|---|---|---|
|Milisekund|Średnia, Min, Max|Brak|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Metryki awarii

Metryki w **błędy** pokazują problemy z przetwarzaniem żądań, wywołania zależności i zgłoszonych wyjątków.

### <a name="browser-exceptions-exceptionsbrowser"></a>Wyjątki przeglądarki (wyjątki/przeglądarka)

Ta metryka odzwierciedla liczbę zgłoszonych wyjątków z kodu aplikacji uruchomionego w przeglądarce. Tylko wyjątki, które są ```trackException()``` śledzone za pomocą wywołania interfejsu API usługi Application Insights są uwzględniane w metryce.

|Jednostka miary|Obsługiwane agregacje|Wstępnie zagregowane wymiary|Uwagi|
|---|---|---|---|
|Liczba|Liczba|Brak|Wersja oparta na dzienniku używa agregacji **sum**|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Niepowodzenia wywołania zależności (zależności/nie powiodły się)

Liczba wywołań zależności nie powiodło się.

|Jednostka miary|Obsługiwane agregacje|Wstępnie zagregowane wymiary|Uwagi|
|---|---|---|---|
|Liczba|Liczba|Brak|Wersja oparta na dzienniku używa agregacji **sum**|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Wyjątki (wyjątki/liczba)

Za każdym razem, gdy rejestrujesz wyjątek do usługi Application Insights, istnieje wywołanie [metody trackException()](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) sdk. Metryka Wyjątki pokazuje liczbę zarejestrowanych wyjątków.

|Jednostka miary|Obsługiwane agregacje|Wstępnie zagregowane wymiary|Uwagi|
|---|---|---|---|
|Liczba|Liczba|Nazwa roli w chmurze, wystąpienie roli w chmurze, typ urządzenia|Wersja oparta na dzienniku używa agregacji **sum**|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Żądania nie powiodły się (żądania/niepowodzenie)

Liczba śledzonych żądań serwera, które zostały oznaczone jako *nieudane.* Domyślnie SDK usługi Application Insights automatycznie oznacza każde żądanie serwera, które zwróciło kod odpowiedzi HTTP 5xx lub 4xx jako żądanie nieudane. Tę logikę można dostosować, modyfikując właściwość *sukcesu* elementu telemetrii żądania w [niestandardowym inicjatorze telemetrii](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

|Jednostka miary|Obsługiwane agregacje|Wstępnie zagregowane wymiary|Uwagi|
|---|---|---|---|
|Liczba|Liczba|Wystąpienie roli w chmurze, nazwa roli w chmurze, ruch rzeczywisty lub syntetyczny, wydajność żądania, kod odpowiedzi|Wersja oparta na dzienniku używa agregacji **sum**|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Wyjątki serwera (wyjątki/serwer)

Ta metryka pokazuje liczbę wyjątków serwera.

|Jednostka miary|Obsługiwane agregacje|Wstępnie zagregowane wymiary|Uwagi|
|---|---|---|---|
|Liczba|Liczba|Nazwa roli w chmurze, wystąpienie roli w chmurze|Wersja oparta na dzienniku używa agregacji **sum**|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Liczniki wydajności

Użyj metryk w kategorii **Liczniki wydajności,** aby uzyskać dostęp do [liczników wydajności systemu zebranych przez aplikację Application Insights](../../azure-monitor/app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Dostępna pamięć (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Współczynnik wyjątków (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Czas wykonywania żądania HTTP (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Szybkość żądania HTTP (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Żądania HTTP w kolejce aplikacji (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Procesor przetwarzania (performanceCounters/processCpuPercentage)

Metryka pokazuje, ile całkowitej pojemności procesora jest zużywana przez proces, który jest hostowany monitorowanej aplikacji.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|Procentowe|Średnia, Min, Max|Wystąpienie roli w chmurze

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Szybkość we/wy procesu (wydajnośćLiczniki/procesIOBytesPerSekund)

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|Bajty na sekundę|Średnia, Min, Max|Wystąpienie roli w chmurze

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Przetwarzanie bajtów prywatnych (performanceCounters/processPrivateBytes)

Ilość pamięci nieudzielonej, która jest przydzielona przez monitorowany proces dla swoich danych.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|Bajty|Średnia, Min, Max|Wystąpienie roli w chmurze

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Czas procesora (performanceCounters/processorCpuPercentage)

Zużycie procesora przez *wszystkie* procesy uruchomione w monitorowanym wystąpieniu serwera.

|Jednostka miary|Obsługiwane agregacje|Obsługiwane wymiary|
|---|---|---|
|Procentowe|Średnia, Min, Max|Wystąpienie roli w chmurze

>[!NOTE]
> Metryka czasu procesora nie jest dostępna dla aplikacji hostowanych w usługach Azure App Services. Metryka [Procesora CPU procesowania](#process-cpu-performancecountersprocesscpupercentage) służy do śledzenia wykorzystania procesora CPU aplikacji internetowych hostowanych w usługach app services.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Metryki serwera

### <a name="dependency-calls-dependenciescount"></a>Wywołania zależności (zależności/liczba)

Ta metryka jest w odniesieniu do liczby wywołań zależności.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Czas trwania zależności (zależności/czas trwania)

Ta metryka odnosi się do czasu trwania wywołań zależności.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Żądania serwera (żądania/liczba)

Ta metryka odzwierciedla liczbę przychodzących żądań serwera, które zostały odebrane przez aplikację sieci web.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Czas odpowiedzi serwera (żądania/czas trwania)

Ta metryka odzwierciedla czas, jaki zajęło serwerom przetwarzanie żądań przychodzących.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Metryki użycia

### <a name="page-view-load-time-pageviewsduration"></a>Czas ładowania widoku strony (odsłona strony/czas trwania)

Ta metryka odnosi się do czasu, jaki zajęło załadowanie zdarzeń PageView.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Wyświetlenia strony (odsłony/liczba stron)

Liczba zdarzeń PageView zarejestrowanych w interfejsie API usługi TrackPageView() aplikacji insights.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sesje (sesje/liczba)

Ta metryka odnosi się do liczby różnych identyfikatorów sesji.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Ślady (ślady/liczba)

Liczba instrukcji śledzenia zarejestrowanych za pomocą wywołania interfejsu API usługi TrackTrace() application insights.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Użytkownicy (użytkownicy/liczba)

Liczba różnych użytkowników, którzy uzyskili dostęp do aplikacji. Dokładność tej metryki może mieć znaczący wpływ przy użyciu pobierania próbek telemetrii i filtrowania.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Użytkownicy, uwierzytelnieni (użytkownicy/uwierzytelnieni)

Liczba różnych użytkowników, którzy uwierzytelnili się w aplikacji.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
