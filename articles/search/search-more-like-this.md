---
title: moreLikeThis w usłudze Azure Search (wersja zapoznawcza) — usługa Azure Search
description: Wersję wstępną dokumentacji dla funkcji moreLikeThis (wersja zapoznawcza) udostępniane w interfejsu API REST usługi Azure Search.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d55a6d883e0dcd5ad4b1c1584b76bae06e6c742a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283362"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis w usłudze Azure Search (wersja zapoznawcza)

`moreLikeThis=[key]` jest to parametr zapytania w [interfejsu API wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents). Określając `moreLikeThis` parametrów w zapytaniu wyszukiwania, można znaleźć dokumenty, które są podobne do dokumentu, określony przez klucz dokumentu. Po wysłaniu żądania wyszukiwania za pomocą `moreLikeThis`, jest generowane zapytanie, za pomocą terminów wyszukiwania wyodrębnione z danego dokumentu, które najlepiej opisać tego dokumentu. Wygenerowane zapytanie jest następnie używany do żądania wyszukiwania. Domyślnie zawartość wszystkich `searchable` pola są traktowane jako, chyba że `searchFields` parametr jest używany do ograniczania pola. `moreLikeThis` Parametru nie można używać z parametrem wyszukiwania `search=[string]`.

## <a name="examples"></a>Przykłady 

Poniżej przedstawiono przykład zapytania moreLikeThis. Zapytanie znajdzie dokumentów, których pola Opis są najbardziej podobny do pola dokument źródłowy określony przez `moreLikeThis` parametru.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```

## <a name="feature-availability"></a>Dostępność funkcji

Funkcja moreLikeThis jest obecnie dostępna w wersji zapoznawczej i obsługiwane tylko w wersjach interfejsu api w wersji zapoznawczej, `2015-02-28-Preview` i `2016-09-01-Preview`. Ponieważ wersja interfejsu API jest określony w żądaniu, to możliwość łączenia jest ogólnie dostępna (GA) i interfejsów API w wersji zapoznawczej w tej samej aplikacji. Jednak (wersja zapoznawcza), które interfejsy API nie są objęte umową SLA i funkcje mogą ulec zmianie, dlatego nie zaleca się używania ich w aplikacjach produkcyjnych.
