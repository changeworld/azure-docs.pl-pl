---
title: Dodawanie autouzupełniania i sugestii w polu wyszukiwania
titleSuffix: Azure Cognitive Search
description: Włącz akcje kwerendy typu search-as-you-type w usłudze Azure Cognitive Search, tworząc sugesty i formułując żądania, które automatycznie zapełniają pole wyszukiwania z gotowymi terminami lub frazami. Możesz również zwrócić sugerowane mecze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 8b64a583c11e794c30e1de12eb66941874a25462
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262228"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Dodawanie sugestii lub autouzupełniania do aplikacji usługi Azure Cognitive Search

W tym przykładzie pokazano pole wyszukiwania, które obsługuje zachowania typu wyszukiwania. Istnieją dwie funkcje, których można używać razem lub oddzielnie:

+ *Sugestie* generują wyniki wyszukiwania podczas pisania, gdzie każda sugestia jest pojedynczym wynikiem lub dokumentem wyszukiwania z indeksu, który pasuje do tego, co do tej pory wpisałeś. 

+ *Autouzupełnienie* generuje zapytania przez "wykańczanie" wyrazu lub frazy. Zamiast zwracać wyniki, kończy kwerendę, którą można następnie wykonać, aby zwrócić wyniki. Podobnie jak w sugestiach, ukończone słowo lub fraza w kwerendzie jest uzależniona od dopasowania w indeksie. Usługa nie będzie oferować kwerend, które zwracają zero wyników w indeksie.

Przykładowy kod pokazuje zarówno sugestie, jak i autouzupełnianie w wersjach języka C# i JavaScript. 

Deweloperzy języka C# mogą przechodzić przez ASP.NET aplikację opartą na MVC, która używa [sdk .NET usługi Azure Cognitive Search](https://aka.ms/search-sdk). Logikę tworzenia automatycznego kompletu i sugerowanych wywołań zapytań można znaleźć w pliku HomeController.cs. 

Deweloperzy JavaScript znajdą równoważną logikę zapytania w pliku IndexJavaScript.cshtml, która obejmuje bezpośrednie wywołania [interfejsu API REST usługi Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/). 

W obu wersjach językowych środowisko użytkownika frontonu jest oparte na bibliotekach [jQuery UI](https://jqueryui.com/autocomplete/) i [XDSoft.](https://xdsoft.net/jqplugins/autocomplete/) Używamy tych bibliotek do tworzenia pola wyszukiwania obsługującego zarówno sugestie, jak i autouzupełnianie. Dane wejściowe zebrane w polu wyszukiwania są łączone z sugestiami i akcjami autouzupełniania, takimi jak te zdefiniowane w HomeController.cs lub IndexJavaScript.cshtml.

## <a name="prerequisites"></a>Wymagania wstępne

+ [Program Visual Studio](https://visualstudio.microsoft.com/downloads/)

Usługa Azure Cognitive Search jest opcjonalna dla tego ćwiczenia, ponieważ rozwiązanie używa usługi hosta i indeksu demo NYCJobs. Jeśli chcesz utworzyć ten indeks na własną usługę wyszukiwania, zobacz [Tworzenie indeksu zadań NYC](#configure-app) dla instrukcji. W przeciwnym razie można użyć istniejącej usługi i indeksu do wycofania aplikacji klienckiej JavaScript.

<!-- The sample is comprehensive, covering suggestions, autocomplete, faceted navigation, and client-side caching. Review the readme and comments for a full description of what the sample offers. -->

## <a name="download-files"></a>Pobieranie plików

Przykładowy kod dla deweloperów języka C# i JavaScript można znaleźć w [folderze DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) repozytorium GitHub **azure-samples/search-dotnet-getting-started.**

Przykład jest przeznaczony dla istniejącej usługi wyszukiwania demo i wstępnie utworzonego indeksu wypełnionego [danymi demonstracyjnymi NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) Indeks NYCJobs zawiera [konstrukcję suggester](index-add-suggesters.md), która jest wymagana do korzystania z sugestii lub autouzupełniania.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Otwórz **autouzupełnianieTutorial.sln** w programie Visual Studio. Rozwiązanie zawiera projekt ASP.NET MVC z połączeniem z istniejącą usługą wyszukiwania i indeksem.

1. Zaktualizuj pakiety NuGet:

   1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **pozycję DotNetHowHowToAutoComplete** i wybierz polecenie **Zarządzaj pakietami NuGet**.  
   1. Wybierz kartę **Aktualizacje,** zaznacz wszystkie pakiety i kliknij pozycję **Aktualizuj**. Zaakceptuj wszelkie umowy licencyjne. Do aktualizacji wszystkich pakietów może być wymagana więcej niż jedno podanie.

1. Naciśnij klawisz F5, aby uruchomić projekt i załadować stronę do przeglądarki.

U góry strony zobaczysz opcję wyboru języka C# lub JavaScript. Opcja C# wywołuje HomeController z przeglądarki i używa azure cognitive search .NET SDK do pobierania wyników. 

Opcja JavaScript wywołuje interfejs API REST usługi Azure Cognitive Search bezpośrednio z przeglądarki. Ta opcja ma zazwyczaj zauważalnie lepszą wydajność, ponieważ eliminuje kontroler z przepływu. Opcję można wybrać na podstawie potrzeb i preferencji językowych. Na stronie znajduje się kilka przykładów autouzupełniania, a dla każdego z nich są dostępne wskazówki. Każdy przykład ma zalecany tekst przykładowy, który możesz wypróbować.  

![Przykładowa strona startowa](media/search-autocomplete-tutorial/startup-page.png "Przykładowa strona startowa w localhost")

Spróbuj wpisać kilka liter w każdym polu wyszukiwania, aby zobaczyć, co się dzieje.

## <a name="query-inputs"></a>Dane wejściowe kwerendy

W wersjach języka C# i JavaScript implementacja pola wyszukiwania jest dokładnie taka sama. 

Otwórz plik **Index.cshtml** w folderze \Views\Home, aby wyświetlić kod:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

W tym przykładzie jest proste pole tekstowe wprowadzania z klasy do stylizacji, identyfikator, do którego odwołuje się JavaScript i tekst zastępczy.  Magia jest w osadzonym JavaScript.

Przykład języka C# używa języka JavaScript w index.cshtml do wykorzystania [biblioteki autouzupełniania interfejsu użytkownika jQuery.](https://jqueryui.com/autocomplete/) Ta biblioteka dodaje środowisko autouzupełniania do pola wyszukiwania, wykonując asynchroniczne wywołania do kontrolera MVC w celu pobrania sugestii. Wersja językowa JavaScript jest w IndexJavaScript.cshtml. Zawiera skrypt poniżej paska wyszukiwania, a także wywołania interfejsu API REST do usługi Azure Cognitive Search.

Przyjrzyjmy się kodowi JavaScript w pierwszym przykładzie, który wywołuje funkcję autouzupełniania interfejsu użytkownika jQuery, przekazując żądanie sugestii:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Powyższy kod jest uruchamiany w przeglądarce przy wczytyniu strony w celu skonfigurowania autouzupełnienia interfejsu użytkownika jQuery dla pola wprowadzania "example1a".  Parametr `minLength: 3` zapewnia, że rekomendacje będą wyświetlane tylko po wpisaniu co najmniej trzech znaków w polu wyszukiwania.  Ważna jest wartość źródłowa:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Powyższy wiersz informuje funkcję autouzupełniania interfejsu użytkownika jQuery, gdzie można uzyskać listę elementów do wyświetlenia w polu wyszukiwania. Ponieważ ten projekt jest projekt MVC, wywołuje Suggest funkcji w HomeController.cs, który zawiera logikę zwracania sugestie kwerendy (więcej o Zasugeruj w następnej sekcji). Ta funkcja przekazuje również kilka parametrów do kontrolowania podświetleń, dopasowywania rozmytego i terminu. Interfejs API autouzupełniania języka JavaScript dodaje parametr term.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Rozszerzanie przykładu pod kątem obsługi dopasowywania rozmytego

Wyszukiwanie rozmyte pozwala uzyskać wyniki na podstawie zbliżonych dopasowań nawet wtedy, gdy użytkownik błędnie napisze wyraz w polu wyszukiwania. Chociaż nie jest to wymagane, znacznie poprawia wytrzymałość doświadczenia typeahead. Wypróbujmy to przez zmianę wiersza kodu źródłowego w celu włączenia dopasowywania rozmytego.

Zmień następujący wiersz z takiego:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

wprowadź następujące zmiany:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Uruchom aplikację, naciskając klawisz F5.

Spróbuj wpisać tekst w rodzaju „execative” i zwróć uwagę, jak zwracane wyniki dotyczą wyrazu „executive”, mimo że nie pasują dokładnie do wpisanych liter.

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>autouzupełniania jQuery wspierane przez autouzupełnienie usługi Azure Cognitive Search

Do tej pory kod UX wyszukiwania został wyśrodkowany na sugestie. Następny blok kodu pokazuje funkcję autouzupełniania interfejsu użytkownika jQuery (wiersz 91 w index.cshtml), przekazując żądanie autouzupełniania usługi Azure Cognitive Search:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>Przykład języka C#

Teraz, gdy przejrzeliśmy kod JavaScript dla strony sieci Web, przyjrzyjmy się kodowi kontrolera po stronie serwera c#, który faktycznie pobiera sugerowane dopasowania przy użyciu narzędzia Azure Cognitive Search .NET SDK.

Otwórz plik **HomeController.cs** w katalogu Kontrolery. 

Pierwszą rzeczą, którą możesz zauważyć, jest metoda `InitSearch`na górze klasy o nazwie . Ta metoda tworzy uwierzytelnionego klienta indeksu HTTP do usługi Azure Cognitive Search. Aby uzyskać więcej informacji, zobacz [Jak używać usługi Azure Cognitive Search z aplikacji .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

W wierszu 41 zwróć uwagę na funkcję Sugeruj. Jest on oparty na [DocumentsOperationsExtensions.Suggest metody](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

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

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Funkcja Suggest przyjmuje dwa parametry, które określają, czy są zwracane wyróżnienia trafień oraz czy oprócz wprowadzonego terminu wyszukiwania jest stosowane dopasowywanie rozmyte. Metoda tworzy [SuggestParameters obiektu](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), który jest następnie przekazywany do Suggest API. Wynik jest następnie konwertowany na format JSON, dzięki czemu można go wyświetlić w kliencie.

W wierszu 69 zwróć uwagę na funkcję Autouzupełniania. Jest on oparty na [DocumentsOperationsExtensions.Autocomplete metody](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Funkcja Autouzupełniania pobiera dane wejściowe wyszukiwane. Metoda tworzy [obiekt Autouzupełniajparametry](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Wynik jest następnie konwertowany na format JSON, dzięki czemu można go wyświetlić w kliencie.

(Opcjonalnie) Dodaj punkt przerwania na początku funkcji Suggest i przejdź krokami przez kod. Zwróć uwagę na odpowiedź zwróconą przez zestaw SDK i sposób, w jaki jest konwertowany na wynik zwrócony z metody.

Inne przykłady na stronie wykonaj ten sam wzorzec, aby dodać wyróżnianie trafień i aspekty do obsługi buforowania po stronie klienta wyników autouzupełniania. Przejrzyj każdy z nich, aby zrozumieć, jak działają i jak można je wykorzystać w środowisku wyszukiwania.

## <a name="javascript-example"></a>Przykład języka JavaScript

Implementacja języka JavaScript autouzupełniania i sugestie wywołuje interfejs API REST, przy użyciu identyfikatora URI jako źródła, aby określić indeks i operację. 

Aby przejrzeć implementację javascript, otwórz **indexJavaScript.cshtml**. Należy zauważyć, że funkcja autouzupełniania interfejsu użytkownika jQuery jest również używana w polu wyszukiwania, zbierając dane wejściowe wyszukiwanych terminów i wykonując asynchroniczne wywołania usługi Azure Cognitive Search w celu pobrania sugerowanych dopasowań lub ukończonych terminów. 

Przyjrzyjmy się kodowi JavaScript w pierwszym przykładzie:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Jeśli porównasz ten przykład z powyższym przykładem, który wywołuje kontroler home, zauważysz kilka podobieństw.  Konfiguracja autouzupełniania dla elementów `minLength` i `position` jest dokładnie taka sama. 

Istotna zmiana w tym przypadku to kod źródłowy. Zamiast wywoływać Suggest metody w kontrolerze domowym, żądanie REST jest tworzony w funkcji JavaScript i wykonywane przy użyciu Ajax. Odpowiedź jest następnie przetwarzana w sekcji „success” i używana jako źródło.

Wywołania REST używają identyfikatorów URI, aby określić, czy jest wywoływane [autouzupełnianie](https://docs.microsoft.com/rest/api/searchservice/autocomplete) lub [sugestie](https://docs.microsoft.com/rest/api/searchservice/suggestions) interfejsu API. Następujące identyfikatory URI znajdują się odpowiednio na liniach 9 i 10.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

W wierszu 148 można znaleźć skrypt, który wywołuje `autocompleteUri`. Pierwsze wezwanie `suggestUri` znajduje się na linii 39.

> [!Note]
> Wykonywanie wywołań REST do usługi w języku JavaScript jest oferowane w tym miejscu jako wygodna demonstracja interfejsu API REST, ale nie powinny być interpretowane jako najlepsze rozwiązanie lub zalecenie. Włączenie klucza interfejsu API i punktu końcowego w skrypcie otwiera usługę do ataków typu "odmowa usługi" dla każdego, kto może odczytać te wartości poza skryptem. Podczas gdy jego bezpieczne używanie języka JavaScript do celów edukacyjnych, być może w indeksach hostowanych w bezpłatnej usłudze, zalecamy używanie języka Java lub C# do indeksowania i wykonywania zapytań w kodzie produkcyjnym. 

<a name="configure-app"></a>

## <a name="create-an-nycjobs-index"></a>Tworzenie indeksu NYCJobs

Do tej pory używasz hostowanego indeksu demo NYCJobs. Jeśli chcesz pełny wgląd w cały kod, w tym indeksu, postępuj zgodnie z tymi instrukcjami, aby utworzyć i załadować indeks we własnej usłudze wyszukiwania.

1. [Utwórz usługę Azure Cognitive Search](search-create-service-portal.md) lub znajdź [istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym przykładzie można skorzystać z bezpłatnej usługi. 

   > [!Note]
   > Jeśli korzystasz z bezpłatnej usługi Azure Cognitive Search, są ograniczone do trzech indeksów. Moduł ładujący dane NYCJobs tworzy dwa indeksy. Upewnij się, że miejsce w usłudze jest wystarczające do zaakceptowania nowych indeksów.

1. Pobierz przykładowy kod [NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)

1. W folderze DataLoader przykładowego kodu NYCJobs otwórz **plik DataLoader.sln** w programie Visual Studio.

1. Dodaj informacje o połączeniu dla usługi Azure Cognitive Search. Otwórz App.config w ramach projektu DataLoader i zmień targetSearchServiceName i TargetSearchServiceApiKey appSettings, aby odzwierciedlić usługę Azure Cognitive Search i klucz interfejsu API usługi Azure Cognitive Search. Te informacje można znaleźć w witrynie Azure portal.

1. Naciśnij klawisz F5, aby uruchomić aplikację, tworząc dwa indeksy i importując przykładowe dane NYCJob.

1. Otwórz **autouzupełnianieTutorial.sln** i edytuj plik Web.config w projekcie **Autouzupełniania.** Zmień searchservicename i SearchServiceApiKey wartości do wartości, które są prawidłowe dla usługi wyszukiwania.

1. Naciśnij klawisz F5, aby uruchomić aplikację. Przykładowa aplikacja internetowa zostanie otwarta w przeglądarce domyślnej. Środowisko jest identyczne z wersją piaskownicy, tylko indeks i dane są hostowane w usłudze.

## <a name="next-steps"></a>Następne kroki

W tym przykładzie przedstawiono podstawowe kroki tworzenia pola wyszukiwania, które obsługuje autouzupełnianie i sugestie. Widziałeś, jak można utworzyć ASP.NET aplikacji MVC i użyć interfejsu Azure Cognitive Search .NET SDK lub REST API do pobierania sugestii.

Następnym krokiem jest próba zintegrowania sugestii i autouzupełniania w wyszukiwaniu. Poniższe artykuły referencyjne powinny pomóc.

> [!div class="nextstepaction"]
> [Autouzupełniaj sugestie interfejsu API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> REST[REST](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Apis atrybut indeksu w interfejsie API Create Index REST](https://docs.microsoft.com/rest/api/searchservice/create-index)