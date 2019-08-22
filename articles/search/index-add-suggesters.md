---
title: Dodawanie zapytań typeahead do indeksu Azure Search
description: Włącz akcje zapytań typu "w Azure Search", tworząc sugestie i formułowane żądania, które wywołują Autouzupełnianie lub proponowane automatycznie terminy zapytań.
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 73cfdb6a4185689a6485f55a4f6bdd1e7e3b14be
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648839"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Dodawanie sugestii do indeksu dla typeahead w Azure Search

**Sugerował** jest konstrukcja w [indeksie Azure Search](search-what-is-an-index.md) , który obsługuje środowisko "wyszukiwanie jako typ". Zawiera listę pól, dla których chcesz włączyć dane wejściowe zapytania typeahead. W indeksie ta sama sugestia obsługuje jedną lub obie te dwa warianty typeahead: *Autouzupełnianie* uzupełnia wpisany termin lub frazę. *sugestie* przedstawiają krótką listę wyników. 

Poniższy zrzut ekranu ilustruje obie funkcje typeahead. Na tej stronie wyszukiwania w usłudze Xbox elementy autouzupełniania przenoszą Cię do nowej strony wyników wyszukiwania dla tego zapytania, a sugestie są rzeczywistymi wynikami, które przenoszą do strony tej konkretnej gry. Można ograniczyć Autouzupełnianie do jednego elementu na pasku wyszukiwania lub podać listę, taką jak pokazana tutaj. W przypadku sugestii można wystawić każdą część dokumentu, która najlepiej opisuje wynik.

![Wizualne porównanie autouzupełniania i sugerowanych zapytań](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Wizualne porównanie autouzupełniania i sugerowanych zapytań")

Aby zaimplementować te zachowania w Azure Search, istnieje indeks i składnik zapytania. 

+ Składnik indeksu jest sugestią. Możesz użyć portalu, interfejsu API REST lub zestawu .NET SDK, aby utworzyć sugestię. 

+ Składnik zapytania jest akcją określoną w żądaniu zapytania (akcję sugestii lub autouzupełniania). 

Obsługa wyszukiwania jako typu jest włączana dla poszczególnych pól. Można zaimplementować oba zachowania typeahead w ramach tego samego rozwiązania wyszukiwania, jeśli chcesz, aby środowisko było podobne do określonego na zrzucie ekranu. Oba żądania są kierowane do kolekcji *dokumenty* określonego indeksu i odpowiedzi są zwracane po podaniu przez użytkownika co najmniej trzech ciągów wejściowych znaku.

## <a name="create-a-suggester"></a>Tworzenie sugestora

Chociaż program sugerujący ma kilka właściwości, jest głównie kolekcją pól, dla których jest włączone środowisko typeahead. Na przykład aplikacja podróży może chcieć włączyć typeahead wyszukiwanie w miejscach docelowych, miejscowości i attractions. W związku z tym wszystkie trzy pola zostałyby umieszczone w kolekcji Fields.

Aby utworzyć sugestię, Dodaj ją do schematu indeksu. W indeksie może znajdować się jeden element sugerujący (w przypadku kolekcji sugerującej). 

### <a name="use-the-rest-api"></a>Używanie interfejsu API REST

W interfejsie API REST można dodać sugestie za pomocą pozycji [Utwórz indeks](https://docs.microsoft.com/rest/api/searchservice/create-index) lub [Aktualizuj indeks](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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
Po utworzeniu sugestii Dodaj [interfejs API sugestii](https://docs.microsoft.com/rest/api/searchservice/suggestions) lub [interfejs API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) autouzupełniania w logice zapytania, aby wywołać funkcję.

### <a name="use-the-net-sdk"></a>Korzystanie z zestawu SDK dla platformy .NET

W C#programie Zdefiniuj [obiekt sugerujący](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`jest kolekcją, ale może przyjmować tylko jeden element. 

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

Kluczowe punkty, które należy zwrócić uwagę na temat sugestii, to to, że w żądaniu występuje nazwa (sugestii odwołująca się do niej nazwa), typu wyszukiwania (obecnie tylko jeden, "analyzingInfixMatching") i listy pól, dla których włączono typeahead. 

Właściwości definiujące funkcję sugerują obejmują następujące elementy:

|Właściwość      |Opis      |
|--------------|-----------------|
|`name`        |Nazwa sugestii. Podczas wywoływania [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/suggestions) lub [interfejsu API REST usługi Autouzupełnianie](https://docs.microsoft.com/rest/api/searchservice/autocomplete)należy użyć nazwy narzędzia do sugerowania.|
|`searchMode`  |Strategia używana do wyszukiwania fraz kandydatów. Jedynym obsługiwanym trybem jest `analyzingInfixMatching`, który wykonuje elastyczne Dopasowywanie fraz na początku lub w środku zdania.|
|`sourceFields`|Lista co najmniej jednego pola, które jest źródłem zawartości dla sugestii. Tylko pola typu `Edm.String` i `Collection(Edm.String)` mogą być źródłami dla sugestii. Można używać tylko pól, które nie mają niestandardowego zestawu analizatora języka.<p/>Określ tylko pola, które nadają się do oczekiwanej i odpowiedniej odpowiedzi, czy jest to kompletny ciąg na pasku wyszukiwania lub liście rozwijanej.<p/>Nazwa hotelu jest dobrym kandydatem, ponieważ ma dokładnooć. Pełne pola, takie jak opisy i komentarze, są zbyt gęste. Podobnie powtarzające się pola, takie jak kategorie i Tagi, są mniej efektywne. W przykładach zawieramy "kategorię" Mimo to, aby pokazać, że można uwzględnić wiele pól. |

#### <a name="analysis-of-sourcefields-in-a-suggester"></a>Analiza SourceFields w sugestii

Azure Search analizuje zawartość pola, aby umożliwić wykonywanie zapytań dotyczących poszczególnych warunków. Sugestie wymagają, aby prefiksy były indeksowane wraz z pełnymi postanowieniami, które wymagają dodatkowej analizy względem pól źródłowych. Konfiguracje analizatora niestandardowego mogą łączyć dowolne z różnych tokenizatory i filtrów, często w sposób umożliwiający tworzenie prefiksów wymaganych dla sugestii. Z tego powodu **Azure Search uniemożliwiają uwzględnienie pól z**niestandardowymi analizatorami.

> [!NOTE] 
>  Zalecanym podejściem do obejścia powyższych ograniczeń jest użycie 2 oddzielnych pól dla tej samej zawartości. Pozwoli to jednemu z pól na posiadanie sugestii, a drugi można skonfigurować przy użyciu konfiguracji analizatora niestandardowego.

## <a name="when-to-create-a-suggester"></a>Kiedy należy utworzyć sugestię

Aby uniknąć ponownego kompilowania indeksu, należy utworzyć sugestię i pola określone `sourceFields` w elemencie w tym samym czasie.

Jeśli dodajesz sugestię do istniejącego indeksu, w którym znajdują się istniejące pola w `sourceFields`, definicja pola zostanie zmieniona, a ponowna kompilacja jest wymagana. Aby uzyskać więcej informacji, zobacz [jak ponownie skompilować indeks Azure Search](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Jak używać sugestii

Jak wspomniano wcześniej, można użyć sugestii dla sugerowanych zapytań, autouzupełniania lub obu tych funkcji. 

Do żądania jest przywoływany sugestia wraz z operacją. Na przykład w wywołaniu Get REST Określ albo `suggest` `autocomplete` w kolekcji dokumentów. W przypadku usługi REST po utworzeniu sugestii Użyj [interfejsu API sugestii](https://docs.microsoft.com/rest/api/searchservice/suggestions) lub [interfejsu API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) autouzupełniania w logice zapytania.

W przypadku platformy .NET Użyj [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet) lub [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet).

Przykład pokazujący oba żądania, zobacz [przykład dodawania autouzupełniania i sugestii w Azure Search](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Przykładowy kod

Przykład [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) zawiera kod zarówno C# , jak i Java, a także demonstruje konstruowanie, sugerowane zapytania, Autouzupełnianie i nawigację aspektów. 

Korzysta ona z usługi Azure Search piaskownicy i wstępnie załadowanego indeksu, więc wystarczy nacisnąć klawisz F5, aby go uruchomić. Brak subskrypcji lub logowania.

## <a name="next-steps"></a>Następne kroki

Zalecamy następujący przykład, aby zobaczyć, jak są formułowane żądania.

> [!div class="nextstepaction"]
> [Sugestie i przykłady autouzupełniania](search-autocomplete-tutorial.md) 
