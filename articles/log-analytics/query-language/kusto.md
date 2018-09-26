---
title: Różnice języka usługi Azure Monitor Log Analytics i Kusto | Dokumentacja firmy Microsoft
description: W tym artykule opisano różnice między zapytań usługi Log Analytics i core Kusto języka.
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
ms.date: 09/25/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 109ffa6abb34dad6a00210a5c2c726bdfdde094f
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184836"
---
# <a name="log-analytics-and-kusto-language-differences"></a>Usługa log Analytics i Kusto różnice języka
[Zaloguj się zapytania usługi Analytics](../log-analytics-queries.md) są zapisywane z użyciem [języka Kusto](/azure/kusto/query). Istnieją jednak zgodnie z opisem w tym artykule pewne różnice standardowego języka i implementacji usługi Log Analytics.


## <a name="statements-not-supported-in-log-analytics"></a>Instrukcje nie są obsługiwane w usłudze Log Analytics
Poniższe instrukcje nie są obsługiwane w usłudze Log Analytics.

* [Alias](/kusto/query/aliasstatement)
* [Parametry zapytania](/azure/kusto/query/queryparametersstatement)

## <a name="functions-not-supported-in-log-analytics"></a>Funkcje nieobsługiwane w usłudze Log Analytics
Następujące funkcje nie są obsługiwane w usłudze Log Analytics.

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [Database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

## <a name="operators-not-supported-in-log-analytics"></a>Operatory nie obsługiwane w usłudze Log Analytics
Następujące operatory nie są obsługiwane w usłudze Log Analytics.

* [Sprzężenie krzyżowe klastra](/azure/kusto/query/joincrosscluster)
* [externaldata operator](/azure/kusto/query/externaldata-operator)

## <a name="plugins-not-supported-in-log-analytics"></a>Dodatki nie są obsługiwane w usłudze Log Analytics
Następujące wtyczki nie są obsługiwane w usłudze Log Analytics.
* [Wtyczka sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="log-analytics-specific-operators"></a>Log Analytics szczególnych operatorów
* [metodzie App()](app-expression.md)
* [Workspace()](workspace-expression.md)

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj o zapytaniach w [usługi Log Analytics](../log-analytics-queries.md).
- Przeprowadzenie lekcji na zapis na [zapytanie usługi Log Analytics](/log-analytics/query-language/get-started-queries.md).
- Dostęp do pełnego [dokumentacji dla Kusto](/azure/kusto/query/).