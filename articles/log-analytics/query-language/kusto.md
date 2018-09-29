---
title: Dokumentacja języka w usłudze Azure Monitor Log Analytics | Dokumentacja firmy Microsoft
description: Informacje referencyjne dotyczące języka Kusto używane przez usługę Log Analytics. Zawiera dodatkowe elementy specyficzne dla usługi Log Analytics i elementy nieobsługiwane w zapytaniach usługi Log Analytics.
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
ms.openlocfilehash: 5173790436a29fa9947346d711da1a2ddb32bf62
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451072"
---
# <a name="log-analytics-query-language-reference"></a>Dokumentacja języka zapytań analizy dziennika
[Dziennika zapytań analitycznych](../log-analytics-queries.md) skorzystaj z tego samego języka zapytań i używane przez aparat [Eksploratora danych usługi Azure](/azure/data-explorer/). Możesz uzyskać dostęp Skorowidz języka i inne szczegóły dotyczące języka z następującej lokalizacji: [Skorowidz języka Kusto](/azure/kusto/query)



## <a name="kusto-elements-not-support-in-log-analytics"></a>Elementy Kusto nie jest obsługiwane w usłudze Log Analytics
Podczas gdy zapytań usługi Log Analytics używają implementację Kusto, istnieją pewne elementy Kusto, które nie obsługuje zgodnie z opisem w poniższych sekcjach.

### <a name="statements-not-supported-in-log-analytics"></a>Instrukcje nie są obsługiwane w usłudze Log Analytics

* [Alias](/kusto/query/aliasstatement)
* [Parametry zapytania](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-log-analytics"></a>Funkcje nieobsługiwane w usłudze Log Analytics

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [Database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-log-analytics"></a>Operatory nie obsługiwane w usłudze Log Analytics

* [Sprzężenie krzyżowe klastra](/azure/kusto/query/joincrosscluster)
* [externaldata operator](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-log-analytics"></a>Dodatki nie są obsługiwane w usłudze Log Analytics

* [Wtyczka sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-log-analytics"></a>Dodatkowe operatory w usłudze Log Analytics
W celu obsługi określonych funkcji usługi Log Analytics, następujące dodatkowe operatory Kusto znajdują się na które nie są dostępne poza usługą Log Analytics. 

* [metodzie App()](app-expression.md)
* [Workspace()](workspace-expression.md)

## <a name="next-steps"></a>Kolejne kroki

- Przeczytaj o zapytaniach w [usługi Log Analytics](../log-analytics-queries.md).
- Przeprowadzenie lekcji na zapis na [zapytanie usługi Log Analytics](/log-analytics/query-language/get-started-queries.md).
- Dostęp do pełnego [dokumentacji dla Kusto](/azure/kusto/query/).