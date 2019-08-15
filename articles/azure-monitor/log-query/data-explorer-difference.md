---
title: Różnice w języku zapytań dziennika Azure Monitor | Microsoft Docs
description: Informacje referencyjne dotyczące języka zapytań Kusto używanego przez Azure Monitor. Zawiera dodatkowe elementy charakterystyczne dla Azure Monitor i elementy nieobsługiwane w zapytaniach dziennika Azure Monitor.
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
ms.date: 08/13/2019
ms.author: bwren
ms.openlocfilehash: 9892b8884d901033a80fae4168f25da97edb5a68
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990181"
---
# <a name="azure-monitor-log-query-language-differences"></a>Różnice w języku zapytań dziennika Azure Monitor

Podczas gdy [dzienniki w Azure monitor](log-query-overview.md) są oparte na [platformie Azure Eksplorator danych](/azure/data-explorer) i korzystają z tego samego [języka zapytań Kusto](/azure/kusto/query), wersja języka zawiera pewne różnice. Ten artykuł zawiera elementy, które różnią się w zależności od wersji języka używanej do Eksplorator danych i wersji używanej do Azure Monitor zapytań dzienników.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Elementy KQL nie są obsługiwane w Azure Monitor
W poniższych sekcjach opisano elementy języka zapytania Kusto, które nie są obsługiwane przez Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instrukcje nie są obsługiwane w Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Parametry zapytania](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funkcje nie są obsługiwane w Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operatory nie są obsługiwane w Azure Monitor

* [Cross-Cluster Join](/azure/kusto/query/joincrosscluster)
* [operator externaldata](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Wtyczki nie są obsługiwane w Azure Monitor

* [Wtyczka języka Python](/azure/kusto/query/pythonplugin)
* [sql_request plugin](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Dodatkowe operatory w Azure Monitor
Następujące operatory obsługują określone funkcje Azure Monitor i nie są dostępne poza programem Azure Monitor.

* [Aplikacja ()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Następne kroki

- Pobierz odwołania do różnych [zasobów na potrzeby zapisywania Azure monitor zapytań dzienników](query-language.md).
- Uzyskaj dostęp do kompletnej [dokumentacji referencyjnej dotyczącej języka zapytań Kusto](/azure/kusto/query/).
