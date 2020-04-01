---
title: C# samouczek na temat korzystania z aspektów do pomocy nawigacji
titleSuffix: Azure Cognitive Search
description: Ten samouczek opiera się na "Wyniki wyszukiwania podział na strony — Azure Cognitive Search" projektu, aby dodać nawigacji aspekt. Dowiedz się, jak można łatwo zawęzić wyszukiwanie.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: d88a9d7efdabd493fd31b961748bb6ad3bd8d738
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121567"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-cognitive-search"></a>Samouczek C#: Używanie aspektów do pomocy w nawigacji — Usługa Azure Cognitive Search

Aspekty są używane do pomocy nawigacji, zapewniając użytkownikowi zestaw łączy do użycia, aby skupić się na ich wyszukiwania. Aspekty są atrybutami danych (takimi jak kategoria lub określona funkcja hotelu w naszych przykładowych danych).

Ten samouczek tworzy na projekt stronicowania utworzony w [samouczku C#: Wyniki wyszukiwania na podziale na strony — Azure Cognitive Search](tutorial-csharp-paging.md) samouczek.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Ustawianie właściwości modelu jako _IsFacetable_
> * Dodawanie nawigacji do aspektu do aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

Mieć [c# Samouczek: Wyniki wyszukiwania na podziale na strony — Azure Cognitive Search](tutorial-csharp-paging.md) projektu i uruchomione. Ten projekt może być własną wersją lub zainstalować ją z GitHub: [Utwórz pierwszą aplikację.](https://github.com/Azure-Samples/azure-search-dotnet-samples)

## <a name="set-model-properties-as-isfacetable"></a>Ustawianie właściwości modelu jako IsFacetable

Aby właściwość modelu znajdowała się w wyszukiwaniu aspektu, musi ona być oznaczona tagiem **IsFacetable**.

1. Sprawdź klasę **Hotel.** **Kategoria** i **tagi**, na przykład, są oznaczone jako **IsFacetable**, ale **HotelName** i **Description** nie są. 

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

2. Nie będziemy zmieniać żadnych tagów w ramach tego samouczka, więc zamknij plik hotel.cs w stanie nienaruszonym.

    > [!Note]
    > Wyszukiwanie aspektu spowoduje błąd, jeśli pole wymagane w wyszukiwaniu nie zostanie odpowiednio oznakowane.


## <a name="add-facet-navigation-to-your-app"></a>Dodawanie nawigacji do aspektu do aplikacji

W tym przykładzie umożliwimy użytkownikowi wybranie jednej kategorii hotelu lub jednego udogodnienia z list linków wyświetlanych po lewej stronie wyników. Użytkownik rozpoczyna od wprowadzenia niektórych tekst wyszukiwania, a następnie można zawęzić wyniki wyszukiwania, wybierając kategorię i można zawęzić wyniki dalej, wybierając udogodnienia, lub mogą wybrać udogodnienia pierwszy (kolejność nie jest ważne).

Potrzebujemy kontrolera, aby przekazać listy aspektów do widoku. Musimy zachować wybór użytkowników w miarę postępu wyszukiwania i ponownie używamy magazynu tymczasowego jako mechanizmu zachowania danych.

![Korzystanie z nawigacji aspektu, aby zawęzić wyszukiwanie "puli"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Dodawanie ciągów filtrów do modelu SearchData

1. Otwórz plik SearchData.cs i dodaj właściwości ciągu do klasy **SearchData,** aby przytrzymać ciągi filtru aspektu.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Dodaj metodę akcji Facet

Kontroler domowy potrzebuje jednej nowej akcji, **Facet**i aktualizacji istniejących akcji **Indeks** i **Strona,** a także aktualizacji metody **RunQueryAsync.**

1. Otwórz plik kontrolera macierzystego i dodaj **using** instrukcji, aby włączyć **listę&lt;konstrukcji&gt; ciągu.**

    ```cs
    using System.Collections.Generic;
    ```

2. Zastąp metodę akcji **Indeks(model SearchData).**

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

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. Zastąp metodę akcji **Page(SearchData model).**

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                // Calculate the page that should be displayed.
                switch (model.paging)
                {
                    case "prev":
                        page = (int)TempData["page"] - 1;
                        break;

                    case "next":
                        page = (int)TempData["page"] + 1;
                        break;

                    default:
                        page = int.Parse(model.paging);
                        break;
                }

                // Recover the leftMostPage.
                int leftMostPage = (int)TempData["leftMostPage"];

                // Recover the filters.
                string catFilter = TempData["categoryFilter"].ToString();
                string ameFilter = TempData["amenityFilter"].ToString();

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Search for the new page.
                await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

4. Dodaj metodę akcji **Facet(SearchData model),** która ma zostać aktywowana, gdy użytkownik kliknie łącze aspektu. Model będzie zawierał filtr wyszukiwania kategorii lub filtr wyszukiwania udogodnień. Być może dodaj go po akcji **Strona.**

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
        {
            try
            {
                // Filters set by the model override those stored in temporary data.
                string catFilter;
                string ameFilter;
                if (model.categoryFilter != null)
                {
                    catFilter = model.categoryFilter;
                } else
                {
                    catFilter = TempData["categoryFilter"].ToString();
                }

                if (model.amenityFilter != null)
                {
                    ameFilter = model.amenityFilter;
                } else
                {
                    ameFilter = TempData["amenityFilter"].ToString();
                }

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Initiate a new search.
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

### <a name="set-up-the-search-filter"></a>Konfigurowanie filtru wyszukiwania

Gdy użytkownik wybierze określony aspekt, na przykład, klika kategorię **Resort and Spa,** w wynikach powinny zostać zwrócone tylko hotele, które są określone jako ta kategoria. Aby zawęzić wyszukiwanie w ten sposób, musimy skonfigurować _filtr_.

1. Zastąp **RunQueryAsync** metody z następującym kodem. Przede wszystkim zajmuje ciąg filtru kategorii i ciąg filtru udogodnień i ustawia parametr **Filtr** **parametrów wyszukiwania**.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
        {
            InitSearch();

            string facetFilter = "";

            if (catFilter.Length > 0 && ameFilter.Length > 0)
            {
                // Both facets apply.
                facetFilter = $"{catFilter} and {ameFilter}"; 
            } else
            {
                // One, or zero, facets apply.
                facetFilter = $"{catFilter}{ameFilter}";
            }

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
            {
                // Trigger a switch to a higher page range.
                leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            // Ensure Temp data is stored for the next call.
            TempData["page"] = page;
            TempData["leftMostPage"] = model.leftMostPage;
            TempData["searchfor"] = model.searchText;
            TempData["categoryFilter"] = catFilter;
            TempData["amenityFilter"] = ameFilter;

            // Return the new view.
            return View("Index", model);
        }
    ```

    Dodaliśmy właściwości **Kategoria** i **Tagi** do listy **Wybierz** elementy do zwrócenia. To dodanie nie jest wymagane dla nawigacji aspekt do pracy, ale używamy tych informacji, aby sprawdzić, czy filtrujemy poprawnie.

### <a name="add-lists-of-facet-links-to-the-view"></a>Dodawanie list łączy aspektu do widoku

Widok będzie wymagał pewnych istotnych zmian. 

1. Zacznij od otwarcia pliku hotels.css (w folderze wwwroot/css) i dodaj następujące klasy.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;    
    }
    ```

2. Dla widoku organizujemy dane wyjściowe w tabeli, aby starannie wyrównać listy facetów po lewej stronie i wyniki po prawej stronie. Otwórz plik index.cshtml. Zastąp całą &lt;zawartość&gt; znaczników treści HTML następującym kodem.

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

                        @if (Model != null && Model.resultList != null)
                        {
                            List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                            if (categories.Count > 0)
                            {
                                <h5 class="facetheader">Category:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < categories.Count; c++)
                                    {
                                        var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                        </li>
                                    }
                                </ul>
                            }

                            List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                            if (tags.Count > 0)
                            {
                                <h5 class="facetheader">Amenities:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < tags.Count; c++)
                                    {
                                        var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                        </li>
                                    }
                                </ul>
                            }
                        }
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
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
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

    Zwróć uwagę na użycie wywołania **Html.ActionLink.** To wywołanie komunikuje prawidłowe ciągi filtrów do kontrolera, gdy użytkownik kliknie łącze aspektu. 

### <a name="run-and-test-the-app"></a>Uruchamianie i testowanie aplikacji

Zaletą nawigacji aspekt do użytkownika jest to, że można zawęzić wyszukiwania za pomocą jednego kliknięcia, które możemy wyświetlić w następującej kolejności.

1. Uruchom aplikację, wpisz "lotnisko" jako tekst wyszukiwania. Sprawdź, czy lista aspektów jest starannie wyświetlana po lewej stronie. Te aspekty to wszystko, co ma zastosowanie do hoteli, które mają "lotnisko" w swoich danych tekstowych, z liczbą tego, jak często występują.

    ![Korzystanie z nawigacji aspekt zawęzić wyszukiwanie "lotnisko"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Kliknij kategorię **Ośrodek i spa.** Sprawdź, czy wszystkie wyniki znajdują się w tej kategorii.

    ![Zawężenie wyszukiwania do "Resort and Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Kliknij śniadanie **kontynentalne.** Sprawdź, czy wszystkie wyniki są nadal w kategorii "Resort and Spa", z wybranym uosobieniem.

    ![Zawężenie wyszukiwania do "śniadania kontynentalnego"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Spróbuj wybrać dowolną inną kategorię, a następnie jedną umiębienie i wyświetlić wyniki zawężenia. Następnie spróbuj w drugą stronę, jedno umiębienie, a następnie jedną kategorię.

    >[!Note]
    > Gdy jeden wybór zostanie dokonany na liście aspektów (takich jak kategoria) zastąpi wszystkie poprzednie zaznaczenia na liście kategorii.

## <a name="takeaways"></a>Wnioski

Rozważmy następujące dania na wynos z tego projektu:

* Konieczne jest oznaczenie każdej właściwości jako **IsFacetable**, jeśli mają one być uwzględnione w nawigacji aspekt.
* Nawigacja facet zapewnia użytkownikowi łatwy i intuicyjny sposób zawężenia wyszukiwania.
* Nawigacja facet jest najlepiej podzielona na sekcje (kategorie hotelu, udogodnienia hotelu, przedziały cenowe, zakresy klasyfikacji, itp.), każda sekcja z odpowiednim nagłówkiem.

## <a name="next-steps"></a>Następne kroki

W następnym samouczku przyjrzymy się zamówieniu wyników. Do tego momentu wyniki są uporządkowane po prostu w kolejności, w jakiej znajdują się w bazie danych.

> [!div class="nextstepaction"]
> [Samouczek C#: Zamów wyniki- Azure Cognitive Search](tutorial-csharp-orders.md)
