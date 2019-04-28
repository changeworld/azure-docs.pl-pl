---
title: Przykłady zapytań dzienników w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Przykłady zapytań dzienników w usłudze Azure Monitor, za pomocą języka zapytania Kusto.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/03/2018
ms.author: bwren
ms.openlocfilehash: 2c35bc4026c81cbc8b95225e688a3922bc320554
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60759915"
---
# <a name="azure-monitor-log-query-examples"></a>Przykłady zapytań dzienników w usłudze Azure Monitor
W tym artykule przedstawiono różne przykłady [zapytania](log-query-overview.md) przy użyciu [język zapytania Kusto](/azure/kusto/query/) pobrać różnego rodzaju dane dzienników z usługi Azure Monitor. Różne metody są używane do konsolidacji i analizowania danych, aby można było używać tych przykładów, aby zidentyfikować różne strategie, których można użyć do własnych wymagań.  

Zobacz [Skorowidz języka Kusto](https://docs.microsoft.com/azure/kusto/query/) szczegółowe informacje dotyczące innych słów kluczowych, używane w tych przykładów. Zapoznaj się z artykułem [lekcji na temat tworzenia zapytań](get-started-queries.md) Jeśli jesteś nowym użytkownikiem usługi Azure Monitor.

## <a name="events"></a>Zdarzenia

### <a name="search-application-level-events-described-as-cryptographic"></a>Wyszukać zdarzenia na poziomie aplikacji określana jako "Kryptograficznych"
W tym przykładzie tabela **Zdarzenia** jest przeszukiwana pod kątem rekordów, które w parametrze **EventLog** mają wartość _Application_ oraz zawierają ciąg _cryptographic_ w parametrze **RenderedDescription**. Wyświetlane są rekordy z ostatnich 24 godzin.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription == "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Wyszukaj zdarzenia związane z unmarshaling
Wyszukaj tabele **zdarzeń** i **SecurityEvents** dla rekordów tego wzmiankę _unmarshaling_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Puls

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Widok Tydzień over tygodniowych, liczby komputerów, które wysyłają dane wykresu

Poniższy przykład wykresy liczba odrębne komputery wysyłające pulsy, co tydzień.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Znajdź komputery ze starymi

Poniższy przykład umożliwia znalezienie komputerów, które były aktywne w ciągu ostatniego dnia, ale nie wysłał pulsu w ciągu ostatniej godziny.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Pobierz najnowszy rekord pulsu na adres IP komputera

W tym przykładzie zwraca ostatni rekord pulsu dla każdego adresu IP komputera.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Zgodne z rekordami stan chronionych za pomocą rekordy pulsu

W tym przykładzie wyszukuje powiązane ochrony stan rekordów i rekordy pulsu, dopasowane na komputerze i czas.
Należy pamiętać, że wartość pola Czas jest zaokrąglana do najbliższej minuty. Użyliśmy obliczeń bin środowiska uruchomieniowego, aby to zrobić: `round_time=bin(TimeGenerated, 1m)`.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Współczynnik dostępności serwera

Oblicz współczynnik dostępności serwera na podstawie rekordów pulsu. Dostępność jest definiowana jako "co najmniej 1 pulsu na godzinę".
Tak Jeśli serwer był dostępny 98 100 godzin, stopa dostępności to 98%.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Wiele typów danych

### <a name="chart-the-record-count-per-table"></a>Wykres liczba rekordów na tabelę
Poniższy przykład umożliwia zbieranie informacji o wszystkich rekordów z wszystkich tabel z ostatnich pięciu godzin i zlicza liczbę rekordów znajdowały się w każdej tabeli. Wyniki są wyświetlane w wykres czasu.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Liczba wszystkich dzienników zebranych w ciągu ostatniej godziny według typu
W poniższym przykładzie wyszukuje wszystkie zgłoszone w ciągu ostatniej godziny i zlicza rekordy w każdej tabeli przez **typu**. Wyniki są wyświetlane na wykresie słupkowym.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Policz rekordy diagnostyki platformy Azure na kategorię
W tym przykładzie zlicza wszystkie rekordy usługi Diagnostyka Azure dla każdej kategorii unikatowy.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Pobierz losową rekord dla każdej kategorii unikatowy
W tym przykładzie pobiera rekord pojedynczego losowe diagnostyki platformy Azure dla każdej kategorii unikatowy.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Pobierz najnowszy rekord dla każdej kategorii
W tym przykładzie pobiera najnowszy rekord diagnostyki platformy Azure w poszczególnych kategoriach unikatowy.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Monitorowanie sieci

### <a name="computers-with-unhealthy-latency"></a>Komputery z opóźnieniem w złej kondycji
W tym przykładzie tworzy listę unikatowych komputerów, z opóźnieniem w złej kondycji.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Wydajność

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Dołącz do rekordów wydajności komputerów do skorelowania w pamięci i procesora CPU
W tym przykładzie jest dany komputer **wydajności** rejestruje i tworzy dwa wykresy czasu, średnie użycie procesora CPU i maksymalna ilość pamięci.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>Wykres wydajności procesora dla każdego komputera
W tym przykładzie jest obliczana i wykresy wykorzystanie procesora CPU, komputerów, które zaczyna się _Contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Stan ochrony

### <a name="computers-with-non-reporting-protection-status-duration"></a>Komputery z niezgłaszających ochrony czas trwania stanu
W tym przykładzie wyświetlana jest lista komputerów, których stan ochrony to _Not Reporting_, oraz informacja o tym, przez jaki czas komputery znajdowały się w tym stanie.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Zgodne z rekordami stan chronionych za pomocą rekordy pulsu
W tym przykładzie wyszukuje powiązane ochrony stanu rekordów i rekordy pulsu dopasowane na komputerze i czasu.
Wartość pola Czas jest zaokrąglana do najbliższej minuty przy użyciu **bin**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Rekordy zabezpieczeń

### <a name="count-security-events-by-activity-id"></a>Liczba zdarzeń zabezpieczeń za pomocą Identyfikatora działania


W tym przykładzie opiera się na strukturze stały **działania** kolumny: \<Identyfikator\>-\<nazwa\>.
Analizuje **działania** wartości do dwóch nowych kolumn i zlicza wystąpienia każdego **activityID**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Liczba zdarzeń zabezpieczeń powiązanych z uprawnieniami
Ten przykład pokazuje liczbę rekordów **securityEvent**, w których kolumna **Działania** zawiera całe wyrażenie _Permissions_. Kwerenda dotyczy rekordów utworzonych w ciągu ostatnich 30 minut.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Wyszukiwania kont, których nie można zalogować na komputerach za pomocą wykrywania zabezpieczeń
W tym przykładzie wyszukuje i liczby kont, które nie powiodło się, aby zalogować się z komputerów, na których zidentyfikować wykrywania zabezpieczeń.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Moje dane zabezpieczeń jest dostępne?
Uruchamianie danych często eksploracji rozpoczyna się od sprawdzenia dostępności danych. Ten przykład pokazuje liczbę **SecurityEvent** rekordów w ciągu ostatnich 30 minut.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Nazwa działania analizy i identyfikator
Poniższych dwóch przykładach opierają się na strukturze stały **działania** kolumny: \<Identyfikator\>-\<nazwa\>. W pierwszym przykładzie użyto **przeanalizować** operator przypisywania wartości do dwie nowe kolumny: **activityID** i **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

W tym przykładzie użyto **podziału** operatora do utworzenia tablicy osobne wartości
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Jawne poświadczenia procesów
Poniższy przykład przedstawia wykres kołowy procesy, które używały jawne poświadczenia w ostatnim tygodniu

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>TOP uruchomionego procesu

Poniższy przykład przedstawia oś czasu działań w przypadku 5 procesów najbardziej typowe ostatnich trzech dni.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Znajdź powtarzanie nieudanych prób logowania przez tego samego konta z różnych adresów IP

Poniższy przykład umożliwia znalezienie nieudanych prób logowania przy użyciu tego samego konta z więcej niż pięć różnych adresów IP w ciągu ostatnich sześciu godzin.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Znajdowania kont użytkowników, których nie udało się zalogować 
Poniższy przykład określa konta użytkowników, których nie można zalogować się w więcej niż pięć razy w ciągu ostatniego dnia, gdy ostatnia próba zalogować się.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Za pomocą **sprzężenia**, i **umożliwiają** instrukcji, można sprawdzić, jeśli te same konta podejrzane później były w stanie pomyślnie zalogować.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Sposób użycia

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>Oblicz średni rozmiar raporty użycia wydajności na komputerze

W tym przykładzie oblicza średni rozmiar raporty użycia wydajności na komputerze, w ciągu ostatnich 3 godzin.
Wyniki są wyświetlane na wykresie słupkowym.
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>Wykres czasu opóźnienia percentyle 50 i 95

Ten przykład oblicza i zgłaszanych w wykresach 50. i 95. percentyle **avgLatency** godzin w ciągu ostatnich 24 godzin.

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>Korzystanie z określonych komputerów już dziś
W tym przykładzie pobiera **użycia** danych z poprzedniego dnia dla nazwy komputera, który zawiera ciąg _ContosoFile_. Wyniki są sortowane według **TimeGenerated**.

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>Aktualizacje

### <a name="computers-still-missing-updates"></a>Komputery nadal brakujące aktualizacje
Ten przykład przedstawia listę komputerów, na których brakuje co najmniej jednej aktualizacji krytycznej kilka dni temu i nadal brakuje aktualizacji.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(3d)..ago(2d))
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| summarize makeset(Computer);

Update
| where TimeGenerated > ago(1d)
| where  Classification == "Critical Updates" and UpdateState != "Not needed" and UpdateState != "NotNeeded"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Kolejne kroki

- Zapoznaj się [Skorowidz języka Kusto](/azure/kusto/query) szczegółowe informacje na temat języka.
- Przeprowadzenie [lekcji o pisaniu zapytań dzienników w usłudze Azure Monitor](get-started-queries.md).
