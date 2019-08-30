---
title: moreLikeThis w Azure Search (wersja zapoznawcza) — Azure Search
description: Wstępna dokumentacja funkcji moreLikeThis (wersja zapoznawcza) uwidoczniona w interfejsie API REST Azure Search.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d7c816c545c6647907aa9d700a4eb6ed91277465
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182306"
---
# <a name="morelikethis-in-azure-search"></a>moreLikeThis w Azure Search

> [!Note]
> moreLikeThis jest w wersji zapoznawczej i nie jest przeznaczony do użycia w środowisku produkcyjnym. [Interfejs API REST w wersji 2019-05-06 —](search-api-preview.md) wersja zapoznawcza zawiera tę funkcję. W tej chwili nie ma obsługi zestawu SDK platformy .NET.

`moreLikeThis=[key]`to parametr zapytania w interfejsie [API dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) , który znajduje dokumenty podobne do dokumentu określonego przez klucz dokumentu. Gdy zostanie wysłane `moreLikeThis`żądanie wyszukiwania, zapytanie jest generowane z wyszukiwanymi terminami wyciągniętymi z danego dokumentu, który opisuje ten dokument. Wygenerowane zapytanie jest następnie używane do wykonywania żądania wyszukiwania. Domyślnie zawartość wszystkich pól, które można przeszukiwać, jest uwzględniana, minus wszystkie pola z ograniczeniami, które `searchFields` zostały określone za pomocą parametru. Nie można użyć `search=[string]`parametruzparametrem wyszukiwania. `moreLikeThis`

Domyślnie są brane pod uwagę zawartość wszystkich pól z możliwością wyszukiwania najwyższego poziomu. Jeśli chcesz zamiast tego określić określone pola, możesz użyć `searchFields` parametru. 

Nie można użyć moreLikeThis do wyszukiwania podpól w [typie złożonym](search-howto-complex-data-types.md).

## <a name="examples"></a>Przykłady 

Poniżej znajduje się przykład zapytania moreLikeThis. Zapytanie znajduje dokumenty, których pola opisu są najbardziej podobne do pola dokumentu źródłowego określonego przez `moreLikeThis` parametr.

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


## <a name="next-steps"></a>Następne kroki

Możesz użyć dowolnego narzędzia do testowania sieci Web, aby eksperymentować z tą funkcją.  Zalecamy używanie programu dla tego ćwiczenia.

> [!div class="nextstepaction"]
> [Eksplorowanie interfejsów API REST Azure Search przy użyciu programu Poster](search-get-started-postman.md)