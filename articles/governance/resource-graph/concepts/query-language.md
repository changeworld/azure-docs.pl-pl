---
title: Zrozumienie języka zapytań wykres zasobów platformy Azure
description: Zawiera opis sposobu działania języka zapytań dla wykresu zasobów platformy Azure.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 3600fc47a0fb318a49c1b37722cb7fffa51ec6f2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951954"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Zrozumienie języka zapytań wykres zasobów platformy Azure

Język zapytań dla wykresu zasobów platformy Azure obsługuje szereg operatorów i funkcji. Każdy może działać, a następnie działają podobnie do języka zapytania Kusto (KQL). Jednak należy pamiętać, że chociaż język zapytań wykres zasobów jest podobny do [KQL](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators), nie jest taka sama.

> [!NOTE]
> Linki do dokumentacji KQL może wymagać uwierzytelniania.

Najlepszym sposobem poznania językiem zapytań używanym przez usługę Graph zasobu ma rozpoczynać się w dokumentacji dotyczącej KQL uzyskać informacje dotyczące struktury języka i jak różne obsługiwane operatory i funkcje współpracują ze sobą.

## <a name="supported-tabular-operators"></a>Obsługiwane operatory tabelarycznych

Poniżej przedstawiono listę obsługiwanych operatorów tabelarycznych w wykresie zasobów:

- [Odrębne](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator)
- [Rozszerzanie](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator)
- [Limit](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/limit-operator)
- [kolejność według](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/order-operator)
- [Projekt](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator)
- [Projekt niedostępny](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-away-operator)
- [Próbki](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-operator)
- [różne próbki](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-distinct-operator)
- [Sortuj według](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
- [Podsumowanie](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator)
- [Wypełnij](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
- [Do góry](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator)
- [zagnieżdżone TOP](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-nested-operator)
- [TOP hitters](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-hitters-operator)
- [gdzie](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

## <a name="supported-functions"></a>Obsługiwane funkcje

Poniżej przedstawiono listę obsługiwanych funkcji w programie Graph zasobów:

- [ago()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago%28%29)
- [buldschema()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/buildschema%28%29)
- [Count()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/count%28%29)
- [strcat()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/strcat%28%29)
- [isnotempty()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/isnotempty%28%29_-notempty%28%29)
- [toString()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/tostring%28%29)

## <a name="next-steps"></a>Kolejne kroki

- Zobacz język używany w [początkowego zapytania](../samples/starter.md)
- Zobacz zaawansowane używa w [zaawansowanych zapytań](../samples/advanced.md)
- Dowiedz się, jak [zapoznaj się z zasobami](explore-resources.md)