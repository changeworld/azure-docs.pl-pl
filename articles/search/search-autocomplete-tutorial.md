---
title: Dodawanie sugestii i autouzupełniania w polu wyszukiwania — Azure Search
description: Włącz akcje zapytania typeahead w Azure Search przez tworzenie sugestii i formułowanie żądań, które wypełniają pole wyszukiwania z wypełnionymi warunkami lub frazami.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.openlocfilehash: dc8bc43d6d7b17d1ecd4cf2a1dbe7b2890594e55
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640459"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Dodawanie sugestii lub autouzupełniania do aplikacji Azure Search

W tym artykule dowiesz się, jak [](https://docs.microsoft.com/rest/api/searchservice/suggestions) używać sugestii i [funkcji](https://docs.microsoft.com/rest/api/searchservice/autocomplete) autouzupełniania do tworzenia zaawansowanego pola wyszukiwania, które obsługuje zachowania typu "wyszukiwanie zgodnie z oczekiwaniami".

+ *Sugestie* są sugerowanymi wynikami generowanymi podczas wpisywania, gdzie każda sugestia jest pojedynczym wynikiem indeksu, który jest zgodny z wpisanymi do tej pory. 

+ *Autouzupełnianie* "kończy" wyraz lub frazę, która jest aktualnie wpisywana przez użytkownika. Zamiast zwracać wyniki, wykonuje zapytanie, które następnie można wykonać w celu zwrócenia wyników. Podobnie jak w przypadku sugestii, kompletny wyraz lub fraza w zapytaniu jest predykatem zgodnym z indeksem. Usługa nie będzie oferować zapytań, które zwracają zero wyników w indeksie.

Możesz pobrać i uruchomić przykładowy kod w **DotNetHowToAutocomplete** , aby oszacować te funkcje. Przykładowy kod jest przeznaczony dla wstępnie skompilowanego indeksu wypełnionego [danymi demonstracyjnymi NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). Indeks NYCJobs zawiera [konstrukcję sugerującą](index-add-suggesters.md), która jest wymaganiem do korzystania z sugestii lub autouzupełniania. Można użyć przygotowanego indeksu hostowanego w usłudze piaskownicy lub [wypełnić własny indeks](#configure-app) przy użyciu modułu ładującego dane w przykładowym rozwiązaniu NYCJobs. 

Przykład **DotNetHowToAutocomplete** ilustruje zarówno sugestie, jak i Autouzupełnianie, w C# wersjach językowych języka JavaScript. C#Deweloperzy mogą przechodzić przez ASP.NET aplikację opartą na MVC, która używa [zestawu SDK platformy .net Azure Search](https://aka.ms/search-sdk). Logika wykonywania autouzupełniania i sugerowanych wywołań zapytań można znaleźć w pliku HomeController.cs. Deweloperzy języka JavaScript znajdą równoważną logikę zapytań w IndexJavaScript. cshtml, która obejmuje bezpośrednie wywołania [interfejsu API REST Azure Search](https://docs.microsoft.com/rest/api/searchservice/). 

W przypadku obu wersji językowej środowisko użytkownika frontonu jest oparte na interfejsie [UI jQuery](https://jqueryui.com/autocomplete/) i bibliotekach [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) . Te biblioteki są używane do tworzenia pola wyszukiwania z obsługą obu sugestii i autouzupełniania. Dane wejściowe zebrane w polu wyszukiwania są sparowane z sugestiami i akcjami autouzupełniania, takimi jak te zdefiniowane w HomeController.cs lub IndexJavaScript. cshtml.

W tym ćwiczeniu przedstawiono następujące zadania:

> [!div class="checklist"]
> * Zaimplementuj pole wejściowe wyszukiwania w języku JavaScript i wystawiaj żądania dotyczące sugerowanych dopasowań lub warunków autouzupełniania
> * W C#programie Zdefiniuj sugestie i akcje autouzupełniania w programie HomeController.cs
> * W języku JavaScript Wywołaj interfejsy API REST bezpośrednio, aby zapewnić tę samą funkcjonalność

## <a name="prerequisites"></a>Wymagania wstępne

W tym ćwiczeniu usługa Azure Search jest opcjonalna, ponieważ rozwiązanie używa usługi piaskownicy na żywo, która obsługuje przygotowany indeks demonstracyjny NYCJobs. Jeśli chcesz uruchomić ten przykład na własnej usłudze wyszukiwania, zobacz temat [Konfigurowanie indeksu zadań NYC](#configure-app) w celu uzyskania instrukcji.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), jakakolwiek Edycja. Przykładowy kod i instrukcje zostały przetestowane w wersji bezpłatnej społeczności.

* Pobierz [przykład DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

Przykład jest kompleksowy, obejmujący sugestie, Autouzupełnianie, nawigację aspektową i buforowanie po stronie klienta. Przejrzyj plik Readme i komentarze, aby uzyskać pełny opis ofert przykładowych.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

1. Otwórz **AutocompleteTutorial. sln** w programie Visual Studio. Rozwiązanie zawiera projekt ASP.NET MVC z połączeniem z indeksem demonstracyjnym zadań NYC.

2. Naciśnij klawisz F5, aby uruchomić projekt i załadować stronę w wybranej przeglądarce.

U góry strony zobaczysz opcję wyboru języka C# lub JavaScript. C# Opcja wywołuje HomeController z przeglądarki i Azure Search używa zestawu SDK platformy .NET do pobierania wyników. 

Opcja języka JavaScript powoduje wywołanie interfejsu API REST usługi Azure Search bezpośrednio z przeglądarki. Ta opcja ma zazwyczaj zauważalnie lepszą wydajność, ponieważ eliminuje kontroler z przepływu. Opcję można wybrać na podstawie potrzeb i preferencji językowych. Na stronie znajduje się kilka przykładów autouzupełniania, a dla każdego z nich są dostępne wskazówki. Każdy przykład ma zalecany tekst przykładowy, który możesz wypróbować.  

Spróbuj wpisać kilka liter w każdym polu wyszukiwania, aby zobaczyć, co się dzieje.

## <a name="search-box"></a>Pole wyszukiwania

W przypadku C# wersji językowych i języka JavaScript implementacja pola wyszukiwania jest dokładnie taka sama. 

Otwórz plik **index. cshtml** w folderze \Views\Home, aby wyświetlić kod:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Ten przykład to proste wejściowe pole tekstowe z klasą do ustawiania stylu, IDENTYFIKATORem, do którego odwołuje się kod JavaScript, oraz tekst zastępczy.  Magiczna wartość jest w osadzonym kodzie JavaScript.

Przykładowy C# język używa języka JavaScript w index. cshtml, aby użyć [biblioteki autouzupełniania interfejsu użytkownika jQuery](https://jqueryui.com/autocomplete/). Ta biblioteka dodaje do pola wyszukiwania środowisko autouzupełniania, wykonując wywołania asynchroniczne do kontrolera MVC w celu pobrania sugestii. Wersja języka JavaScript znajduje się w IndexJavaScript. cshtml. Zawiera poniższy skrypt dla paska wyszukiwania, a także wywołania interfejsu API REST do Azure Search.

Przyjrzyjmy się kodowi JavaScript dla pierwszego przykładu, który wywołuje funkcję autouzupełniania interfejsu użytkownika jQuery, przekazując żądanie do sugestii:

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

Powyższy kod jest uruchamiany w przeglądarce podczas ładowania strony, aby skonfigurować funkcję autouzupełniania interfejsu użytkownika jQuery dla pola wejściowego "example1a".  Parametr `minLength: 3` zapewnia, że rekomendacje będą wyświetlane tylko po wpisaniu co najmniej trzech znaków w polu wyszukiwania.  Ważna jest wartość źródłowa:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Powyższy wiersz informuje funkcję Autouzupełnianie interfejsu użytkownika jQuery, gdzie można pobrać listę elementów do wyświetlenia w polu wyszukiwania. Ponieważ ten projekt jest projektem MVC, wywołuje funkcję Sugeruj w HomeController.cs, która zawiera logikę do zwracania sugestii zapytania (więcej informacji na temat sugestii w następnej sekcji). Ta funkcja przekazuje również kilka parametrów w celu kontrolowania świateł, dopasowywania rozmytego i warunku. Interfejs API autouzupełniania języka JavaScript dodaje parametr term.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Rozszerzanie przykładu pod kątem obsługi dopasowywania rozmytego

Wyszukiwanie rozmyte pozwala uzyskać wyniki na podstawie zbliżonych dopasowań nawet wtedy, gdy użytkownik błędnie napisze wyraz w polu wyszukiwania. Chociaż nie jest to wymagane, znacznie poprawia niezawodność środowiska typeahead. Wypróbujmy to przez zmianę wiersza kodu źródłowego w celu włączenia dopasowywania rozmytego.

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

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>Automatyczne Autouzupełnianie w programie jQuery Azure Search Autouzupełnianie

Do tej pory kod środowiska użytkownika wyszukiwania został wyśrodkowany na sugestii. Następny blok kodu pokazuje funkcję Autouzupełnianie interfejsu użytkownika jQuery (wiersz 91 w indeksie index. cshtml), przekazując żądanie do Azure Search Autouzupełnianie:

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

## <a name="c-example"></a>C#przyklad

Po przejrzeniu kodu JavaScript strony sieci Web Przyjrzyjmy się C# kodowi kontrolera po stronie serwera, który faktycznie pobiera sugerowane dopasowania przy użyciu zestawu SDK Azure Search .NET.

Otwórz plik **HomeController.cs** w katalogu controllers. 

Pierwszą rzeczą, którą można zauważyć, jest metoda w górnej części klasy o nazwie `InitSearch`. Ta metoda tworzy uwierzytelnionego klienta indeksu HTTP w usłudze Azure Search. Aby uzyskać więcej informacji, zobacz [jak używać Azure Search z poziomu aplikacji .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

W wierszu 41 Zwróć uwagę na funkcję Sugeruj. Jest on oparty na [metodzie DocumentsOperationsExtensions. sugerował](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

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

Funkcja Suggest przyjmuje dwa parametry, które określają, czy są zwracane wyróżnienia trafień oraz czy oprócz wprowadzonego terminu wyszukiwania jest stosowane dopasowywanie rozmyte. Metoda tworzy [obiekt SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), który następnie jest przenoszona do interfejsu API sugerowania. Wynik jest następnie konwertowany na format JSON, dzięki czemu można go wyświetlić w kliencie.

W wierszu 69 Zwróć uwagę na funkcję Autouzupełnianie. Jest on oparty na [metodzie DocumentsOperationsExtensions. Autouzupełnianie](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

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

Funkcja Autouzupełnianie pobiera dane wejściowe terminu wyszukiwania. Metoda tworzy [obiekt AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Wynik jest następnie konwertowany na format JSON, dzięki czemu można go wyświetlić w kliencie.

(Opcjonalnie) Dodaj punkt przerwania na początku funkcji Suggest i przejdź krokami przez kod. Zwróć uwagę na odpowiedź zwróconą przez zestaw SDK i sposób jej konwersji na wynik zwrócony przez metodę.

Inne przykłady na stronie są zgodne z tym samym wzorcem w celu dodania wyróżniania trafień i aspektów obsługi buforowania po stronie klienta dla wyników autouzupełniania. Przejrzyj każdy z nich, aby zrozumieć, jak działają i jak można je wykorzystać w środowisku wyszukiwania.

## <a name="javascript-example"></a>Przykład JavaScript

Implementacja funkcji Autouzupełnianie i sugestii języka JavaScript wywołuje interfejs API REST, używając identyfikatora URI jako źródła, aby określić indeks i operację. 

Aby przejrzeć implementację języka JavaScript, Otwórz **IndexJavaScript. cshtml**. Zwróć uwagę, że funkcja autouzupełniania interfejsu użytkownika jQuery jest również używana dla pola wyszukiwania, zbierając dane wejściowe wyszukiwanego terminu i wykonujące wywołania asynchroniczne do Azure Search, aby pobrać sugerowane dopasowania lub ukończone warunki. 

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

W przypadku porównania tego przykładu z powyższym przykładem, który wywołuje kontroler Home, zobaczysz kilka podobieństw.  Konfiguracja autouzupełniania dla elementów `minLength` i `position` jest dokładnie taka sama. 

Istotna zmiana w tym przypadku to kod źródłowy. Zamiast wywoływania metody Sugeruj w kontrolerze głównym, żądanie REST jest tworzone w funkcji języka JavaScript i wykonywane przy użyciu technologii AJAX. Odpowiedź jest następnie przetwarzana w sekcji „success” i używana jako źródło.

Wywołania REST używają identyfikatorów URI, aby określić [](https://docs.microsoft.com/rest/api/searchservice/autocomplete) , czy jest wykonywane wywołanie interfejsu API autouzupełniania lub [sugestii](https://docs.microsoft.com/rest/api/searchservice/suggestions) . Poniższe identyfikatory URI są odpowiednio w wierszach 9 i 10.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

W wierszu 148 można znaleźć skrypt, który wywołuje `autocompleteUri`. Pierwsze wywołanie `suggestUri` jest w wierszu 39.

> [!Note]
> Tworzenie wywołań REST do usługi w języku JavaScript jest oferowane tutaj jako wygodna Prezentacja interfejsu API REST, ale nie powinna być interpretowana jako najlepsze rozwiązanie ani zalecenie. Włączenie klucza interfejsu API i punktu końcowego w skrypcie spowoduje otwarcie usługi do odmowy ataków na wszystkich użytkowników, którzy mogą odczytywać te wartości poza skryptem. Gdy bezpiecznie jest używać języka JavaScript do celów edukacyjnych, na przykład w przypadku indeksów hostowanych w bezpłatnej usłudze zalecamy używanie C# języka Java lub do indeksowania i wykonywania zapytań w kodzie produkcyjnym. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Konfigurowanie NYCJobs do uruchamiania w usłudze

Do tej pory używany jest indeks demonstracyjny hostowanej NYCJobs. Jeśli chcesz mieć pełny wgląd w cały kod, w tym indeks, postępuj zgodnie z tymi instrukcjami, aby utworzyć i załadować indeks we własnej usłudze wyszukiwania.

1. [Utwórz usługę Azure Search](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym przykładzie możesz użyć bezpłatnej usługi. 

   > [!Note]
   > Jeśli używasz bezpłatnej usługi Azure Search, możesz korzystać tylko z trzech indeksów. Moduł ładujący dane NYCJobs tworzy dwa indeksy. Upewnij się, że miejsce w usłudze jest wystarczające do zaakceptowania nowych indeksów.

1. Pobierz przykładowy kod [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) .

1. W folderze dataloader przykładowego kodu NYCJobs Otwórz program dataloader **. sln** w programie Visual Studio.

1. Dodaj informacje o połączeniu dla usługi Azure Search. Otwórz plik App.config w projekcie DataLoader i zmień elementy appSettings TargetSearchServiceName i TargetSearchServiceApiKey odpowiednio do używanej usługi Azure Search i używanego klucza interfejsu API usługi Azure Search. Te informacje można znaleźć w Azure Portal.

1. Naciśnij klawisz F5, aby uruchomić aplikację, utworzyć dwa indeksy i zaimportować przykładowe dane NYCJob.

1. Otwórz **AutocompleteTutorial. sln** i edytuj plik Web. config w projekcie **AutocompleteTutorial** . Zmień wartości SearchServiceName i SearchServiceApiKey na wartości, które są prawidłowe dla usługi wyszukiwania.

1. Naciśnij klawisz F5, aby uruchomić aplikację. Przykładowa aplikacja internetowa zostanie otwarta w domyślnej przeglądarce. Środowisko jest takie samo jak wersja piaskownicy, tylko indeks i dane są hostowane w usłudze.

## <a name="next-steps"></a>Następne kroki

W tym przykładzie przedstawiono podstawowe kroki tworzenia pola wyszukiwania, które obsługuje Autouzupełnianie i sugestie. Zawarto informacje na temat tworzenia aplikacji ASP.NET MVC i Azure Search używania zestawu SDK platformy .NET lub interfejsu API REST do pobierania sugestii.

W następnym kroku, próbując zintegrować sugestie i Autouzupełnianie w środowisku wyszukiwania. Poniższe artykuły referencyjne powinny pomóc.

> [!div class="nextstepaction"]
> [Interfejs API REST autouzupełniania](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [Interfejs API REST sugestii](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Atrybut indeksu aspektów dotyczący interfejsu API REST tworzenia indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index)

