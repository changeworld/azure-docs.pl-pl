---
title: Opis języka zapytań
description: Opisuje dostępne operatory Kusto i funkcje możliwe do użycia w usłudze Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c6e35d688581d0839e12806117e63c7d71fbc459
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231512"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Informacje o języku zapytań grafu zasobów platformy Azure

Język zapytań dla wykresu zasobów platformy Azure obsługuje wiele operatorów i funkcji. Każda z nich działa i działa w oparciu o [Eksplorator danych platformy Azure](../../../data-explorer/data-explorer-overview.md).

Najlepszym sposobem na zapoznanie się z językiem zapytań używanym przez Graf zasobów jest rozpoczęcie pracy z dokumentacją dla [języka zapytań](/azure/kusto/query/index)Eksplorator danych platformy Azure. Zawiera ona informacje o tym, jak język jest strukturalny oraz jak różne obsługiwane operatory i funkcje współpracują ze sobą.

## <a name="supported-tabular-operators"></a>Obsługiwane operatory tabelaryczne

Poniżej znajduje się lista obsługiwanych operatorów tabelarycznych na wykresie zasobów:

- [count](/azure/kusto/query/countoperator)
- [itp](/azure/kusto/query/distinctoperator)
- [sunąć](/azure/kusto/query/extendoperator)
- [granice](/azure/kusto/query/limitoperator)
- [Porządkuj według](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [projekt — poza](/azure/kusto/query/projectawayoperator)
- [Northwind](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [Sortuj według](/azure/kusto/query/sortoperator)
- [Podsumuj](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [Do góry](/azure/kusto/query/topoperator)
- [Góra — zagnieżdżone](/azure/kusto/query/topnestedoperator)
- [Top-hitters](/azure/kusto/query/tophittersoperator)
- [miejscu](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Obsługiwane funkcje

Poniżej znajduje się lista obsługiwanych funkcji na wykresie zasobów:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [ToString ()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Znaki ucieczki

Niektóre nazwy właściwości, takie jak te, które zawierają `.` lub `$`, muszą być opakowane lub wyprowadzane w zapytaniu lub nazwa właściwości jest interpretowane niepoprawnie i nie zapewniają oczekiwanych wyników.

- `.`— Zawiń nazwę właściwości w taki sposób, aby:`['propertyname.withaperiod']`
  
  Przykładowe zapytanie, które zawija Właściwość _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`— Znak ucieczki w nazwie właściwości. Używany znak ucieczki zależy od wykresu zasobów powłoki jest uruchamiany z.

  - **bash** - `\`

    Przykładowe zapytanie, które wyprowadza  _\$typ_ właściwości w bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** — nie `$` wyznaczania znaku ucieczki.

  - **PowerShell** - ``` ` ```

    Przykładowe zapytanie, które określa  _\$typ_ właściwości w programie PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Następne kroki

- Zobacz język używany w zapytaniach [początkowych](../samples/starter.md)
- Zobacz zaawansowane zastosowania w [zaawansowanych](../samples/advanced.md) zapytaniach
- Dowiedz się, jak [eksplorować zasoby](explore-resources.md)