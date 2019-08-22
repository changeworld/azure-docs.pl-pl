---
title: Odwołanie do wyboru protokołu OData — Azure Search
description: Dokumentacja języka OData dla składni SELECT w zapytaniach Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 64e9ad75d88f595ab5def6fe8b63fee9407ae0fe
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647869"
---
# <a name="odata-select-syntax-in-azure-search"></a>Składnia $select OData w Azure Search

 Można użyć [parametru **$SELECT** OData](query-odata-filter-orderby-syntax.md) , aby wybrać pola do uwzględnienia w wynikach wyszukiwania z Azure Search. W tym artykule opisano szczegółowo składnię **$SELECT** . Aby uzyskać ogólne informacje na temat używania **$SELECT** podczas prezentowania wyników wyszukiwania, zobacz jak korzystać [z wyników wyszukiwania w Azure Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Składnia

**$SELECT** parametr określa, które pola dla każdego dokumentu są zwracane w zestawie wyników zapytania. Następujący EBNF ([Extended back-Naura form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definiuje gramatykę dla parametru **$SELECT** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Dostępny jest również interaktywny diagram składni:

> [!div class="nextstepaction"]
> [Diagram składni OData dla Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Zapoznaj się z informacjami o [składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md) , aby uzyskać pełną EBNF.

**$SELECT** parametr znajduje się w dwóch formach:

1. Pojedyncza gwiazdka`*`(), wskazująca, że wszystkie pola do pobierania powinny być zwracane.
1. Rozdzielana przecinkami lista ścieżek pól, które identyfikują pola, które powinny być zwracane.

W przypadku korzystania z drugiego formularza można określić tylko na liście pola do pobierania.

Jeśli lista zawiera pole złożone bez określania jego pól podrzędnych, wszystkie możliwe do pobierania pola podrzędne zostaną uwzględnione w zestawie wyników zapytania. Załóżmy na przykład `Address` , że indeks zawiera `Street`pola, `City`i `Country` pola podrzędne, które są do pobierania. W przypadku określenia `Address` w **$SELECT**wyniki zapytania będą zawierać wszystkie trzy pola podrzędne.

## <a name="examples"></a>Przykłady

`Rating` `Address` `City` Uwzględnij pola `HotelName` ,inajwyższegopoziomuwwynikach,atakżepolapodrzędne:`HotelId`

    $select=HotelId, HotelName, Rating, Address/City

Przykładowy wynik może wyglądać następująco:

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

`BaseRate` `Address` `Type` Uwzględnij pole `Rooms` najwyższego poziomu w wynikach, a także wszystkie pola podrzędne i i podrzędne pola dla każdego obiektu w kolekcji: `HotelName`

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Przykładowy wynik może wyglądać następująco:

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

## <a name="next-steps"></a>Następne kroki  

- [Jak korzystać z wyników wyszukiwania w Azure Search](search-pagination-page-layout.md)
- [Przegląd języka wyrażenia OData dla Azure Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla Azure Search](search-query-odata-syntax-reference.md)
- [Wyszukaj dokumenty &#40;Azure Search interfejs API REST usługi&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
