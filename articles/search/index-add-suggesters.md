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
ms.openlocfilehash: a8bc86c2d3511fa04e595b8b2988d9a98bf084b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844447"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Dodaj sugestory do indeksu dla typeahead w usłudze Azure Search

A **sugestora** to konstrukcja w [indeksu usługi Azure Search](search-what-is-an-index.md) , która obsługuje środowisko "search jako — możesz type". Zawiera listę pól, dla których chcesz włączyć typeahead zapytania w danych wejściowych. W ramach indeksu, ten sam sugestora obsługuje jeden lub oba te dwa warianty typeahead: *autouzupełniania* kończy termin lub frazę wpisywania *sugestie* zawiera krótką listę wyników. 

Poniższy zrzut ekranu przedstawia obie funkcje typeahead. Na tej stronie wyszukiwania Xbox elementów autouzupełniania przejście do nowej strony wyników wyszukiwania dla tego zapytania sugestie są rzeczywiste wyniki, które przejście do strony dla tego konkretnego gry. Można ograniczyć autouzupełniania jednego elementu w pasku wyszukiwania lub Podaj listę, tak jak pokazano poniżej. Masz sugestie może pojawić się dowolnej części dokumentu, która najlepiej opisuje wynik.

![Porównanie Visual Autouzupełnianie i sugerowane zapytania](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Visual porównanie Autouzupełnianie i sugerowane zapytania")

Aby zaimplementować te zachowania w usłudze Azure Search, jest składnikiem indeksów i zapytań. 

+ Składnik indeksu jest sugestora. Portalu, interfejsu API REST lub zestawu SDK platformy .NET umożliwia tworzenie sugestora. 

+ Składnik kwerendy jest akcja określona w żądaniu zapytania (Akcja sugestię lub automatycznego uzupełniania). 

> [!Important]
> Autouzupełnianie jest obecnie dostępna w wersji zapoznawczej, dostępna w wersji zapoznawczej interfejsów API REST i zestawu SDK platformy .NET. Nie jest on przeznaczony dla aplikacji produkcyjnych. 

Obsługa wyszukiwania jako użytkownik typ-jest włączona na poszczególnych pól. Można zaimplementować zarówno zachowania typeahead w obrębie tego samego rozwiązania wyszukiwania, chcąc środowisko podobny do wskazanej na zrzucie ekranu. Zarówno docelowego żądania *dokumenty* kolekcji określonego indeksu i odpowiedzi są zwracane po użytkownik udostępnił co najmniej trzech wejściowy ciąg znaków.

## <a name="create-a-suggester"></a>Tworzenie sugestora

Mimo że sugestora ma kilka właściwości, to przede wszystkim zbiór pól, dla których jest włączane w środowisku typeahead. Na przykład aplikacji turystycznej chcieć umożliwić wyszukiwanie typeahead miejsc docelowych, miasta i atrakcje. W efekcie wszystkie trzy pola musieli przejść z kolekcji pól.

Aby utworzyć sugestora, dodaj je do schematu indeksu. Jeden sugestora może mieć w ramach indeksu (w szczególności jeden sugestora w kolekcji sugestory). 

### <a name="use-the-rest-api"></a>Używanie interfejsu API REST

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
Po utworzeniu sugestora Dodaj [sugestie dotyczące interfejsów API](https://docs.microsoft.com/rest/api/searchservice/suggestions) lub [interfejsu API automatycznego uzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete) w logice zapytania, aby wywołać tę funkcję.

### <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

W C#, zdefiniuj [obiektu Sugestora](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` jest to kolekcja, ale może potrwać tylko jeden element. 

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

## <a name="property-reference"></a>Odwołania do właściwości

Klucz wskazuje informacja dotycząca sugestory jest nazwę (sugestory są przywoływane przez nazwę, na żądanie), searchMode (obecnie tylko jeden "analyzingInfixMatching") i listę pól, dla których włączono typeahead. 

Właściwości, które definiują sugestora są następujące:

|Właściwość      |Opis      |
|--------------|-----------------|
|`name`        |Nazwa sugestora. Użyj nazwy sugestora podczas wywoływania [interfejsu API REST sugestie](https://docs.microsoft.com/rest/api/searchservice/suggestions) lub [autouzupełniania REST API (wersja zapoznawcza)](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |Strategia używana do wyszukiwania fraz kandydujących. To jedyny obecnie obsługiwany tryb `analyzingInfixMatching`, który przeprowadza elastyczne dopasowywanie fraz na początku lub w środku zdań.|
|`sourceFields`|Lista jednego lub więcej pól, które są źródłem zawartości sugestie. Tylko pola typu `Edm.String` i `Collection(Edm.String)` może być źródeł dla sugestie. Można tylko pola, które nie mają niestandardowego analizatora języków zestawu.<p/>Te pola, które nadają się do oczekiwanego i odpowiednich odpowiedzi, należy określić, czy jest ono ukończone ciąg w pasku wyszukiwania lub na liście rozwijanej.<p/>Nazwa hotelu jest dobrym kandydatem, ponieważ ma ona dokładności. Pełne pól, takich jak opisy i komentarze są zbyt gęste. Podobnie powtarzających się pola, takie jak kategorie i tagów, są mniej skuteczne. W przykładach zawiera "category" mimo to wykazać, że może zawierać wiele pól. |

## <a name="when-to-create-a-suggester"></a>Kiedy należy utworzyć sugestora

Aby uniknąć odbudowywanie indeksu, sugestora i pola określone w parametrze `sourceFields` musi zostać utworzona w tym samym czasie.

Jeśli dodasz sugestora do istniejącego indeksu, gdzie istniejące pola są uwzględnione w `sourceFields`, całkowicie zmienia definicję pola i ponownej kompilacji jest wymagana. Aby uzyskać więcej informacji, zobacz [sposobie odbudowania indeksu usługi Azure Search](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Jak używać sugestora

Jak wspomniano wcześniej można użyć sugestora sugerowane zapytania i/lub automatycznego uzupełniania. 

Sugestora odwołuje się na żądanie, wraz z operacji. Na przykład na wywołanie GET REST, określ opcję `suggest` lub `autocomplete` w kolekcji dokumentów. REST, po utworzeniu sugestora użytku [sugestie dotyczące interfejsów API](https://docs.microsoft.com/rest/api/searchservice/suggestions) lub [autouzupełniania interfejsu API (wersja zapoznawcza)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) w logice zapytania.

Dla platformy .NET, należy użyć [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) lub [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Aby uzyskać przykład, pokazujący zarówno w przypadku żądań, zobacz [przykład dodawania automatycznego uzupełniania i sugestii w usłudze Azure Search](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Przykładowy kod

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) zawiera przykładowe C# i kod Java i pokazuje konstrukcji sugestora, sugerowane zapytania, automatycznego uzupełniania i nawigacji zestawu reguł. 

Używa piaskownicy usługi Azure Search i wstępnie załadowane indeksu, dzięki czemu wszystkie trzeba jest naciśnij klawisz F5, aby go uruchomić. Brak subskrypcji i logowania w razie potrzeby.

## <a name="next-steps"></a>Kolejne kroki

Firma Microsoft zaleca poniższy przykład, aby zobaczyć, jak są formułowane żądania.

> [!div class="nextstepaction"]
> [Przykłady Autouzupełnianie i sugestie](search-autocomplete-tutorial.md) 
