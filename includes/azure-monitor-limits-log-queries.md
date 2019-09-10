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
ms.openlocfilehash: f007cf0d46d6cbee39a950b9784bbc9bde702ff5
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "69657833"
---
| Limit | Opis |
|:---|:---|
| Język zapytań | Azure Monitor używa tego samego [języka zapytań Kusto](/azure/kusto/query/) jako platformy Azure Eksplorator danych. Zobacz, [Azure monitor różnice w języku zapytań dziennika](../articles/azure-monitor/log-query/data-explorer-difference.md) dla elementów języka KQL nie są obsługiwane w programie Azure monitor. |
| Regiony platformy Azure | Zapytania dziennika mogą wystąpić nadmierne obciążenie, gdy dane zajmują Log Analytics obszary robocze w wielu regionach świadczenia usługi Azure. Szczegóły można znaleźć w temacie [limity zapytań](../articles/azure-monitor/log-query/scope.md#query-limits) . |
| Zapytania między zasobami | Maksymalna liczba zasobów Application Insights i Log Analytics obszarów roboczych w pojedynczym zapytaniu ograniczonym do 100.<br>Zapytanie krzyżowe nie jest obsługiwane w projektancie widoków.<br>Zapytanie między zasobami w ramach alertów dziennika jest obsługiwane w nowym interfejsie API scheduledQueryRules.<br>Aby uzyskać szczegółowe informacje, zobacz [limity zapytań między zasobami](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) . |