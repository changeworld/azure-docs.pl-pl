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
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795829"
---
| Limit | Opis |
|:---|:---|
| Język zapytań | Azure Monitor używa tego samego [języka zapytań Kusto](/azure/kusto/query/) jako platformy Azure Eksplorator danych. Zobacz, [Azure monitor różnice w języku zapytań dziennika](../articles/azure-monitor/log-query/data-explorer-difference.md) dla elementów języka KQL nie są obsługiwane w programie Azure monitor. |
| Regiony systemu Azure | Zapytania dziennika mogą wystąpić nadmierne obciążenie, gdy dane zajmują Log Analytics obszary robocze w wielu regionach świadczenia usługi Azure. Szczegóły można znaleźć w temacie [limity zapytań](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Zapytania między zasobami | Maksymalna liczba zasobów Application Insights i Log Analytics obszarów roboczych w pojedynczym zapytaniu ograniczonym do 100.<br>Zapytanie krzyżowe nie jest obsługiwane w projektancie widoków.<br>Zapytanie między zasobami w ramach alertów dziennika jest obsługiwane w nowym interfejsie API scheduledQueryRules.<br>Aby uzyskać szczegółowe informacje, zobacz [limity zapytań między zasobami](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |
| Ograniczanie zapytania | Użytkownik jest ograniczony do 200 zapytań na 30 sekund na dowolną liczbę obszarów roboczych. Ten limit dotyczy zapytań programistycznych lub zapytań inicjowanych przez części wizualizacji, takich jak pulpity nawigacyjne platformy Azure i strona podsumowania obszaru roboczego Log Analytics. |
