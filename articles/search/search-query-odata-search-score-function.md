---
title: Odwołanie do funkcji search.score OData
titleSuffix: Azure Cognitive Search
description: Składnia i dokumentacja referencyjna do korzystania z funkcji search.score w zapytaniach usługi Azure Cognitive Search.
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113139"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>Funkcja OData `search.score` w usłudze Azure Cognitive Search

Po wysłaniu kwerendy do usługi Azure Cognitive Search bez [parametru **$orderby** ](search-query-odata-orderby.md)wyniki, które wracają, zostaną posortowane w kolejności malejącej według wyniku istotności. Nawet jeśli używasz **$orderby,** wynik trafności będzie domyślnie używany do zerwania więzi. Jednak czasami warto użyć wyniku trafności jako początkowych kryteriów sortowania, a niektóre inne kryteria jako tie-breaker. Funkcja `search.score` pozwala to zrobić.

## <a name="syntax"></a>Składnia

Składnia `search.score` w **$orderby** jest `search.score()`. Funkcja `search.score` nie przyjmuje żadnych parametrów. Może być używany `asc` z `desc` specyfikatorem lub sort-order, podobnie jak każda inna klauzula w **parametrze $orderby.** Może pojawić się w dowolnym miejscu na liście kryteriów sortowania.

## <a name="example"></a>Przykład

Sortuj hotele `search.score` w `rating`porządku malejącym według i , a następnie w kolejności rosnącej według odległości od podanych współrzędnych, tak aby między dwoma hotelami o identycznych ocenach, najbliższy jest wymieniony jako pierwszy:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Następne kroki  

- [Omówienie języka wyrażenia OData dla usługi Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;interfejsu API usługi Azure Cognitive Search EST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
