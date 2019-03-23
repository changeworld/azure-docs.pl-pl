---
title: Automatyczne sugerowanie przykład dodawania warunków listy rozwijanej do pola wyszukiwania — usługa Azure Search
description: Dodaj dane wejściowe sugerowane zapytania, tworząc sugestory i sformułowania żądań, które wywołują proponowanych zapytań w usłudze Azure Search.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.openlocfilehash: 1db085d61c60d303aaff5c3b0d16998805fcda90
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373078"
---
# <a name="example-add-autosuggest-for-dropdown-query-selections"></a>Przykład: Dodaj automatycznego sugerowania zaznaczeń zapytań listy rozwijanej

Termin wyszukiwania w danych wejściowych może zawierać listę rozwijaną terminów sugerowane zapytania. W tym samouczku tej możliwości w wyszukiwarkach komercyjnych i można zaimplementować podobne możliwości w usłudze Azure Search przy użyciu [konstrukcji sugestora](index-add-suggesters.md) i operacją sugestie żądania zapytania. W tym artykule używa przykładów, aby zademonstrować formułowanie zapytania automatycznego sugerowania, przy użyciu sugestora, które zostały już zdefiniowane. 

## <a name="rest-api"></a>Interfejs API REST

Możesz użyć [Postman](search-fiddler.md) lub [PowerShell](search-create-index-rest-api.md) i [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/) możesz wypróbować ten przykład, ale wyniki zostaną zwrócone jako dokumenty JSON. Środowisko bardziej realistycznego i visual można znaleźć, używając [przykładowego kodu do automatycznego uzupełniania](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

### <a name="1---index-with-a-suggester-construct"></a>1 — indeksowanie za pomocą konstrukcji sugestora

Automatyczne sugerowanie zaczyna się od [konstrukcji sugestora](index-add-suggesters.md) dodane do indeksu, składa się z pól, które przyczyniają się wartości list rozwijanych. Biorąc pod uwagę poniższe schemat, sugerowane zapytania będą formułować za pomocą wartości z pola hotelName i kategorii.

Zakładając, że z minimalnym przykładowych zestawów danych, w przewodników Szybki Start, hotelu nazwy zawierają "Pozostać ozdobnych" i "Roach motel" i kategorie obejmują "Luksusowe" i "Budget".

Jeśli masz już indeksu hotels, należy porzucić i ponowne utworzenie go za pomocą następującego schematu. Ten schemat dodaje sugestora. Pamiętaj, aby załadować dane, jak również.

```json
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ]
}

```

### <a name="2---query-with-suggestions-operator"></a>2 — zapytanie z operatorem sugestie

Użyj sugestora i wywoływać automatyczne sugerowanie, konieczne jest wysłanie [sugestie dotyczące interfejsów API](https://docs.microsoft.com/rest/api/searchservice/suggestions) żądania przy użyciu GET lub POST. Na takie żądanie usługi wyszukiwania skanowania pod kątem potencjalnych dopasowań, po pierwsze trzy znaki są odbierane. 

W nagłówku żądania ustaw **klucz interfejsu api** klucz administratora lub zapytanie i **Content-Type** application/JSON. 

```http
GET https://mydemo.search.windows.net/indexes/hotels/docs/suggest?search=fan&&suggesterName=sg
```

Skanowanie żądania wszystkich pól objętych sugestora hotelName i kategorii, zwraca następującą odpowiedź:

```
{
    "@odata.context": "https://mydemo.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "value": [
        {
            "@search.text": "Fancy Stay",
            "hotelId": "1"
        }
    ]
}
```

Aby zapewnić oczekiwany wynik, kod klienta będzie renderować wyniki jako listy rozwijanej w pasku wyszukiwania.

## <a name="net-sdk-c"></a>ZESTAW SDK PLATFORMY .NET (C#)

### <a name="1---index-with-a-suggester-construct"></a>1 — indeksowanie za pomocą konstrukcji sugestora

W zestawie SDK platformy .NET za pomocą [klasy Sugestora](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Sugestora to kolekcja, ale może potrwać tylko jeden element.

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="2---query-with-suggest-method"></a>2 - wykonywać zapytania za pomocą sugerowanej — metoda

[Metoda DocumentsOperationsExtensions.Suggest](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet) służy do zwracania ciągów sugerowane zapytania.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult suggestResult = _indexClient.Documents.Suggest(term, "sg",sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

## <a name="see-also"></a>Zobacz także

+ [Poznaj interfejs API REST przy użyciu narzędzia Postman](search-fiddler.md)
+ [Przykład: Autouzupełnianie](search-autocomplete-tutorial.md)
+ [Dodaj sugestory do indeksu](index-add-suggesters.md)
+ [Dodaj klasę Sugestory przy użyciu platformy .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)
+ [Sugestie dotyczące wywołań przy użyciu GET lub POST (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/suggestions)
+ [Wywołaj sugestie przy użyciu SuggestWithHttpMessagesAsync (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) lub 