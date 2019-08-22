---
title: Wyszukiwanie OData. Informacje o funkcji oceny — Azure Search
description: Funkcja Search. Score w zapytaniach Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: b6bf56a61ca685b306a15e474623336216ba531b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647516"
---
# <a name="odata-searchscore-function-in-azure-search"></a>Funkcja `search.score` OData w Azure Search

Po wysłaniu zapytania do Azure Search bez [parametru **$OrderBy** ](search-query-odata-orderby.md), wyniki, które powrócisz, zostaną posortowane w kolejności malejącej według oceny istotności. Nawet w przypadku używania **$OrderBy**ocena znaczenia będzie używana w celu podzielenia powiązań domyślnie. Jednak czasami warto użyć oceny istotności jako początkowego kryterium sortowania i innych kryteriów jako powiązania. `search.score` Funkcja ta umożliwia wykonywanie tych czynności.

## <a name="syntax"></a>Składnia

Składnia dla `search.score` w **$OrderBy** ma wartość `search.score()`. Funkcja `search.score` nie przyjmuje żadnych parametrów. Może być używany z `asc` specyfikatorem kolejności sortowania lub `desc` , podobnie jak każda inna klauzula w parametrze **$OrderBy** . Może pojawić się w dowolnym miejscu na liście kryteriów sortowania.

## <a name="example"></a>Przykład

Sortuj Hotele w kolejności malejącej `search.score` według `rating`i, a następnie w kolejności rosnącej według odległości od podanych współrzędnych, tak aby między dwiema hoteli z identycznymi klasyfikacjami była wyświetlana pierwsza z nich:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Następne kroki  

- [Przegląd języka wyrażenia OData dla Azure Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;Azure Search interfejs API REST usługi&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
