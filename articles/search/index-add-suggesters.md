---
title: Tworzenie sugestora
titleSuffix: Azure Cognitive Search
description: Włącz akcje zapytania z wyprzedzeniem typu w usłudze Azure Cognitive Search, tworząc sugesty i formułując żądania, które wywołują terminy zapytania autouzupełniania lub automatycznego sugerowania.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: a6c4051a5b3d557f9ac2927a62492425e7636c0d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113482"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggestions-in-azure-cognitive-search"></a>Tworzenie sugestatora umożliwiającego automatyczne kompletowanie i sugestie w usłudze Azure Cognitive Search

W usłudze Azure Cognitive Search funkcja "wyszukiwanie zgodnie z typem" lub funkcja typeahead jest oparta na konstrukcji **sugestywnej,** którą można dodać do [indeksu wyszukiwania.](search-what-is-an-index.md) Sugestant obsługuje dwa warianty wyszukiwania zgodnie z typem: *autouzupełnianie*, które kończy wpisywany termin lub frazę, oraz *sugestie,* które zwracają krótką listę pasujących dokumentów.  

Poniższy zrzut ekranu, z [Tworzenie pierwszej aplikacji w c#](tutorial-csharp-type-ahead-and-suggestions.md) przykładu, ilustruje oba środowiska. Autouzupełnianie przewiduje, co użytkownik może wpisać, kończąc "tw" z "in" jako potencjalny termin wyszukiwania. Sugestie są rzeczywistymi wynikami wyszukiwania, z których każdy reprezentuje pasujący dokument. W przypadku sugestii można wyeksliować dowolną część dokumentu, która najlepiej opisuje wynik. W tym przykładzie sugestie są reprezentowane przez pole nazwy hotelu.

![Wizualne porównanie autouzupełniania i sugerowanych zapytań](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Wizualne porównanie autouzupełniania i sugerowanych zapytań")

Aby zaimplementować te zachowania w usłudze Azure Cognitive Search, istnieje składnik indeksu i kwerendy. 

+ W indeksie dodaj sugestywny do indeksu. Można użyć portalu, [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)lub [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Dalsza część artykułu koncentruje się na tworzeniu sugesty.

+ W żądaniu kwerendy należy wywołać jeden z [interfejsów API wymienionych poniżej](#how-to-use-a-suggester).

Obsługa wyszukiwania zgodnie z typem jest włączona dla w zależności od pola. Można zaimplementować zarówno zachowania typuhead w ramach tego samego rozwiązania wyszukiwania, jeśli chcesz doświadczenie podobne do wskazanego na zrzucie ekranu. Oba żądania są przeznaczone dla *kolekcji dokumentów* określonego indeksu i odpowiedzi są zwracane po użytkownik dostarczył co najmniej trzy znakowy ciąg wejściowy.

## <a name="what-is-a-suggester"></a>Co to jest sugest?

Sugestywny jest strukturą danych, która obsługuje zachowania typu wyszukiwania jako typ, przechowując prefiksy do dopasowywania w kwerendach częściowych. Podobnie jak w przypadku terminów tokenizowanych, prefiksy są przechowywane w odwróconych indeksach, po jednym dla każdego pola określonego w kolekcji pól sugestywnych.

Podczas tworzenia prefiksów sugestator ma własny łańcuch analizy, podobny do tego używanego do wyszukiwania pełnotekstowego. Jednak w przeciwieństwie do analizy w wyszukiwaniu pełnotekstowym, sugestator może używać tylko wstępnie zdefiniowanych analizatorów (standardowe lucene, [analizatory języka](index-add-language-analyzers.md)lub inne analizatory na [wstępnie zdefiniowanej liście analizatorów.](index-add-custom-analyzers.md#predefined-analyzers-reference) [Niestandardowe analizatory](index-add-custom-analyzers.md) i konfiguracje są specjalnie niedozwolone, aby uniknąć losowych konfiguracji, które dają słabe wyniki.

> [!NOTE]
> Jeśli chcesz obejść ograniczenie analizatora, użyj dwóch oddzielnych pól dla tej samej zawartości. Pozwoli to na jedno z pól, aby mieć sugest, podczas gdy inne mogą być skonfigurowane za pomocą konfiguracji analizatora niestandardowego.

## <a name="create-a-suggester"></a>Tworzenie sugestora

Chociaż sugest ma kilka właściwości, jest to przede wszystkim zbiór pól, dla których są włączanie środowiska typeahead. Na przykład aplikacja turystyczna może chcieć włączyć wyszukiwanie typu w celach docelowych, miastach i atrakcjach. W związku z tym wszystkie trzy pola pójdą w kolekcji pól.

Aby utworzyć sugest, dodaj go do schematu indeksu. Możesz mieć jeden sugest w indeksie (w szczególności jeden sugest w kolekcji sugestów).

### <a name="when-to-create-a-suggester"></a>Kiedy utworzyć sugest

Najlepszy czas, aby utworzyć sugest jest, gdy są również tworzenie definicji pola się.

Jeśli spróbujesz utworzyć sugest przy użyciu wcześniej istniejących pól, interfejs API nie zezwala na to. Tekst typu w nagłówku jest tworzony podczas indeksowania, gdy częściowe terminy w dwóch lub więcej kombinacji znaków są tokenizowane obok całych terminów. Biorąc pod uwagę, że istniejące pola są już tokenizowane, trzeba będzie odbudować indeks, jeśli chcesz dodać je do sugesty. Aby uzyskać więcej informacji na temat ponownego indeksowania, zobacz [Jak odbudować indeks usługi Azure Cognitive Search](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Tworzenie przy użyciu interfejsu API REST

W interfejsie API REST dodaj sugestywne za pomocą [programu Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) lub Update [Index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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

### <a name="create-using-the-net-sdk"></a>Tworzenie przy użyciu sdk .NET

W języku C#zdefiniuj [obiekt sugestywny](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`jest kolekcją, ale może zająć tylko jeden element. 

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
|`name`        |Nazwa sugestatora.|
|`searchMode`  |Strategia używana do wyszukiwania zwrotów kandydatów. Jedynym trybem obecnie obsługiwanym jest `analyzingInfixMatching`, który wykonuje elastyczne dopasowywanie fraz na początku lub w środku zdań.|
|`sourceFields`|Lista co najmniej jednego pola, które są źródłem zawartości sugestii. Pola muszą być `Edm.String` `Collection(Edm.String)`typu i . Jeśli analizator jest określony w polu, musi to być nazwany analizator z [tej listy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (nie analizatora niestandardowego).<p/>Najlepszym rozwiązaniem jest określenie tylko tych pól, które nadają się do oczekiwanej i odpowiedniej odpowiedzi, niezależnie od tego, czy jest to ukończony ciąg na pasku wyszukiwania, czy na liście rozwijanej.<p/>Nazwa hotelu jest dobrym kandydatem, ponieważ ma precyzję. Pełne pola, takie jak opisy i komentarze, są zbyt gęste. Podobnie powtarzające się pola, takie jak kategorie i tagi, są mniej skuteczne. W przykładach i tak uwzględniamy "kategorię", aby wykazać, że można dołączyć wiele pól. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Używanie sugestatora w kwerendzie

Po utworzeniu sugestatora wywołaj odpowiedni interfejs API w logice kwerendy, aby wywołać tę funkcję. 

+ [Sugestie REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Interfejs API REST autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Sugestia z metodąHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Autouzupełnienie z metodąHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

Użycie interfejsu API jest zilustrowane w poniższym wywołaniu do interfejsu API REST autouzupełnianie. Istnieją dwa wynos z tego przykładu. Po pierwsze, podobnie jak w przypadku wszystkich zapytań, operacja jest przeciwko kolekcji dokumentów indeksu. Po drugie, można dodać parametry kwerendy. Podczas gdy wiele parametrów kwerendy są wspólne dla obu interfejsów API, lista jest inna dla każdego z nich.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Jeśli sugest nie jest zdefiniowany w indeksie, wywołanie autouzupełnienia lub sugestie zakończy się niepowodzeniem.

## <a name="sample-code"></a>Przykładowy kod

+ [Tworzenie pierwszej aplikacji w](tutorial-csharp-type-ahead-and-suggestions.md) przykładzie języka C# pokazuje konstrukcję sugesty, sugerowane zapytania, autouzupełnianie i nawigacji aspektowej. Ten przykładowy kod jest uruchamiany w usłudze Azure Cognitive Search w piaskownicy i używa wstępnie załadowanego indeksu hoteli, więc wszystko, co musisz zrobić, to nacisnąć klawisz F5, aby uruchomić aplikację. Nie jest wymagana subskrypcja ani logowanie.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) jest starsza próbka zawierająca kod Języka C# i Java. Pokazuje również konstrukcję sugestywny, sugerowane zapytania, autouzupełnianie i nawigację fasetacją. Ten przykładowy kod używa hostowanych danych przykładowych [NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 


## <a name="next-steps"></a>Następne kroki

Zalecamy poniższy przykład, aby zobaczyć, jak żądania są sformułowane.

> [!div class="nextstepaction"]
> [Sugestie i przykłady autouzupełniania](search-autocomplete-tutorial.md) 
