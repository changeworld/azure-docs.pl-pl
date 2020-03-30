---
title: OData wybierz odwołanie
titleSuffix: Azure Cognitive Search
description: Odwołanie do składni i języka dla jawnego wyboru pól do zwrócenia w wynikach wyszukiwania zapytań usługi Azure Cognitive Search.
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
ms.openlocfilehash: 64f15bf3d262249cdda2760c7ddf768be2590419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113102"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Składnia $select OData w usłudze Azure Cognitive Search

 [ParametrU OData **$select** ](query-odata-filter-orderby-syntax.md) można wybrać pola, które mają być uwzględniane w wynikach wyszukiwania z usługi Azure Cognitive Search. W tym artykule opisano szczegółowo **składnię $select.** Aby uzyskać bardziej ogólne informacje dotyczące **używania $select** podczas prezentowania wyników wyszukiwania, zobacz [Jak pracować z wynikami wyszukiwania w usłudze Azure Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Składnia

Parametr **$select** określa, które pola dla każdego dokumentu są zwracane w zestawie wyników kwerendy. Następujący EBNF[(Rozszerzony formularz Backus-Naur)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)definiuje gramatykę **dla** $select parametru:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Dostępny jest również interaktywny diagram składniowy:

> [!div class="nextstepaction"]
> [Diagram składni OData dla usługi Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Zobacz [odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md) dla pełnego EBNF.

Parametr **$select** występuje w dwóch formach:

1. Pojedyncza gwiazda`*`( ), wskazująca, że wszystkie pola do odzyskania powinny zostać zwrócone, lub
1. Oddzielona przecinkami lista ścieżek pól, określająca, które pola powinny być zwracane.

Korzystając z drugiego formularza, można określić tylko pola, które można pobrać na liście.

Jeśli pole złożone zostanie wyświetlone bez jawnego określenia jego pól podrzędnych, wszystkie pola podrzędne, które można pobrać, zostaną uwzględnione w zestawie wyników kwerendy. Załóżmy na przykład, `Address` że `Street` `City`indeks `Country` ma pole z , i pod-pola, które są możliwe do pobrania. Jeśli określisz `Address` w **$select,** wyniki kwerendy będą zawierać wszystkie trzy podobasy.

## <a name="examples"></a>Przykłady

Uwzględnij `HotelId` `HotelName`pola `Rating` , i najwyższego poziomu w wynikach, a także `City` podobsąże: `Address`

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

Uwzględnij `HotelName` pole najwyższego poziomu w wynikach, a `Address`także wszystkie `Type` `BaseRate` poda fields , i `Rooms` pod-pola każdego obiektu w kolekcji:

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

- [Jak pracować z wynikami wyszukiwania w usłudze Azure Cognitive Search](search-pagination-page-layout.md)
- [Omówienie języka wyrażenia OData dla usługi Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Odwołanie do składni wyrażenia OData dla usługi Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
