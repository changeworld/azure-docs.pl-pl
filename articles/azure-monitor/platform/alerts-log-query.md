---
title: Rejestrowania alertów zapytań w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Zawiera zalecenia dotyczące pisania wydajne zapytania dotyczące alertów dziennika w aktualizacji usługi Azure Monitor i procesu konwersji istniejących zapytań.
author: yossi-y
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: bwren
ms.subservice: alerts
ms.openlocfilehash: 429770b7651a93473c03f5e386d8f7b72692c161
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006102"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Jeśli kwerendy alertów dzienników w usłudze Azure Monitor
[Reguły na podstawie dzienników usługi Azure Monitor alertów](alerts-unified-log.md) uruchamiane w regularnych odstępach czasu, dlatego należy upewnić się, że są one zapisywane zminimalizować koszty i opóźnienia. Ten artykuł zawiera zalecenia dotyczące pisania wydajne zapytania dotyczące alertów dzienników i procesu konwersji istniejących zapytań. 

## <a name="types-of-log-queries"></a>Typy zapytań log
[Rejestrowania zapytań w usłudze Azure Monitor](../log-query/log-query-overview.md) rozpoczynać jedną tabelę lub [wyszukiwania](/azure/kusto/query/searchoperator) lub [Unii](/azure/kusto/query/unionoperator) operatora.

Na przykład poniższe zapytanie jest ograniczone do _SecurityEvent_ tabeli i wyszukuje identyfikator określonego zdarzenia. Jest to tylko tabelę, która musi przetworzyć zapytania.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Zapytania, które zaczyna się `search` lub `union` umożliwiają wyszukiwanie w wielu kolumnach tabeli lub nawet utworzyć wiele tabel. W poniższych przykładach pokazano kilka metod uzyskania termin wyszukiwania _pamięci_:

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Mimo że `search` i `union` są przydatne podczas eksploracji danych, wyszukiwanie warunków w modelu danych są mniej wydajne niż przy użyciu tabeli, ponieważ wymaga ich skanowania dla wielu tabel. Ponieważ zapytania w reguł alertów są uruchamiane w regularnych odstępach czasu, może to spowodować nadmierne obciążenie, dodając opóźnienie do alertu. Ze względu na to obciążenie zapytania dotyczące reguł alertów dzienników na platformie Azure zawsze należy zaczynać tabeli, aby zdefiniować zakres Wyczyść, co zwiększa wydajność zapytań i znaczenie wyników.

## <a name="unsupported-queries"></a>Nieobsługiwana zapytań
Od stycznia 11,2019, tworzenie lub modyfikowanie reguł alertów dzienników, które używają `search`, lub `union` operatorów nie jest obsługiwana w witrynie Azure portal. Korzystanie z tych operatorów w regule alertu zwróci komunikat o błędzie. Ta zmiana nie dotyczy istniejących reguł alertów i reguły alertów utworzone i edytować za pomocą interfejsu API programu Log Analytics. Nadal zastanów się, że zmiana dowolny alert, reguły używające tego rodzaju zapytania, jednak aby zwiększyć ich wydajność.  

Zaloguj się przy użyciu reguł alertów [zapytania obejmujące wiele zasobów](../log-query/cross-workspace-query.md) nie jest narażony na tę zmianę, ponieważ używa zapytania obejmujące wiele zasobów `union`, co ogranicza zakres kwerendy do określonych zasobów. Nie jest to równoważne z `union *` nie mogą być użyte.  Poniższy przykład są prawidłowymi reguł alertów dzienników:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[Zapytania obejmujące wiele zasobów](../log-query/cross-workspace-query.md) w dzienniku alertów jest obsługiwana w nowym [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Domyślnie używa usługi Azure Monitor [interfejsu API starszych Log Analytics alertu](api-alerts.md) do tworzenia reguł alertów nowy dziennik z witryny Azure portal, jeśli nie możesz przejść z wersji [starszej wersji interfejsu API z alertów dziennika](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). Po przełączniku nowy interfejs API staje się domyślnie na nowe reguły alertów w witrynie Azure portal i umożliwia tworzenie dziennika alertów reguły zapytania obejmujące wiele zasobów. Można utworzyć [zapytania obejmujące wiele zasobów](../log-query/cross-workspace-query.md) rejestrowanie reguł alertów bez wprowadzania przełącznik przy użyciu [szablon ARM scheduledQueryRules interfejsu API](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) — ale tę regułę alertu jest łatwe w zarządzaniu jednak [ Interfejs API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , a nie z witryny Azure portal.

## <a name="examples"></a>Przykłady
Poniższe przykłady zapytań log, które używają `search` i `union` i zawierają opis etapów, można użyć, aby zmodyfikować te zapytania do użycia z reguł alertów.

### <a name="example-1"></a>Przykład 1
Aby utworzyć regułę alertu dziennika za pomocą następującego zapytania, który pobiera informacje wydajności przy użyciu `search`: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Aby zmodyfikować to zapytanie, uruchom przy użyciu następującej kwerendy do identyfikowania tabelę, która właściwości należą do:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Wynik tego zapytania będą wyświetlane, _CounterName_ właściwość pochodzi z _wydajności_ tabeli. 

Ten wynik może być użyty do utworzenia następującego zapytania, który zostanie wykorzystany dla reguły alertu:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Przykład 2
Aby utworzyć regułę alertu dziennika za pomocą następującego zapytania, który pobiera informacje wydajności przy użyciu `search`: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Aby zmodyfikować to zapytanie, uruchom przy użyciu następującej kwerendy do identyfikowania tabelę, która właściwości należą do:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Wynik tego zapytania będą wyświetlane, _ObjectName_ i _CounterName_ właściwość pochodzi z _wydajności_ tabeli. 

Ten wynik może być użyty do utworzenia następującego zapytania, który zostanie wykorzystany dla reguły alertu:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Przykład 3

Aby utworzyć regułę alertu dziennika za pomocą następującego zapytania, który używa zarówno `search` i `union` można pobrać informacji o wydajności: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Aby zmodyfikować to zapytanie, uruchom przy użyciu następującej kwerendy do identyfikowania tabelę, która właściwość w pierwszej części zapytania należy do: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Wynik tego zapytania będą wyświetlane, że te właściwości pochodzi od _wydajności_ tabeli. 

Teraz za pomocą `union` z `withsource` polecenie, aby zidentyfikować tabeli źródłowej, która współtworzonej każdego wiersza.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

Wynik tego zapytania będą wyświetlane, że te właściwości również pochodzi _wydajności_ tabeli. 

Wyniki te można użyć do utworzenia następującego zapytania, który zostanie wykorzystany dla reguły alertu: 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>Przykład 4
Aby utworzyć regułę alertu dziennika za pomocą następującego zapytania, która łączy wyniki dwóch `search` zapytania:

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```
 

Aby zmodyfikować zapytanie, uruchom przy użyciu następującej kwerendy do identyfikowania tabelę, która zawiera właściwości do lewej strony sprzężenia: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Wynik wskazuje, że właściwości po lewej stronie sprzężenia należą do _SecurityEvent_ tabeli. 

Teraz Użyj następującego zapytania, aby zidentyfikować tabeli, która zawiera właściwości po prawej stronie sprzężenia: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Wynik wskazuje, że właściwości po prawej stronie sprzężenia należą do tabeli pulsu. 

Wyniki te można użyć do utworzenia następującego zapytania, który zostanie wykorzystany dla reguły alertu: 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [alerty dzienników](alerts-log.md) w usłudze Azure Monitor.
- Dowiedz się więcej o [rejestrowania zapytań](../log-query/log-query-overview.md).

