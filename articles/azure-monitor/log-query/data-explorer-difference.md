---
title: Różnice językowe zapytań dziennika usługi Azure Monitor | Dokumenty firmy Microsoft
description: Informacje referencyjne dotyczące języka zapytań Kusto używanego przez usługę Azure Monitor. Zawiera dodatkowe elementy specyficzne dla usługi Azure Monitor i elementy nie są obsługiwane w kwerendach dziennika usługi Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: 265179909c8ae4a6fa630b835bc9993f042d6460
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585710"
---
# <a name="azure-monitor-log-query-language-differences"></a>Różnice językowe języka zapytań dziennika usługi Azure Monitor

Podczas [gdy dzienniki w usłudze Azure Monitor](log-query-overview.md) jest zbudowany na Azure Data [Explorer](/azure/data-explorer) i używa tego samego języka [zapytania Kusto](/azure/kusto/query), wersja języka ma pewne różnice. W tym artykule identyfikowane elementy, które różnią się między wersją języka używanego dla Eksploratora danych i wersji używanej dla zapytań dziennika usługi Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Elementy KQL nie są obsługiwane w usłudze Azure Monitor
W poniższych sekcjach opisano elementy języka zapytań Kusto, które nie są obsługiwane przez usługę Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instrukcje nie są obsługiwane w usłudze Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Parametry zapytania](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funkcje nie są obsługiwane w usłudze Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [baza danych()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operatorzy nie są obsługiwani w usłudze Azure Monitor

* [Sprzężenie krzyżowe klastra](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Wtyczki nie są obsługiwane w usłudze Azure Monitor

* [Wtyczka Pythona](/azure/kusto/query/pythonplugin)
* [wtyczka sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Dodatkowi operatorzy w usłudze Azure Monitor
Następujący operatorzy obsługują określone funkcje usługi Azure Monitor i nie są dostępne poza usługą Azure Monitor.

* [app()](app-expression.md)
* [obszar roboczy()](workspace-expression.md)

## <a name="next-steps"></a>Następne kroki

- Uzyskaj odwołania do różnych [zasobów do pisania zapytań dziennika usługi Azure Monitor](query-language.md).
- Uzyskaj dostęp do pełnej [dokumentacji referencyjnej języka zapytań Kusto](/azure/kusto/query/).
