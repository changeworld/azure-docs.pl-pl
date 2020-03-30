---
title: Właściwości standardowe w rekordach dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano właściwości, które są wspólne dla wielu typów danych w dziennikach usługi Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 252ddeb372744986df0b8ba9b742d0462a4e8202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274478"
---
# <a name="standard-properties-in-azure-monitor-logs"></a>Właściwości standardowe w dziennikach monitora platformy Azure
Dane w dziennikach usługi Azure Monitor są [przechowywane jako zestaw rekordów w obszarze roboczym usługi Log Analytics lub aplikacji usługi Application Insights](../log-query/logs-structure.md), każdy z określonym typem danych, który ma unikatowy zestaw właściwości. Wiele typów danych będzie miało standardowe właściwości, które są wspólne dla wielu typów. W tym artykule opisano te właściwości i przedstawiono przykłady, jak można ich używać w kwerendach.

> [!NOTE]
> Niektóre standardowe właściwości nie będą wyświetlane w widoku schematu lub intellisense w usłudze Log Analytics i nie będą wyświetlane w wynikach kwerendy, chyba że jawnie określisz właściwość w danych wyjściowych.

## <a name="timegenerated-and-timestamp"></a>CzasGenerowany i sygnatura czasowa
Właściwości **TimeGenerated** (Log Analytics workspace) i **sygnatury czasowej** (application Insights) zawierają datę i godzinę utworzenia rekordu przez źródło danych. Aby uzyskać więcej informacji, zobacz [Rejestrowanie czasu pozyskiwania danych w usłudze Azure Monitor.](data-ingestion-time.md)

**CzasGenerowany** i **sygnatura czasowa** zapewniają wspólną właściwość do filtrowania lub podsumowania według czasu. Po wybraniu zakresu czasu dla widoku lub pulpitu nawigacyjnego w witrynie Azure portal, używa TimeGenerated lub sygnatury czasowej do filtrowania wyników. 

### <a name="examples"></a>Przykłady

Następująca kwerenda zwraca liczbę zdarzeń błędów utworzonych dla każdego dnia w poprzednim tygodniu.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

Następująca kwerenda zwraca liczbę wyjątków utworzonych dla każdego dnia w poprzednim tygodniu.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_CzasReceived
** \_Właściwość TimeReceived** zawiera datę i godzinę odebranie rekordu przez punkt pozyskiwania usługi Azure Monitor w chmurze platformy Azure. Może to być przydatne do identyfikowania problemów z opóźnieniami między źródłem danych a chmurą. Przykładem może być problem z siecią powodujący opóźnienie z danymi wysyłanymi z agenta. Aby uzyskać więcej informacji, zobacz [Rejestrowanie czasu pozyskiwania danych w usłudze Azure Monitor.](data-ingestion-time.md)

Poniższa kwerenda daje średnie opóźnienie według godziny dla rekordów zdarzeń od agenta. Obejmuje to czas od agenta do chmury i całkowity czas dla rekordu, który ma być dostępny dla zapytań dziennika.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Typ i typ elementu
Właściwości **Type** (Log Analytics workspace) i **itemType** (Application Insights application) przechowują nazwę tabeli, z której pobrano rekord, z której można również traktować jako typ rekordu. Ta właściwość jest przydatna w kwerendach, które łączą rekordy `search` z wielu tabel, takich jak te, które używają operatora, do rozróżniania rekordów różnych typów. **$table** mogą być używane w miejsce **Typu** w niektórych miejscach.

### <a name="examples"></a>Przykłady
Następująca kwerenda zwraca liczbę rekordów według typu zebranych w ciągu ostatniej godziny.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_Itemid
** \_Właściwość ItemId** zawiera unikatowy identyfikator rekordu.


## <a name="_resourceid"></a>\_ResourceId
** \_Właściwość ResourceId** zawiera unikatowy identyfikator zasobu, z który jest skojarzony rekord. Daje to standardową właściwość do użycia do zakresu kwerendy tylko rekordy z określonego zasobu lub do łączyć powiązanych danych w wielu tabelach.

W przypadku zasobów platformy Azure wartością **_ResourceId** jest [adres URL identyfikatora zasobu platformy Azure](../../azure-resource-manager/templates/template-functions-resource.md). Właściwość jest obecnie ograniczona do zasobów platformy Azure, ale zostanie rozszerzona na zasoby poza platformą Azure, takie jak komputery lokalne.

> [!NOTE]
> Niektóre typy danych mają już pola, które zawierają identyfikator zasobu platformy Azure lub przynajmniej jego części, takie jak identyfikator subskrypcji. Chociaż te pola są przechowywane w celu zapewnienia zgodności z powrotem, zaleca się użycie _ResourceId do wykonywania korelacji krzyżowej, ponieważ będzie bardziej spójne.

### <a name="examples"></a>Przykłady
Poniższa kwerenda łączy dane wydajności i zdarzeń dla każdego komputera. Pokazuje wszystkie zdarzenia o identyfikatorze _101_ i wykorzystanie procesora ponad 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Następująca kwerenda łączy rekordy _usługi AzureActivity_ z rekordami _SecurityEvent._ Pokazuje wszystkie operacje działania z użytkownikami, którzy byli zalogowani do tych maszyn.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

Poniższa kwerenda analizuje **_ResourceId** i agreguje rozliczane woluminy danych na subskrypcję platformy Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Użyj `union withsource = tt *` tych kwerend oszczędnie, ponieważ skanowanie w różnych typach danych są kosztowne do wykonania.

## <a name="_isbillable"></a>\_IsBillable (Możliwe jest rozliczanie się)
Właściwość IsBillable określa, czy pogoń za danymi jest rozliczalna. ** \_** Dane z ** \_IsBillable** równe _false_ są zbierane za darmo i nie rozliczane na koncie platformy Azure.

### <a name="examples"></a>Przykłady
Aby uzyskać listę komputerów wysyłających typy danych z rozliczeniami, użyj następującej kwerendy:

> [!NOTE]
> Użyj kwerend `union withsource = tt *` z oszczędnie, ponieważ skanowanie w różnych typach danych są kosztowne do wykonania. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Można to rozszerzyć, aby zwrócić liczbę komputerów na godzinę, które wysyłają typy danych naliczonych naliczania:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_Rozliczony rozmiar
** \_Właściwość BilledSize** określa rozmiar w bajtach danych, które będą rozliczane na koncie platformy Azure, jeśli ** \_jest spełniony.**


### <a name="examples"></a>Przykłady
Aby zobaczyć rozmiar zdarzeń podlegających rozliczaniu po spożyciu na komputerze, użyj `_BilledSize` właściwości, która zapewnia rozmiar w bajtach:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Aby wyświetlić rozmiar zdarzeń podlegających rozliczaniu przynajmowanych na subskrypcję, użyj następującej kwerendy:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Aby wyświetlić rozmiar zdarzeń rozliczanych przynajmowanych według grupy zasobów, użyj następującej kwerendy:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Aby wyświetlić liczbę zdarzeń przynajmowanych na komputer, użyj następującej kwerendy:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Aby wyświetlić liczbę zdarzeń rozliczanych na komputerze, należy użyć następującej kwerendy: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Aby wyświetlić liczbę typów danych podlegaalnych z rozliczeń z określonego komputera, użyj następującej kwerendy:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [sposobie przechowywania danych dziennika usługi Azure Monitor.](../log-query/log-query-overview.md)
- Pobierz lekcję na [temat pisania zapytań dziennika](../../azure-monitor/log-query/get-started-queries.md).
- Pobierz lekcję na [temat łączenia tabel w zapytaniach dziennika](../../azure-monitor/log-query/joins.md).
