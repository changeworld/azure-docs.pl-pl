---
title: C#Samouczek dotyczący autouzupełniania i sugestii
titleSuffix: Azure Cognitive Search
description: W tym samouczku przedstawiono sposób automatycznego uzupełniania i sugestii w celu zebrania danych wejściowych terminu wyszukiwania od użytkowników przy użyciu listy rozwijanej. Jest ona oparta na istniejącym projekcie hoteli.
manager: nitinme
author: PeterTurcan
ms.author: v-pettur
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: b542476ac1c9b6d4368d97eb4db76518eb2dba03
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114559"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-cognitive-search"></a>C#Samouczek: Dodawanie autouzupełniania i sugestii — Azure Wyszukiwanie poznawcze

Dowiedz się, jak zaimplementować Autouzupełnianie (propozycje i sugestie), gdy użytkownik zaczyna pisać w polu wyszukiwania. W tym samouczku pokażemy wyniki z wyprzedzeniem i wyniki sugestii oddzielnie, a następnie pokażemy metodę łączenia ich w celu utworzenia bogatszego środowiska użytkownika. Użytkownik może tylko wpisać dwa lub trzy klucze, aby znaleźć wszystkie dostępne wyniki. Ten samouczek kompiluje się do projektu stronicowania utworzonego w [ C# samouczku: wyniki wyszukiwania stronicowania — Samouczek platformy Azure wyszukiwanie poznawcze](tutorial-csharp-paging.md) .

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Dodawanie sugestii
> * Dodawanie wyróżniania do sugestii
> * Dodaj Autouzupełnianie
> * Łączenie autouzupełniania i sugestii

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

[ C# Samouczek: wyniki wyszukiwania na stronie stronicowania — usługa Azure wyszukiwanie poznawcze](tutorial-csharp-paging.md) Project w górę i uruchomiona. Ten projekt może być własną wersją zakończono w poprzednim samouczku lub zainstalować go z witryny GitHub: [Utwórz pierwszą aplikację](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="add-suggestions"></a>Dodawanie sugestii

Zacznijmy od najprostszego przypadku zaoferowania użytkownikowi alternatywy: listy rozwijanej sugestii.

1. W pliku index. cshtml Zmień instrukcję **TextBoxFor** na następującą.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

    W tym kluczu należy ustawić identyfikator pola wyszukiwania na **azureautosuggest**.

2. Zgodnie z tą instrukcją po zamknięciu **&lt;/div&gt;** wprowadź ten skrypt.

    ```javascript
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

    Ten skrypt został podłączony do pola wyszukiwania za pomocą tego samego identyfikatora. Ponadto do wyzwolenia wyszukiwania są niezbędne co najmniej dwa znaki, a my wywołamy akcję **Sugeruj** na kontrolerze głównym z dwoma parametrami zapytania: **wyróżnieniami** i **rozmytymi**, w tym wystąpieniu ustawiono wartość false.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Dodawanie odwołań do skryptów jQuery do widoku

Funkcja autouzupełniania wywołana w skrypcie powyżej nie jest coś, co należy napisać wypróbujemy, ponieważ jest on dostępny w bibliotece jQuery. 

1. Aby uzyskać dostęp do biblioteki jQuery, Zmień sekcję&gt; &lt;głównego pliku widoku na następujący kod.

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Musimy również usunąć lub dodać komentarz do wiersza, który odwołuje się do jQuery w pliku _Layout. cshtml (w **widokach/folderze udostępnionym** ). Znajdź poniższe wiersze i Skomentuj pierwszy wiersz skryptu, jak pokazano. Ta zmiana pozwala uniknąć konfliktów odwołań do jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Teraz możemy używać wstępnie zdefiniowanych funkcji Autouzupełnianie jQuery.

### <a name="add-the-suggest-action-to-the-controller"></a>Dodaj akcję Sugeruj do kontrolera

1. Na kontrolerze głównym Dodaj akcję **Sugeruj** (Powiedz, po akcji **strony** ).

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

    **Górny** parametr określa liczbę wyników do zwrócenia (jeśli nie zostanie określony, wartość domyślna to 5). W indeksie platformy Azure jest określona funkcja _sugerowania_ , która jest wykonywana, gdy dane są skonfigurowane, a nie przez aplikację kliencką, taką jak ten samouczek. W takim przypadku sugerowany jest nazywany "SG" i przeszukuje pole **hotelname** -Nothing. 

    Dopasowanie rozmyte umożliwia uwzględnienie "blisko chybień" w danych wyjściowych. Jeśli dla parametru **wyróżnienia** określono wartość true, znaczniki HTML pogrubione są dodawane do danych wyjściowych. Te dwa parametry zostaną ustawione na wartość true w następnej sekcji.

2. Mogą wystąpić błędy składniowe. Jeśli tak, Dodaj następujące dwie instrukcje **using** na początku pliku.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Uruchom aplikację. Czy po wprowadzeniu "ZZ" uzyskasz różne opcje? Teraz spróbuj "PA".

    ![Wpisanie "po" powoduje wyświetlenie dwóch sugestii](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Zauważ, że wprowadzane litery _muszą_ rozpoczynać się od słowa, a nie tylko w tym słowie.

4. W skrypcie widoku Ustaw **& rozmyte** na true, a następnie ponownie uruchom aplikację. Teraz wprowadź "po". Zwróć uwagę na to, że wyszukiwanie założono, że masz jedną literę niewłaściwą!
 
    ![Wpisywanie "PA" z rozmytym ustawieniem true](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Jeśli interesuje Cię, [Składnia zapytania Lucene na platformie Azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/query-lucene-syntax) opisuje logikę używaną w przypadku wyszukiwania rozmytego.

## <a name="add-highlighting-to-the-suggestions"></a>Dodawanie wyróżniania do sugestii

Możemy poprawić wygląd sugestii użytkownika jako bit, ustawiając dla parametru " **podświetli** " wartość true. Należy jednak najpierw dodać kod do widoku, aby wyświetlić tekst pogrubiony.

1. W widoku (index. cshtml) Dodaj następujący skrypt po powyższym skrypcie **azureautosuggest** .

    ```javascript
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

2. Teraz zmień identyfikator pola tekstowego, tak aby odczytał się w następujący sposób.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Uruchom aplikację ponownie i zobaczysz wpisany tekst pogrubiony w sugestiach. Załóżmy, spróbuj wpisać "PA".
 
    ![Wpisywanie "PA" z wyróżnieniem](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. Logika użyta w powyższym skrypcie wyróżniania nie jest foolproof. Jeśli wprowadzisz termin, który występuje dwa razy pod tą samą nazwą, pogrubione wyniki nie będą całkiem wykonywane. Spróbuj wpisać "mo".

    Jednym z pytań, na które musi odpowiadać Deweloper, jest to, gdy skrypt działa prawidłowo, a kiedy należy osobliwości. W tym samouczku nie zajmiemy się wyróżnieniem, ale znalezienie dokładnego algorytmu ma na celu podjęcie dalszych starań.

## <a name="add-autocompletion"></a>Dodaj Autouzupełnianie

Inna odmiana, która nieco się różni od sugestii, jest autouzupełniania (czasami nazywa się "typu"). Od tego pory zaczniemy od najprostszej implementacji przed przejściem do usprawnienia środowiska użytkownika.

1. Wprowadź następujący skrypt do widoku, wykonując poprzednie skrypty.

    ```javascript
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

2. Teraz zmień identyfikator pola tekstowego, aby odczytał się w następujący sposób.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Na kontrolerze głównym musimy wprowadzić akcję **autouzupełniania** , powiedzmy, poniżej akcji **Sugeruj** .

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

    Zwróć uwagę, że używamy tej samej funkcji *sugerującej* o nazwie "SG" w wyszukiwaniu autouzupełniania zgodnie z sugestiami (w związku z czym próbujemy tylko Autouzupełnianie nazw hotelów).

    Istnieje wiele ustawień opcji **AutoComplete** i **OneTermWithContext**. Opis zakresu opcji można znaleźć w [funkcji Autouzupełnianie platformy Azure](https://docs.microsoft.com/rest/api/searchservice/autocomplete) .

4. Uruchom aplikację. Zwróć uwagę na to, jak zakres opcji wyświetlanych na liście rozwijanej to pojedyncze słowa. Spróbuj wpisać słowa zaczynające się od "re". Zauważ, że liczba opcji jest mniejsza, jeśli wpisano więcej liter.

    ![Wpisywanie przy użyciu podstawowego autouzupełniania](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Jak to się dzieje, skrypt sugestii, który był uruchamiany wcześniej jest prawdopodobnie bardziej użyteczny niż ten skrypt autouzupełniania. Aby zapewnić Autouzupełnianie bardziej przyjazny dla użytkownika, najlepiej jest dodać do wyszukiwania sugestii.

## <a name="combine-autocompletion-and-suggestions"></a>Łączenie autouzupełniania i sugestii

Łączenie autouzupełniania i sugestii jest najbardziej skomplikowane dla naszych opcji i prawdopodobnie zapewnia najlepsze środowisko użytkownika. Elementy, które chcemy wyświetlić, są wbudowane przy wpisywaniu tekstu, pierwszy wybór Wyszukiwanie poznawcze platformy Azure do autouzupełniania tekstu. Ponadto chcemy mieć zakres sugestii jako listę rozwijaną.

Istnieją biblioteki, które oferują tę funkcję — często nazywane "wbudowanym autouzupełnianiem" lub podobną nazwą. Jednak chcemy natywnie zaimplementować tę funkcję, aby zobaczyć, co się dzieje. Zaczniemy najpierw korzystać z kontrolera w tym przykładzie.

1. Musimy dodać do kontrolera akcję zwracającą tylko jeden wynik autouzupełniania wraz z określoną liczbą sugestii. Wywołamy tę akcję **AutocompleteAndSuggest**. Na kontrolerze głównym Dodaj następującą akcję, postępując zgodnie z innymi nowymi akcjami.

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

    Jedna opcja autouzupełniania jest zwracana u góry listy **wyników** , a następnie wszystkie sugestie.

2. W widoku najpierw implementujemy lewę, aby jasne, szare słowo uzupełniające jest renderowane bezpośrednio w obszarze tekstu pogrubionego przez użytkownika. Język HTML zawiera Pozycjonowanie względne do tego celu. Zmień instrukcję **TextBoxFor** (wraz z otaczającymi &lt;DIV&gt;) na następujące, zwracając uwagę na to, że drugie pole wyszukiwania **zidentyfikowane jako część** jest bezpośrednio pod naszym normalnym polem wyszukiwania, pobierając to pole wyszukiwania 39 pikseli z lokalizacji domyślnej.

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Pamiętaj, że zmienimy identyfikator ponownie, aby **azureautocomplete** w tym przypadku.

3. Ponadto w widoku wprowadź następujący skrypt, po wykonaniu wszystkich skryptów wprowadzonych do tej pory. Istnieje całkiem wiele.

    ```javascript
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

    Zwróć uwagę na sprytne użycie funkcji **Interval** , aby wyczyocić tekst podstawowy, gdy nie jest już zgodny z wpisanym przez użytkownika, a także ustawić ten sam przypadek (górny lub niższy), gdy użytkownik pisze tekst ("PA" pasuje do "PA", "PA", "PA" podczas wyszukiwania), tak aby należąca do niego wartość jest przejrzysta.

    Zapoznaj się z komentarzami w skrypcie, aby uzyskać pełniejsze informacje.

4. Na koniec musimy wprowadzić drobne dopasowanie do dwóch klas HTML, aby były one przezroczyste. Dodaj następujący wiersz do klas **searchBoxForm** i **searchBox** w pliku hoteli. css.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Teraz uruchom aplikację. Wprowadź wartość "PA" w polu wyszukiwania. Czy otrzymujesz "Palace" jako sugestię autouzupełniania wraz z dwiema hotelimi zawierającymi "PA"?

    ![Pisanie przy użyciu wbudowanego autouzupełniania i sugestii](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Spróbuj użyć tabulacji w celu zaakceptowania sugestii autouzupełniania i spróbuj wybrać sugestie przy użyciu klawiszy strzałek i klawisza Tab, a następnie spróbuj ponownie, używając myszy i jednego kliknięcia. Sprawdź, czy skrypt obsługuje wszystkie te sytuacje w sposób starannie.

    Możesz zdecydować, że jest to prostsze do załadowania w bibliotece, która oferuje tę funkcję, ale teraz wiesz, co najmniej jeden sposób uzyskania wbudowanego autouzupełniania.

## <a name="takeaways"></a>Wnioski

Rozważmy następujący wnioski z tego projektu:

* Funkcja autouzupełniania (znana także jako "typu") i sugestie mogą umożliwić użytkownikowi wpisanie tylko kilku kluczy, aby znaleźć dokładnie to, czego chcą.
* Autouzupełnianie i sugestie pracujące razem mogą zapewnić bogate środowisko użytkownika.
* Zawsze Testuj funkcje autouzupełniania przy użyciu wszystkich form danych wejściowych.
* Użycie funkcji **setInterval** może być przydatne podczas sprawdzania i poprawiania elementów interfejsu użytkownika.

## <a name="next-steps"></a>Następne kroki

W następnym samouczku Przyjrzyjmy się innemu sposobowi ulepszania środowiska użytkownika przy użyciu aspektów, aby zawęzić wyszukiwanie za pomocą jednego kliknięcia.

> [!div class="nextstepaction"]
> [C#Samouczek: używanie aspektów w celu ułatwienia nawigacji — Wyszukiwanie poznawcze platformy Azure](tutorial-csharp-facets.md)


