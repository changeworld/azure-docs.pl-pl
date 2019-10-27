---
title: wyrażenie Resource () w kwerendzie dziennika Azure Monitor | Microsoft Docs
description: Wyrażenie zasobu jest używane w zasobie zorientowanym na zasoby Azure Monitor zapytania dziennika do pobierania danych z wielu zasobów.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 370b2547c9e726ab4f5ebc4dd732cc0bfa17f760
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933008"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>wyrażenie Resource () w zapytaniu dziennika Azure Monitor

Wyrażenie `resource` jest używane w zapytaniu Azure Monitor [objętym zakresem zasobu](scope.md#query-scope) w celu pobrania danych z innych zasobów. 


## <a name="syntax"></a>Składnia

*identyfikator* `resource(``)`

## <a name="arguments"></a>Argumenty

- *Identyfikator*: Identyfikator zasobu zasobu.

| Identyfikatora | Opis | Przykład
|:---|:---|:---|
| Zasób | Zawiera dane dla zasobu. | zasób ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/Providers/Microsoft.COMPUTE/virtualmachines/MyVM") |
| Grupa zasobów lub subskrypcja | Zawiera dane dotyczące zasobu i wszystkich zasobów, które zawiera.  | zasób ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup") |


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

- Aby uzyskać szczegółowe informacje na temat zakresu zapytania [, zobacz zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](scope.md) .
- Uzyskaj dostęp do pełnej dokumentacji dotyczącej [języka zapytań Kusto](/azure/kusto/query/).
