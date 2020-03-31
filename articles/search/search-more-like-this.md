---
title: więcej Funkcja kwerendyLikeThis (podgląd)
titleSuffix: Azure Cognitive Search
description: W tym artykule opisano funkcję moreLikeThis (wersja zapoznawcza), która jest dostępna w wersjach zapoznawców interfejsu API REST usługi Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 95b9c76a2ff962cb2fa4bacbb1b1e9a953b7014f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873815"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (wersja zapoznawcza) w usłudze Azure Cognitive Search

> [!IMPORTANT] 
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej. Funkcja w wersji zapoznawczej jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana dla obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Ta funkcja zapewnia [interfejs API REST w wersji 2019-05-06-Preview.](search-api-preview.md) Obecnie nie ma obsługi portalu ani SDK .NET.

`moreLikeThis=[key]`jest parametrem zapytania w [interfejsie API wyszukiwania dokumentów,](https://docs.microsoft.com/rest/api/searchservice/search-documents) który znajduje dokumenty podobne do dokumentu określonego przez klucz dokumentu. W przypadku żądania wyszukiwania `moreLikeThis`za pomocą kwerendy jest generowane z wyszukiwanych terminów wyodrębnionych z danego dokumentu, które najlepiej opisują ten dokument. Wygenerowane zapytanie jest następnie używane do żądania wyszukiwania. Domyślnie uwzględniana jest zawartość wszystkich pól z wyszukujnymi `searchFields` pomniejszony o wszystkie pola z ograniczeniami określone przy użyciu parametru. Parametru `moreLikeThis` nie można używać z `search=[string]`parametrem wyszukiwania, .

Domyślnie uwzględniana jest zawartość wszystkich pól najwyższego poziomu wyszukiwania. Jeśli chcesz określić określone pola zamiast tego, można użyć parametru. `searchFields` 

Nie można `MoreLikeThis` używać w podpolach z wyszukujalnymi typu [złożonego](search-howto-complex-data-types.md).

## <a name="examples"></a>Przykłady

Wszystkie poniższe przykłady używają przykładu hoteli z [przewodnika Szybki start: utwórz indeks wyszukiwania w witrynie Azure portal](search-get-started-portal.md).

### <a name="simple-query"></a>Proste zapytanie

Następująca kwerenda znajduje dokumenty, których pola opisu są najbardziej `moreLikeThis` podobne do pola dokumentu źródłowego określonego przez parametr:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2019-05-06-Preview
```

W tym przykładzie żądanie wyszukuje `HotelId` hotele podobne do tego z 29.
Zamiast używać protokołu HTTP GET, `MoreLikeThis` można również wywoływać przy użyciu protokołu HTTP POST:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Stosowanie filtrów

`MoreLikeThis`można łączyć z innymi `$filter`typowymi parametrami zapytania, takimi jak . Na przykład zapytanie może być ograniczone tylko do hoteli, których kategoria to "Budżet" i gdzie ocena jest wyższa niż 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2019-05-06-Preview
```

### <a name="select-fields-and-limit-results"></a>Zaznaczanie pól i ograniczanie wyników

Selektor `$top` może służyć do ograniczenia liczby wyników powinny `MoreLikeThis` być zwracane w kwerendzie. Ponadto pola można wybrać `$select`za pomocą pliku . Tutaj wybierane są trzy najlepsze hotele wraz z ich identyfikatorem, nazwą i oceną: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2019-05-06-Preview
```

## <a name="next-steps"></a>Następne kroki

Do eksperymentowania z tą funkcją można użyć dowolnego narzędzia do testowania sieci Web.  Zalecamy użycie postmana do tego ćwiczenia.

> [!div class="nextstepaction"]
> [Eksploruj interfejsy API REST usługi Azure Cognitive Search przy użyciu listonosza](search-get-started-postman.md)
