---
title: Funkcja zapytania moreLikeThis (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Opisuje funkcję moreLikeThis (wersja zapoznawcza), która jest dostępna w wersji zapoznawczej interfejsu API REST usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b7959beca8a7787a331388b77ebe4060c3675e6d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793474"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (wersja zapoznawcza) na platformie Azure Wyszukiwanie poznawcze

> [!Note]
> moreLikeThis jest w wersji zapoznawczej i nie jest przeznaczony do użycia w środowisku produkcyjnym. [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. W tej chwili nie ma obsługi zestawu SDK platformy .NET.

`moreLikeThis=[key]` to parametr zapytania w [interfejsie API dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) , który znajduje dokumenty podobne do dokumentu określonego przez klucz dokumentu. Gdy żądanie Search zostanie wykonane przy użyciu `moreLikeThis`, zostanie wygenerowane zapytanie z wyszukiwanymi terminami z danego dokumentu, które opisują ten dokument. Wygenerowane zapytanie jest następnie używane do wykonywania żądania wyszukiwania. Domyślnie zawartość wszystkich pól, które można przeszukiwać, jest uwzględniana, minus wszystkie pola z ograniczeniami, które zostały określone przy użyciu `searchFields` parametru. Nie można użyć parametru `moreLikeThis` z parametrem wyszukiwania, `search=[string]`.

Domyślnie są brane pod uwagę zawartość wszystkich pól z możliwością wyszukiwania najwyższego poziomu. Jeśli zamiast tego chcesz określić określone pola, możesz użyć parametru `searchFields`. 

Nie można użyć moreLikeThis do wyszukiwania podpól w [typie złożonym](search-howto-complex-data-types.md).

## <a name="examples"></a>Przykłady 

Poniżej znajduje się przykład zapytania moreLikeThis. Zapytanie znajduje dokumenty, których pola opisu są najbardziej podobne do pola dokumentu źródłowego zgodnie z parametrem `moreLikeThis`.

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
> [Poznaj interfejsy API REST platformy Azure Wyszukiwanie poznawcze przy użyciu programu Poster](search-get-started-postman.md)