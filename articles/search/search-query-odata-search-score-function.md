---
title: Odwołanie do funkcji search.score OData — usługa Azure Search
description: Funkcja search.score OData w zapytaniach usługi Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: dc444216c4677b9970b867e92aa5ae259a197220
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079693"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` funkcji w usłudze Azure Search

Podczas wysyłania zapytania do usługi Azure Search bez [ **$orderby** parametru](search-query-odata-orderby.md), wyniki, które możesz wrócić zostaną posortowane w kolejności malejącej według istotności. Nawet jeśli używasz **$orderby**, istotności będzie służyć do przerwania ties domyślnie. Jednak czasami warto użyć istotności jako kryteriów sortowania początkowej i innych kryteriów jako decydujący. `search.score` Dzięki funkcji, można to zrobić.

## <a name="syntax"></a>Składnia

Składnia `search.score` w **$orderby** jest `search.score()`. Funkcja `search.score` nie przyjmuje żadnych parametrów. Mogą być używane z `asc` lub `desc` specyfikator kolejność sortowania, podobnie jak wszelkie inne klauzule w **$orderby** parametru. Go może występować w dowolnym miejscu na liście kryteriów sortowania.

## <a name="example"></a>Przykład

Sortowanie hotele malejąco według `search.score` i `rating`, a następnie w polu rosnąco przez odległości od danej współrzędne tak, aby między dwa hotele z ocenami identyczne, znajdującego się najbliżej jest wymienione jako pierwsze:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Kolejne kroki  

- [Omówienie języka wyrażenia OData dla usługi Azure Search](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
