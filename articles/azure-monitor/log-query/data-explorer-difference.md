---
title: Różnice języka zapytań usługi Azure Monitor dziennika | Dokumentacja firmy Microsoft
description: Informacje referencyjne dotyczące języka zapytań Eksploratora danych używane przez usługi Azure Monitor. Zawiera dodatkowe elementy specyficzne dla usługi Azure Monitor i elementy nieobsługiwane w zapytaniach dzienników usługi Azure Monitor.
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
ms.date: 10/31/2018
ms.author: bwren
ms.openlocfilehash: 9c58796fa19ffb6d38582c809f7bb6ca948bd92c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003635"
---
# <a name="azure-monitor-log-query-language-differences"></a>Różnice języka zapytań usługi Azure Monitor dziennika

Gdy [dzienników w usłudze Azure Monitor](log-query-overview.md) jest oparta na [Eksploratora danych usługi Azure](/azure/data-explorer) i używa [sam język zapytań](/azure/kusto/query), wersję języka ma pewne różnice. W tym artykule identyfikuje elementy, które różnią się między wersją język używany do Eksploratora danych i wersję używaną dla usługi Azure Monitor dziennika zapytań.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="data-explorer-elements-not-supported-in-azure-monitor"></a>Elementy Eksploratora danych nie jest obsługiwane w usłudze Azure Monitor
W poniższych sekcjach opisano elementy języka zapytań Eksploratora danych, które nie są obsługiwane przez usługi Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instrukcje nie są obsługiwane w usłudze Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Parametry zapytania](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funkcje nieobsługiwane w usłudze Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operatory nie obsługiwane w usłudze Azure Monitor

* [Cross-Cluster Join](/azure/kusto/query/joincrosscluster)
* [externaldata operator](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Dodatki nie są obsługiwane w usłudze Azure Monitor

* [sql_request plugin](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Dodatkowe operatory w usłudze Azure Monitor
Następujące operatory obsługi określonych funkcji usługi Azure Monitor i nie są dostępne spoza usługi Azure Monitor.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Kolejne kroki

- Pobieranie odwołań do różnych [zasoby dotyczące pisania usługi Azure Monitor rejestrowania zapytań](query-language.md).
- Dostęp do pełnego [odwoływać się do dokumentacji języka zapytań w Eksploratorze danych](/azure/kusto/query/).
