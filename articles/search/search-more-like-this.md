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
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873815"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (wersja zapoznawcza) na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT] 
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej. Funkcje wersji zapoznawczej są dostępne bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Interfejs API REST w wersji 2019-05-06 — wersja zapoznawcza](search-api-preview.md) zawiera tę funkcję. Obecnie nie ma obsługi portalu lub zestawu SDK platformy .NET.

`moreLikeThis=[key]` to parametr zapytania w [interfejsie API dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents) , który znajduje dokumenty podobne do dokumentu określonego przez klucz dokumentu. Gdy żądanie Search zostanie wykonane przy użyciu `moreLikeThis`, zostanie wygenerowane zapytanie z wyszukiwanymi terminami z danego dokumentu, które opisują ten dokument. Wygenerowane zapytanie jest następnie używane do wykonywania żądania wyszukiwania. Domyślnie zawartość wszystkich pól, które można przeszukiwać, jest uwzględniana, minus wszystkie pola z ograniczeniami, które zostały określone przy użyciu `searchFields` parametru. Nie można użyć parametru `moreLikeThis` z parametrem wyszukiwania, `search=[string]`.

Domyślnie są brane pod uwagę zawartość wszystkich pól z możliwością wyszukiwania najwyższego poziomu. Jeśli zamiast tego chcesz określić określone pola, możesz użyć parametru `searchFields`. 

Nie można użyć `MoreLikeThis` przy wyszukiwaniu pól podrzędnych w [typie złożonym](search-howto-complex-data-types.md).

## <a name="examples"></a>Przykłady

W poniższych przykładach użyto przykładu hoteli z [przewodnika Szybki Start: Tworzenie indeksu wyszukiwania w Azure Portal](search-get-started-portal.md).

### <a name="simple-query"></a>Proste zapytanie

Poniższe zapytanie umożliwia znalezienie dokumentów, których pola opisu są najbardziej podobne do pola dokumentu źródłowego zgodnie z parametrem `moreLikeThis`:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

W tym przykładzie żądanie wyszukuje Hotele podobne do tego z `HotelId` 29.
Zamiast korzystać z protokołu HTTP GET, można również wywołać `MoreLikeThis` przy użyciu protokołu HTTP POST:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Stosowanie filtrów

`MoreLikeThis` można łączyć z innymi typowymi parametrami zapytań, takimi jak `$filter`. Na przykład zapytanie może być ograniczone tylko do hoteli, którego kategoria ma wartość "budżet" i gdzie klasyfikacja jest wyższa niż 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Wybierz pola i Ogranicz wyniki

Selektor `$top` może służyć do ograniczenia liczby wyników, które powinny być zwracane w `MoreLikeThis` zapytaniu. Ponadto pola mogą być wybierane z `$select`. W tym miejscu wybiera się trzy pierwsze Hotele z IDENTYFIKATORem, nazwą i klasyfikacją: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>Następne kroki

Możesz użyć dowolnego narzędzia do testowania sieci Web, aby eksperymentować z tą funkcją.  Zalecamy używanie programu dla tego ćwiczenia.

> [!div class="nextstepaction"]
> [Poznaj interfejsy API REST platformy Azure Wyszukiwanie poznawcze przy użyciu programu Poster](search-get-started-postman.md)
