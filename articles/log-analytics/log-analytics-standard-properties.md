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
ms.devlang: na
ms.topic: article
ms.date: 08/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 663f0b04c528c180e4130c1c157441cbc0ceb98b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955871"
---
# <a name="standard-properties-in-log-analytics-records"></a>Właściwości standardowe w rekordach usługi Log Analytics
Dane w [usługi Log Analytics](../log-analytics/log-analytics-queries.md) jest przechowywany jako zestaw rekordów, każdy z typem danych, który ma unikatowego zestawu właściwości. Wiele typów danych, ma standardowych właściwości, które są wspólne dla wielu typów. W tym artykule opisano te właściwości i przedstawiono przykłady jak ich używać w zapytaniach.

Niektóre z tych właściwości są nadal w trakcie zaimplementowana, może je wyświetlić, w niektórych typach danych, ale jeszcze nie w innych.


## <a name="resourceid"></a>_ResourceId
**_ResourceId** właściwość przechowuje unikatowy identyfikator zasobu, która jest skojarzony rekord. Dzięki temu standardowy właściwość użycie ustal zakres zapytania do tylko rekordy z określonego zasobu lub Dołącz do powiązanych danych dla wielu tabel.

Dla zasobów platformy Azure, wartość **_ResourceId** jest [zasobów platformy Azure, adres URL Identyfikatora](../azure-resource-manager/resource-group-template-functions-resource.md). Właściwość jest obecnie ograniczona do zasobów platformy Azure, ale będzie można rozszerzyć do zasobów spoza platformy Azure, takich jak komputery w środowisku lokalnym. 

### <a name="examples"></a>Przykłady
Poniższy kod przedstawia zapytanie powodujące sprzężenie wydajności i danych zdarzeń dla każdego komputera. Pokazuje wszystkie zdarzenia o identyfikatorze _101_ i użycie procesora przez ponad 50%.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

W poniższym przykładzie przedstawiono zapytanie powodujące sprzężenie _AzureActivity_ rekordy z _SecurityEvent_ rekordów. Pokazuje wszystkie operacje wykonywane działania użytkowników, które zostały zarejestrowane w na tych maszynach.

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

- Przeczytaj więcej na temat [są przechowywane dane usługi Log Analytics](../log-analytics/log-analytics-queries.md).
- Uzyskaj lekcji na [Pisanie zapytań w usłudze Log Analytics](../log-analytics/query-language/get-started-queries.md).
- Uzyskaj lekcji na [sprzężenie tabel w zapytań usługi Log Analytics](../log-analytics/query-language/joins.md).