---
title: C#samouczek dotyczący korzystania z zestawów reguł do pomocy Nawigacja — usługa Azure Search
description: Ten samouczek opiera się na "Podział na strony — Usługa Azure Search w wynikach wyszukiwania" projektu, aby dodać funkcje nawigacji zestawu reguł. Dowiedz się, że aspektami może służyć do łatwego zawęzić kryteria wyszukiwania.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: 62326ad3bc5f2d740ce744819df559bce8658eb7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443771"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-search"></a>C#Samouczek: Użyj aspektami ułatwiające Nawigacja — usługa Azure Search

Zestawy reguł są używane do pomocy nawigacji, zapewniając użytkownika z zestawu łączy, można użyć do skoncentrowania się ich wyszukiwanie. Zestawy reguł są atrybuty danych (na przykład kategoria lub określoną funkcję hotelu w nasze przykładowe dane).

Ten samouczek opiera się na stronicowania projektu utworzonego w [ C# samouczka: Podział na strony — wyniki wyszukiwania, usługa Azure Search](tutorial-csharp-paging.md) samouczka.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Ustaw właściwości modelu jako _IsFacetable_
> * Dodawanie reguł nawigacji do aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

Masz [ C# samouczka: Podział na strony — wyniki wyszukiwania, usługa Azure Search](tutorial-csharp-paging.md) projektu działanie. Ten projekt może być własną wersję lub go zainstalować z witryny GitHub: [Tworzenie pierwszej aplikacji](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Ustawianie właściwości modelu jako IsFacetable

Aby dla właściwości modelu muszą znajdować się w wyszukiwania reguł, muszą być oznakowane za pomocą **IsFacetable**.

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

2. Firma Microsoft nie zmienia się żadnych tagów w ramach tego samouczka, więc Zamknij pliku hotel.cs niezmieniony.

    > [!Note]
    > Wyszukiwanie zestawu reguł będzie sygnalizować błąd, jeśli pole wymagane w wyszukiwaniu nie jest oznaczone tagami odpowiednio.


## <a name="add-facet-navigation-to-your-app"></a>Dodawanie reguł nawigacji do aplikacji

W tym przykładzie użyjemy umożliwia użytkownikowi wybranie jednej kategorii w hotelu lub rekreacyjne jednego z listy łączy wyświetlane po lewej stronie wyników. Rozpoczyna się użytkownika, wprowadzając niektóre wyszukiwany tekst, a następnie można zawęzić wyniki wyszukiwania, wybierając odpowiednią kategorię i zawęzić wyniki dodatkowo, wybierając rekreacyjne lub użytkownik może wybrać rekreacyjne pierwszy (kolejność nie jest ważna).

Potrzebujemy kontrolera w celu przekazania listę zestawów reguł do widoku. Musimy zachować wybór użytkownika w miarę postępów wyszukiwania, a następnie ponownie magazynu tymczasowego zostaną wykorzystane jako ochronę danych przy użyciu mechanizmu.

![Zawęź wyszukiwanie "puli" przy użyciu reguł nawigacji](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Dodaj parametry filtru do modelu SearchData

1. Otwórz plik SearchData.cs i Dodaj właściwości ciągu do **SearchData** klasy do przechowywania ciągów Filtr zestawu reguł.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Dodaj metodę akcji reguł

Główny kontroler musi jedną nową akcję, **aspekt**i aktualizacje istniejące **indeksu** i **strony** akcje, a także aktualizacje **RunQueryAsync**  metody.

1. Otwórz plik głównego kontrolera i Dodaj **przy użyciu** instrukcji, aby umożliwić **listy&lt;ciąg&gt;**  konstruowania.

    ```cs
    using System.Collections.Generic;
    ```

2. Zastąp **indeksu (SearchData model)** metody akcji.

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

3. Zastąp **strony (SearchData model)** metody akcji.

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

4. Dodaj **aspekt (SearchData model)** metody akcji, zostanie uaktywniony, gdy użytkownik kliknie link zestawu reguł. Model będzie zawierać filtr wyszukiwania kategorii lub rekreacyjne filtr wyszukiwania. Być może dodać go po **strony** akcji.

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

### <a name="set-up-the-search-filter"></a>Ustawić filtr wyszukiwania

Gdy użytkownik wybierze niektórych reguł, na przykład kliknięciu **możliwości i Spa** kategorii, a następnie tylko hotele, które są zdefiniowane w tej kategorii powinny być zwrócone w wynikach. Aby zawęzić zakres wyszukiwania w ten sposób, musimy skonfigurować _filtru_.

1. Zastąp **RunQueryAsync** metoda następującym kodem. Przede wszystkim przyjmuje ciąg filtru kategorii i ciąg filtru rekreacyjne i ustawia **filtru** parametru **obiektu SearchParameters**.

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

    Dodaliśmy **kategorii** i **tagi** właściwości do listy **wybierz** elementów do zwrócenia. To dodawanie nie jest wymagane dla reguł nawigacji do pracy, ale używamy tych informacji, aby zweryfikować, że firma Microsoft filtrowania poprawnie.

### <a name="add-lists-of-facet-links-to-the-view"></a>Dodawanie listy reguł łączy do widoku

Widok będzie wymagać wprowadzono istotne zmiany. 

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

2. Widoku firma Microsoft organizować dane wyjściowe w tabeli, do starannego wyrównania aspekt Wyświetla z lewej strony oraz wyniki po prawej stronie. Otwórz plik index.cshtml. Zamień całą zawartość HTML &lt;treści&gt; tagów, z następującym kodem.

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

    Zwróć uwagę na **Html.ActionLink** wywołania. To wywołanie komunikuje się ciągi prawidłowy filtr do kontrolera, gdy użytkownik kliknie łącze zestawu reguł. 

### <a name="run-and-test-the-app"></a>Uruchamianie i testowanie aplikacji

Zaletą aspekt nawigacji do użytkownika jest to, że one zawęzić kryteria wyszukiwania za pomocą jednego kliknięcia pokazujemy w następującej kolejności.

1. Uruchom aplikację, typu "port lotniczy" jako tekst wyszukiwania. Sprawdź listę zestawów reguł pojawia się po lewej stronie starannego. Te zestawy reguł są wszystkie opcje, które dotyczą hotele, które mają "port lotniczy" w ich dane tekstowe, wraz z liczbą jak często występują one.

    ![Zawęź wyszukiwanie "lotniczego" przy użyciu reguł nawigacji](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Kliknij przycisk **możliwości i Spa** kategorii. Sprawdź, czy wszystkie wyniki znajdują się w tej kategorii.

    ![Zawężanie wyszukiwania dla "Możliwości i Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Kliknij przycisk **śniadanie kontynentalnych** rekreacyjne. Sprawdź, czy wszystkie wyniki wciąż znajdują się w kategorii "Możliwości i Spa", przy użyciu wybranego rekreacyjne.

    ![Zawężanie wyszukiwanie "kontynentalnych śniadanie"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Spróbuj wybrać innej kategorii, następnie co rekreacyjne i wyświetlić wyniki zawężającą. Spróbuj odwrotnie, co rekreacyjne, a następnie jedną kategorię.

    >[!Note]
    > Po dokonaniu wyboru jednej liście reguł (na przykład kategoria) zastąpią wszelkie poprzednie wybór na liście kategorii.

## <a name="takeaways"></a>Wnioski

Należy wziąć pod uwagę następujące wnioski z tego projektu:

* Konieczne jest do oznaczania każdej właściwości jako **IsFacetable**, jeśli mają zostać uwzględnione w nawigacji zestawu reguł.
* Aspekt nawigacji zapewnia użytkownikowi proste i intuicyjne, sposób zawęzić wyszukiwanie.
* Aspekt nawigacji najlepiej jest podzielona na sekcje (kategorie hotelu), pozwalającego hotel, cena zakresów, ocena zakresów itp., każda sekcja z odpowiednią nazwą.

## <a name="next-steps"></a>Kolejne kroki

W następnym samouczku przyjrzymy się porządkowanie wyników. Do tej pory wyniki są uporządkowane po prostu w kolejności, znajdują się one w bazie danych.

> [!div class="nextstepaction"]
> [C#Samouczek: Kolejność wyników — usługi Azure Search](tutorial-csharp-orders.md)
