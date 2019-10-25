---
title: Dodawanie zapytań typeahead do indeksu
titleSuffix: Azure Cognitive Search
description: Włącz akcje kwerendy typu "w Wyszukiwanie poznawcze na platformie Azure", tworząc sugerowane i formułowane żądania, które wywołują Autouzupełnianie lub proponowane automatycznie terminy zapytań.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: a312068d5c8c574e7b069263cf37e3b855810e4b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790099"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>Dodawanie sugestii do indeksu dla typeahead na platformie Azure Wyszukiwanie poznawcze

Na platformie Azure Wyszukiwanie poznawcze funkcje "Search-as-Type" lub "typeahead" opierają się na konstrukcji **sugerującej** , która została dodana do [indeksu wyszukiwania](search-what-is-an-index.md). Jest to lista co najmniej jednego pola, dla którego ma zostać włączone typeahead.

Program sugerujący obsługuje dwie typeahead wariantów: *Autouzupełnianie*, które kończy wpisywany termin lub frazę, oraz *sugestie* , które zwracają krótką listę pasujących dokumentów.  

Poniższy zrzut ekranu, z przykładu [Tworzenie pierwszej aplikacji w C# ](tutorial-csharp-type-ahead-and-suggestions.md) przykładzie, ilustruje typeahead. Funkcja autouzupełniania przewiduje, co użytkownik może wpisać w polu wyszukiwania. Rzeczywiste dane wejściowe to "TW", którego Autouzupełnianie kończy się znakiem "in", w rozwiązaniu jako "bliźniaczy" jako wyszukiwany termin. Sugestie są wizualizowane na liście rozwijanej. W przypadku sugestii można wystawić każdą część dokumentu, która najlepiej opisuje wynik. W tym przykładzie sugestie są nazwami hotelu. 

![Wizualne porównanie autouzupełniania i sugerowanych zapytań](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Wizualne porównanie autouzupełniania i sugerowanych zapytań")

Aby zaimplementować te zachowania w usłudze Azure Wyszukiwanie poznawcze, istnieje indeks i składnik zapytania. 

+ W indeksie Dodaj sugestię do indeksu. Możesz użyć portalu, [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)lub [zestawu .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Pozostała część tego artykułu koncentruje się na tworzeniu sugestii. 

+ W żądaniu zapytania Zadzwoń do jednego z [interfejsów API wymienionych poniżej](#how-to-use-a-suggester).

Obsługa wyszukiwania jako typu jest włączana dla poszczególnych pól. Można zaimplementować oba zachowania typeahead w ramach tego samego rozwiązania wyszukiwania, jeśli chcesz, aby środowisko było podobne do określonego na zrzucie ekranu. Oba żądania są kierowane do kolekcji *dokumenty* określonego indeksu i odpowiedzi są zwracane po podaniu przez użytkownika co najmniej trzech ciągów wejściowych znaku.

## <a name="create-a-suggester"></a>Tworzenie sugestora

Chociaż program sugerujący ma kilka właściwości, jest głównie kolekcją pól, dla których jest włączone środowisko typeahead. Na przykład aplikacja podróży może chcieć włączyć typeahead wyszukiwanie w miejscach docelowych, miejscowości i attractions. W związku z tym wszystkie trzy pola zostałyby umieszczone w kolekcji Fields.

Aby utworzyć sugestię, Dodaj ją do schematu indeksu. W indeksie może znajdować się jeden element sugerujący (w przypadku kolekcji sugerującej). 

### <a name="when-to-create-a-suggester"></a>Kiedy należy utworzyć sugestię

Najlepszym terminem tworzenia sugestii jest również utworzenie samej definicji pola.

Jeśli spróbujesz utworzyć sugestię przy użyciu istniejących pól, interfejs API nie będzie go zezwalać. Typeahead tekst jest tworzony podczas indeksowania, gdy częściowe warunki w dwóch lub większej liczbie znaków są rozliczanie tokenów obok całych warunków. Mając na względzie, że istniejące pola są już tokenami, należy ponownie skompilować indeks, jeśli chcesz dodać je do sugestii. Aby uzyskać więcej informacji na temat ponownego indeksowania, zobacz [jak ponownie skompilować indeks wyszukiwanie poznawcze platformy Azure](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Tworzenie przy użyciu interfejsu API REST

W interfejsie API REST Dodaj sugestie za pomocą pozycji [Utwórz indeks](https://docs.microsoft.com/rest/api/searchservice/create-index) lub [Aktualizuj indeks](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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


### <a name="create-using-the-net-sdk"></a>Tworzenie przy użyciu zestawu .NET SDK

W C#programie Zdefiniuj [obiekt sugerujący](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` jest kolekcją, ale może przyjmować tylko jeden element. 

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

### <a name="property-reference"></a>Odwołanie do właściwości

|Właściwość      |Opis      |
|--------------|-----------------|
|`name`        |Nazwa sugestii.|
|`searchMode`  |Strategia używana do wyszukiwania fraz kandydatów. Jedynym obsługiwanym trybem jest `analyzingInfixMatching`, który wykonuje elastyczne Dopasowywanie fraz na początku lub w środku zdania.|
|`sourceFields`|Lista co najmniej jednego pola, które jest źródłem zawartości dla sugestii. Pola muszą być typu `Edm.String` i `Collection(Edm.String)`. Jeśli analizator jest określony w polu, musi to być nazwany Analizator z [tej listy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (nie Analizator niestandardowy).<p/>Najlepszym rozwiązaniem jest określenie tylko tych pól, które nadają się do oczekiwanej i odpowiedniej odpowiedzi, niezależnie od tego, czy jest to kompletny ciąg na pasku wyszukiwania, czy na liście rozwijanej.<p/>Nazwa hotelu jest dobrym kandydatem, ponieważ ma dokładnooć. Pełne pola, takie jak opisy i komentarze, są zbyt gęste. Podobnie powtarzające się pola, takie jak kategorie i Tagi, są mniej efektywne. W przykładach zawieramy "kategorię" Mimo to, aby pokazać, że można uwzględnić wiele pól. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>Ograniczenia analizatora dla sourceFields w sugestii

Usługa Azure Wyszukiwanie poznawcze analizuje zawartość pola, aby umożliwić wykonywanie zapytań dotyczących poszczególnych warunków. Sugestie wymagają, aby prefiksy były indeksowane wraz z pełnymi postanowieniami, które wymagają dodatkowej analizy względem pól źródłowych. Konfiguracje analizatora niestandardowego mogą łączyć dowolne z różnych tokenizatory i filtrów, często w sposób umożliwiający tworzenie prefiksów wymaganych dla sugestii. Z tego powodu usługa Azure Wyszukiwanie poznawcze uniemożliwia uwzględnienie pól z niestandardowymi analizatorami.

> [!NOTE] 
>  Jeśli musisz obejść powyższe ograniczenie, użyj dwóch oddzielnych pól dla tej samej zawartości. Pozwoli to jednemu z pól na posiadanie sugestii, podczas gdy drugi można skonfigurować przy użyciu konfiguracji analizatora niestandardowego.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Używanie sugestii w zapytaniu

Po utworzeniu sugestii Wywołaj odpowiedni interfejs API w logice zapytania, aby wywołać funkcję. 

+ [Sugestie interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Autouzupełnianie interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync, Metoda](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync, Metoda](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

Użycie interfejsu API jest zilustrowane w następującym wywołaniu interfejsu API REST autouzupełniania. W tym przykładzie istnieją dwa wnioski. Po pierwsze, podobnie jak w przypadku wszystkich zapytań, operacja jest względem kolekcji dokumentów indeksu. Następnie można dodać parametry zapytania. Chociaż wiele parametrów zapytania jest wspólnych dla obu interfejsów API, lista jest różna dla każdej z nich.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Jeśli nie zdefiniowano sugestii w indeksie, wywołanie funkcji Autouzupełnianie lub sugestii zakończy się niepowodzeniem.

## <a name="sample-code"></a>Przykładowy kod

+ [Tworzenie pierwszej aplikacji w C# ](tutorial-csharp-type-ahead-and-suggestions.md) przykładzie pokazuje, jak konstrukcja sugerująca, sugerowane zapytania, Autouzupełnianie i nawigacja aspektów. Ten przykładowy kod jest uruchamiany w usłudze Azure Wyszukiwanie poznawcze piaskownicy i używa wstępnie załadowanego indeksu hoteli, więc wystarczy nacisnąć klawisz F5, aby uruchomić aplikację. Żadna subskrypcja ani logowanie nie są wymagane.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) jest starszym przykładem zawierającym kod języka C# i Java. Przedstawiono w nim także zasugerowaną konstrukcję, sugerowane zapytania, Autouzupełnianie i nawigację aspektową. Ten przykładowy kod używa hostowanych danych [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) . 


## <a name="next-steps"></a>Następne kroki

Zalecamy następujący przykład, aby zobaczyć, jak są formułowane żądania.

> [!div class="nextstepaction"]
> [Sugestie i przykłady autouzupełniania](search-autocomplete-tutorial.md) 
