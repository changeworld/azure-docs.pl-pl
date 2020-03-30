---
title: Rejestrowanie zapytań alertów w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Zawiera zalecenia dotyczące pisania wydajnych zapytań o alerty dziennika w aktualizacjach usługi Azure Monitor i proces konwertowania istniejących zapytań.
author: yossi-y
ms.author: yossiy
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: alerts
ms.openlocfilehash: fdf492b8f103e725046b9b1cbbd079c4d249664a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667792"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Rejestrowanie zapytań alertów w usłudze Azure Monitor
[Reguły alertów oparte na dziennikach usługi Azure Monitor](alerts-unified-log.md) są uruchamiane w regularnych odstępach czasu, dlatego należy upewnić się, że są one zapisywane w celu zminimalizowania narzutów i opóźnień. Ten artykuł zawiera zalecenia dotyczące pisania wydajnych zapytań dla alertów dziennika i procesu konwertowania istniejących zapytań. 

## <a name="types-of-log-queries"></a>Typy zapytań dziennika
[Zapytania dziennika w usłudze Azure Monitor](../log-query/log-query-overview.md) rozpoczynają się od tabeli lub [operatora wyszukiwania](/azure/kusto/query/searchoperator) lub [unii.](/azure/kusto/query/unionoperator)

Na przykład następująca kwerenda jest objęta zakresem tabeli _SecurityEvent_ i wyszukuje określony identyfikator zdarzenia. Jest to jedyna tabela, którą musi przetworzyć kwerenda.

``` Kusto
SecurityEvent | where EventID == 4624 
```

Kwerendy rozpoczynające `search` `union` się od wielu kolumn w tabeli lub nawet w wielu tabelach lub umożliwiają wyszukiwanie w wielu kolumnach. Poniższe przykłady przedstawiają wiele metod wyszukiwania terminu _Pamięć:_

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

Chociaż `search` `union` i są przydatne podczas eksploracji danych, wyszukiwanie terminów w całym modelu danych, są one mniej wydajne niż przy użyciu tabeli, ponieważ muszą skanować w wielu tabelach. Ponieważ kwerendy w reguły alertów są uruchamiane w regularnych odstępach czasu, może to spowodować nadmierne obciążenie dodając opóźnienie do alertu. Z powodu tego obciążenia kwerendy dotyczące reguł alertów dziennika na platformie Azure należy zawsze rozpocząć od tabeli, aby zdefiniować jasny zakres, co poprawia wydajność kwerendy i trafność wyników.

## <a name="unsupported-queries"></a>Nieobsługiwały kwerendy
Od 11 stycznia 2019 r. tworzenie lub modyfikowanie reguł alertów dziennika, które używają `search`lub `union` operatorów, nie będą obsługiwane w witrynie Azure portal. Za pomocą tych operatorów w regule alertu zwróci komunikat o błędzie. Ta zmiana nie ma wpływu na istniejące reguły alertów i reguły alertów utworzone i edytowane za pomocą interfejsu API usługi Log Analytics. Należy jednak rozważyć zmianę wszystkich reguł alertów, które używają tych typów zapytań, aby zwiększyć ich wydajność.  

Ta zmiana nie ma wpływu na reguły alertów dziennika przy użyciu `union`kwerend między [zasobami,](../log-query/cross-workspace-query.md) ponieważ używane są kwerendy między zasobami , co ogranicza zakres kwerendy do określonych zasobów. Nie jest to `union *` równoważne, z których nie można użyć.  Poniższy przykład może być prawidłowy w regule alertu dziennika:

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>[Kwerenda między zasobami](../log-query/cross-workspace-query.md) w alertach dziennika jest obsługiwana w nowym [interfejsie API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Domyślnie usługa Azure Monitor używa [starszego interfejsu API alertów usługi Log Analytics](api-alerts.md) do tworzenia nowych reguł alertów dziennika z witryny Azure Portal, chyba że przełączysz się ze [starszego interfejsu API alertów dziennikowych.](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) Po przełączeniu nowy interfejs API staje się domyślny dla nowych reguł alertów w witrynie Azure Portal i umożliwia tworzenie reguł alertów dziennika zapytań między zasobami. Można utworzyć reguły alertów dziennika [zapytań między zasobami](../log-query/cross-workspace-query.md) bez wprowadzania przełącznika przy użyciu [szablonu ARM dla scheduledQueryRules API](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) — ale ta reguła alertu jest zarządzana, choć [scheduledQueryRules API,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) a nie z witryny Azure portal.

## <a name="examples"></a>Przykłady
Poniższe przykłady obejmują kwerendy `search` `union` dziennika, które używają i zapewniają kroki, których można użyć do modyfikowania tych kwerend do użycia z regułami alertów.

### <a name="example-1"></a>Przykład 1
Chcesz utworzyć regułę alertu dziennika przy użyciu następującej `search`kwerendy, która pobiera informacje o wydajności przy użyciu: 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

Aby zmodyfikować tę kwerendę, należy rozpocząć od użycia następującej kwerendy w celu zidentyfikowania tabeli, do której należą właściwości:

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

Wynik tej kwerendy pokaże, że _Właściwość CounterName_ pochodzi z tabeli _Perf._ 

Ten wynik służy do tworzenia następującej kwerendy, która służy do reguły alertu:

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>Przykład 2
Chcesz utworzyć regułę alertu dziennika przy użyciu następującej `search`kwerendy, która pobiera informacje o wydajności przy użyciu: 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

Aby zmodyfikować tę kwerendę, należy rozpocząć od użycia następującej kwerendy w celu zidentyfikowania tabeli, do której należą właściwości:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

Wynik tej kwerendy pokaże, że _ObjectName_ i _CounterName_ właściwość pochodzi z _tabeli Perf._ 

Ten wynik służy do tworzenia następującej kwerendy, która służy do reguły alertu:

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>Przykład 3

Chcesz utworzyć regułę alertu dziennika przy użyciu `search` następującej kwerendy, która używa obu i `union` do pobierania informacji o wydajności: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

Aby zmodyfikować tę kwerendę, należy rozpocząć od użycia następującej kwerendy w celu zidentyfikowania tabeli, do której należą właściwości w pierwszej części kwerendy: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

Wynik tej kwerendy pokaże, że wszystkie te właściwości pochodzą z _tabeli Perf._ 

Teraz `union` użyj `withsource` polecenia, aby zidentyfikować tabelę źródłą, która przyczyniła się do każdego wiersza.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

Wynik tej kwerendy pokaże, że te właściwości również pochodzi z _tabeli Perf._ 

Za pomocą tych wyników można utworzyć następującą kwerendę, która będzie używana dla reguły alertu: 

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
Chcesz utworzyć regułę alertu dziennika przy użyciu następującej `search` kwerendy, która łączy wyniki dwóch kwerend:

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
 

Aby zmodyfikować kwerendę, należy rozpocząć od użycia następującej kwerendy w celu zidentyfikowania tabeli zawierającej właściwości po lewej stronie sprzężenia: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

Wynik wskazuje, że właściwości po lewej stronie sprzężenia należą do tabeli _SecurityEvent._ 

Teraz użyj następującej kwerendy, aby zidentyfikować tabelę zawierającą właściwości po prawej stronie sprzężenia: 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
Wynik wskazuje, że właściwości po prawej stronie sprzężenia należą do tabeli Pulsu. 

Za pomocą tych wyników można utworzyć następującą kwerendę, która będzie używana dla reguły alertu: 


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

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [alertach dziennika](alerts-log.md) w usłudze Azure Monitor.
- Dowiedz się więcej o [zapytaniach dziennika](../log-query/log-query-overview.md).

