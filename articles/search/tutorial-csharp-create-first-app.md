---
title: C#samouczka, aby utworzyć swoją pierwszą aplikację — usługa Azure Search
description: Ten samouczek zawiera przewodnik krok po kroku dotyczące tworzenia pierwszej aplikacji usługi Azure Search. Samouczek zarówno z linkiem do działającą aplikację na platformie GitHub i kompletnego procesu, aby utworzyć aplikację od podstaw. Dowiedz się więcej na temat podstawowych składników usługi Azure Search.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: 71668b41125de323640dd668f733c1bd1982f583
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304498"
---
# <a name="c-tutorial-create-your-first-app---azure-search"></a>C#Samouczek: Tworzenie pierwszej aplikacji — Azure Search

Dowiedz się, jak utworzyć interfejs sieci web do zapytania i obecne wyniki wyszukiwania z indeksu za pomocą usługi Azure Search. Ten samouczek rozpoczyna się od istniejący indeks hostowanej, dzięki czemu możesz skupić się na tworzeniu strony wyszukiwania. Indeks zawiera hotelu fikcyjne dane. Po utworzeniu strony podstawowej, można zwiększyć, w kolejnych lekcjach obejmujący stronicowania, aspektami oraz środowisko wpisywania z wyprzedzeniem.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie środowiska deweloperskiego
> * Struktury danych modelu
> * Utwórz stronę sieci web
> * Definiowanie metody
> * Testowanie aplikacji

Dowiedz się także, jak proste jest wywołanie wyszukiwania. Kluczowe instrukcji w kodzie, użytkownik opracuje są hermetyzowane w następującym kilka wierszy.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Ten jedno wywołanie inicjuje wyszukiwanie danych platformy Azure i zwraca wyniki.

![Trwa wyszukiwanie "puli"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

[Zainstaluj program Visual Studio](https://visualstudio.microsoft.com/) do użycia jako IDE.

### <a name="install-and-run-the-project-from-github"></a>Instalowanie i uruchamianie projektu z usługi GitHub

1. Znajdź próbki w witrynie GitHub: [Tworzenie pierwszej aplikacji](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Wybierz **klonowania lub pobierania** i prywatne lokalną kopię projektu.
1. Za pomocą programu Visual Studio, przejdź do, otwórz rozwiązanie dla strony wyszukiwania podstawowego i wybierz pozycję **Uruchom bez debugowania** (lub naciśnij klawisz F5).
1. Wpisz część wyrazów (na przykład "sieć Wi-Fi", "view", "bar", "parkowania") i sprawdź wyniki!

    ![Trwa wyszukiwanie "Wi-Fi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Miejmy nadzieję ten projekt będzie działać bez problemów i masz uruchamianie aplikacji platformy Azure. Liczbę niezbędnych składników dla bardziej zaawansowanych wyszukiwania znajdują się w jednej aplikacji, dzięki czemu jest dobrym rozwiązaniem się i Utwórz ponownie krok po kroku.

Do tworzenia tego projektu, od początku, a więc wzmacnia składników usługi Azure Search w zdanie, wykonaj następujące kroki.

## <a name="set-up-a-development-environment"></a>Konfigurowanie środowiska deweloperskiego

1. W programie Visual Studio 2017 lub później, wybierz opcję **nowy lub projekt docelowy** następnie **aplikacji sieci Web programu ASP.NET Core**. Nazwij projekt takich jak "FirstAzureSearchApp".

    ![Tworzenie projektu w chmurze](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Po kliknięciu **OK** dla tego typu projektu, użytkownik będzie miał drugi zestaw opcji, które są stosowane do tego projektu. Wybierz **(Model-View-Controller) aplikacji sieci Web**.

    ![Tworzenie projektu MVC](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Następnie w **narzędzia** menu, wybierz opcję **Menedżera pakietów NuGet** i następnie **Zarządzaj pakietami NuGet dla rozwiązania...** . Istnieje jeden pakiet, który należy zainstalować. Wybierz **Przeglądaj** kartę, a następnie w polu wyszukiwania wpisz "Usługi Azure Search". Zainstaluj **Microsoft.Azure.Search** , gdy pojawia się na liście (w wersji 9.0.1, lub nowszej). Trzeba będzie przejść przez kilka dodatkowych okien dialogowych do ukończenia instalacji.

    ![Aby dodać bibliotek platformy Azure za pomocą narzędzia NuGet](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-search"></a>Inicjowanie usługi Azure Search

W tym przykładzie używamy hotelu publicznie dostępnych danych. Te dane są kolekcji 50 hotelu fikcyjne nazwy i opisy, tworzone wyłącznie w celu dostarczania danych demonstracyjnych. Aby uzyskać dostęp do tych danych, należy określić nazwę i klucz dla niego.

1. Otwórz plik appsettings.json w nowym projekcie i Zastąp domyślnych linii następująca Nazwa i klucz. Klucz interfejsu API, pokazywane w tym miejscu nie jest przykładem klucza, jest _dokładnie_ klucza, musisz uzyskać dostęp do danych w hotelu. Plik appsettings.json powinna teraz wyglądać następująco.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Firma Microsoft nie odbywa się za pomocą tego pliku, ale wybranie właściwości dla tego pliku i zmień **Kopiuj do katalogu wyjściowego** ustawienie **Kopiuj Jeśli nowszy**.

    ![Kopiowanie ustawień aplikacji do danych wyjściowych](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Struktury danych modelu

Modele (C# klas) są używane do przekazywania danych między klienta (Widok), serwera (kontroler) i chmury platformy Azure przy użyciu architektury MVC (model, widok kontrolera). Zazwyczaj te modele odzwierciedlają strukturę danych, który jest dostępny. Ponadto potrzebujemy modelu do obsługi komunikacji widok i kontroler.

1. Otwórz **modeli** folderu projektu, przy użyciu Eksploratora rozwiązań, a zostanie wyświetlony jeden domyślny model tam: **ErrorViewModel.cs**.

2. Kliknij prawym przyciskiem myszy **modeli** i wybierz polecenie **Dodaj** następnie **nowy element**. W wyświetlonym oknie dialogowym wybierz **platformy ASP.NET Core** następnie pierwsza opcja **klasy**. Zmień nazwę pliku .cs Hotel.cs, a następnie kliknij przycisk **Dodaj**. Zastąp zawartość pliku Hotel.cs następującym kodem. Zwróć uwagę **adres** i **pokoju** elementów członkowskich klasy te pola są klas samych, więc musimy modeli dla nich zbyt.

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

3. Postępuj zgodnie z tym samym procesie tworzenia modelu na potrzeby **adres** klasy, z wyjątkiem nazwy pliku Address.cs. Zastąp zawartość następujących czynności.

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. I ponownie, postępuj zgodnie z tego samego procesu tworzenia **pokoju** klasy, plikowi Room.cs. Ponownie Zastąp zawartość następujących czynności.

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. Zbiór **hotelu**, **adres**, i **pokoju** klas są określane na platformie Azure jako [ _typy złożone_ ](search-howto-complex-data-types.md), ważną funkcją usługi Azure Search. Typy złożone można mieć wiele poziomów w głąb klas i podklas i włączyć bardziej złożonych struktur danych może być reprezentowana niż przy użyciu _typów prostych_ (zawierający tylko do pierwotnych elementy członkowskie klasy). Potrzebujemy jeden model więcej, więc przejść przez proces tworzenia nowej klasy modelu ponownie, jednak tym razem wywołać klasy SearchData.cs i Zamień domyślny kod poniżej.

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    Ta klasa zawiera dane wejściowe użytkownika (**Tekstprzeszukiwany**), a wyszukiwanie danych wyjściowych (**resultList**). Typ danych wyjściowych ma krytyczne znaczenie, **DocumentSearchResult&lt;hotelu&gt;** , jak tego typu dokładnie odpowiada wyników wyszukiwania i dlatego trzeba przekazać odwołanie za pośrednictwem widoku.



## <a name="create-a-web-page"></a>Utwórz stronę sieci web

Projekt, który został utworzony domyślnie utworzy numer klienta widoków. Widoki dokładnie zależą od wersji programu .NET Core, używane są (używamy 2.1 w tym przykładzie). Są one wszystkie **widoków** folderu projektu. Tylko musisz zmodyfikować plik Index.cshtml (w **widoków domowych** folder).

Usuń zawartość Index.cshtml w całości, a następnie odbudować plik w poniższych krokach.

1. Używamy dwóch małych obrazów w widoku. Korzystanie z własnych lub skopiować między obrazy z projektu usługi GitHub: azure-logo.png i search.png. Te dwa obrazy powinny być umieszczone w **wwwroot/obrazów** folderu.

2. Pierwszy wiersz Index.cshtml powinny odwoływać się do modelu, firma Microsoft będzie używany do przekazywania danych między klientem (view) i serwera (kontroler), który jest **SearchData** modelu utworzyliśmy. Dodaj następujący wiersz do pliku Index.cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Jest standardową praktyką wprowadź tytuł dla widoku, więc powinno być następującymi wierszami:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Następujące tytułu wprowadź odwołanie do arkusza stylów HTML, który utworzymy wkrótce.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Teraz, aby rodzaje widoku. Kluczową kwestią do zapamiętania polega na tym, czy widoku musi do obsługi dwóch sytuacji. Po pierwsze jego musi obsługiwać wyświetlanie po pierwszym uruchomieniu aplikacji, a użytkownik nie wprowadził jeszcze dowolny tekst wyszukiwania. Po drugie jego musi obsługiwać wyświetlania wyników, oprócz polu tekstowym wyszukiwania do wielokrotnego użytku przez użytkownika. Aby obsługiwać te dwie sytuacje, musimy sprawdzić, czy model, pod warunkiem do widoku ma wartość null. Model o wartości null oznacza, że jesteśmy pierwszych dwóch sytuacji (początkowa działającej aplikacji). Dodaj następujący kod do pliku Index.cshtml i zapoznaj się z artykułem komentarze.

    ```cs
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea("desc", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Na koniec należy dodać arkusza stylów. W programie Visual Studio w **pliku** menu wybierz opcję **nowy/plik** następnie **arkusza stylów** (przy użyciu **ogólne** wyróżniony). Zamień domyślny kod poniżej. Firma Microsoft będzie nie przechodzić do tego pliku w żadnym bardziej szczegółowo, style standardowego kodu HTML.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

7. Zapisz plik arkusza stylów jako hotels.css, w folderze wwwroot/css, wraz z pliku site.css domyślne.

Nasze widok zostanie ukończona. Możemy to robi postępy. Modele i widoki zostały wykonane, pozostało tylko kontroler wszystko, czego powiązanie.

## <a name="define-methods"></a>Definiowanie metody

Potrzebujemy do zmodyfikowania zawartości jednego kontrolera (**Home kontrolera**), która jest domyślnie tworzone.

1. Otwórz plik HomeController.cs i Zastąp **przy użyciu** instrukcji następującym kodem.

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>Dodaj metody indeksu

Potrzebujemy dwa **indeksu** metod, co wykonanie żadnych parametrów (na potrzeby przypadków, po pierwszym otwarciu aplikacji) i jeden biorąc modelu jako parametru (w przypadku gdy użytkownik wpisze tekst wyszukiwania). Pierwsza z tych metod jest domyślnie tworzone. 

1. Dodaj następującą metodę po domyślnie **indeks()** metody.

    ```cs
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Zwróć uwagę **async** deklaracji metody, a **await** wywołanie **RunQueryAsync**. Te słowa kluczowe zajmie się wywołań naszych asynchronicznego i dlatego należy unikać blokowania wątków na serwerze.

    **Catch** błąd modelu, który został utworzony dla nas domyślnie korzysta z bloku.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Należy pamiętać, obsługa błędów i innych widokach domyślnych i metod

W zależności od wersji programu .NET Core używane są nieco inny zestaw domyślne widoki są tworzone domyślnie. W przypadku platformy .NET Core 2.1 w widokach domyślnych są indeks, informacje, skontaktuj się z pomocą, ochrony prywatności i błędów. Dla platformy .NET Core 2.2, na przykład domyślne widoki są indeksu, ochrony prywatności i błąd. W obu przypadkach można wyświetlić te domyślne strony podczas uruchamiania aplikacji i sprawdzić, jak są obsługiwane na kontrolerze.

Firma Microsoft będzie testowana widoku błędów później w tym samouczku.

W tym przykładzie GitHub został usunięty nieużywane widoków i skojarzonych z nimi działań.

### <a name="add-the-runqueryasync-method"></a>Dodaj metodę RunQueryAsync

Wywołanie usługi Azure Search jest hermetyzowany w naszym **RunQueryAsync** metody.

1. Najpierw dodaj statyczne zmienne konfiguracji usługi platformy Azure oraz wywołanie w celu zainicjowania je.

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. Teraz Dodaj **RunQueryAsync** sama metoda.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    W przypadku tej metody możemy najpierw upewnij się, nasze konfiguracji platformy Azure jest inicjowane, a następnie ustawić niektóre parametry wyszukiwania. Nazwy pól w **wybierz** parametr odpowiadać dokładnie nazwy właściwości w **hotelu** klasy. Istnieje możliwość Opuść **wybierz** parametru, w którym to przypadku zwracane są wszystkie właściwości. Jednak ustawienia nie **wybierz** parametrów jest nieefektywne, jeśli firma Microsoft jest zainteresowany wyłącznie podzbiór danych. Określając właściwości, które jesteśmy zainteresowani, zwracane są tylko te właściwości.

    Asynchroniczne wywołanie wyszukiwania (**model.resultList = await _indexClient.Documents.SearchAsync&lt;hotelu&gt;(model.searchText, parametry);** ) jest tego samouczka, a aplikacja to wszystko na temat. **DocumentSearchResult** klasy jest ciekawe i dobrze (Jeśli aplikacja jest uruchomiona) jest w tym miejscu Ustaw punkt przerwania i użyć debugera, aby sprawdzić zawartość **model.resultList**. Należy uznać, że jest intuicyjny, udostępniając dane, które prosił i nie znacznie else.

Teraz do prawdziwych danych z chwili.

### <a name="test-the-app"></a>Testowanie aplikacji

Teraz sprawdźmy poprawnie uruchomienia aplikacji.

1. Wybierz **debugowania/uruchomić bez debugowania** lub naciśnij klawisz F5. Jeśli zostały poprawnie kodowane rzeczy, zostanie wyświetlony widok początkowy indeks.

     ![Otwieranie aplikacji](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Wprowadź tekst, na przykład "beach" (lub dowolny tekst, który jest dostarczany do głowy), a następnie kliknij ikonę wyszukiwania. Powinna pojawić się niektórych wyników.

     ![Trwa wyszukiwanie "beach"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Spróbuj wprowadzić "five star". Należy pamiętać o tym, jak uzyskać żadnych wyników. Bardziej zaawansowanego wyszukiwania będzie traktować "five star" jako synonim dla "luksusowe" i zwracają te wyniki. Użyj synonimów jest dostępna w usłudze Azure Search, chociaż firma Microsoft będzie nie obejmował go w ramach pierwszej samouczków.
 
4. Spróbuj wprowadzić oznaczenie "gorąca" jako tekst wyszukiwania. Robi _nie_ Zwróć wpisów z wyrazem "hotel". Nasze wyszukiwanie tylko lokalizuje całe wyrazy, chociaż kilka wyniki są zwracane.

5. Spróbuj innych wyrazów: "puli", "słońca", "view" i niezależnie od rodzaju. Usługa Azure Search, pracy w najprostszym, ale nadal przekonujące poziom zostaną wyświetlone.

## <a name="test-edge-conditions-and-errors"></a>Warunki krawędzi testów i błędów

Należy sprawdzić, czy nasze funkcje obsługi błędów działają jak powinny, nawet wtedy, gdy jest to wszystko działa doskonale. 

1. W **indeksu** metoda po **spróbuj {** wywołanie, wprowadź wiersz **zgłosić nowy Exception()** . Ten wyjątek wymusza wystąpił błąd podczas przeszukiwania w tekście.

2. Uruchom aplikację, wprowadzić "bar", tak jak wyszukiwany tekst, a następnie kliknij ikonę wyszukiwania. Wyjątek powinno spowodować widoku błędów.

     ![Wymuś błąd](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Jest on uznawany za zagrożenie bezpieczeństwa do zwrócenia numery błąd wewnętrzny w stron błędów. Jeśli aplikacja jest przeznaczona do użytku ogólnego, należy wykonać niektóre sprawdzać bezpieczny i najważniejsze wskazówki co do zwrócenia, jeśli wystąpi błąd.

3. Usuń **zgłosić nowy Exception()** gdy jesteś zadowolony błąd obsługi działa zgodnie z oczekiwaniami.

## <a name="takeaways"></a>Wnioski

Należy wziąć pod uwagę następujące wnioski z tego projektu:

* Wywołanie usługi Azure Search są zwięzły, oraz łatwe do interpretacji wyników.
* Wywołania asynchroniczne dodać niewielką ilość złożoności z kontrolerem, ale są najlepszym rozwiązaniem, jeśli zamierzasz tworzyć wysokiej jakości aplikacji.
* Ta aplikacja przeszukiwanych prosty tekst, zdefiniowane przez co to jest skonfigurowana w **obiektu searchParameters**. Jednak można wypełnić to jedna klasa z wieloma elementami członkowskimi dodających wiedzy do wyszukiwania. Nie dużo dodatkowej pracy jest potrzebny do Udostępnij tę aplikację znacznie bardziej wydajne.

## <a name="next-steps"></a>Kolejne kroki

Aby zapewnić najlepsze środowisko użytkownika przy użyciu usługi Azure Search, należy dodać więcej funkcji, szczególnie stronicowania (przy użyciu numery stron lub przewijanie nieskończona) i automatycznego uzupełniania/sugestii. Możemy również uwzględnić bardziej zaawansowane parametry wyszukiwania (na przykład geograficzny wyszukiwania na hoteli w obrębie wybranego promienia danego punktu i wyniki wyszukiwania, w kolejności).

Podjęcie następujących kroków zostały rozwiązane w serii samouczków. Zacznijmy od stronicowania.

> [!div class="nextstepaction"]
> [C#Samouczek: Podział na strony — wyniki wyszukiwania, usługa Azure Search](tutorial-csharp-paging.md)


