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
ms.openlocfilehash: d18069335bb20f78a5bcda85eb6fcb2a5abe75f7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024674"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis w usłudze Azure Search (wersja zapoznawcza)

`moreLikeThis=[key]` jest to parametr zapytania w [interfejsu API wyszukiwania w dokumentach](https://docs.microsoft.com/rest/api/searchservice/search-documents) służący do wyszukiwania dokumentów podobnie jak dokument, określony przez klucz dokumentu. Po wysłaniu żądania wyszukiwania za pomocą `moreLikeThis`, jest generowane zapytanie, za pomocą terminów wyszukiwania wyodrębnione z danego dokumentu, które najlepiej opisać tego dokumentu. Wygenerowane zapytanie jest następnie używany do żądania wyszukiwania. Domyślnie, wszystkie pola z możliwością wyszukiwania zawartości są traktowane jako, minus żadnych ograniczone pól, które można określić za pomocą `searchFields` parametru. `moreLikeThis` Parametru nie można używać z parametrem wyszukiwania `search=[string]`.

Domyślnie są traktowane jako zawartość wszystkie pola z możliwością wyszukiwania najwyższego poziomu. Jeśli chcesz określić konkretnego pola zamiast tego możesz użyć `searchFields` parametru. 

> [!NOTE]
> `moreLikeThis` (wersja zapoznawcza) nie działa na wyszukiwanie pól podrzędnych w [typu złożonego](search-howto-complex-data-types.md).

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

## <a name="feature-availability"></a>Dostępność funkcji

`moreLikeThis` Parametr jest dostępny w wersji zapoznawczej interfejsów API REST tylko (`api-version=2019-05-06-Preview`).

## <a name="next-steps"></a>Kolejne kroki

Aby poeksperymentować z tą funkcją, można użyć dowolnego narzędzia do testowania w sieci web.  Zaleca się przy użyciu narzędzia Postman na potrzeby tego ćwiczenia.

> [!div class="nextstepaction"]
> [Zapoznaj się z wyszukiwania interfejsów API REST Azure przy użyciu narzędzia Postman](search-fiddler.md)