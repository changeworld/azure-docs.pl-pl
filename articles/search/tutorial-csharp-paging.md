---
title: C# samouczek na stronie podział wyników wyszukiwania
titleSuffix: Azure Cognitive Search
description: W tym samouczku pokazano stronicowanie wyników wyszukiwania. Opiera się na istniejącym projekcie hoteli, z stronicowania przez pierwszy, następny, poprzedni, ostatni i numerowane przyciski. Drugi system stronicowania używa nieskończonego przewijania, wyzwalanego przez przesunięcie pionowego paska przewijania do dolnej granicy.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 9abfeb54be6e22885b8e973034a6d89df8272146
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121525"
---
# <a name="c-tutorial-search-results-pagination---azure-cognitive-search"></a>Samouczek C#: Podział na strony wyników wyszukiwania — usługa Azure Cognitive Search

Dowiedz się, jak zaimplementować dwa różne systemy stronicowania, pierwszy oparty na numerach stron, a drugi na nieskończonym przewijaniu. Oba systemy stronicowania są szeroko stosowane, a wybór odpowiedniego zależy od doświadczenia użytkownika, które chcesz z wynikami. Ten samouczek tworzy systemy stronicowania w projekcie utworzonym w [samouczku języka C#: Utwórz pierwszą aplikację — samouczek Azure Cognitive Search.](tutorial-csharp-create-first-app.md)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Rozszerzanie aplikacji za pomocą ponumerowanego stronicowania
> * Rozszerzanie aplikacji dzięki nieskończonym przewijaniu

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

Mieć [c# Samouczek: Tworzenie pierwszej aplikacji — Azure Cognitive Search](tutorial-csharp-create-first-app.md) projektu i uruchomione. Ten projekt może być własną wersją lub zainstalować ją z GitHub: [Utwórz pierwszą aplikację.](https://github.com/Azure-Samples/azure-search-dotnet-samples)

## <a name="extend-your-app-with-numbered-paging"></a>Rozszerzanie aplikacji za pomocą ponumerowanego stronicowania

Ponumerowane stronicowanie to system stronicowania z wyboru głównych wyszukiwarek internetowych i większości innych witryn wyszukiwania. Ponumerowane stronicowanie zazwyczaj zawiera opcję "następny" i "poprzedni" oprócz zakresu rzeczywistych numerów stron. Dostępna może być również opcja "pierwsza strona" i "ostatnia strona". Te opcje z pewnością dają użytkownikowi kontrolę nad poruszaniem się po wynikach opartych na stronie.

Dodamy system, który zawiera pierwszą, poprzednią, następną i ostatnią opcje, wraz z numerami stron, które nie zaczynają się od 1, ale zamiast tego otaczają bieżącą stronę, na której znajduje się użytkownik (więc, na przykład, jeśli użytkownik patrzy na stronę 10, wyświetlane są numery stron 8, 9, 10, 11 i 12).

System będzie wystarczająco elastyczny, aby umożliwić ustawienie liczby widocznych numerów stron w zmiennej globalnej.

System potraktuje przyciski numerów stron najbardziej po lewej i prawej stronie jako specjalne, co oznacza, że wyzwolą zmianę zakresu wyświetlanych numerów stron. Jeśli na przykład zostaną wyświetlone numery stron 8, 9, 10, 11 i 12, a użytkownik kliknie 8, zakres wyświetlanych numerów stron zmieni się na 6, 7, 8, 9 i 10. I jest podobne przesunięcie w prawo, jeśli wybrali 12.

### <a name="add-paging-fields-to-the-model"></a>Dodawanie pól stronicowania do modelu

Otwórz podstawowe rozwiązanie strony wyszukiwania.

1. Otwórz plik SearchData.cs modelu.

2. Najpierw dodaj kilka zmiennych globalnych. W MVC zmienne globalne są deklarowane we własnej klasie statycznej. **ResultsPerPage** ustawia liczbę wyników na stronę. **MaxPageRange** określa liczbę widocznych numerów stron w widoku. **PageRangeDelta** określa, ile stron w lewo lub w prawo zakres stron powinien zostać przesunięty, gdy zaznaczony jest numer strony najbardziej lewej lub prawej. Zazwyczaj ta ostatnia liczba wynosi około połowy **MaxPageRange**. Dodaj następujący kod do obszaru nazw.

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
    >Jeśli ten projekt jest uruchomiony na urządzeniu z mniejszym ekranem, takim jak laptop, rozważ zmianę **programu ResultsPerPage** na 2.

3. Dodaj właściwości stronicowania do **SearchData** klasy, powiedzmy, po **searchText** właściwości.

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

### <a name="add-a-table-of-paging-options-to-the-view"></a>Dodawanie tabeli opcji stronicowania do widoku

1. Otwórz plik index.cshtml i dodaj następujący kod &lt;tuż&gt; przed tagiem zamknięcia /body. Ten nowy kod przedstawia tabelę opcji stronicowania: pierwszy, poprzedni, 1, 2, 3, 4, 5, następny, ostatni.

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

    Używamy tabeli HTML, aby starannie wyrównać rzeczy. Jednak wszystkie akcje pochodzą @Html.ActionLink z instrukcji, każdy wywołując kontroler z **nowym** modelem utworzonym z różnymi wpisami do właściwości **stronicowania** dodaliśmy wcześniej.

    Opcje pierwszej i ostatniej strony nie wysyłają ciągów, takich jak "pierwszy" i "ostatni", ale zamiast tego wysyłają poprawne numery stron.

2. Dodaj kilka klas stronicowania do listy stylów HTML w pliku hotels.css. **PageSelected** klasa służy do identyfikowania strony, na która użytkownik aktualnie wyświetla (przez pogrubienie liczby) na liście numerów stron.

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

### <a name="add-a-page-action-to-the-controller"></a>Dodawanie akcji strony do kontrolera

1. Otwórz plik HomeController.cs i dodaj akcję **Strona.** Ta akcja jest odpowiedzią na dowolną z wybranych opcji strony.

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

    **RunQueryAsync** Metoda będzie teraz pokazać błąd składni, ze względu na trzeci parametr, który przyjdzie do nieco.

    > [!Note]
    > **Wywołania TempData** przechowywać wartość **(obiekt)** w magazynie tymczasowym, chociaż ten magazyn będzie się powtarzał _tylko_ dla jednego wywołania. Jeśli zapiszemy coś w tymczasowych danych, będzie on dostępny dla następnego wywołania działania kontrolera, ale na pewno zniknie przez połączenie po tym! Ze względu na tę krótką żywotność, przechowujemy tekst wyszukiwania i właściwości stronicowania z powrotem w tymczasowym magazynie każdego połączenia do **strony**.

2. Akcja **Index(model)** musi być zaktualizowana w celu przechowywania zmiennych tymczasowych i dodania parametru strony po lewej stronie do wywołania **RunQueryAsync.**

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

3. **RunQueryAsync** metoda wymaga znacznie zaktualizowane. Pola **Skip**, **Top**i **IncludeTotalResultCount** klasy **SearchParameters** umożliwia żądanie wyników o wartości tylko jednej strony, zaczynając od ustawienia **Pomiń.** Musimy również obliczyć zmienne stronicowania dla naszego widoku. Zastąp całą metodę następującym kodem.

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
                leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            return View("Index", model);
        }
    ```

4. Wreszcie, musimy wprowadzić niewielką zmianę w widoku. **Zmienna resultsList.Results.Count** będzie teraz zawierać liczbę wyników zwróconych na jednej stronie (3 w naszym przykładzie), a nie całkowitą liczbę. Ponieważ ustawiliśmy **IncludeTotalResultCount** na true, **variable resultsList.Count** zawiera teraz całkowitą liczbę wyników. Więc zlokalizuj, gdzie liczba wyników jest wyświetlany w widoku i zmienić go na następujący kod.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Występuje trafienie wydajności, choć zwykle nie wiele z jednego, przez ustawienie **IncludeTotalResultCount** true, jak ta suma musi być obliczana przez usługę Azure Cognitive Search. W złożonych zestawach danych pojawia się ostrzeżenie, że zwracana wartość jest _przybliżeniem_. Dla naszych danych hotelowych, będzie to dokładne.

### <a name="compile-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Teraz wybierz **start bez debugowania** (lub naciśnij klawisz F5).

1. Wyszukaj tekst, który da mnóstwo wyników (np. Można strony starannie przez wyniki?

    ![Ponumerowane stronicowanie za pomocą wyników "puli"](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Spróbuj kliknąć na najczęściej po prawej stronie i nowsze numery stron po lewej stronie. Czy numery stron są odpowiednio dostosowywane do wyśrodkować stronę, na której się znajdujesz?

3. Czy przydatne są opcje "pierwszy" i "ostatni"? Niektóre popularne wyszukiwania w internecie korzystają z tych opcji, a inne nie.

4. Przejdź do ostatniej strony wyników. Ostatnia strona jest jedyną stroną, która może zawierać mniej niż **wyniki ResultsPerPage.**

    ![Badanie ostatniej strony "wifi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Wpisz "miasto" i kliknij szukaj. Nie są wyświetlane żadne opcje stronicowania, jeśli wyniki są mniej niż jedna strona.

    ![Wyszukiwanie "miasta"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Teraz zapisz ten projekt i spróbujmy alternatywy dla tej formy stronicowania.

## <a name="extend-your-app-with-infinite-scrolling"></a>Rozszerzanie aplikacji dzięki nieskończonym przewijaniu

Nieskończone przewijanie jest wyzwalane, gdy użytkownik przewija pionowy pasek przewijania do ostatniego wyświetlanego wyniku. W takim przypadku wywołanie serwera jest dokonywane dla następnej strony wyników. Jeśli nie ma więcej wyników, nic nie jest zwracany i pionowy pasek przewijania nie zmienia. Jeśli jest więcej wyników, są one dołączane do bieżącej strony, a pasek przewijania zmienia się, aby pokazać, że dostępnych jest więcej wyników.

Ważne jest to, że wyświetlana strona nie jest zastępowana, ale dołączana do nowych wyników. Użytkownik zawsze może przewinąć z powrotem do pierwszych wyników wyszukiwania.

Aby zaimplementować nieskończone przewijanie, zacznijmy od projektu przed dodaniem dowolnych elementów przewijania numeru strony. Tak więc, jeśli chcesz, zrób kolejną kopię podstawowej strony wyszukiwania z GitHub: [Utwórz pierwszą aplikację](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Dodawanie pól stronicowania do modelu

1. Najpierw dodaj właściwość **stronicowania** do klasy **SearchData** (w pliku modelu SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Ta zmienna jest ciągiem, który zawiera "następny", jeśli następna strona wyników powinna zostać wysłana lub wartość null dla pierwszej strony wyszukiwania.

2. W tym samym pliku i w obszarze nazw dodaj globalną klasę zmiennych z jedną właściwością. W MVC zmienne globalne są deklarowane we własnej klasie statycznej. **ResultsPerPage** ustawia liczbę wyników na stronę. 

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

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Dodawanie pionowego paska przewijania do widoku

1. Znajdź sekcję pliku index.cshtml, w których są wyświetlane wyniki (zaczyna się od ** @if (Model != null)**).

2. Zastąp sekcję poniższym kodem. Nowa ** &lt;sekcja&gt; div** znajduje się wokół obszaru, który powinien być przewijany, i dodaje zarówno atrybut **przepełnienia** i wywołanie funkcji **onscroll** o nazwie "scrolled()", podobnie jak tak.

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
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Bezpośrednio pod pętlą, po &lt;tagu&gt; /div, dodaj **przewijaną** funkcję.

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

    Instrukcja **if** w powyższym skrypcie sprawdza, czy użytkownik przewinął się do dołu pionowego paska przewijania. Jeśli tak, wywołanie kontrolera **macierzystego** jest wywoływane do akcji o nazwie **Dalej**. Administrator nie potrzebuje żadnych innych informacji, a następnie zwróci następną stronę danych. Te dane są następnie formatowane przy użyciu identycznych stylów HTML jak oryginalna strona. Jeśli żadne wyniki nie zostaną zwrócone, nic nie zostanie dołączone, a rzeczy pozostaną takie, jak są.

### <a name="handle-the-next-action"></a>Obsługa akcji Dalej

Istnieją tylko trzy akcje, które muszą być wysyłane do kontrolera: pierwsze uruchomienie aplikacji, która wywołuje **Index()**, pierwsze wyszukiwanie przez użytkownika, który wywołuje **Index (model),** a następnie kolejne wywołania więcej wyników za pośrednictwem **Next(model)**.

1. Otwórz plik kontrolera macierzystego i usuń **metodę RunQueryAsync** z oryginalnego samouczka.

2. Zastąp akcję **Index(model)** następującym kodem. Teraz obsługuje pole **stronicowania,** gdy jest null lub ustawiona na "dalej" i obsługuje wywołanie usługi Azure Cognitive Search.

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

    Podobnie jak w przypadku metody stronicowania numerowane, używamy **pomiń** i **Góry** ustawienia wyszukiwania, aby zażądać tylko danych, których potrzebujemy, jest zwracany.

3. Dodaj akcję **Dalej** do kontrolera macierzystego. Zwróć uwagę na to, jak zwraca listę, a każdy hotel dodaje do listy dwa elementy: nazwę hotelu i opis hotelu. Ten format jest ustawiony tak, aby **dopasować przewijane** funkcji korzystania z zwróconych danych w widoku.

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

4. Jeśli pojawia się błąd składni na **&lt;&gt;ciąg listy,** następnie dodać następujące **za pomocą** dyrektywy do głowy pliku kontrolera.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Kompilowanie i uruchamianie projektu

Teraz wybierz **start bez debugowania** (lub naciśnij klawisz F5).

1. Wprowadź termin, który da wiele wyników (takich jak "pool"), a następnie przetestować pionowy pasek przewijania. Czy wyzwala nową stronę wyników?

    ![Nieskończone przewijanie wyników "puli"](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Aby upewnić się, że pasek przewijania pojawia się na pierwszej stronie, pierwsza strona wyników musi nieznacznie przekraczać wysokość obszaru, w którym są wyświetlane. W naszym przykładzie **.box1** ma wysokość 30 pikseli, **.box2** ma wysokość 100 pikseli _i_ dolny margines 24 pikseli. Więc każdy wpis używa 154 pikseli. Trzy wpisy zajmą 3 x 154 = 462 piksele. Aby upewnić się, że wyświetlany jest pionowy pasek przewijania, należy ustawić wysokość obszaru wyświetlania, który jest mniejszy niż 462 piksele, a nawet 461 działa. Ten problem występuje tylko na pierwszej stronie, po tym pasek przewijania na pewno pojawi się. Linia do aktualizacji to: ** &lt;div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;"&gt;onscroll="scrolled()"**.

2. Przewiń w dół do dołu wyników. Zwróć uwagę, jak wszystkie informacje znajdują się teraz na stronie jednego widoku. Możesz przewijać całą drogę z powrotem na górę bez wyzwalania żadnych wywołań serwera.

Bardziej zaawansowane systemy nieskończonego przewijania mogą używać kółka myszy lub podobnego innego mechanizmu, aby wyzwolić ładowanie nowej strony wyników. Nie będziemy przy nieskończone przewijanie dalej w tych tutoriali, ale ma pewien urok do niego, ponieważ unika dodatkowych kliknięć myszką, i może chcesz zbadać inne opcje dalej!

## <a name="takeaways"></a>Wnioski

Rozważmy następujące dania na wynos z tego projektu:

* Ponumerowane stronicowanie jest dobre dla wyszukiwań, w których kolejność wyników jest nieco dowolna, co oznacza, że użytkownicy mogą być zainteresowani na późniejszych stronach.
* Nieskończone przewijanie jest dobre, gdy kolejność wyników jest szczególnie ważna. Na przykład, jeśli wyniki są uporządkowane na odległość od centrum miasta docelowego.
* Ponumerowane stronicowanie pozwala na lepszą nawigację. Na przykład użytkownik może pamiętać, że ciekawy wynik był na stronie 6, podczas gdy nie ma takiego łatwego odwołania istnieje w nieskończonym przewijaniu.
* Nieskończone przewijanie ma łatwy urok, przewijanie w górę iw dół bez wybrednych numerów stron do kliknięcia.
* Kluczową cechą nieskończonego przewijania jest to, że wyniki są dołączane do istniejącej strony, nie zastępując tej strony, co jest skuteczne.
* Magazyn tymczasowy utrzymuje się tylko dla jednego wywołania i musi zostać zresetowany, aby przetrwać dodatkowe wywołania.


## <a name="next-steps"></a>Następne kroki

Stronicowanie ma fundamentalne znaczenie dla wyszukiwania w Internecie. Z stronicowania dobrze omówione, następnym krokiem jest poprawa doświadczenia użytkownika dalej, dodając wyszukiwania typu z wyprzedzeniem.

> [!div class="nextstepaction"]
> [Samouczek C#: Dodawanie autouzupełniania i sugestii — Azure Cognitive Search](tutorial-csharp-type-ahead-and-suggestions.md)
