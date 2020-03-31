---
title: Przykłady zapytań dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: Przykłady zapytań dziennika w usłudze Azure Monitor przy użyciu języka zapytań Kusto.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2020
ms.openlocfilehash: 18cd74ac9298b7dd058de2b224f677ec0d8f2d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480287"
---
# <a name="azure-monitor-log-query-examples"></a>Przykłady zapytań dziennika usługi Azure Monitor
Ten artykuł zawiera różne przykłady [zapytań](log-query-overview.md) przy użyciu [języka zapytań Kusto](/azure/kusto/query/) do pobierania różnych typów danych dziennika z usługi Azure Monitor. Różne metody są używane do konsolidacji i analizowania danych, dzięki czemu można użyć tych przykładów do identyfikowania różnych strategii, które mogą być używane dla własnych wymagań.  

Szczegółowe informacje na temat różnych słów kluczowych używanych w tych przykładach można znaleźć w [języku Kusto.](https://docs.microsoft.com/azure/kusto/query/) Przejdź przez [lekcję na temat tworzenia zapytań,](get-started-queries.md) jeśli jesteś nowy w usłudze Azure Monitor.

## <a name="events"></a>Zdarzenia

### <a name="search-application-level-events-described-as-cryptographic"></a>Wyszukiwanie zdarzeń na poziomie aplikacji opisanych jako "Kryptograficzne"
W tym przykładzie wyszukuje w tabeli **Zdarzenia** rekordy, w których **EventLog** jest _Aplikacją_ i **RenderedDescription** zawiera _kryptograficzną_. Zawiera rekordy z ostatnich 24 godzin.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Wyszukiwanie zdarzeń związanych z odłączanym
Tabele wyszukiwania **Zdarzenia** i **zabezpieczeniaUjawniania** dla rekordów, które wspominają o _roztrzaśliwiania_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Puls

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Wykres tygodniowo na temat liczby komputerów wysyłających dane

W poniższym przykładzie przedstawiono liczbę różnych komputerów, które co tydzień wysyłały pulsy.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Znajdź przestarzałe komputery

Poniższy przykład znajduje komputery, które były aktywne w ciągu ostatniego dnia, ale nie wysyłały pulsów w ciągu ostatniej godziny.

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

### <a name="match-protected-status-records-with-heartbeat-records"></a>Dopasowywalaj chronione rekordy stanu do rekordów pulsu

W tym przykładzie znajduje się rekordy stanu ochrony pokrewne i rekordy pulsu, dopasowane zarówno na komputerze, jak i w czasie.
Należy zwrócić uwagę, że pole czasu jest zaokrąglane do najbliższej minuty. Do tego wykorzystaliśmy obliczenie `round_time=bin(TimeGenerated, 1m)`pojemnika wykonawczego: .

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Wskaźnik dostępności serwera

Oblicz wskaźnik dostępności serwera na podstawie rekordów pulsu. Dostępność jest zdefiniowana jako "co najmniej 1 bicie serca na godzinę".
Tak więc, jeśli serwer był dostępny 98 z 100 godzin, wskaźnik dostępności wynosi 98%.

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

### <a name="chart-the-record-count-per-table"></a>Wykres liczby rekordów na tabelę
Poniższy przykład zbiera wszystkie rekordy wszystkich tabel z ostatnich pięciu godzin i zlicza, ile rekordów było w każdej tabeli. Wyniki są wyświetlane w wykresie czasowym.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Zlicz wszystkie dzienniki zebrane w ciągu ostatniej godziny według typu
Poniższy przykład przeszukuje wszystko zgłoszone w ciągu ostatniej godziny i zlicza rekordy każdej tabeli według **typu**. Wyniki są wyświetlane na wykresie słupkowym.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Zliczanie rekordów diagnostyki platformy Azure według kategorii
W tym przykładzie zlicza się wszystkie rekordy diagnostyki platformy Azure dla każdej kategorii unikatowych.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Uzyskaj losowy rekord dla każdej kategorii unikatowej
W tym przykładzie pobiera jeden losowy rekord diagnostyki platformy Azure dla każdej kategorii unikatowych.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Uzyskaj najnowszy rekord dla kategorii
W tym przykładzie pobiera najnowszy rekord diagnostyki platformy Azure w każdej kategorii unikatowych.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Monitorowanie sieci

### <a name="computers-with-unhealthy-latency"></a>Komputery ze niezdrowym opóźnieniem
W tym przykładzie tworzy listę różnych komputerów ze złej opóźnienia.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Wydajność

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Dołączanie rekordów wydajności komputera w celu skorelowania pamięci i procesora
W tym przykładzie koreluje rekordy **perf** określonego komputera i tworzy dwa wykresy czasowe, średni procesor CPU i maksymalną pamięć.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
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

### <a name="perf-cpu-utilization-graph-per-computer"></a>Wykres wykorzystania procesora PERF na komputer
W tym przykładzie oblicza i wykresy wykorzystania procesora CPU komputerów, które zaczynają się od _Contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Stan ochrony

### <a name="computers-with-non-reporting-protection-status-duration"></a>Komputery z czasem trwania stanu ochrony bez raportowania
W tym przykładzie wymieniono komputery, które miały stan ochrony _Nie raportowania_ i czas ich trwania w tym stanie.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Dopasowywalaj chronione rekordy stanu do rekordów pulsu
W tym przykładzie znajduje się rekordy stanu ochrony pokrewne i rekordy pulsu dopasowane zarówno na komputerze, jak i w czasie.
Pole czasu jest zaokrąglane do najbliższej minuty przy użyciu **pojemnika**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Rekordy zabezpieczeń

### <a name="count-security-events-by-activity-id"></a>Zliczanie zdarzeń zabezpieczeń według identyfikatora działania


W tym przykładzie opiera się na \<stałej\>strukturze **działania** kolumny: Nazwa identyfikatora\>-\<.
Analizuje wartość **działania** na dwie nowe kolumny i zlicza wystąpienie każdego **identyfikatora działania.**

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Zliczanie zdarzeń zabezpieczeń związanych z uprawnieniami
W tym przykładzie pokazano liczbę rekordów **zabezpieczeńEvent,** w którym **kolumna Działanie** zawiera cały okres _Uprawnienia_. Kwerenda ma zastosowanie do rekordów utworzonych w ciągu ostatnich 30 minut.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Znajdowanie kont, których nie można zalogować się z komputerów z wykrywaniem zabezpieczeń
W tym przykładzie wyszukuje i zlicza konta, których nie udało się zalogować z komputerów, na których identyfikujemy wykrywanie zabezpieczeń.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Czy moje dane zabezpieczające są dostępne?
Rozpoczęcie eksploracji danych często rozpoczyna się od sprawdzenia dostępności danych. W tym przykładzie pokazano liczbę rekordów **SecurityEvent** w ciągu ostatnich 30 minut.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Analizuj nazwę działania i identyfikator
Dwa poniższe przykłady opierają się na stałej\>-\<strukturze\>kolumny **Działanie:** \<Nazwa identyfikatora . W pierwszym przykładzie **użyto** operatora analizy do przypisania wartości do dwóch nowych kolumn: **activityID** i **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

W tym przykładzie użyto operatora **podziału** do utworzenia tablicy oddzielnych wartości
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Jawne procesy poświadczeń
Poniższy przykład przedstawia wykres kołowy procesów, które używały jawnych poświadczeń w ostatnim tygodniu

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Najlepsze uruchomione procesy

W poniższym przykładzie przedstawiono wiersz czasu działania dla pięciu najczęstszych procesów w ciągu ostatnich trzech dni.

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


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Znajdowanie powtarzających się nieudanych prób logowania przez to samo konto z różnych adresów IP

Poniższy przykład znajduje nieudane próby logowania przez to samo konto z więcej niż pięciu różnych adresów IP w ciągu ostatnich sześciu godzin.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Znajdowanie kont użytkowników, których nie można się zalogować 
Poniższy przykład identyfikuje konta użytkowników, których nie można zalogować się więcej niż pięć razy w ciągu ostatniego dnia i kiedy ostatnio próbował się zalogować.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Za pomocą **join**, i **niech** wyciągi możemy sprawdzić, czy te same podejrzane konta były później w stanie zalogować się pomyślnie.

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

Typ `Usage` danych może służyć do śledzenia pogoń za pomocą woluminu danych według rozwiązania lub typu danych. Istnieją inne techniki badania pozyskiwania woluminów danych według [komputera](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#data-volume-by-computer) lub [subskrypcji platformy Azure, grupy zasobów lub zasobu.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#data-volume-by-azure-resource-resource-group-or-subscription)

#### <a name="data-volume-by-solution"></a>Ilość danych wg rozwiązania

Kwerenda używana do wyświetlania rozliczanego woluminu danych według rozwiązania w ciągu ostatniego miesiąca (z wyłączeniem ostatniego częściowego dnia) jest następująca:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Należy zauważyć, `where IsBillable = true` że klauzula odfiltrowuje typy danych z niektórych rozwiązań, dla których nie ma opłaty za pojące.  Również klauzula `TimeGenerated` z jest tylko w celu zapewnienia, że środowisko kwerendy w witrynie Azure portal będzie patrzeć wstecz poza domyślne 24 godzin. Podczas korzystania z użycia `StartTime` `EndTime` typu danych i reprezentują przedziały czasu, dla których są prezentowane wyniki. 

#### <a name="data-volume-by-type"></a>Objętość danych według typu

Można przejść do szczegółów, aby zobaczyć trendy danych według typu danych:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Lub aby zobaczyć tabelę według rozwiązania i wpisz dla ostatniego miesiąca,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by Solution, DataType
| sort by Solution asc, DataType asc
```

> [!NOTE]
> Niektóre pola typu dane użycia, podczas gdy nadal w schemacie, zostały przestarzałe i ich wartości nie są już wypełniane. Są to **pola komputer,** a także pola związane z pozyskiwaniam (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** i **AverageProcessingTimeMs**.

## <a name="updates"></a>Aktualizacje

### <a name="computers-still-missing-updates"></a>Komputery wciąż brakuje aktualizacji
W tym przykładzie pokazano listę komputerów, na których kilka dni temu brakowało jednej lub kilku aktualizacji krytycznych i nadal brakuje aktualizacji.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Następne kroki

- Szczegółowe informacje na temat języka można znaleźć w [języku Kusto.](/azure/kusto/query)
- Przejmij [lekcję pisania zapytań dziennika w usłudze Azure Monitor](get-started-queries.md).