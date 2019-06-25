---
title: C#samouczek dotyczący korzystania z zestawów reguł w celu zwiększenia wydajności nawigacji i sieci — usługa Azure Search
description: Ten samouczek opiera się na "Podział na strony — Usługa Azure Search w wynikach wyszukiwania" projektu, aby dodać wyszukiwania reguł. Dowiedz się, że mogą posłużyć w nawigacji i automatycznego uzupełniania.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: a81042869564533050fef42a983f2f8fb9bc7b23
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304671"
---
# <a name="c-tutorial-use-facets-for-navigation-and-network-efficiency---azure-search"></a>C#Samouczek: Zestawy reguł na użytek wydajność nawigacji i sieci — usługa Azure Search

Aspekty mają dwie odrębne używa w usłudze Azure Search. Aspektami może służyć do pomocy nawigacji, zapewniając użytkownika przy użyciu zestawu pól wyboru, aby użyć do skoncentrowania się ich wyszukiwanie. Ponadto one można zwiększyć wydajność sieci, gdy używane do automatycznego uzupełniania. Wyszukiwanie zestawu reguł są wydajne, ponieważ są wykonywane tylko raz dla każdego ładowania strony, a nie raz dla każdego naciśnięcia klawisza. 

Zestawy reguł są atrybuty danych (na przykład kategoria hotelu w nasze przykładowe dane) i rozwijać najważniejsze dla czasu działania wyszukiwania.

Ten samouczek opiera się dwa projekty, jeden dla zestawu reguł nawigacji i inne reguł automatycznego uzupełniania. Oba projekty są kompilowane do projektu stronicowania utworzonego w [ C# samouczka: Podział na strony — wyniki wyszukiwania, usługa Azure Search](tutorial-csharp-paging.md) samouczka.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Ustaw właściwości modelu jako _IsFacetable_
> * Dodawanie reguł nawigacji do aplikacji
> * Dodawanie reguł automatycznego uzupełniania do aplikacji
> * Zdecyduj, kiedy należy używać reguł automatycznego uzupełniania

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

Masz [ C# samouczka: Podział na strony — wyniki wyszukiwania, usługa Azure Search](tutorial-csharp-paging.md) projektu działanie. To jest własnej wersji lub go zainstalować z witryny GitHub: [Tworzenie pierwszej aplikacji](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-fields-as-isfacetable"></a>Zestaw pól modelu IsFacetable

Aby dla właściwości modelu muszą znajdować się w wyszukiwaniu aspekt (nawigacji lub automatycznego uzupełniania), muszą być oznakowane za pomocą **IsFacetable**.

1. Sprawdź **hotelu** klasy. **Kategoria** i **tagi**, na przykład, są oznaczone jako **IsFacetable**, ale **HotelName** i **opis** nie są. 

    ```cs
    public partial class Hotel
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsFilterable]
        public string HotelId { get; set; }

        [IsSearchable, IsSortable]
        public string HotelName { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnLucene)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Category { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public bool? ParkingIncluded { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [IsFilterable, IsSortable]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }

    }
    ```

2. Firma Microsoft nie zmienia się żadnych tagów w ramach tego samouczka. Wyszukiwanie zestawu reguł będzie sygnalizować błąd, jeśli pole wymagane w wyszukiwaniu nie jest oznaczone tagami odpowiednio.


## <a name="add-facet-navigation-to-your-app"></a>Dodawanie reguł nawigacji do aplikacji

W tym przykładzie użyjemy umożliwia użytkownikowi wybranie jednego lub więcej kategorii hotelu, na liście wyświetlane po lewej stronie wyników. Potrzebujemy kontrolera wiedzieć na liście kategorii, podczas pierwszego uruchomienia aplikacji i przekazania tej listy do widoku będzie wyświetlana po pierwszy ekran jest renderowany. Ponieważ każda strona jest renderowany, musimy upewnij się, że firma Microsoft ma obsługiwane listę zestawów reguł i bieżący wybór użytkownika mają być przekazane wzdłuż do kolejnych stron. Ponownie używamy magazynu tymczasowego w mechanizmie się, aby zachować dane.

![Zawęź wyszukiwanie "puli" przy użyciu reguł nawigacji](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="modify-the-searchdata-model"></a>Modyfikowanie modelu SearchData

1. Otwórz plik SearchData.cs i Dodaj dodatkowego **przy użyciu** instrukcji. Musimy włączyć **listy&lt;ciąg&gt;**  konstruowania.

    ```cs
    using System.Collections.Generic;
    ```

2. W tym samym pliku Dodaj następujące wiersze do **SearchData** klasy. Nie usuwaj żadnych istniejących właściwości klasy, ale Dodaj następujące konstruktory i tablice właściwości.

    ```cs
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each facet.
        public string[] facetText { get; set; }

        // Array to hold the check box setting.
        public bool[] facetOn { get; set; }
    ```


### <a name="search-for-facets-on-the-first-index-call"></a>Wyszukaj aspektami przy pierwszym wywołaniu indeksu

Znaczących zmian potrzeb w zakresie głównego kontrolera. Pierwsze wywołanie **indeks()** nie zwraca już widoku z nie inne procesy przetwarzania. Chcemy zapewnić pełną listę zestawów reguł widoku, a pierwsze wywołanie jest właściwy dla tego celu.

1. Otwórz plik głównego kontrolera i dodać dwie **przy użyciu** instrukcji.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

2. Teraz Zastąp kilka wierszy bieżącego **indeks()** metodę z metodą, która przeprowadza wyszukiwanie aspekt hotelu kategorii. Jak wyszukiwanie powinny być przeprowadzane asynchronicznie, firma Microsoft musi deklarować **indeksu** metodę jako **async**.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 categories.
                // Field names specified here must be marked as "IsFacetable" in the model, or the search call will throw an exception.
                Facets = new List<string> { "Category,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> categories = searchResult.Facets["Category"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(categories);

            // Save the facet text for the next view.
            SaveFacets(model);

            // Render the view including the facets.
            return View(model);
        }
    ```

    Kilka kwestii, należy pamiętać, w tym miejscu. Możemy przekonwertować wyniki wyszukiwania wywołanie listy ciągów, a następnie te ciągi reguł są dodawane do **SearchData** model komunikacji z widoku. Firma Microsoft oszczędzają tych ciągów do magazynu tymczasowego przed na koniec renderowania widoku. Zapisywanie w tym odbywa się tak, aby ta lista jest dostępna dla następnego wywołania metody akcji kontrolera.

3. Dodajmy dwie metody prywatnej do zapisywania i przywracania aspekty, model i magazynu tymczasowego.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

### <a name="save-and-restore-facet-text-on-all-calls"></a>Zapisywanie i przywracanie tekst reguł dla wszystkich wywołań

1. Dwie inne akcje głównego kontrolera **indeksu (SearchData model)** i **strony (SearchData model)** , zarówno trzeba odzyskać aspekty przed wywołaniem wyszukiwania, a następnie zapisać je ponownie po wywołaniu wyszukiwania. Zmiana **indeksu (SearchData model)** do tych dwóch połączeń.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Recover the facet text.
                RecoverFacets(model);

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;

                // Facets
                SaveFacets(model, true);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

2. Teraz Zrób to samo dla **strony (SearchData model)** metody. Tylko podajemy odpowiedni kod poniżej. Dodaj **RecoverFacets** i **SaveFacets** wywołuje wokół **RunQueryAsync** wywołania.

    ```cs
                // Recover facet text and check marks.
                RecoverFacets(model, true);

                await RunQueryAsync(model, page, leftMostPage);

                // Save facets and check marks.
                SaveFacets(model, true);
    ```

### <a name="set-up-a-search-filter"></a>Ustawić filtr wyszukiwania

Gdy użytkownik wybierze niektóre aspekty, na przykład, załóżmy, że kliknięciu **budżetu** i **możliwości i Spa** kategorie, a następnie tylko hotele, które są określone jako jeden z tych dwóch kategorii powinny zostać zwrócone w wyniki. Aby zoptymalizować wyszukiwania w ten sposób, musimy skonfigurować _filtru_.

1. W **RunQueryAsync** metody, Dodaj kod do pętli za pomocą ustawień reguł podanego modelu, aby utworzyć parametry filtru. I Dodaj filtr do **obiektu SearchParameters**, jak pokazano w poniższym kodzie.

    ```cs
            // Create a filter for selected facets.
            string selectedFacets = "";

            for (int f = 0; f < model.facetText.Length; f++)
            {
                if (model.facetOn[f])
                {
                    if (selectedFacets.Length > 0)
                    {
                        // If there is more than one selected facet, logically OR them together.
                        selectedFacets += " or ";
                    }
                    selectedFacets += "(Category eq \'" + model.facetText[f] + "\')";
                }
            }

            var parameters = new SearchParameters
            {
                // Facets: add the filter.
                Filter = selectedFacets,

                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description", "Category" },
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };
    ```

    Dodaliśmy **kategorii** właściwości do listy **wybierz** elementów do zwrócenia. Dodanie tej właściwości nie jest to wymagane, ale w ten sposób możemy zweryfikować, że firma Microsoft filtrowania poprawnie.

### <a name="define-a-few-additional-html-styles"></a>Definiowanie kilka dodatkowych stylów HTML

Widok będzie wymagać wprowadzono istotne zmiany. 

1. Zacznij od otwarcia pliku hotels.css (w folderze wwwroot/css) i dodaj następujące klasy.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 200px;
        background-color: lightgoldenrodyellow;
        display: normal;
        color: #666;
        margin: 10px;
    }
    ```

### <a name="add-a-list-of-facet-checkboxes-to-the-view"></a>Dodaj listę reguł pól wyboru w widoku

Widoku firma Microsoft organizować dane wyjściowe w tabeli, do starannego wyrównania aspektami po lewej stronie, a wyniki po prawej stronie. Otwórz plik index.cshtml.

1. Zamień całą zawartość HTML &lt;treści&gt; tagów, z następującym kodem.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Facet Navigation
                    </h1>
                </td>
            </tr>

            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it.-->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">
                        <h5>Filter by Category:</h5>
                        <ul class="facetlist">
                            @for (var i = 0; i < Model.facetText.Length; i++)
                            {
                                <li> @Html.CheckBoxFor(m => m.facetOn[i], new { @id = "check" + i.ToString() }) @Model.facetText[i] </li>
                            }
                        </ul>
                    </div>
                </td>
                <td valign="top">
                    <div id="resultsplace">
                        @if (Model != null && Model.resultList != null)
                        {
                            // Show the result count.
                            <p class="sampleText">
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea("desc", Model.resultList.Results[i].Document.Description + "\nCategory:  " +  Model.resultList.Results[i].Document.Category, new { @class = "box2" })
                            }
                        }
                    </div>
                </td>
            </tr>

            <tr>
                <td></td>
                <td valign="top">
                    @if (Model != null && Model.pageCount > 1)
                    {
                        // If there is more than one page of results, show the paging buttons.
                        <table>
                            <tr>
                                <td class="tdPage">
                                    @if (Model.currentPage > 0)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">|&lt;</p>
                                    }
                                </td>

                                <td class="tdPage">
                                    @if (Model.currentPage > 0)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&lt;</p>
                                    }
                                </td>

                                @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                {
                                    <td class="tdPage">
                                        @if (Model.currentPage == pn)
                                        {
                                            // Convert displayed page numbers to 1-based and not 0-based.
                                            <p class="pageSelected">@(pn + 1)</p>
                                        }
                                        else
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                                            </p>
                                        }
                                    </td>
                                }

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&gt;</p>
                                    }
                                </td>

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&gt;|</p>
                                    }
                                </td>
                            </tr>
                        </table>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

    Zwróć uwagę na **CheckBoxFor** wywołanie, aby wypełnić **facetOn** tablica z wyborów użytkownika. Ponadto dodaliśmy kategorii hotelu-to-end w hotelu opisu. Ten tekst jest po prostu upewnij się, że nasze wyszukiwanie działa poprawnie. Nie ma wiele else zmienił się z wcześniej samouczków, z tą różnicą, że firma Microsoft ma zorganizowane dane wyjściowe w tabeli.

### <a name="run-and-test-the-app"></a>Uruchamianie i testowanie aplikacji

1. Uruchom aplikację i sprawdź listę zestawów reguł jest wyświetlany po lewej stronie starannego.

2. Spróbuj wybrać jedno, dwa, trzy lub więcej pól wyboru i sprawdź wyniki.

    ![Aby zawęzić wyszukiwanie "Wi-Fi" przy użyciu reguł nawigacji](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

3. Ma niewielkie kompilacji za pomocą reguł nawigacji. Co ma się zdarzyć, jeśli użytkownik zmieni wybór zestawu reguł (Wybieranie lub anulowanie wyboru pól wyboru), ale następnie klika przycisk jedną z opcji stronicowania, a nie na pasku wyszukiwania? W efekcie zmianę należy zainicjować nowego wyszukiwania, ponieważ bieżących stron nie będzie już prawidłowy. Alternatywnie zmiany użytkownika może być ignorowane, a następnej strony wyników podane, zgodnie z ustawieniami oryginalnego zestawu reguł. Firma Microsoft w tym przykładzie wybrano drugie rozwiązanie, ale być może należy wziąć pod uwagę implementacji poprzedniego rozwiązania. Być może wyzwalać nowego wyszukiwania, jeśli najnowsze zaznaczenie wybranego zestawu reguł nie jest dokładnie dopasowana zaznaczenie w magazyn tymczasowy?

Przykładowa Nawigacja aspekt zostanie ukończona. Ale być może można również rozważyć jak ta aplikacja może zostać rozszerzony. Lista reguł można rozszerzyć, aby obejmował inne możliwe reguł pola (powiedzieć **tagi**), dzięki czemu użytkownik może wybrać różne opcje, takie jak pula, sieci Wi-Fi, pasek, bezpłatne parkowania i tak dalej. 

Zaletą aspekt nawigacji do użytkownika jest to że muszą oni zachować wprowadź ten sam tekst, swoich opcje reguł są zachowywane dla cyklem życia bieżąca sesja z aplikacji. Użytkownik może wybrać kategorie i inne atrybuty, za pomocą jednego kliknięcia, następnie wyszukaj w innym określonym tekstem.

Teraz Przeanalizujmy zastosowanie różne aspekty.

## <a name="add-facet-autocompletion-to-your-app"></a>Dodawanie reguł automatycznego uzupełniania do aplikacji

Zestaw reguł automatycznego uzupełniania działa, wprowadzając początkowe wyszukiwanie po pierwszym uruchomieniu aplikacji. To wyszukiwanie zbiera listę zestawów reguł do użycia jako sugestie, gdy wpisywany przez użytkownika.

![Wpisz "ponownie", co spowoduje wyświetlenie trzy aspekty](./media/tutorial-csharp-create-first-app/azure-search-facet-type-re.png)

Firma Microsoft użyje numerowane aplikacji stronicowania, może być ukończone w drugim samouczku jako podstawy dla tego przykładu.

Aby zaimplementować reguł automatycznego uzupełniania, nie musimy zmienia żadnych modeli (klas danych). Musimy dodać kilka skryptów do widoku i akcji kontrolera.

### <a name="add-an-autocomplete-script-to-the-view"></a>Dodawanie skryptu automatycznego uzupełniania w widoku

Aby zainicjować wyszukiwanie zestawu reguł, musimy wysłać zapytanie. Następujący kod JavaScript, dodawane do pliku index.cshtml zapewnia logiki zapytania i prezentacji, której potrzebujesz.

1. Znajdź **@Html.TextBoxFor(m = > m.searchText...)** instrukcji i Dodaj Unikatowy identyfikator, podobny do następującego.

    ```cs
    <div class="searchBoxForm">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresearchfacets" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

2. Teraz Dodaj następujący kod JavaScript (po zamknięciu **&lt;/DIV&gt;** powyżej działa poprawnie).

    ```JavaScript
     <script>
            $(function () {
                $.getJSON("/Home/Facets", function (data) {

                    $("#azuresearchfacets").autocomplete({
                        source: data,
                        minLength: 2,
                        position: {
                            my: "left top",
                            at: "left-23 bottom+10"
                        }
                    });
                });
            });
        </script>
    ```

    Należy zauważyć, że skrypt wywołuje **aspektami** akcji w kontrolerze macierzystego, bez żadnych parametrów, po osiągnięciu długość co najmniej dwa znaki.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Dodawanie odwołań do skryptów jquery do widoku

Funkcja autouzupełniania, wywoływana w skrypcie powyżej nie jest coś, co mamy do zapisania określić główną przyczynę, ponieważ jest dostępny w bibliotece jquery. 

1. Aby uzyskać dostęp do biblioteki jquery, Zastąp &lt;head&gt; sekcji Widok pliku następującym kodem.

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Facets demo</title>
        <link href="https://code.jquery.com/ui/1.10.4/themes/ui-lightness/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.10.4/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Należy również usunąć lub skomentować wiersza odwołuje się do technologii jquery w pliku _Layout.cshtml (w **widoków/Shared** folder). Znajdź następujące wiersze, a komentarz pierwszy wiersz skryptu, jak pokazano. Usuwając ten wiersz, firma Microsoft Unikaj niejednoznacznych odwołań do technologii jquery.

    ```html
     <environment include="Development">
            <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
            <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
            <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

Teraz możemy użyć funkcji autouzupełniania wstępnie zdefiniowanych jquery.

### <a name="add-a-facet-action-to-the-controller"></a>Dodawanie akcji reguł do kontrolera

1. Otwórz głównego kontrolera, a następnie dodaj dwie poniższe **przy użyciu** instrukcje nagłówek pliku.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

2. Język JavaScript w widoku wyzwalacze **aspektami** akcji na kontrolerze Dodajmy tę akcję do głównego kontrolera (powiedzmy, poniżej **strony** akcji).

    ```cs
        public async Task<ActionResult> Facets()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search all Tags, but limit the total number to 100, and add up to 20 categories.
                // Field names specified here must be marked as "IsFacetable" in the model, or the search call will throw an exception.
                Facets = new List<string> { "Tags,count:100", "Category,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to two lists that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();
            List<string> categories = searchResult.Facets["Category"].Select(x => x.Value.ToString()).ToList();

            // Combine and return the lists.
            facets.AddRange(categories);
            return new JsonResult(facets);
        }
    ```

    Zwróć uwagę, żądają maksymalnie 100 aspektami z **tagi** pól i maksymalnie 20 od **kategorii** pola. **Liczba** wpisy są opcjonalne, jeśli żadna liczba nie jest ustawiona wartość domyślna wynosi 10.

    Potrzebujemy dwie listy, które są następnie łączone w jeden, ponieważ firma Microsoft o podanie dwóch pól, które mają być wyszukiwane (**tagi** i **kategorii**). W razie firma Microsoft ma prośby o trzy pola, które mają być wyszukiwane, firma Microsoft musi połączyć trzy listy w jedną i tak dalej.

    > [!NOTE]
    > Można ustawić jedną lub więcej z następujących parametrów dla każdego pola w wyszukiwaniu aspekt: **liczba**, **sortowania**, **interwał**, i **wartości**. Aby uzyskać więcej informacji, zobacz [jak wdrożyć nawigację aspektową w usłudze Azure Search](https://docs.microsoft.com/azure/search/search-faceted-navigation).

### <a name="compile-and-run-your-project"></a>Kompilowanie i uruchamianie projektu

Teraz przetestuj program.

1. Spróbuj wpisać "fr" w polu wyszukiwania, który powinien być wyświetlony kilku wyników.

    ![Wpisywanie "fr", co spowoduje wyświetlenie trzy aspekty](./media/tutorial-csharp-create-first-app/azure-search-facet-type-fr.png)

2. Teraz Dodaj "o" na "dla" i zwróć uwagę, że zakres opcji jest ograniczona do jednego.

3. Wpisz inne kombinacje dwóch liter i zobacz, co jest wyświetlana. Należy zauważyć, że podczas wpisywania tekstu serwer jest *nie* wywoływana. Zestawy reguł są buforowane lokalnie, gdy aplikacja jest uruchomiona, a teraz wykonywane jest wywołanie, tylko na serwerze po użytkownik zażąda wyszukiwania.

## <a name="decide-when-to-use-a-facet-autocompletion-search"></a>Zdecyduj, kiedy należy używać reguł automatycznego uzupełniania wyszukiwania

Wyczyść różnica między wyszukiwania reguł i innych operacji wyszukiwania, takie jak sugestie i automatycznego uzupełniania, że jest wyszukiwania reguł _zaprojektowane_ przeprowadzenie tylko jeden raz po załadowaniu strony. Jest inne wyszukiwania automatycznego uzupełniania _zaprojektowane_ ma być wywoływana po każdym znaku. Przy użyciu zestawu reguł w ten sposób potencjalnie powoduje zapisywanie wielu wywołań do serwera. 

Jednak podczas automatycznego uzupełniania zestaw reguł należy używać?

Najlepiej jest zestaw reguł automatycznego uzupełniania używany, gdy:
* Głównym powodem jest wydajność innych wyszukiwania, które wywołują serwer w każdym naciśnięciu klawisza problemu.
* Aspekty zwrócił Podaj użytkownika z listy opcji rozsądny podawaniu kilku znaków.
* Aspekty zwrócił zapewniają szybki sposób dostęp do najbardziej, lub najlepiej wszystkie dostępne dane.
* Maksymalna liczba zezwala na większości aspektów do uwzględnienia. W naszym kodzie ustawiamy maksymalnie 100 aspektami dla **tagi** i 20 aspektami dla **kategorii**. Ustaw maksymalne wartości musi działać z rozmiar zestawu danych. Jeśli zbyt wiele potencjalnych zestawów reguł są trwa wycinania, następnie prawdopodobnie wyszukiwanie nie jest jako pomocny, należy go.

> [!NOTE]
> Mimo że wyszukiwania reguł są zaprojektowane do wywołania po na załadowanie strony mogą oczywiście można wywołać znacznie częściej zależy Twój kod JavaScript. Równie PRAWDA jest, że automatycznego uzupełniania/sugestii wyszukiwania mogą być przeprowadzane częściej niż raz na naciśnięcie klawisza. Ponownie jest to określane przez Twój kod JavaScript, a nie usługi Azure Search. Jednak wyszukiwania reguł powinien zostać wywołany tylko raz na każdej stronie jako zestawy reguł są konstruowane przez usługę Azure Search na podstawie wyszukiwanego dokumentów, to na uwadze. Jest dobrą praktyką, aby wziąć pod uwagę zestaw reguł automatycznego uzupełniania wyszukiwania jako formę nieco mniej elastyczne, ale sieci — wydajniejsze Pomoc dla użytkownika.

## <a name="takeaways"></a>Wnioski

Należy wziąć pod uwagę następujące wnioski z tego projektu:

* Konieczne jest do oznaczania każdego pola jako **IsFacetable**, jeśli mają zostać uwzględnione w nawigacji zestawu reguł lub automatycznego uzupełniania.
* Aspekt nawigacji zapewnia użytkownikowi proste i intuicyjne, sposób zawęzić wyszukiwanie.
* Aspekt nawigacji najlepiej jest podzielona na sekcje (kategorie hotelu), funkcje hotel, cena zakresów, itp., każda sekcja z odpowiednią nazwą.
* Zestaw reguł automatycznego uzupełniania jest skutecznym sposobem uzyskiwania przydatne komfortu bez wywołania serwera powtarzanych innych wyszukiwań automatycznego uzupełniania.
* Zestaw reguł automatycznego uzupełniania jest _alternatywnych_ do automatycznego uzupełniania/sugestii, nie dodatku.

## <a name="next-steps"></a>Kolejne kroki

Zostały wykonane w tej serii C# samouczków — użytkownik powinien weszły cenne wiedzę na temat interfejsów API usługi Azure Search.

Dalsze dokumentacja i samouczki należy wziąć pod uwagę przeglądania [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), lub inne samouczki w [dokumentacji wyszukiwania platformy Azure](https://docs.microsoft.com/azure/search/).
