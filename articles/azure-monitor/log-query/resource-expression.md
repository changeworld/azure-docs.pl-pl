---
title: wyrażenie resource() w kwerendzie dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: Wyrażenie zasobu jest używane w kwerendzie dziennika usługi Azure Monitor zorientowanej na zasoby do pobierania danych z wielu zasobów.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665703"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>wyrażenie resource() w kwerendzie dziennika usługi Azure Monitor

Wyrażenie `resource` jest używane w zapytaniu usługi Azure Monitor [o zakresie do zasobu](scope.md#query-scope) w celu pobrania danych z innych zasobów. 


## <a name="syntax"></a>Składnia

`resource(`*Identyfikator*`)`

## <a name="arguments"></a>Argumenty

- *Identyfikator:* Identyfikator zasobu.

| Identyfikator | Opis | Przykład
|:---|:---|:---|
| Zasób | Zawiera dane dla zasobu. | ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| Grupa zasobów lub subskrypcja | Zawiera dane dla zasobu i wszystkie zasoby, które zawiera.  | ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Uwagi

* Musisz mieć dostęp do odczytu do zasobu.


## <a name="examples"></a>Przykłady

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>Następne kroki

- Zobacz [zakres zapytań dziennika i zakres czasu w usłudze Azure Monitor Log Analytics, aby](scope.md) uzyskać szczegółowe informacje na temat zakresu kwerendy.
- Uzyskaj dostęp do pełnej dokumentacji [języka zapytań Kusto](/azure/kusto/query/).
