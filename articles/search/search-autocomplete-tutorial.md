---
title: Dodawanie sugestii i automatycznego uzupełniania w polu wyszukiwania — usługa Azure Search
description: Włącz typeahead akcji zapytania w usłudze Azure Search, tworząc sugestory i sformułowania żądań, które wypełnić pole wyszukiwania z ukończonych terminów ani fraz.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: 43d289f2688bbf4927ee244d6ae9992782bf380e
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009822"
---
# <a name="example-add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Przykład: Dodawanie sugestie lub automatycznego uzupełniania do aplikacji usługi Azure Search

Ten artykuł zawiera informacje o sposobie użycia [sugestie](https://docs.microsoft.com/rest/api/searchservice/suggestions) i [autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete) tworzyć pola zaawansowane wyszukiwanie, które obsługuje wyszukiwania jako użytkownik typ-zachowania.

+ *Sugestie dotyczące* są sugerowane wyniki generowane podczas wpisywania, w którym każdego sugestia jest pojedynczym wynikiem z indeksu, który pasuje do wpisanego do tej pory. 

+ *Autouzupełnianie*, [funkcja w wersji zapoznawczej](search-api-preview.md), "zakończeniu", wyraz lub frazę, która obecnie wpisywany przez użytkownika. Zamiast zwracać wyniki, zostanie ukończone kwerendę, która następnie można wykonać w celu zwracania wyników. Podobnie jak w przypadku sugestii, ukończone wyraz lub frazę w zapytaniu jest uzależniona od dopasowania w indeksie. Usługa nie oferują zapytań, które zwracają wartości zerowe w indeksie.

Możesz pobrać i uruchomić przykładowy kod w **DotNetHowToAutocomplete** do oceny tych funkcji. Przykładowy kod jest przeznaczony dla wbudowanych indeks, wypełniony [NYCJobs pokaz danych](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). Indeks NYCJobs zawiera [konstrukcji Sugestora](index-add-suggesters.md), która jest wymagana dla przy użyciu sugestie lub automatycznego uzupełniania. Można użyć indeksu przygotowany hostowanej w usłudze piaskownicy, lub [wypełnić indeks własne](#configure-app) w przykładowym rozwiązaniu NYCJobs przy użyciu modułu ładującego dane. 

**DotNetHowToAutocomplete** w przykładzie pokazano zarówno sugestie, jak i automatycznego uzupełniania w obu C# i wersje języka JavaScript. C#Deweloperzy mogą przejść przez aplikację na podstawie platformy ASP.NET MVC, która korzysta [zestawu .NET SDK usługi Azure Search](https://aka.ms/search-sdk). Logika składania Autouzupełnianie i wywołania sugerowane zapytania można znaleźć w pliku HomeController.cs. Deweloperzy języka JavaScript znajduje się logiki równoważne zapytania w IndexJavaScript.cshtml, która zawiera bezpośrednie wywołania do [interfejsu API REST usługi Azure Search](https://docs.microsoft.com/rest/api/searchservice/). 

Dla obu wersji języka interfejsu użytkownika frontonu opiera się na [interfejs użytkownika jQuery](https://jqueryui.com/autocomplete/) i [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) bibliotek. Używamy tych bibliotek do tworzenia w polu wyszukiwania, obsługa zarówno sugestie, jak i automatycznego uzupełniania. Dane wejściowe, zbierane w polu wyszukiwania są skojarzone z sugestii i automatycznego uzupełniania akcji, takich jak zgodnie z definicją w HomeController.cs lub IndexJavaScript.cshtml.

To ćwiczenie przeprowadzi Cię przez następujące zadania:

> [!div class="checklist"]
> * Implementuje pole wejściowe wyszukiwania w żądaniach JavaScript i problem sugestie lub warunki autouzupełniania
> * W C#, zdefiniuj HomeController.cs sugestie i akcji autouzupełniania
> * W języku JavaScript wywołania interfejsów API REST, bezpośrednio w celu zapewnienia funkcji

## <a name="prerequisites"></a>Wymagania wstępne

Usługa Azure Search jest opcjonalnie na potrzeby tego ćwiczenia, ponieważ rozwiązanie korzysta z piaskownicy na żywo usługi hostingu przygotowany indeksu pokaz NYCJobs. Jeśli chcesz uruchomić ten przykład usługi wyszukiwania, zobacz [indeksu skonfigurować Pokazowa](#configure-app) instrukcje.

* [Program Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), w każdej wersji. Przykładowy kod i instrukcje zostały przetestowane na bezpłatna wersja Community.

* Pobierz [przykładowe DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

Próbka jest kompleksowy, obejmujący sugestie, automatycznego uzupełniania, nawigacji aspektowej i buforowanie po stronie klienta. Należy przejrzeć plik readme i komentarze, aby uzyskać pełny opis przykładu oferuje.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Otwórz **AutocompleteTutorial.sln** w programie Visual Studio. Rozwiązanie zawiera projekt składnika ASP.NET MVC za pomocą połączenia z indeksem pokaz Pokazowa.

2. Naciśnij klawisz F5, aby uruchomić projekt i załadować stronę w wybranej przeglądarce.

U góry strony zobaczysz opcję wyboru języka C# lub JavaScript. C# Opcja wywoła HomeController w przeglądarce i korzysta z zestawu .NET SDK usługi Azure Search do pobierania wyników. 

Opcja języka JavaScript powoduje wywołanie interfejsu API REST usługi Azure Search bezpośrednio z przeglądarki. Ta opcja ma zazwyczaj zauważalnie lepszą wydajność, ponieważ eliminuje kontroler z przepływu. Opcję można wybrać na podstawie potrzeb i preferencji językowych. Na stronie znajduje się kilka przykładów autouzupełniania, a dla każdego z nich są dostępne wskazówki. Każdy przykład ma zalecany tekst przykładowy, który możesz wypróbować.  

Spróbuj wpisać kilka liter w każdym polu wyszukiwania, aby zobaczyć, co się dzieje.

## <a name="search-box"></a>Pole wyszukiwania

Dla obu C# i wersje języka JavaScript, implementacji pole wyszukiwania jest dokładnie taka sama. 

Otwórz **Index.cshtml** pliku w obszarze \Views\Home folderu do wyświetlania kodu:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

To pole prosty tekst wejściowy, za pomocą klasy do określania stylu, identyfikator, aby odwoływać się do JavaScript i tekst zastępczy.  Magic jest osadzony kod JavaScript.

C# Przykładowy języka używa języka JavaScript w Index.cshtml wykorzystywać [biblioteki autouzupełniania interfejsu użytkownika jQuery](https://jqueryui.com/autocomplete/). Ta biblioteka wywołań asynchronicznych do kontrolera MVC można pobrać sugestie dodaje środowisko automatycznego uzupełniania w polu wyszukiwania. Wersja języka JavaScript jest IndexJavaScript.cshtml. Obejmuje ona poniższego skryptu, na pasku wyszukiwania, a także wywołania interfejsu API REST do usługi Azure Search.

Przyjrzyjmy się kod JavaScript, na przykład pierwszy, która wywołuje jQuery funkcję autouzupełniania interfejsu użytkownika, przekazując żądanie dla sugestie:

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

Powyższy kod działa w przeglądarce na ładowanie strony, aby skonfigurować Autouzupełnianie interfejsu użytkownika jQuery dla pola wejściowego "example1a".  `minLength: 3` zapewnia, że zalecenia będą wyświetlane tylko w przypadku co najmniej trzy znaki w polu wyszukiwania.  Ważna jest wartość źródłowa:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Powyższy wiersz o tym funkcja autouzupełniania interfejsu użytkownika jQuery, gdzie można pobrać listy elementów do wyświetlenia w polu wyszukiwania. Ponieważ jest to projekt programu MVC, wywołanie funkcji sugerowanej w HomeController.cs, która zawiera logikę do zwracania podpowiedzi dla zapytania (więcej na temat sugerowanej w następnej sekcji). Tę funkcję, przekazuje również kilka parametrów najważniejsze funkcje kontroli, dopasowywania rozmytego i czas trwania. Interfejs API autouzupełniania języka JavaScript dodaje parametr term.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Rozszerzanie przykładu pod kątem obsługi dopasowywania rozmytego

Wyszukiwanie rozmyte pozwala uzyskać wyniki na podstawie zbliżonych dopasowań nawet wtedy, gdy użytkownik błędnie napisze wyraz w polu wyszukiwania. Chociaż nie jest to wymagane, znacznie zwiększa odporność środowiska typeahead. Wypróbujmy to przez zmianę wiersza kodu źródłowego w celu włączenia dopasowywania rozmytego.

Zmień następujący wiersz z takiego:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

na taki:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Uruchom aplikację, naciskając klawisz F5.

Spróbuj wpisać tekst w rodzaju „execative” i zwróć uwagę, jak zwracane wyniki dotyczą wyrazu „executive”, mimo że nie pasują dokładnie do wpisanych liter.

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>jQuery autouzupełniania wspierana przez usługi Azure Search autouzupełniania

Do tej pory wyszukiwania kod UX ma wyśrodkowane na sugestie. Następny blok kodu pokazuje funkcja autouzupełniania interfejsu użytkownika jQuery (wiersz index.cshtml w 91), przekazując żądanie do usługi Azure Search autouzupełniania:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 http://xdsoft.net/jqplugins/autocomplete/
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

## <a name="c-example"></a>Przykład w języku C#

Teraz, gdy firma Microsoft sprawdzono kod JavaScript do strony sieci web, Przyjrzyjmy się C# kodu po stronie serwera kontrolera, który faktycznie pobiera sugestie przy użyciu zestawu .NET SDK usługi Azure Search.

Otwórz **HomeController.cs** pliku w katalogu kontrolerów. 

Pierwszą rzeczą, którą można zauważyć jest metodą u góry klasy o nazwie `InitSearch`. Umożliwia ona utworzenie uwierzytelnianego klienta HTTP indeksu dla usługi Azure Search. Aby uzyskać więcej informacji, zobacz [jak używać usługi Azure Search z poziomu aplikacji .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

W wierszu 41 Zwróć uwagę, funkcji sugerowanej. Jest on oparty na [metoda DocumentsOperationsExtensions.Suggest](https://docs.microsoft.com/dotnet/api/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet-preview).

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

Funkcja Suggest przyjmuje dwa parametry, które określają, czy są zwracane wyróżnienia trafień oraz czy oprócz wprowadzonego terminu wyszukiwania jest stosowane dopasowywanie rozmyte. Ta metoda tworzy [obiektu SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), który jest następnie przekazywany do interfejsu API zasugerować. Wynik jest następnie konwertowany na format JSON, dzięki czemu można go wyświetlić w kliencie.

W wierszu 69 Zwróć uwagę, funkcja automatycznego uzupełniania. Jest on oparty na [metoda DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet-preview).

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

Funkcja autouzupełniania przyjmuje dane wejściowe termin wyszukiwania. Ta metoda tworzy [obiektu AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Wynik jest następnie konwertowany na format JSON, dzięki czemu można go wyświetlić w kliencie.

(Opcjonalnie) Dodaj punkt przerwania na początku funkcji Suggest i przejdź krokami przez kod. Zwróć uwagę, odpowiedź zwrócona przez zestaw SDK i jak jest konwertowany na wynik zwracany przez metodę.

Inne przykłady na stronie postępuj zgodnie z tego samego wzorca można dodać, wyróżnianie trafień oraz zestawów reguł do obsługi klienta buforowanie wyników funkcji autouzupełniania. Przejrzyj każdy z nich, aby zrozumieć, jak działają i jak można je wykorzystać w środowisku wyszukiwania.

## <a name="javascript-example"></a>Przykład JavaScript

Implementację Javascript Autouzupełnianie i sugestie dotyczące wywołań interfejsu API REST, przy użyciu identyfikatora URI jako źródło, aby określić indeks i operacji. 

Aby zapoznać się z implementacji języka JavaScript, otwórz **IndexJavaScript.cshtml**. Zwróć uwagę, funkcja automatycznego uzupełniania interfejsu użytkownika jQuery jest również używany dla pola wyszukiwania, zbierania danych wejściowych termin wyszukiwania i wywołań asynchronicznych do usługi Azure Search można pobrać sugerowane dopasowań lub zakończone warunki. 

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

Jeśli porównasz go z przykładem powyżej wywołującym kontroler Home, zauważysz kilka podobieństw.  Konfiguracja autouzupełniania dla elementów `minLength` i `position` jest dokładnie taka sama. 

Istotna zmiana w tym przypadku to kod źródłowy. Zamiast wywoływać metodę sugerowanej na głównym kontrolerze, żądania REST jest tworzony w funkcji języka JavaScript i wykonywane za pomocą interfejsu Ajax. Odpowiedź jest następnie przetwarzana w sekcji „success” i używana jako źródło.

Wywołania REST Użyj identyfikatorów URI, aby określić czy [autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete) lub [sugestie](https://docs.microsoft.com/rest/api/searchservice/suggestions) Wykonano wywołanie interfejsu API. Następujące identyfikatory URI są w wierszach, 9, 10, odpowiednio.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

W wierszu 148, możesz znaleźć skryptu, który wywołuje `autocompleteUri`. Pierwsze wywołanie `suggestUri` w wierszu 39.

> [!Note]
> Wywołania REST do usługi w języku JavaScript jest oferowana jako wygodny pokaz interfejsu API REST, ale nie powinny być traktowane jako najlepsze rozwiązanie lub zalecenia. Włączenie klucza interfejsu API i punktu końcowego w skrypcie otwiera usługi maksymalnie atakom typu odmowa usługi dla każdego, kto może odczytać te wartości off skryptu. Podczas jego można bezpiecznie używany język JavaScript dla celów szkoleniowych, być może indeksów w serwisie bezpłatnej usługi, zaleca się przy użyciu języka Java lub C# dla operacji indeksowania i zapytania w kodzie produkcyjnym. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Konfigurowanie NYCJobs do uruchamiania w usłudze

Do tej pory korzystasz hostowanej indeksu NYCJobs w wersji demonstracyjnej. Jeśli chcesz, aby pełny wgląd w cały kod, łącznie z indeksu, wykonaj te instrukcje, aby utworzyć i załadować indeks w usłudze wyszukiwania.

1. [Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa, w tym przykładzie. 

   > [!Note]
   > Jeśli używasz bezpłatnej usługi Azure Search, możesz korzystać tylko z trzech indeksów. Moduł ładujący dane NYCJobs tworzy dwa indeksy. Upewnij się, że miejsce w usłudze jest wystarczające do zaakceptowania nowych indeksów.

1. Pobierz [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) przykładowego kodu.

1. W folderze DataLoader NYCJobs przykładowego kodu, otwórz **DataLoader.sln** w programie Visual Studio.

1. Dodaj informacje o połączeniu dla usługi Azure Search. Otwórz plik App.config w projekcie DataLoader i zmień elementy appSettings TargetSearchServiceName i TargetSearchServiceApiKey odpowiednio do używanej usługi Azure Search i używanego klucza interfejsu API usługi Azure Search. Są one dostępne w witrynie Azure Portal.

1. Naciśnij klawisz F5, aby uruchomić aplikację, tworzenie dwa indeksy i importowanie NYCJob przykładowych danych.

1. Otwórz **AutocompleteTutorial.sln** i edytować plik Web.config w **AutocompleteTutorial** projektu. Zmień wartości SearchServiceName i SearchServiceApiKey na wartości, które są prawidłowe dla Twojej usługi wyszukiwania.

1. Naciśnij klawisz F5, aby uruchomić aplikację. Przykładowa aplikacja sieci web zostanie otwarta w domyślnej przeglądarce. Środowisko jest taka sama jak wersja piaskownicy, indeksu i danych, które są przechowywane w usłudze.

## <a name="next-steps"></a>Kolejne kroki

W tym przykładzie przedstawiono podstawowe kroki tworzenia pole wyszukiwania, które obsługuje Autouzupełnianie i sugestie. Pokazano, jak można zbudować aplikację ASP.NET MVC i pobrać sugestie za pomocą zestawu .NET SDK usługi Azure Search lub interfejsu API REST.

Kolejnym krokiem, podjęcie próby integrowanie środowiska wyszukiwania sugestie i automatycznego uzupełniania. Powinny pomóc w następujących artykułach odwołania.

> [!div class="nextstepaction"]
> [Interfejs API REST autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Interfejs API REST sugestii](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Atrybut indeksu aspektów dotyczący interfejsu API REST tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index)

