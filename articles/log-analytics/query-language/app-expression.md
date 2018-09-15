---
title: wyrażenie metodzie App() w zapytaniu usługi Azure Log Analytics | Dokumentacja firmy Microsoft
description: Wyrażenie aplikacji jest używany w zapytaniu usługi Log Analytics można pobrać danych z określonej aplikacji usługi Application Insights w tej samej grupie zasobów, innej grupy zasobów lub innej subskrypcji.
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
ms.openlocfilehash: b6e3311c378f648caeae72ff1f8aff123515226b
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45637436"
---
# <a name="app-expression-in-log-analytics-query"></a>wyrażenie metodzie App() zapytanie usługi Log Analytics

`app` Wyrażenie jest używane w zapytaniu usługi Log Analytics można pobrać danych z określonej aplikacji usługi Application Insights w tej samej grupie zasobów, innej grupy zasobów lub innej subskrypcji. Jest to przydatne do uwzględnienia w zapytaniu usługi Log Analytics i wykonywać zapytania względem danych dane aplikacji dla wielu aplikacji w zapytaniu usługi Application Insights.



## <a name="syntax"></a>Składnia

`app(`*Identyfikator*`)`


## <a name="arguments"></a>Argumenty

- *Identyfikator*: identyfikuje aplikację przy użyciu jednego z formatów w poniższej tabeli.

| Identyfikator | Opis | Przykład
|:---|:---|:---|
| Nazwa zasobu | Przez ludzi dla użytkownika nazwę aplikacji (zwane również "Nazwa składnika") | App("fabrikamapp") |
| Kwalifikowana nazwa | Pełna nazwa aplikacji w postaci: "componentName-subscriptionName/resourceGroup" | App('AI-prototype/Fabrikam/fabrikamapp') |
| ID | Identyfikator GUID aplikacji | App("988ba129-363e-4415-8fe7-8cbab5447518") |
| Identyfikator zasobu platformy Azure | Identyfikator zasobu platformy Azure |App("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/Providers/Microsoft.insights/Components/fabrikamapp") |


## <a name="notes"></a>Uwagi

* Musi mieć dostęp do odczytu do aplikacji.
* Identyfikowanie aplikacji za pomocą nazwy przyjęto założenie, że jest ona unikatowa we wszystkich subskrypcjach dostępne. Jeśli masz wiele aplikacji o określonej nazwie, zapytanie zakończy się niepowodzeniem z powodu niejednoznaczności. W takim przypadku należy użyć jednego z innych identyfikatorów.
* Użyj wyrażenia powiązane [obszaru roboczego](workspace-expression.md) do wykonywania zapytań w obszarach roboczych usługi Log Analytics.

## <a name="examples"></a>Przykłady

```
app("fabrikamapp").requests | count
```
```
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- Zobacz [wyrażenie obszaru roboczego](workspace-expression.md) do odwoływania się do obszaru roboczego usługi Log Analytics.
- Przeczytaj o tym, jak [danych usługi Log Analytics](../../log-analytics/log-analytics-log-search.md) są przechowywane.