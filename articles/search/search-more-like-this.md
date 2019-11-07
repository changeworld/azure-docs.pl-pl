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
ms.openlocfilehash: fdde89f9ff88b15c464af805b81708b268e5ddf5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721737"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (wersja zapoznawcza) na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT] 
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. Obecnie nie ma obsługi portalu lub zestawu SDK platformy .NET.

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