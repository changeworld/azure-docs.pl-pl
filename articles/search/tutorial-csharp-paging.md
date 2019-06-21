---
title: C#samouczek dotyczący stronicowanie wyników wyszukiwania — usługa Azure Search
description: Ten samouczek opiera się na projekcie "Utwórz swoją pierwszą aplikację — usługa Azure Search", z możliwością wybrania dwa rodzaje stronicowania. Pierwszy korzysta z szeregu przycisków numer strony, a także pierwszy, następnie poprzedniego i ostatniej strony przycisków. Drugi stronicowania używane nieskończonej przewijania, zobaczyć wyzwalane przez przeniesienie pionowy pasek przewijania, do jego dolną granicę.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: fc2f358921380803e89c7a8ed5c2ef0fc8e1e467
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304319"
---
# <a name="c-tutorial-search-results-pagination---azure-search"></a>C#Samouczek: Podział na strony — wyniki wyszukiwania, usługa Azure Search

Dowiedz się, jak zaimplementować dwóch różnych systemów stronicowania, pierwszy na podstawie numery stron i drugi na nieskończonej przewijania. Obu systemów stronicowania są powszechnie używane, a następnie wybierając właściwy zależy od środowiska użytkownika, które Twoim zdaniem z wynikami. Ten samouczek opiera się systemy stronicowania do projektu utworzonego w [ C# samouczka: Tworzenie pierwszej aplikacji — Azure Search](tutorial-csharp-create-first-app.md) samouczka.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Rozszerz swoją aplikację przy użyciu numerowane stronicowania
> * Rozszerz swoją aplikację przy użyciu nieskończonej przewijania

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

Masz [ C# samouczka: Tworzenie pierwszej aplikacji — Azure Search](tutorial-csharp-create-first-app.md) projektu działanie. Ten projekt może być własną wersję lub go zainstalować z witryny GitHub: [Tworzenie pierwszej aplikacji](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Rozszerz swoją aplikację przy użyciu numerowane stronicowania

Numerowany stronicowania jest system stronicowania wybór główne aparaty wyszukiwania w Internecie i większości innych wyszukiwania witryn sieci Web. Numerowane stronicowania zazwyczaj zawiera "dalej" i "starszych" opcja poza zakres numerów rzeczywistą stronę. Również "pierwszy page" i "Ostatnia strona" opcja może być również dostępne. Te opcje bez obaw nadać użytkownikowi kontrolę nad przechodzenia między opartej na stronach wyników.

Dodamy systemu, która zawiera pierwszy, poprzednia, następna i ostatniej opcji wraz z numery stron, które nie rozpoczynają się od 1, ale zamiast tego Otocz bieżącej strony użytkownika znajduje się w (czyli na przykład, jeśli użytkownik przegląda strony 10, być może strona liczby 8 9, 10, 11 i 12 są wyświetlane).

System będzie wystarczająco elastyczny, aby zezwolić na liczbę cyfr widoczną stronę w zmiennej globalnej.

System będzie traktować skrajnie po lewej i prawej stronie przycisków cyfr jako specjalne, co oznacza, że spowoduje wyzwolenie zmienić zakres numerów wyświetlane. Na przykład jeśli są wyświetlane numery stron, 8, 9, 10, 11 i 12, a użytkownik kliknie 8, zakresu numerów strony wyświetlony zmiany do 6, 7, 8, 9 i 10. I ma podobnych przesunięcia w prawo, jeśli została wybrana opcja 12.

### <a name="add-paging-fields-to-the-model"></a>Dodawanie stronicowania pola do modelu

Mieć Otwórz rozwiązanie strony wyszukiwania podstawowego.

1. Otwórz plik modelu SearchData.cs.

2. Najpierw należy dodać niektóre zmienne globalne. W przypadku platformy MVC zmienne globalne są deklarowane w ich własnych klas statycznych. **: ResultsPerPage** Ustawia liczbę wyników na stronę. **MaxPageRange** określa liczbę cyfr widoczną stronę w widoku. **PageRangeDelta** określa liczbę stron w lewo lub w prawo, zakres stron powinny przesunięte, po wybraniu numer skrajnej lewej lub prawej strony. Zazwyczaj jest to ostatni numer wokół połowę **MaxPageRange**. Dodaj następujący kod do przestrzeni nazw.

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Jeśli używasz tego projektu na urządzeniu z mniejszych ekranu, takich jak laptop, rozważ zmianę **: ResultsPerPage** do 2.

3. Dodaj właściwości stronicowania do **SearchData** klasy powiedzieć, po **Tekstprzeszukiwany** właściwości.

    ```cs
        // The current page being displayed.
        public int currentPage { get; set; }

        // The total number of pages of results.
        public int pageCount { get; set; }

        // The left-most page number to display.
        public int leftMostPage { get; set; }

        // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
        public int pageRange { get; set; }

        // Used when page numbers, or next or prev buttons, have been selected.
        public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Dodaj tabelę opcje stronicowania do widoku

1. Otwórz plik index.cshtml i Dodaj następujący kod bezpośrednio przed tagiem zamykającym &lt;/body&gt; tagu. Ten nowy kod przedstawia tabelę opcje stronicowania: po pierwsze, poprzedniego, 1, 2, 3, 4, 5, następnie ostatnie.

    ```cs
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
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

            <td>
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
                <td>
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

            <td>
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

            <td>
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
    ```

    Używamy tabeli HTML do starannego wyrównania elementów. Jednak wszystkie akcje pochodzi z @Html.ActionLink instrukcji, każda wywołująca kontroler z **nowe** modelu utworzonego za pomocą różnych wpisów, aby **stronicowania** właściwości, które dodaliśmy wcześniej.

    Opcje pierwszej i ostatniej strony nie wysyłaj ciągów, takich jak "first" i "last", ale zamiast tego wysłać poprawne numery stron.

2. Niektóre klasy stronicowania należy dodać do listy stylów HTML w pliku hotels.css. **PageSelected** klasy polega na określeniu strony, użytkownik jest aktualnie wyświetlany (włączając numer pogrubienie) na liście numery stron.

    ```html
        .pageButton {
            border: none;
            color: darkblue;
            font-weight: normal;
            width: 50px;
        }

        .pageSelected {
            border: none;
            color: black;
            font-weight: bold;
            width: 50px;
        }

        .pageButtonDisabled {
            border: none;
            color: lightgray;
            font-weight: bold;
            width: 50px;
        }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Dodaj akcję strony do kontrolera

1. Otwórz plik HomeController.cs i Dodaj **strony** akcji. Ta akcja odnosi się do dowolnej wybrane opcje strony.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

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

                // Recover the search text and search for the data for the new page.
                model.searchText = TempData["searchfor"].ToString();

                await RunQueryAsync(model, page, leftMostPage);

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
                TempData["page"] = (object)page;
                TempData["searchfor"] = model.searchText;
                TempData["leftMostPage"] = model.leftMostPage;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

    **RunQueryAsync** metody będą teraz pokazywać błąd składniowy, ze względu na trzeci parametr, firma Microsoft rozpocznie się w nieco.

    > [!Note]
    > **TempData** wywołania przechowywać wartości ( **obiektu**) w magazyny tymczasowe, mimo że ten magazyn będzie nadal występować po _tylko_ jedno wywołanie. Jeśli coś są przechowywane w danych tymczasowych, będą dostępne dla następnego wywołania metody akcji kontrolera, ale najbardziej zdecydowanie znikną przez wywołanie po tym! Ze względu na to krótki czas przechowujemy wyszukiwany tekst i właściwości stronicowania ponownie do niej magazynu tymczasowego każdego wywołania **strony**.

2. **Index(model)** potrzeb akcji aktualizacji do przechowywania zmiennych tymczasowych i dodać parametr skrajnie po lewej stronie, aby **RunQueryAsync** wywołania.

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
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. **RunQueryAsync** potrzeb metoda znacznie zaktualizowany. Używamy **Pomiń**, **górnej**, i **IncludeTotalResultCount** pola **obiektu SearchParameters** klasy, aby zażądać tylko jedna strona, przez które wyniki, zaczynając od **Pomiń** ustawienie. Musimy również obliczyć zmienne stronicowania dla naszych widoku. Zastąp całą metodę z następującym kodem.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
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
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

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
                leftMostPage = Math.Min(leftMostPage + GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            return View("Index", model);
        }
    ```

4. Na koniec musimy wprowadzić niewielką zmianę w widoku. Zmienna **resultsList.Results.Count** będą teraz zawierać liczbę wyników zwróconych w jedną stronę (3 w naszym przykładzie), liczba całkowita. Ponieważ ustawiliśmy **IncludeTotalResultCount** na wartość true, zmienna **resultsList.Count** teraz zawiera całkowitą liczbę wyników. Więc zlokalizować, gdy liczba wyników jest wyświetlany w widoku, a następnie zmień go na następujący kod.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Nastąpi trafienie wydajności, chociaż nie jest zwykle znacznie jedna, ustawiając **IncludeTotalResultCount** na wartość true, ponieważ ta musi zostać obliczona przez usługę Azure Search. Za pomocą złożonych zestawów danych występuje ostrzeżenie, że wartość zwracana jest _zbliżenia_. Dla danych hotelu będzie ona dokładne.

### <a name="compile-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wybierz **Rozpocznij bez debugowania** (lub naciśnij klawisz F5).

1. Wyszukaj jakiś tekst, który zapewni odpowiednią ilość wyników (na przykład "sieć Wi-Fi"). Można podczas przeglądania starannego wyniki?

    ![Numerowany stronicować wyniki "puli"](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Spróbuj kliknąć na najdalej po prawej stronie, a później skrajnie po lewej stronie cyfry. Czy numery stron Dostosuj odpowiednio do strony, w której znajdują się w Centrum?

3. Opcje "first" i "last" są przydatne? Niektóre wyszukiwania popularnych internetowych za pomocą tych opcji, a inne nie.

4. Przejdź do ostatniej strony wyników. Ostatnia strona jest jedyną stroną, która może zawierać mniej niż **: ResultsPerPage** wyników.

    ![Badanie ostatniej strony "Wi-Fi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Wpisz "Miejscowość", a następnie kliknij przycisk wyszukiwania. Żadne opcje stronicowania są wyświetlane w przypadku mniejszym niż jedna strona pomagają w zrealizowaniu wyników.

    ![Trwa wyszukiwanie "Miejscowość"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Teraz zapisać ten projekt i spróbuj zamiast tego formularza stronicowania.

## <a name="extend-your-app-with-infinite-scrolling"></a>Rozszerz swoją aplikację przy użyciu nieskończonej przewijania

Nieskończona przewijania jest wyzwalany, gdy użytkownik przewija widok pionowy pasek przewijania do ostatniego elementu wyniki są wyświetlane. W takiej sytuacji do następnej strony wyników Wykonano wywołanie z serwerem. Jeśli nie ma żadnych innych wyników, nic nie zostanie zwrócone, a pionowy pasek przewijania nie zmienia się. W przypadku więcej wyników są one dołączane do bieżącej strony i zmiany paska przewijania, aby pokazać, że będą dostępne wyniki.

Istotną kwestią jest, czy strony są wyświetlane nie są zastępowane, ale dołączonych do przy użyciu nowych wyników. Użytkownik może zawsze przewijać kopii zapasowej na pierwsze wyniki wyszukiwania.

Aby zaimplementować nieskończonej przewijania, Zacznijmy od projektu przed dodaniem elementy przewijania numeru strony. Tak Jeśli zachodzi potrzeba, należy utworzyć kolejną kopię strony wyszukiwania podstawowego z usługi GitHub: [Tworzenie pierwszej aplikacji](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Dodawanie stronicowania pola do modelu

1. Najpierw dodaj **stronicowania** właściwości **SearchData** klasy (w pliku modelu SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Ta zmienna jest ciąg, który zawiera "dalej", jeśli mają być wysyłane następnej strony wyników lub mieć wartości null dla pierwszej strony wyszukiwania.

2. W tym samym pliku, a w przestrzeni nazw należy dodać klasy zmiennej globalnej z jedną właściwością. W przypadku platformy MVC zmienne globalne są deklarowane w ich własnych klas statycznych. **: ResultsPerPage** Ustawia liczbę wyników na stronę. 

    ```cs
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Dodanie pionowy pasek przewijania widoku

1. Zlokalizuj sekcję pliku index.cshtml, który służy do wyświetlania wyników (zaczyna się od  **@if (Model! = null)** ).

2. Zastąp sekcję poniższym kodem. Nowy **&lt;div&gt;** sekcja jest wokół obszaru, który powinien być przewijany i dodaje zarówno **overflow-y** atrybut i wywołanie **onscroll**funkcję o nazwie "scrolled()" w następujący sposób.

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea("desc", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Bezpośrednio pod pętli po &lt;/DIV&gt; tagów, należy dodać **przewijane** funkcji.

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 2) {
                                div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                            }
                        });
                    }
                }
        </script>
    ```

    **Jeśli** instrukcji w skrypcie powyższe testy, aby zobaczyć, jeśli użytkownik ma być przewijane w dolnej części paska przewijania pionowego. Jeśli mają wywołanie **Home** kontroler zostanie podjęta Akcja wywoływana **dalej**. Żadne inne informacje są wymagane przez administratora, to zostanie zwrócona następnej strony danych. Tych danych będzie sformatowany przy użyciu identycznych stylów HTML jako oryginalnej strony. Jeśli żadne wyniki nie zostaną zwrócone, nic nie jest dołączany i pozostaną czynności, jak są one.

### <a name="handle-the-next-action"></a>Dojście do następnej akcji

Tylko trzy czynności, które muszą być wysyłane do kontrolera: pierwsze uruchomienie aplikacji, która wywołuje **indeks()** , pierwszy wyszukiwanie według użytkownika, który wywołuje **Index(model)** , a następnie kolejne wymaga więcej wyników za pośrednictwem **Next(model)** .

1. Otwórz plik głównego kontrolera i Usuń **RunQueryAsync** metody z oryginalnego samouczka.

2. Zastąp **Index(model)** akcji z następującym kodem. Teraz obsługuje **stronicowania** pole ma wartość null lub wartość "dalej", a obsługi wywołań do usługi Azure Search.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First call. Check for valid text input.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    page = 0;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
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

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Podobnie jak numerowane metody stronicowania, używamy **Pomiń** i **górnej** ustawienia wyszukiwania, aby zażądać tylko te dane, które są potrzebne jest zwracana.

3. Dodaj **dalej** akcji do głównego kontrolera. Należy zauważyć, jak funkcja zwraca listę każdego hotelu, dodając dwa elementy do listy: Nazwa hotelu i opis hotelu. Ten format jest ustawiony na zgodny **przewijane** użycie funkcji zwracanych danych w widoku.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Jeśli otrzymujesz błąd składniowy **listy&lt;ciąg&gt;** , dodaj następującą **przy użyciu** dyrektywę nagłówek pliku kontrolera.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Kompilowanie i uruchamianie projektu

Teraz wybierz **Rozpocznij bez debugowania** (lub naciśnij klawisz F5).

1. Wprowadź termin, który będzie zapewniają odpowiednią ilość wyników (na przykład "puli"), a następnie przetestować pionowy pasek przewijania. Powoduje wyzwolenia nową stronę wyników?

    ![Nieskończona przewijanie za pośrednictwem wyników z "puli"](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Aby upewnić się, czy pasek przewijania jest wyświetlany na pierwszej stronie pierwszej strony wyników nieco przekraczać wysokość obszaru, w którym zostaną one wyświetlone w. W naszym przykładzie **.box1** wysokość 30 pikseli **.box2** o wysokości 100 pikseli _i_ dolny margines 24 pikseli. Dlatego każdego wpisu używa 154 pikseli. Trzy wpisy zajmuje się 3 x 154 = 462 pikseli. Aby upewnić się, że pionowy pasek przewijania jest wyświetlany, wysokość, do obszaru wyświetlania muszą być ustawione, jest mniejszy niż 462 pikseli, nawet 461 działa. Ten problem występuje tylko na pierwszej stronie po tym to się pojawiają się, że pasek przewijania. Wiersz, aby zaktualizować:  **&lt;identyfikatora DZIEL = "myDiv" style = "width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()"&gt;** .

2. Przewiń w dół do dolnej części wyników. Zwróć uwagę, jak wszystkie informacje są teraz na stronie jeden widok. Podczas przewijania, aż do góry bez powodowania żadnych wywołań serwera.

Bardziej zaawansowanych nieskończonej systemów przewijania mogą używać kółka myszy lub podobne innego mechanizmu, aby wyzwolić ładowanie nowej strony wyników. Firma Microsoft nie być wykorzystywana nieskończonej przewijanie dowolne dalsze w tych samouczkach, ale ma pewne uda mu pozwala ona na uniknięcie dodatkowych myszą i możesz chcieć zbadać inne opcje dodatkowo!

## <a name="takeaways"></a>Wnioski

Należy wziąć pod uwagę następujące wnioski z tego projektu:

* Numerowany stronicowania jest dobra dla wyszukiwań, w którym kolejność wyników jest nieco dowolnego, co oznacza mogą istnieć również być w stanie się coś istotnego użytkownikom na kolejnych stronach.
* Nieskończona przewijania jest dobra w przypadku, gdy szczególnie ważna jest kolejność wyników. Na przykład, jeśli wyniki są uporządkowane na odległość od centrum miasta docelowego.
* Numerowany stronicowania umożliwia lepsze nawigacji. Na przykład użytkownik może należy pamiętać, że interesujące wyniku na stronie 6, istnieje żadne odwołanie łatwy w nieskończonej przewijania.
* Przewijanie nieskończona ma proste odwołania, przewijania w górę i w dół bez numerów kłopotliwych strony kliknij.
* Kluczową cechą nieskończonej przewijania jest, że wyniki są dołączane do istniejącej strony, nie zastąpienie tej strony, która jest efektywne.
* Magazyn tymczasowy będzie nadal występować tylko jedno wywołanie i powinna zostać zresetowana przetrwać dodatkowych połączeń.


## <a name="next-steps"></a>Kolejne kroki

Stronicowanie ma podstawowe znaczenie dla wyszukiwania w Internecie. Za pomocą stronicowania również objęte usługą, następnym krokiem jest usprawnienie użytkownika sprawy, dodając wyszukiwanie podpowiedziami pojawiającymi.

> [!div class="nextstepaction"]
> [C#Samouczek: Dodaj automatycznego uzupełniania i sugestie — usługa Azure Search](tutorial-csharp-type-ahead-and-suggestions.md)
