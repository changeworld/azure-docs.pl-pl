---
title: Dodawanie typeahead zapytań do indeksu — usługa Azure Search
description: Włącz operacje zapytania z podpowiedziami pojawiającymi w usłudze Azure Search, tworząc sugestory i sformułowania żądań, które wywołanie autocomplete lub autosuggested terminów zapytania.
ms.date: 03/22/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 877294e80d655ab75be78a5aa57854a03a5f267a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370656"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Dodaj sugestory do indeksu dla typeahead w usłudze Azure Search

A **sugestora** to konstrukcja w [indeksu usługi Azure Search](search-what-is-an-index.md) , która obsługuje środowisko "search jako — możesz type". Zawiera listę pól, dla których chcesz włączyć typeahead zapytania w danych wejściowych. Istnieją dwa warianty typeahead: [ *autouzupełniania* ](search-autocomplete-tutorial.md) kończy termin lub frazę wpisywania [ *automatycznego sugerowania* ](search-autosuggest-example.md) zawiera krótki Lista terminów ani fraz, które można wybrać jako zapytanie wprowadzania. Zauważyliśmy już bez wątpienia te zachowania przed w komercyjnych wyszukiwarek.

![Porównanie Visual Autouzupełnianie i automatycznego sugerowania](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Visual porównanie Autouzupełnianie i automatycznego sugerowania")

Aby zaimplementować te zachowania w usłudze Azure Search, jest składnikiem indeksów i zapytań. 

+ W ramach indeksu należy dodać sugestora. Można użyć portalu, interfejsu API REST lub zestawu .NET SDK, aby utworzyć sugestora. 

+ W zapytaniu Określ akcję automatycznego sugerowania lub automatycznego uzupełniania. 

> [!Important]
> Autouzupełnianie jest obecnie dostępna w wersji zapoznawczej, dostępna w wersji zapoznawczej interfejsów API REST i zestawu SDK platformy .NET i nie są obsługiwane przez aplikacje produkcyjne. 

Obsługa Typeahead jest włączona na poszczególnych pól. Można zaimplementować zarówno zachowania typeahead w obrębie tego samego rozwiązania wyszukiwania, chcąc środowisko podobny do wskazanej na zrzucie ekranu. Zarówno docelowego żądania *dokumenty* kolekcji określonego indeksu i odpowiedzi są zwracane po użytkownik udostępnił co najmniej trzech wejściowy ciąg znaków.

## <a name="create-a-suggester"></a>Utwórz sugestora

Mimo że sugestora ma kilka właściwości, to przede wszystkim zbiór pól, dla których jest włączane w środowisku typeahead. Na przykład aplikacji turystycznej chcieć umożliwić wyszukiwanie typeahead miejsc docelowych, miasta i atrakcje. W efekcie wszystkie trzy pola musieli przejść z kolekcji pól.

Aby utworzyć sugestora, dodaj je do schematu indeksu. Jeden sugestora może mieć w ramach indeksu (w szczególności jeden sugestora w kolekcji sugestory). 

W interfejsie API REST można dodać sugestory za pośrednictwem [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) lub [Aktualizowanie indeksu](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

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

Klucz wskazuje informacja dotycząca sugestory jest nazwę (sugestory są przywoływane przez nazwę, na żądanie), searchMode (obecnie tylko jeden "analyzingInfixMatching") i listę pól, dla których włączono typeahead. 

Po utworzeniu sugestora Dodaj [sugestie dotyczące interfejsów API](https://docs.microsoft.com/rest/api/searchservice/suggestions) w logice zapytania, aby wywołać tę funkcję.

### <a name="property-reference"></a>Odwołania do właściwości

Właściwości, które definiują sugestora są następujące:

|Właściwość      |Opis      |
|--------------|-----------------|
|`name`        |Nazwa sugestora. Użyj nazwy sugestora podczas wywoływania [interfejsu API REST sugestie](https://docs.microsoft.com/rest/api/searchservice/suggestions) lub [autouzupełniania REST API (wersja zapoznawcza)](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |Strategia używana do wyszukiwania fraz kandydujących. To jedyny obecnie obsługiwany tryb `analyzingInfixMatching`, który przeprowadza elastyczne dopasowywanie fraz na początku lub w środku zdań.|
|`sourceFields`|Lista jednego lub więcej pól, które są źródłem zawartości sugestie. Tylko pola typu `Edm.String` i `Collection(Edm.String)` może być źródeł dla sugestie. Można tylko pola, które nie mają niestandardowego analizatora języków zestawu.<p/>Te pola, które nadają się do oczekiwanego i odpowiednich odpowiedzi, należy określić, czy jest ono ukończone ciąg w pasku wyszukiwania lub na liście rozwijanej.<p/>Nazwa hotelu jest dobrym kandydatem, ponieważ ma ona dokładności. Pełne pól, takich jak opisy i komentarze są zbyt gęste. Podobnie powtarzających się pola, takie jak kategorie i tagów, są mniej skuteczne. W przykładach zawiera "category" mimo to wykazać, że może zawierać wiele pól. |

### <a name="index-rebuilds-for-existing-fields"></a>Indeks odbudowuje dla istniejących pól

Sugestory zawierać pola, a jeśli w przypadku dodawania sugestora do istniejącego indeksu lub zmiana jego skład pola, najprawdopodobniej konieczne będzie odbudowanie indeksu.

| Akcja | Wpływ |
|--------|--------|
| Utwórz nowe pola a nowy sugestora równocześnie w tej samej aktualizacji | Wpływ na co najmniej. Jeśli indeks zawiera wcześniej dodane pola, dodawanie nowych pól i nowych sugestora nie ma wpływu na istniejące pola. |
| Dodaj istniejące pola do sugestora | O dużym znaczeniu. Dodawanie pola zmienia definicję pola, wymagających [pełnej rekompilacji](search-howto-reindex.md).|

## <a name="use-a-suggester"></a>Użyj sugestora

Jak wspomniano wcześniej można użyć sugestora dla automatycznego sugerowania i/lub automatycznego uzupełniania. 

Sugestora odwołuje się na żądanie, wraz z operacji. Na przykład na wywołanie GET REST, określ opcję `suggest` lub `autocomplete` w kolekcji dokumentów. REST, po utworzeniu sugestora użytku [sugestie dotyczące interfejsów API](https://docs.microsoft.com/rest/api/searchservice/suggestions) lub [autouzupełniania interfejsu API (wersja zapoznawcza)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) w logice zapytania.

Dla platformy .NET, należy użyć [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) lub [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Przykłady pokazujące każdego żądania:

+ [Dodaj automatycznego sugerowania zaznaczeń zapytań listy rozwijanej](search-autosuggest-example.md)

+ [Dodawanie funkcji autouzupełniania do wejść częściowe termin w usłudze Azure Search](search-autocomplete-tutorial.md) (w wersji zapoznawczej) 

## <a name="sample-code"></a>Przykładowy kod

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) zawiera przykładowe C# i kod Java i pokazuje konstrukcji sugestora, automatyczne sugerowanie Autouzupełnianie i nawigacji zestawu reguł. 

Używa piaskownicy usługi Azure Search i wstępnie załadowane indeksu, dzięki czemu wszystkie trzeba jest naciśnij klawisz F5, aby go uruchomić. Brak subskrypcji i logowania w razie potrzeby.

## <a name="next-steps"></a>Kolejne kroki

Przejrzyj poniższe przykłady, aby zobaczyć, jak są formułowane żądania:

+ [Przykład autosuggested zapytania](search-autosuggest-example.md) 
+ [Przykład zapytania Autouzupełniania (wersja zapoznawcza)](search-autocomplete-tutorial.md) 
