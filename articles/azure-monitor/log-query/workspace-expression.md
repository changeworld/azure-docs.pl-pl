---
title: wyrażenie workspace() w kwerendzie dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: Wyrażenie obszaru roboczego jest używane w kwerendzie dziennika usługi Azure Monitor do pobierania danych z określonego obszaru roboczego w tej samej grupie zasobów, innej grupie zasobów lub innej subskrypcji.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 255888acf5da6149b6a964b23ed038b99715481c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364955"
---
# <a name="workspace-expression-in-azure-monitor-log-query"></a>wyrażenie workspace() w kwerendzie dziennika usługi Azure Monitor

Wyrażenie `workspace` jest używane w kwerendzie usługi Azure Monitor do pobierania danych z określonego obszaru roboczego w tej samej grupie zasobów, innej grupie zasobów lub innej subskrypcji. Jest to przydatne do uwzględnienia danych dziennika w kwerendzie usługi Application Insights i do kwerendy danych w wielu obszarach roboczych w kwerendzie dziennika.


## <a name="syntax"></a>Składnia

`workspace(`*Identyfikator*`)`

## <a name="arguments"></a>Argumenty

- *Identyfikator:* Identyfikuje obszar roboczy przy użyciu jednego z formatów w poniższej tabeli.

| Identyfikator | Opis | Przykład
|:---|:---|:---|
| Nazwa zasobu | Czytelna dla człowieka nazwa obszaru roboczego (AKA "nazwa składnika") | obszar roboczy("contosoretail") |
| Nazwa kwalifikowana | Pełna nazwa obszaru roboczego w formularzu: "subscriptionName/resourceGroup/componentName" | obszar roboczy("Contoso/ContosoResource/ContosoWorkspace") |
| ID | Identyfikator GUID obszaru roboczego | obszar roboczy("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Identyfikator zasobu platformy Azure | Identyfikator zasobu platformy Azure | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>Uwagi

* Musisz mieć dostęp do odczytu do obszaru roboczego.
* Powiązane wyrażenie `app` jest, który umożliwia wykonywanie zapytań w aplikacjach usługi Application Insights.

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

## <a name="next-steps"></a>Następne kroki

- Zobacz [wyrażenie aplikacji,](app-expression.md) aby odwołać się do aplikacji usługi Application Insights.
- Przeczytaj o tym, jak dane [usługi Azure Monitor](log-query-overview.md) są przechowywane.
- Uzyskaj dostęp do pełnej dokumentacji [języka zapytań Kusto](/azure/kusto/query/).
