---
title: C#Samouczek dotyczący automatycznego uzupełniania i sugestie — usługa Azure Search
description: W tym samouczku opiera się na projekcie "Podział na strony — Usługa Azure Search w wynikach wyszukiwania", aby dodać automatycznego uzupełniania i sugestie. Celem jest bardziej zaawansowane środowisko użytkownika. Dowiedz się, jak połączyć z menu rozwijanego sugestii, za pomocą automatycznego uzupełniania w tekście.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: d1722d98b594c8a317fa782eab223a754fc578fe
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166806"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-search"></a>C#Samouczek: Dodaj automatycznego uzupełniania i sugestie — usługa Azure Search

Dowiedz się, jak zaimplementować automatycznego uzupełniania (wpisywania z wyprzedzeniem i sugestii) po uruchomieniu przez użytkownika, wpisując w polu usługi wyszukiwania. W tym samouczku firma Microsoft będzie Pokaż wyniki wpisywania z wyprzedzeniem i wyniki sugestii osobno, a następnie pokazują sposób, łącząc je w celu utworzenia bogatszego środowiska użytkownika. Użytkownik może mieć tylko na typ dwa lub trzy klucze, aby zlokalizować wszystkie wyniki, które są dostępne. Ten samouczek opiera się na stronicowania projektu utworzonego w [ C# samouczka: Podział na strony — wyniki wyszukiwania, usługa Azure Search](tutorial-csharp-paging.md) samouczka.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Dodawanie sugestii
> * Dodawanie wyróżnienia do sugestii
> * Dodaj automatycznego uzupełniania
> * Łączenie z automatycznego uzupełniania i sugestie

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

Masz [ C# samouczka: Podział na strony — wyniki wyszukiwania, usługa Azure Search](tutorial-csharp-paging.md) projektu działanie. Ten projekt może być własnej wersji, które wykonane w poprzednim samouczku lub go zainstalować z witryny GitHub: [Tworzenie pierwszej aplikacji](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="add-suggestions"></a>Dodawanie sugestii

Zacznijmy od najprostszym przypadku oferty się alternatywy dla użytkownika: menu rozwijanego sugestii.

1. W pliku index.cshtml Zmień **TextBoxFor** instrukcji do następującego.

```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
```

Tutaj klucz jest, że możemy ustawić identyfikator w polu wyszukiwania, aby **azureautosuggest**.

2. Po tej instrukcji, po upływie  **&lt;/DIV&gt;** , wprowadź ten skrypt.

```cs
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
```

Firma Microsoft nawiązano połączenie tego skryptu z pola wyszukiwania za pomocą tego samego identyfikatora. Ponadto co najmniej dwóch znaków jest potrzebny do wyzwolenia wyszukiwania i nazywamy **Suggest** akcji w kontrolerze macierzystego z dwoma parametrami zapytania: **wyróżnia** i **rozmyte**, jednocześnie ustawionych na wartość false, w tym wystąpieniu.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Dodawanie odwołań do skryptów jquery do widoku

Funkcja autouzupełniania, wywoływana w skrypcie powyżej nie jest coś, co mamy do zapisania określić główną przyczynę, ponieważ jest dostępny w bibliotece jquery. 

1. Aby uzyskać dostęp do biblioteki jquery, należy zmienić &lt;head&gt; sekcji pliku widoku z następującym kodem.

```cs
<head>
    <meta charset="utf-8">
    <title>Autocomplete demo</title>
    <link href="https://code.jquery.com/ui/1.10.4/themes/ui-lightness/jquery-ui.css"
          rel="stylesheet">
    <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
    <script src="https://code.jquery.com/ui/1.10.4/jquery-ui.js"></script>
    <link rel="stylesheet" href="~/css/hotels.css" />
</head>
```

2. Należy również usunąć lub skomentować wiersza odwołuje się do technologii jquery w pliku _Layout.cshtml (w **widoków/Shared** folder). Znajdź następujące wiersze, a komentarz pierwszy wiersz skryptu, jak pokazano. Ta zmiana eliminuje konfliktu odwołania do technologii jquery.

```cs
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
```

Teraz możemy użyć funkcji autouzupełniania wstępnie zdefiniowanych jquery.

### <a name="add-the-suggest-action-to-the-controller"></a>Dodawanie akcji sugerowanej do kontrolera

1. Na głównym kontrolerze Dodaj **Suggest** akcji (powiedzmy, po **strony** akcji).

```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
```

**Górnej** parametr określa, ile wyników do zwrócenia (Jeśli nie zostanie podany, wartość domyślna to 5). A _sugestora_ jest określona w indeksu usługi Azure, w którym jest wykonywane, gdy danych jest ustawiony w górę, a nie przez aplikację kliencką, np. w tym samouczku. W tym przypadku sugestora nosi nazwę "sg" i przeszukuje **HotelName** pole - nic innego. 

Dopasowywania rozmytego umożliwia "sytuacje" do uwzględnienia w danych wyjściowych. Jeśli **wyróżnia** parametr ma wartość true, następnie bold tagów HTML są dodawane do danych wyjściowych. Firma Microsoft ustawi tych dwóch parametrów na wartość true w następnej sekcji.

2. Mogą wystąpić pewne błędy składni. Jeśli tak, Dodaj dwie poniższe **przy użyciu** instrukcji na górze pliku.

```cs
using System.Collections.Generic;
using System.Linq;
```

3. Uruchom aplikację. Otrzymujesz szeroką gamę opcji po wprowadzeniu "po", na przykład? Wypróbuj teraz "pa".

    ![Wpisywanie "po", co spowoduje wyświetlenie dwóch sugestie](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

Należy zauważyć, że litery wprowadzasz _musi_ Uruchom wyrazu, a nie po prostu zostać uwzględnione w programie word.

4. W skrypcie widoku, należy ustawić **& rozmyte** wartość PRAWDA, a następnie ponownie uruchom aplikację. Teraz wprowadź "po". Należy zauważyć, że wyszukiwanie przyjęto założenie, że po jednej literze otrzymano nieprawidłowy!
 
    ![Wpisywanie "pa" przy użyciu zestawu rozmyte na wartość true](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

Jeśli interesuje Cię, [składnia zapytań Lucene w usłudze Azure Search](https://docs.microsoft.com/azure/search/query-lucene-syntax) przez logikę używaną podczas wyszukiwania rozmytego szczegółowo opisano.

## <a name="add-highlighting-to-the-suggestions"></a>Dodawanie wyróżnienia do sugestii

Usprawnić wygląd sugestie użytkownikowi nieco, ustawiając **wyróżnia** parametru na wartość true. Jednak najpierw musimy dodać kod do widoku, aby wyświetlić tekst pogrubiony.

1. W widoku (index.cshtml), Dodaj następujący skrypt po **azureautosuggest** skrypt podanymi powyżej.

```cs
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
```

2. Teraz zmienić identyfikator w polu tekstowym, więc o następującej treści.

```cs
@Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
```

3. Ponownie uruchom aplikację i powinna zostać wyświetlona Twoja wprowadzony tekst pogrubiony sugestie. Załóżmy, że spróbuj, wpisując "pa".
 
    ![Wpisywanie "pa" Wyróżnianie](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. Przez logikę używaną w skrypcie wyróżnianie powyżej nie jest niezawodne. Jeśli wprowadzasz termin, który pojawia się dwukrotnie w tej samej nazwie, wyniki pogrubiony nie są dość co należałoby. Spróbuj wpisać "miesiąc".

Jedno z pytań, czego potrzebuje Deweloper w odpowiedzi na to, kiedy jest skryptem Praca "oraz wystarczająco dużo" i kiedy należy swoje Osobliwości, Standardy programu się jego rozwiązaniem. Firma Microsoft nie będzie biorąc, wyróżnianie dowolne dalej w tym samouczku, ale wskazujące dokładne algorytm jest coś, co należy wziąć pod uwagę, jeśli dodatkowo dłużej wyróżniania.

## <a name="add-autocompletion"></a>Dodaj automatycznego uzupełniania

Inna wersja, która różni się nieco od sugestie, jest automatycznego uzupełniania (nazywane czasem "wpisywania z wyprzedzeniem"). Ponownie firma Microsoft rozpocznie się za pomocą najprostszej implementacji, przed przejściem na poprawa komfortu pracy użytkowników.

1. Wprowadź następujący skrypt w widoku, zgodnie z poprzednim skryptów.

```cs
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
```

2. Teraz zmienić identyfikator w polu tekstowym, więc o następującej treści.

```cs
@Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
```

3. Na głównym kontrolerze musimy wprowadzić **autouzupełniania** akcji, powiedz, poniżej **sugerowanej** akcji.

```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
```

Zwróć uwagę, że użyto takie same *sugestora* funkcji o nazwie "sg" w wyszukiwaniu autouzupełniania, co w przypadku sugestii (tak, aby tylko próbujemy automatycznego uzupełniania nazw hotelu).

Istnieje szereg **parametr AutocompleteMode** przy użyciu ustawień, a firma Microsoft **OneTermWithContext**. Zapoznaj się [autouzupełniania Azure](https://docs.microsoft.com/rest/api/searchservice/autocomplete) opis zakresu opcji tutaj.

4. Uruchom aplikację. Zwróć uwagę, jak zakres opcji na liście rozwijanej wyświetlane są pojedyncze wyrazy. Spróbuj wpisać wyrazy, rozpoczynając od "re". Zwróć uwagę, jak wiele opcji zmniejsza się w trakcie wpisywania więcej liter.

    ![Wpisywanie za pomocą automatycznego uzupełniania podstawowe](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

Formie skryptu sugestie, który został przeprowadzony wcześniej jest prawdopodobnie bardziej przydatne niż ten skrypt automatycznego uzupełniania. Aby automatycznego uzupełniania był bardziej przyjazny dla użytkownika, najlepiej dodaniu do wyszukiwania sugestię.

## <a name="combine-autocompletion-and-suggestions"></a>Łączenie z automatycznego uzupełniania i sugestie

Łączenie automatycznego uzupełniania i sugestie jest najbardziej złożone dostępnych opcji i prawdopodobnie zapewnia najlepsze środowisko użytkownika. Chcemy jest wyświetlana, wbudowane tekst, który jest jego wpisywania, pierwszy wybór usługi Azure Search automatycznie uzupełnić tekst. Ponadto chcemy szeroką gamę sugestie jako listy rozwijanej.

Brak bibliotek, które oferuje tę funkcję — często nazywanej "wbudowany automatycznego uzupełniania" lub podobnej nazwie. Jednak zamierzamy natywnie zaimplementować tę funkcję, aby było widać, co się dzieje. Firma Microsoft zamierza najpierw uruchomić pracy na kontrolerze, w tym przykładzie.

1. Musimy dodać akcję do kontrolera, który zwraca tylko jeden wynik automatycznego uzupełniania, wraz z określoną liczbą sugestie. Ta akcja spowoduje nazywamy **AutocompleteAndSuggest**. Na głównym kontrolerze Dodaj następującą akcję, zgodnie z nowe akcje.

```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
```

Jedną z opcji automatycznego uzupełniania, jest zwracany w górnej części **wyniki** listy, a następnie wszystkie sugestie.

2. W widoku najpierw wdrażamy lew tak, aby wyraz światła szarego automatycznego uzupełniania renderowania po prawej stronie w obszarze bolder tekstu wprowadzanego przez użytkownika. Kod HTML zawiera pozycjonowanie względne do tego celu. Zmiana **TextBoxFor** — instrukcja (i jego otaczającego &lt;div&gt; instrukcji) w poniższym przykładzie, biorąc pod uwagę, że drugie pole wyszukiwania zidentyfikowane jako **poniżej** jest bezpośrednio w naszych pole normalne wyszukiwania przez pobieranie pikseli 39 pole wyszukiwania zniżki w stosunku do domyślnej lokalizacji!

```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
```

Należy pamiętać, zmieniamy identyfikator ponownie, aby **azureautocomplete** w tym przypadku.

3. Również w widoku wprowadź następujący skrypt, gdy wszystkie skrypty, które zostały wprowadzone do tej pory. Istnieje bardzo dużo do niego.

```cs
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
```

Zwróć uwagę na Sprytne **interwał** funkcja zarówno wyczyść tekst źródłowy, gdy nie jest już zgodny, co wpisywany przez użytkownika, a także ustawić z uwzględnieniem wielkości znaków (górna lub małe) jako użytkownik pisze (jak "pa" pasuje do "PA", "pA", "Pa" po wyszukiwanie) tak, aby tekst nałożone ładnie.

Zapoznaj się z artykułem komentarzy w skrypcie, aby uzyskać pełniejsze informacje.

4. Na koniec musimy upewnić drobnych korekt do dwóch klas kodu HTML, aby były one przezroczysty. Dodaj następujący wiersz do **searchBoxForm** i **searchBox** klasy w pliku hotels.css.

```cs
        background: rgba(0,0,0,0);
```

5. Teraz uruchom aplikację. Wprowadź "pa" w polu wyszukiwania. Otrzymujesz "palace" jako sugestię Autouzupełniania wraz z dwóch hotele, które zawierają "pa"?

    ![Wpisywanie za pomocą wbudowanych funkcji autouzupełniania i sugestie](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. TAB, aby zaakceptować sugestię Autouzupełniania, a następnie spróbuj wybrać sugestie za pomocą klawiszy strzałek i klawisz tab, a następnie spróbuj ponownie przy użyciu myszy i jednym kliknięciem. Sprawdź skrypt starannego obsługi tych sytuacji.

Może się okazać, że jest łatwiejsze do załadowania w bibliotece, która umożliwia tej funkcji, ale teraz wiesz, co najmniej jednym ze sposobów przekazania automatycznego uzupełniania w tekście do pracy!

## <a name="takeaways"></a>Wnioski

Należy wziąć pod uwagę następujące wnioski z tego projektu:

* Automatycznego uzupełniania (znane również jako "wpisywania z wyprzedzeniem") i sugestie można włączyć użytkownika o wpisanie tylko kilka klucze, aby znaleźć dokładnie to, czego oczekują.
* Automatycznego uzupełniania i sugestie, współpracując z zapewnieniem zaawansowanego środowiska użytkownika.
* Należy zawsze przetestować funkcje automatycznego uzupełniania za pomocą wszystkich formularzy danych wejściowych.
* Za pomocą **setInterval** funkcji mogą być przydatne podczas sprawdzania i poprawiania elementy interfejsu użytkownika.

## <a name="next-steps"></a>Kolejne kroki

Zostały wykonane w tej serii C# samouczków — użytkownik powinien weszły cenne wiedzę na temat interfejsów API usługi Azure Search.

Dalsze dokumentacja i samouczki należy wziąć pod uwagę przeglądania [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), lub inne samouczki w [dokumentacji wyszukiwania platformy Azure](https://docs.microsoft.com/azure/search/).
