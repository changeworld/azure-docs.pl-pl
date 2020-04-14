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
ms.openlocfilehash: d40d4cfe1b86448f1e8df307013905d69f203dcd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261061"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Tworzenie sugestatora w celu umożliwienia autouzupełnienia i sugerowanych wyników w kwerendzie

W usłudze Azure Cognitive Search opcja "wyszukiwanie zgodnie z typem" jest włączona za pomocą konstruktora **sugestywnyego** dodanego do [indeksu wyszukiwania.](search-what-is-an-index.md) Sugestator obsługuje dwa środowiska: *autouzupełnianie*, które kończy termin lub frazę, oraz *sugestie,* które zwracają krótką listę pasujących dokumentów.  

Poniższy zrzut ekranu z [Tworzenie pierwszej aplikacji w języku C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustruje zarówno. Autouzupełnienie przewiduje potencjalny termin, kończąc "tw" z "in". Sugestie to mini wyniki wyszukiwania, w których pole takie jak nazwa hotelu reprezentuje pasujący dokument wyszukiwania w hotelu z indeksu. W przypadku sugestii można wystawić dowolne pole, które zawiera informacje opisowe.

![Wizualne porównanie autouzupełniania i sugerowanych zapytań](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Wizualne porównanie autouzupełniania i sugerowanych zapytań")

Te funkcje można używać oddzielnie lub razem. Aby zaimplementować te zachowania w usłudze Azure Cognitive Search, istnieje składnik indeksu i kwerendy. 

+ W indeksie dodaj sugestywny do indeksu. Można użyć portalu, [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index)lub [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Dalsza część artykułu koncentruje się na tworzeniu sugesty.

+ W żądaniu kwerendy należy wywołać jeden z [interfejsów API wymienionych poniżej](#how-to-use-a-suggester).

Obsługa wyszukiwania zgodnie z typem jest włączona dla pól ciągów dla pól ciągów według typu "wyszukiwanie". Można zaimplementować zarówno zachowania typuhead w ramach tego samego rozwiązania wyszukiwania, jeśli chcesz doświadczenie podobne do wskazanego na zrzucie ekranu. Oba żądania są przeznaczone dla *kolekcji dokumentów* określonego indeksu i odpowiedzi są zwracane po użytkownik dostarczył co najmniej trzy znakowy ciąg wejściowy.

## <a name="what-is-a-suggester"></a>Co to jest sugest?

Sugestywny jest strukturą danych, która obsługuje zachowania typu wyszukiwania jako typ, przechowując prefiksy do dopasowywania w kwerendach częściowych. Podobnie jak w przypadku terminów tokenizowanych, prefiksy są przechowywane w odwróconych indeksach, po jednym dla każdego pola określonego w kolekcji pól sugestywnych.

Podczas tworzenia prefiksów sugestator ma własny łańcuch analizy, podobny do tego używanego do wyszukiwania pełnotekstowego. Jednak w przeciwieństwie do analizy w wyszukiwaniu pełnotekstowym, sugest może działać tylko nad polami, które używają standardowego analizatora Lucene (domyślnie) lub [analizatora języka.](index-add-language-analyzers.md) Pola, które używają [analizatorów niestandardowych](index-add-custom-analyzers.md) lub [wstępnie zdefiniowanych analizatorów](index-add-custom-analyzers.md#predefined-analyzers-reference) (z wyjątkiem standardowego Lucene) są jawnie zabronione, aby zapobiec słabym wynikom.

> [!NOTE]
> Jeśli chcesz obejść ograniczenie analizatora, użyj dwóch oddzielnych pól dla tej samej zawartości. Pozwoli to na jedno z pól, aby mieć sugest, podczas gdy inne mogą być skonfigurowane za pomocą konfiguracji analizatora niestandardowego.

## <a name="define-a-suggester"></a>Definiowanie sugestatora

Chociaż sugestator ma kilka właściwości, jest to przede wszystkim zbiór pól, dla których są włączanie wyszukiwania jako typ środowiska. Na przykład aplikacja turystyczna może chcieć włączyć autouzupełnienie miejsc docelowych, miast i atrakcji. W związku z tym wszystkie trzy pola pójdą w kolekcji pól.

Aby utworzyć sugest, dodaj go do schematu indeksu. Możesz mieć jeden sugest w indeksie (w szczególności jeden sugest w kolekcji sugestów). Sugestator przyjmuje listę pól. 

+ W przypadku sugestii wybierz pola, które najlepiej reprezentują pojedynczy wynik. Nazwy, tytuły lub inne unikatowe pola, które rozróżniają dokumenty, działają najlepiej. Jeśli pola składają się z podobnych lub identycznych wartości, sugestie będą składać się z identycznych wyników, a użytkownik nie będzie wiedział, który z nich kliknie.

+ Upewnij się, że każde `sourceFields` pole na liście sugestywnych używa`"analyzer": null`domyślnego standardowego analizatora `"analyzer": "en.Microsoft"`Lucene ( ) lub [analizatora języka](index-add-language-analyzers.md) (na przykład ). 

  Wybór analizatora określa sposób tokenizacji pól, a następnie poprecysowania. Na przykład dla ciągu dzielonego, takich jak "kontekstowe", przy użyciu analizatora języka spowoduje te kombinacje tokenów: "context", "sensitive", "context-sensitive". Gdyby użyto standardowego analizatora Lucene, ciąg dzielenia wyrazów nie istniałby.

> [!TIP]
> Należy rozważyć użycie [interfejsu API analizy tekstu, aby](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) uzyskać wgląd w sposób tokenizacji terminów, a następnie poprecysowania. Po utworzeniu indeksu, można wypróbować różne analizatory na ciąg, aby wyświetlić tokeny, które emituje.

### <a name="when-to-create-a-suggester"></a>Kiedy utworzyć sugest

Najlepszy czas, aby utworzyć sugest jest, gdy są również tworzenie definicji pola się.

Jeśli spróbujesz utworzyć sugest przy użyciu wcześniej istniejących pól, interfejs API nie zezwala na to. Prefiksy są generowane podczas indeksowania, gdy częściowe terminy w dwóch lub więcej kombinacji znaków są tokenizowane obok całych terminów. Biorąc pod uwagę, że istniejące pola są już tokenizowane, trzeba będzie odbudować indeks, jeśli chcesz dodać je do sugesty. Aby uzyskać więcej informacji, zobacz [Jak odbudować indeks usługi Azure Cognitive Search](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Tworzenie przy użyciu interfejsu API REST

W interfejsie API REST dodaj sugestywne za pomocą [programu Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) lub Update [Index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
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
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
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
|`sourceFields`|Lista co najmniej jednego pola, które są źródłem zawartości sugestii. Pola muszą być `Edm.String` `Collection(Edm.String)`typu i . Jeśli analizator jest określony w polu, musi to być nazwany analizator z [tej listy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (nie analizatora niestandardowego).<p/> Najlepszym rozwiązaniem jest określenie tylko tych pól, które nadają się do oczekiwanej i odpowiedniej odpowiedzi, niezależnie od tego, czy jest to ukończony ciąg na pasku wyszukiwania, czy na liście rozwijanej.<p/>Nazwa hotelu jest dobrym kandydatem, ponieważ ma precyzję. Pełne pola, takie jak opisy i komentarze, są zbyt gęste. Podobnie powtarzające się pola, takie jak kategorie i tagi, są mniej skuteczne. W przykładach i tak uwzględniamy "kategorię", aby wykazać, że można dołączyć wiele pól. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Korzystanie z sugestatora

Sugestywny jest używany w kwerendzie. Po utworzeniu sugestatora wywołaj odpowiedni interfejs API w logice kwerendy, aby wywołać tę funkcję. 

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
