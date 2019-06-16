---
title: Odwołanie wybierz OData — usługa Azure Search
description: Dokumentacja języka OData dla wybierz składni zapytań usługi Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 9383ae725fffac55854488ffbc6aeb161ae7e0c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079680"
---
# <a name="odata-select-syntax-in-azure-search"></a>Składnia OData $select w usłudze Azure Search

 Możesz użyć [OData **$select** parametru](query-odata-filter-orderby-syntax.md) wybrać pola do uwzględnienia w wynikach wyszukiwania w usłudze Azure Search. W tym artykule opisano składnię **$select** szczegółowo. Aby uzyskać ogólne informacje o sposobie używania **$select** podczas wyświetlania wyników wyszukiwania, zobacz [sposób pracy z usługą search wyników w usłudze Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Składnia

**$Select** parametr określa, które pola dla każdego dokumentu są zwracane w zestawie wyników zapytania. Następujące EBNF ([rozszerzony formularz Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatyka dla **$select** parametru:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Diagram składni interaktywne jest również dostępna:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Zobacz [dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md) dla EBNF ukończone.

**$Select** parametru jest oferowana w dwóch formach:

1. Jednej gwiazdki (`*`), który wskazuje, że ma zostać zwrócone wszystkie pola z możliwością pobierania, lub
1. Rozdzielaną przecinkami listę ścieżki pola, zidentyfikowanie pola, które powinny być zwrócone.

Korzystając z drugiej formy, można określić tylko pola z możliwością pobierania na liście.

Po wyświetleniu listy pól złożonych bez jawne określenie jej podrzędnych pól, wszystkie pola z możliwością pobierania podrzędne zostaną uwzględnione w zestawie wyników zapytania. Załóżmy na przykład, ma indeksu `Address` pole `Street`, `City`, i `Country` pól podrzędnych, które są wszystkie możliwe do pobierania. Jeśli określisz `Address` w **$select**, wyniki zapytania obejmują wszystkie trzy pola podrzędnego.

## <a name="examples"></a>Przykłady

Obejmują `HotelId`, `HotelName`, i `Rating` najwyższego poziomu pól w wynikach, jak również `City` podrzędne pola `Address`:

    $select=HotelId, HotelName, Rating, Address/City

Przykład wyniku może wyglądać następująco:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Obejmują `HotelName` najwyższego poziomu polem w wyniki, a także wszystkie podrzędne pola `Address`i `Type` i `BaseRate` pól podrzędnych każdego obiektu w `Rooms` kolekcji:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Przykład wyniku może wyglądać następująco:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki  

- [Jak pracować z usługą wyszukiwania wyników w usłudze Azure Search](search-pagination-page-layout.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Search](query-odata-filter-orderby-syntax.md)
- [Dokumentacja składni wyrażenia OData dla usługi Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukiwanie w dokumentach &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
