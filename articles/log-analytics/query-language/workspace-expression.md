---
title: wyrażenie Workspace() w zapytaniu usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: Wyrażenie obszar roboczy jest używany w zapytaniu usługi Log Analytics do pobierania danych z określonego obszaru roboczego w tej samej grupie zasobów, innej grupy zasobów lub innej subskrypcji.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 8b83650acfee638ae2a667cd0925fd91d44b80b2
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282700"
---
# <a name="workspace-expression-in-log-analytics-query"></a>wyrażenie Workspace() zapytanie usługi Log Analytics

`workspace` Wyrażenie jest używane w zapytaniu usługi Log Analytics można pobrać danych z określonego obszaru roboczego w tej samej grupie zasobów, innej grupy zasobów lub innej subskrypcji. Jest to przydatne dołączyć dane dziennika w zapytaniu usługi Application Insights i wykonywać zapytania względem danych w wielu obszarach roboczych w zapytaniu dziennika.


## <a name="syntax"></a>Składnia

`workspace(`*Identyfikator*`)`

## <a name="arguments"></a>Argumenty

- *Identyfikator*: identyfikuje obszaru roboczego przy użyciu jednego z formatów w poniższej tabeli.

| Identyfikator | Opis | Przykład
|:---|:---|:---|
| Nazwa zasobu | Ludzi czytelna nazwa obszaru roboczego (zwane również "Nazwa składnika") | Workspace("contosoretail") |
| Kwalifikowana nazwa | Pełna nazwa obszaru roboczego w postaci: "componentName-subscriptionName/resourceGroup" | Workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | Identyfikator GUID obszaru roboczego | Workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Identyfikator zasobu platformy Azure | Identyfikator zasobu platformy Azure | Workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/Providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Uwagi

* Musi mieć dostęp do odczytu do obszaru roboczego.
* Powiązane wyrażenie jest `app` umożliwiająca zapytania w aplikacjach usługi Application Insights.

## <a name="examples"></a>Przykłady

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [wyrażenie aplikacji](workspace-expression.md) do odwoływania się do aplikacji usługi Application Insights.
- Przeczytaj o tym, jak [danych usługi Log Analytics](../../log-analytics/log-analytics-queries.md) są przechowywane.