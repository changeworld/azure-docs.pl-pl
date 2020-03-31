---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795829"
---
| Limit | Opis |
|:---|:---|
| Język zapytań | Usługa Azure Monitor używa tego samego [języka zapytań Kusto](/azure/kusto/query/) co Eksplorator danych platformy Azure. Zobacz [różnice językowe zapytań dziennika usługi Azure Monitor](../articles/azure-monitor/log-query/data-explorer-difference.md) dla elementów języka KQL, które nie są obsługiwane w usłudze Azure Monitor. |
| Regiony świadczenia usługi Azure | Zapytania dziennika mogą wystąpić nadmierne obciążenie, gdy dane obejmuje obszarów roboczych usługi Log Analytics w wielu regionach platformy Azure. Szczegółowe informacje można znaleźć w [limitach kwerend.](../articles/azure-monitor/log-query/scope.md#query-limits) |
| Kwerendy między zasobami | Maksymalna liczba zasobów usługi Application Insights i obszarów roboczych usługi Log Analytics w jednej kwerendzie ograniczona do 100.<br>Kwerenda między zasobami nie jest obsługiwana w projektancie widoku.<br>Kwerenda między zasobami w alertach dziennika jest obsługiwana w nowym interfejsie API scheduledQueryRules.<br>Szczegółowe informacje można znaleźć w [limitach zapytań między zasobami.](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) |
| Ograniczanie kwerend | Użytkownik jest ograniczony do 200 zapytań na 30 sekund w dowolnej liczbie obszarów roboczych. Ten limit dotyczy zapytań programowych lub zapytań inicjowanych przez części wizualizacji, takie jak pulpity nawigacyjne platformy Azure i strona podsumowania obszaru roboczego usługi Log Analytics. |
