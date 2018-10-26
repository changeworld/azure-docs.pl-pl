---
title: Zrozumienie języka zapytań wykres zasobów platformy Azure
description: Zawiera opis sposobu działania języka zapytań dla wykresu zasobów platformy Azure.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 09bcedc5250755f06ba23b84a0ae90b4d43a23db
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086169"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Zrozumienie języka zapytań wykres zasobów platformy Azure

Język zapytań dla wykresu zasobów platformy Azure obsługuje szereg operatorów i funkcji. Każda pracy i działania na podstawie [Eksploratora danych usługi Azure](../../../data-explorer/data-explorer-overview.md).

Najlepszym sposobem poznania językiem zapytań używanym przez usługę Graph zasobu ma rozpoczynać się w dokumentacji dotyczącej Eksplorator danych usługi Azure [język zapytań](/azure/kusto/query/index). Zawiera opis o struktury języka i jak różne obsługiwane operatory i funkcje współpracują ze sobą.

## <a name="supported-tabular-operators"></a>Obsługiwane operatory tabelarycznych

Poniżej przedstawiono listę obsługiwanych operatorów tabelarycznych w wykresie zasobów:

- [count](/azure/kusto/query/countoperator)
- [Odrębne](/azure/kusto/query/distinctoperator)
- [Rozszerzanie](/azure/kusto/query/extendoperator)
- [Limit](/azure/kusto/query/limitoperator)
- [kolejność według](/azure/kusto/query/orderoperator)
- [Projekt](/azure/kusto/query/projectoperator)
- [Projekt niedostępny](/azure/kusto/query/projectawayoperator)
- [Próbki](/azure/kusto/query/sampleoperator)
- [różne próbki](/azure/kusto/query/sampledistinctoperator)
- [Sortuj według](/azure/kusto/query/sortoperator)
- [Podsumowanie](/azure/kusto/query/summarizeoperator)
- [Wypełnij](/azure/kusto/query/takeoperator)
- [Do góry](/azure/kusto/query/topoperator)
- [zagnieżdżone TOP](/azure/kusto/query/topnestedoperator)
- [TOP hitters](/azure/kusto/query/tophittersoperator)
- [gdzie](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Obsługiwane funkcje

Poniżej przedstawiono listę obsługiwanych funkcji w programie Graph zasobów:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [toString()](/azure/kusto/query/tostringfunction)

## <a name="next-steps"></a>Kolejne kroki

- Zobacz język używany w [początkowego zapytania](../samples/starter.md)
- Zobacz zaawansowane używa w [zaawansowanych zapytań](../samples/advanced.md)
- Dowiedz się, jak [eksplorować zasoby](explore-resources.md)
