---
title: Zrozumienie języka zapytań
description: W tym artykule opisano dostępne operatory Kusto i funkcje, które można używać z wykresem zasobów platformy Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 08e4f09665a3501073f55b7f5b82bf51cf508ea9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276681"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Zrozumienie języka zapytań wykres zasobów platformy Azure

Język zapytań dla wykresu zasobów platformy Azure obsługuje szereg operatorów i funkcji. Każda pracy i działania na podstawie [Eksploratora danych usługi Azure](../../../data-explorer/data-explorer-overview.md).

Najlepszym sposobem poznania językiem zapytań używanym przez usługę Graph zasobu ma rozpoczynać się w dokumentacji dotyczącej Eksplorator danych usługi Azure [język zapytań](/azure/kusto/query/index). Zawiera opis o struktury języka i jak różne obsługiwane operatory i funkcje współpracują ze sobą.

## <a name="supported-tabular-operators"></a>Obsługiwane operatory tabelarycznych

Poniżej przedstawiono listę obsługiwanych operatorów tabelarycznych w wykresie zasobów:

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [Rozszerzanie](/azure/kusto/query/extendoperator)
- [Limit](/azure/kusto/query/limitoperator)
- [kolejność według](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [Sortuj według](/azure/kusto/query/sortoperator)
- [Podsumowanie](/azure/kusto/query/summarizeoperator)
- [Wypełnij](/azure/kusto/query/takeoperator)
- [Do góry](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [TOP hitters](/azure/kusto/query/tophittersoperator)
- [gdzie](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Obsługiwane funkcje

Poniżej przedstawiono listę obsługiwanych funkcji w programie Graph zasobów:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [toString()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="next-steps"></a>Kolejne kroki

- Zobacz język używany w [początkowego zapytania](../samples/starter.md)
- Zobacz zaawansowane używa w [zaawansowanych zapytań](../samples/advanced.md)
- Dowiedz się, jak [eksplorować zasoby](explore-resources.md)