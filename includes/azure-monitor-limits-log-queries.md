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
ms.openlocfilehash: ed840352096f1a1739bc8f655be42096456d3c33
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405677"
---
| Limit | Opis |
|:---|:---|
| Język zapytań | Azure Monitor używa tego samego [języka zapytań Kusto](/azure/kusto/query/) jako platformy Azure Eksplorator danych. Zobacz, [Azure monitor różnice w języku zapytań dziennika](../articles/azure-monitor/log-query/data-explorer-difference.md) dla elementów języka KQL nie są obsługiwane w programie Azure monitor. |
| Regiony platformy Azure | Zapytania dziennika mogą wystąpić nadmierne obciążenie, gdy dane zajmują Log Analytics obszary robocze w wielu regionach świadczenia usługi Azure. Szczegóły można znaleźć w temacie [limity zapytań](../articles/azure-monitor/log-query/scope.md#query-limits) . |
