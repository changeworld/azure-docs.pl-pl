---
title: wyrażenie app() w kwerendach dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: Wyrażenie aplikacji jest używane w kwerendzie dziennika usługi Azure Monitor do pobierania danych z określonej aplikacji usługi Application Insights w tej samej grupie zasobów, innej grupie zasobów lub innej subskrypcji.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2019
ms.openlocfilehash: 5502df1cd119c0f63c65945d73431a17282ebc0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670263"
---
# <a name="app-expression-in-azure-monitor-query"></a>wyrażenie app() w kwerendzie usługi Azure Monitor

Wyrażenie `app` jest używane w kwerendzie usługi Azure Monitor do pobierania danych z określonej aplikacji usługi Application Insights w tej samej grupie zasobów, innej grupie zasobów lub innej subskrypcji. Jest to przydatne do uwzględnienia danych aplikacji w kwerendzie dziennika usługi Azure Monitor i do zapytań danych w wielu aplikacjach w kwerendzie usługi Application Insights.



## <a name="syntax"></a>Składnia

`app(`*Identyfikator*`)`


## <a name="arguments"></a>Argumenty

- *Identyfikator:* Identyfikuje aplikację przy użyciu jednego z formatów w poniższej tabeli.

| Identyfikator | Opis | Przykład
|:---|:---|:---|
| Nazwa zasobu | Czytelna dla człowieka nazwa aplikacji (AKA "nazwa komponentu") | aplikacja("fabrikamapp") |
| Nazwa kwalifikowana | Pełna nazwa aplikacji w formularzu: "subscriptionName/resourceGroup/componentName" | ("AI-Prototype/Fabrikam/fabrikamapp") |
| ID | Identyfikator GUID aplikacji | ("988ba129-363e-4415-8fe7-8cbab5447518") |
| Identyfikator zasobu platformy Azure | Identyfikator zasobu platformy Azure |("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Uwagi

* Musisz mieć dostęp do odczytu do aplikacji.
* Identyfikowanie aplikacji według jej nazwy zakłada, że jest unikatowa we wszystkich dostępnych subskrypcji. Jeśli masz wiele aplikacji o określonej nazwie, kwerenda zakończy się niepowodzeniem z powodu niejednoznaczności. W takim przypadku należy użyć jednego z innych identyfikatorów.
* Użyj obszaru [roboczego](workspace-expression.md) wyrażenia pokrewne do wykonywania zapytań w obszarach roboczych usługi Log Analytics.
* Wyrażenie app() nie jest obecnie obsługiwane w kwerendzie wyszukiwania podczas korzystania z witryny Azure Portal do tworzenia [reguły alertu wyszukiwania dziennika niestandardowego](../platform/alerts-log.md), chyba że aplikacja usługi Application Insights jest używana jako zasób dla reguły alertu.

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

## <a name="next-steps"></a>Następne kroki

- Zobacz [wyrażenie obszaru roboczego,](workspace-expression.md) aby odwołać się do obszaru roboczego usługi Log Analytics.
- Przeczytaj o tym, jak dane [usługi Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) są przechowywane.
- Uzyskaj dostęp do pełnej dokumentacji [języka zapytań Kusto](/azure/kusto/query/).
