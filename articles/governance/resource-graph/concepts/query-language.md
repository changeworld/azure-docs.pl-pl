---
title: Zrozumienie języka zapytań
description: W tym artykule opisano dostępne operatory Kusto i funkcje, które można używać z wykresem zasobów platformy Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: dcb21a6aedf16b034fad4f0822e22758dda03c33
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800506"
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

## <a name="escape-characters"></a>Znaki specjalne

Niektóre nazwy właściwości, takie jak te, które obejmują `.` lub `$`, musi być opakowana lub poprzedzone znakiem zmiany znaczenia w zapytaniu lub właściwość nazwa jest interpretowany nieprawidłowo i nie zapewnia oczekiwanych wyników.

- `.` -Opakować nazwy właściwości w związku z tym: `['propertyname.withaperiod']`
  
  Przykładowe zapytanie, które opakowuje właściwość _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` -Znak ucieczki dla znaków w nazwie właściwości. Znak ucieczki jest zależna od powłoki, który wykres zasobów jest uruchamiany z.

  - **bash** - `\`

    Przykładowe zapytanie, które specjalne właściwości  _\$typu_ w powłoce bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** — nie ucieczki `$` znaków.

  - **PowerShell** - ``` ` ```

    Przykładowe zapytanie, które specjalne właściwości  _\$typu_ w programie PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Kolejne kroki

- Zobacz język używany w [początkowego zapytania](../samples/starter.md)
- Zobacz zaawansowane używa w [zaawansowanych zapytań](../samples/advanced.md)
- Dowiedz się, jak [eksplorować zasoby](explore-resources.md)