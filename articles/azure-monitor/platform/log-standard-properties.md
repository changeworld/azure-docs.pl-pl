---
title: Właściwości standardowe w dokumentacji usługi Azure Monitor Log Analytics | Dokumentacja firmy Microsoft
description: Opisuje właściwości, które są wspólne dla wielu typów danych w usłudze Azure Monitor Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: e4e921223676f4b5d64025c67914fc8b7c29a6fe
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52974976"
---
# <a name="standard-properties-in-log-analytics-records"></a>Właściwości standardowe w rekordach usługi Log Analytics
Dane w [usługi Log Analytics](../log-query/log-query-overview.md) jest przechowywany jako zestaw rekordów, każdy z typem danych, który ma unikatowego zestawu właściwości. Wiele typów danych, ma standardowych właściwości, które są wspólne dla wielu typów. W tym artykule opisano te właściwości i przedstawiono przykłady jak ich używać w zapytaniach.

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

## <a name="resourceid"></a>\_Identyfikator zasobu
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

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj więcej na temat [są przechowywane dane usługi Log Analytics](../log-query/log-query-overview.md).
- Uzyskaj lekcji na [Pisanie zapytań w usłudze Log Analytics](../../azure-monitor/log-query/get-started-queries.md).
- Uzyskaj lekcji na [sprzężenie tabel w zapytań usługi Log Analytics](../../azure-monitor/log-query/joins.md).
