---
title: moreLikeThis w usłudze Azure Search (wersja zapoznawcza) — usługa Azure Search
description: Wersję wstępną dokumentacji dla funkcji moreLikeThis (wersja zapoznawcza) udostępniane w interfejsu API REST usługi Azure Search.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 4d1c691e570d3cfc7e0475c02e4c60ed6ffa8440
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485348"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis w usłudze Azure Search

> [!Note]
> moreLikeThis znajduje się w wersji zapoznawczej i nie przeznaczonych do użycia w środowisku produkcyjnym. [Wersji interfejsu API REST 2019-05-06-Preview](search-api-preview.md) zapewnia tę funkcję. Brak obsługi zestawu SDK platformy .NET w tej chwili.

`moreLikeThis=[key]` jest to parametr zapytania w [interfejsu API wyszukiwania w dokumentach](https://docs.microsoft.com/rest/api/searchservice/search-documents) służący do wyszukiwania dokumentów podobnie jak dokument, określony przez klucz dokumentu. Po wysłaniu żądania wyszukiwania za pomocą `moreLikeThis`, jest generowane zapytanie, za pomocą terminów wyszukiwania wyodrębnione z danego dokumentu, które najlepiej opisać tego dokumentu. Wygenerowane zapytanie jest następnie używany do żądania wyszukiwania. Domyślnie, wszystkie pola z możliwością wyszukiwania zawartości są traktowane jako, minus żadnych ograniczone pól, które można określić za pomocą `searchFields` parametru. `moreLikeThis` Parametru nie można używać z parametrem wyszukiwania `search=[string]`.

Domyślnie są traktowane jako zawartość wszystkie pola z możliwością wyszukiwania najwyższego poziomu. Jeśli chcesz określić konkretnego pola zamiast tego możesz użyć `searchFields` parametru. 

Nie można użyć moreLikeThis na podrzędne pola z możliwością wyszukiwania w [typu złożonego](search-howto-complex-data-types.md).

## <a name="examples"></a>Przykłady 

Poniżej przedstawiono przykład zapytania moreLikeThis. Zapytanie znajdzie dokumentów, których pola Opis są najbardziej podobny do pola dokument źródłowy określony przez `moreLikeThis` parametru.

```
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2019-05-06-Preview
```

```
POST /indexes/hotels/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "1002",
      "searchFields": "description"
    }
```


## <a name="next-steps"></a>Kolejne kroki

Aby poeksperymentować z tą funkcją, można użyć dowolnego narzędzia do testowania w sieci web.  Zaleca się przy użyciu narzędzia Postman na potrzeby tego ćwiczenia.

> [!div class="nextstepaction"]
> [Zapoznaj się z wyszukiwania interfejsów API REST Azure przy użyciu narzędzia Postman](search-get-started-postman.md)