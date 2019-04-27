---
title: metodzie App() wyrażenia w zapytaniach dzienników usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Wyrażenie aplikacji jest używany w zapytaniu dzienników usługi Azure Monitor do pobierania danych z określonej aplikacji usługi Application Insights w tej samej grupie zasobów, innej grupy zasobów lub innej subskrypcji.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/25/2019
ms.author: bwren
ms.openlocfilehash: a1a605bc733597430f64dceeb6c485db0abf657b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589247"
---
# <a name="app-expression-in-azure-monitor-query"></a>wyrażenie metodzie App() w zapytaniu usługi Azure Monitor

`app` Wyrażenie jest używane w zapytaniu usługi Azure Monitor do pobierania danych z określonej aplikacji usługi Application Insights w tej samej grupie zasobów, innej grupy zasobów lub innej subskrypcji. Jest to przydatne do uwzględnienia w zapytaniu dzienników usługi Azure Monitor i wykonywać zapytania względem danych dane aplikacji dla wielu aplikacji w zapytaniu usługi Application Insights.



## <a name="syntax"></a>Składnia

`app(`*Identyfikator*`)`


## <a name="arguments"></a>Argumenty

- *Identyfikator*: Identyfikuje aplikację za pomocą jednego z formatów w poniższej tabeli.

| Identyfikator | Opis | Przykład
|:---|:---|:---|
| Nazwa zasobu | Przez ludzi dla użytkownika nazwę aplikacji (zwane również "Nazwa składnika") | app("fabrikamapp") |
| Kwalifikowana nazwa | Pełna nazwa aplikacji w postaci: "componentName-subscriptionName/resourceGroup" | App('AI-prototype/Fabrikam/fabrikamapp') |
| ID | Identyfikator GUID aplikacji | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| Identyfikator zasobu platformy Azure | Identyfikator zasobu platformy Azure |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Uwagi

* Musi mieć dostęp do odczytu do aplikacji.
* Identyfikowanie aplikacji za pomocą nazwy przyjęto założenie, że jest ona unikatowa we wszystkich subskrypcjach dostępne. Jeśli masz wiele aplikacji o określonej nazwie, zapytanie zakończy się niepowodzeniem z powodu niejednoznaczności. W takim przypadku należy użyć jednego z innych identyfikatorów.
* Użyj wyrażenia powiązane [obszaru roboczego](workspace-expression.md) do wykonywania zapytań w obszarach roboczych usługi Log Analytics.
* Wyrażenie metodzie app() nie jest obecnie obsługiwane w zapytaniu wyszukiwania podczas używania witryny Azure portal do utworzenia [reguły alertów wyszukiwania dziennika niestandardowego](../platform/alerts-log.md), chyba że aplikacja usługi Application Insights jest używana jako zasób dla reguły alertu.

## <a name="examples"></a>Przykłady

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- Zobacz [wyrażenie obszaru roboczego](workspace-expression.md) do odwoływania się do obszaru roboczego usługi Log Analytics.
- Przeczytaj o tym, jak [danych usługi Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) są przechowywane.
- Dostęp do pełną dokumentację dotyczącą [język zapytania Kusto](/azure/kusto/query/).
