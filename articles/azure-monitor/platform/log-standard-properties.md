---
title: Rekordów dziennika standardowe właściwości w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Opisuje właściwości, które są wspólne dla wielu typów danych w dziennikach w usłudze Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/20/2019
ms.author: bwren
ms.openlocfilehash: 4d7c1d9b59e802343f6d8fe258e8e4ac961bb2df
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061011"
---
# <a name="standard-properties-in-azure-monitor-log-records"></a>Standardowe właściwości w usłudze Azure Monitor rekordów dziennika
Dane z dzienników w usłudze Azure Monitor [przechowywane jako zbiór rekordów](../log-query/log-query-overview.md), każdy z typem danych, który ma unikatowego zestawu właściwości. Wiele typów danych, ma standardowych właściwości, które są wspólne dla wielu typów. W tym artykule opisano te właściwości i przedstawiono przykłady jak ich używać w zapytaniach.

Niektóre z tych właściwości są nadal w trakcie zaimplementowana, może je wyświetlić, w niektórych typach danych, ale jeszcze nie w innych.

## <a name="timegenerated"></a>TimeGenerated
**TimeGenerated** właściwość zawiera Data i godzina utworzenia rekordu. Zapewnia wspólnej właściwości na potrzeby filtrowania lub podsumowywanie według czasu. Po wybraniu zakres czasu dla widoku lub pulpitu nawigacyjnego w witrynie Azure portal, używa TimeGenerated do filtrowania wyników.

### <a name="examples"></a>Przykłady

Następujące zapytanie zwraca liczbę zdarzeń błędu utworzone dla każdego dnia w poprzednim tygodniu.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>Typ
**Typu** właściwość przechowuje nazwę tabeli, z którego pobrano rekord może również być uważane za typu rekordu. Ta właściwość jest przydatna w zapytaniach, które łączą rekordy z wielu tabel, takich jak implementacje używające `search` operator rozróżnienie między rekordami różnych typów. **$table** mogą być używane zamiast **typu** w jednych miejscach.

### <a name="examples"></a>Przykłady
Następujące zapytanie zwraca liczbę rekordów według typu zebrane w ciągu ostatniej godziny.

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_ResourceId
**\_ResourceId** właściwość przechowuje unikatowy identyfikator zasobu, który jest skojarzony rekord. Dzięki temu standardowy właściwość użycie ustal zakres zapytania do tylko rekordy z określonego zasobu lub Dołącz do powiązanych danych dla wielu tabel.

Dla zasobów platformy Azure, wartość **_ResourceId** jest [zasobów platformy Azure, adres URL Identyfikatora](../../azure-resource-manager/resource-group-template-functions-resource.md). Właściwość jest obecnie ograniczona do zasobów platformy Azure, ale będzie można rozszerzyć do zasobów spoza platformy Azure, takich jak komputery w środowisku lokalnym.

> [!NOTE]
> Niektóre typy danych mają już pola, które zawierają identyfikator zasobu platformy Azure lub części w co najmniej o takich jak identyfikator subskrypcji. Te pola są zachowywane dla zgodności z poprzednimi wersjami, zaleca się jej na potrzeby _ResourceId wykonywać korelację między, ponieważ będzie bardziej spójny.

### <a name="examples"></a>Przykłady
Następujące zapytanie łączy dane wydajności i zdarzeń dla każdego komputera. Pokazuje wszystkie zdarzenia o identyfikatorze _101_ i użycie procesora przez ponad 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Poniższe zapytanie sprzęga _AzureActivity_ rekordy z _SecurityEvent_ rekordów. Pokazuje wszystkie operacje wykonywane działania użytkowników, które zostały zarejestrowane w na tych maszynach.

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

Poniższe zapytanie analizuje **_ResourceId** i agregacje są rozliczane ilości danych na subskrypcję platformy Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last 
```

Te `union withsource = tt *` zapytania oszczędnie skanowania różnych typów danych są kosztowne do wykonania.

## <a name="isbillable"></a>\_IsBillable
**\_IsBillable** właściwość określa, czy odebrane dane są płatne. Dane za pomocą  **\_IsBillable** równa _false_ są zbierane za darmo i nie są rozliczane na koncie platformy Azure.

### <a name="examples"></a>Przykłady
Aby uzyskać listę komputerów, które wysyłają typy danych rozliczane, użyj następującego zapytania:

> [!NOTE]
> Korzystanie z zapytań za pomocą `union withsource = tt *` oszczędnie skanowania różnych typów danych są drogie do wykonania. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Może to rozszerzona, aby zwrócić liczbę komputerów, na godzinę, które wysyłają rozliczane typów danych:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="billedsize"></a>\_BilledSize
**\_BilledSize** właściwość określa rozmiar w bajtach, danych, które będzie rozliczane na koncie platformy Azure, jeśli  **\_IsBillable** ma wartość true.

### <a name="examples"></a>Przykłady
Aby wyświetlić rozmiar płatnych zdarzeń wprowadzanych na komputerze, użyj `_BilledSize` właściwość, która zapewnia rozmiar w bajtach:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Aby wyświetlić rozmiar płatnych zdarzeń pozyskanych w każdej subskrypcji, użyj następującego zapytania:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId | sort by Bytes nulls last 
```

Aby wyświetlić rozmiar płatnych zdarzeń pozyskanych dla grupy zasobów, użyj następującego zapytania:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Aby wyświetlić liczbę zdarzeń przetwarzanych na komputerze, użyj następującego zapytania:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Aby wyświetlić liczbę płatnych zdarzeń wprowadzanych na komputerze, użyj następującego zapytania: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Aby wyświetlić liczbę płatnych typami z określonego komputera, użyj następującego zapytania:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj więcej na temat [są przechowywane dane dziennika usługi Azure Monitor](../log-query/log-query-overview.md).
- Uzyskaj lekcji na [Pisanie zapytań log](../../azure-monitor/log-query/get-started-queries.md).
- Uzyskaj lekcji na [sprzężenie tabel w dzienniku zapytań](../../azure-monitor/log-query/joins.md).
