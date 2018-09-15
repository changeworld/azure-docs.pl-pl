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
ms.openlocfilehash: 13bf79c919eff220c1b6fbbc7247a44b2c424466
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45637527"
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

```
workspace("contosoretail").Update | count
```
```
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [wyrażenie aplikacji](workspace-expression.md) do odwoływania się do aplikacji usługi Application Insights.
- Przeczytaj o tym, jak [danych usługi Log Analytics](../../log-analytics/log-analytics-log-search.md) są przechowywane.