---
title: Wyszukiwanie OData. Informacje o funkcji oceny
titleSuffix: Azure Cognitive Search
description: Funkcja wyszukiwania OData. Score w zapytaniach Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 500ac4f3a44d54e367ddc4ee5efc9514d603cab6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793267"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Funkcja `search.score` OData na platformie Azure Wyszukiwanie poznawcze

Po wysłaniu zapytania do Wyszukiwanie poznawcze platformy Azure bez parametru [ **$OrderBy** ](search-query-odata-orderby.md), przywracane wyniki zostaną posortowane w kolejności malejącej według oceny istotności. Nawet w przypadku używania **$OrderBy**ocena znaczenia będzie używana w celu podzielenia powiązań domyślnie. Jednak czasami warto użyć oceny istotności jako początkowego kryterium sortowania i innych kryteriów jako powiązania. Funkcja `search.score` umożliwia wykonywanie tych czynności.

## <a name="syntax"></a>Składnia

Składnia `search.score` w **$OrderBy** jest `search.score()`. Funkcja `search.score` nie przyjmuje żadnych parametrów. Może być używany z specyfikatorem kolejności sortowania `asc` lub `desc`, podobnie jak każda inna klauzula w parametrze **$OrderBy** . Może pojawić się w dowolnym miejscu na liście kryteriów sortowania.

## <a name="example"></a>Przykład

Sortuj Hotele w kolejności malejącej według `search.score` i `rating`, a następnie w kolejności rosnącej według odległości od danego współrzędnych, tak aby między dwiema hoteli z identyczną klasyfikacją były najpierw wymienione najbliższe:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Następne kroki  

- [Omówienie języka wyrażeń OData dla platformy Azure Wyszukiwanie poznawcze](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Wyszukiwanie poznawcze](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;w usłudze Azure wyszukiwanie POZNAWCZE Est API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
