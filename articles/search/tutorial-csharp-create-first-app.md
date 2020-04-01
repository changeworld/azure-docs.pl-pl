---
title: Samouczek języka C#, aby utworzyć pierwszą aplikację
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak zbudować pierwszą aplikację do wyszukiwania języka C# krok po kroku. Samouczek zawiera zarówno łącze do działającej aplikacji w usłudze GitHub, jak i pełny proces tworzenia aplikacji od podstaw. Dowiedz się więcej o podstawowych składnikach usługi Azure Cognitive Search.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 2b4f67fc448d98239947fd764d4926f1d590c5e2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121591"
---
# <a name="c-tutorial-create-your-first-app---azure-cognitive-search"></a>Samouczek C#: Tworzenie pierwszej aplikacji — Azure Cognitive Search

Dowiedz się, jak utworzyć interfejs sieci web do wykonywania zapytań i prezentowania wyników wyszukiwania z indeksu przy użyciu usługi Azure Cognitive Search. Ten samouczek rozpoczyna się od istniejącego, hostowanego indeksu, dzięki czemu można skupić się na tworzeniu strony wyszukiwania. Indeks zawiera fikcyjne dane hotelowe. Gdy masz podstawową stronę, można ją ulepszyć w kolejnych lekcjach, aby uwzględnić stronicowanie, aspekty i środowisko typu z wyprzedzeniem.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie środowiska programistycznego
> * Struktury danych modelu
> * Tworzenie strony sieci Web
> * Definiowanie metod
> * Testowanie aplikacji

Dowiesz się również, jak proste jest połączenie wyszukiwania. Kluczowe instrukcje w kodzie, który opracujesz, są hermetyzowane w następujących wierszach.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

To jedno wywołanie inicjuje wyszukiwanie danych platformy Azure i zwraca wyniki.

![Wyszukiwanie "pool"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

[Zainstaluj program Visual Studio](https://visualstudio.microsoft.com/) do użycia jako IDE.

### <a name="install-and-run-the-project-from-github"></a>Instalowanie i uruchamianie projektu z usługi GitHub

1. Znajdź przykład w usłudze GitHub: [Utwórz pierwszą aplikację](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Wybierz **opcję Klonuj lub pobierz** i skonsuj prywatną lokalną kopię projektu.
1. Za pomocą programu Visual Studio przejdź do i otwórz rozwiązanie dla podstawowej strony wyszukiwania i wybierz start **bez debugowania** (lub naciśnij klawisz F5).
1. Wpisz kilka słów (na przykład "wifi", "view", "bar", "parking") i sprawdź wyniki!

    ![Wyszukiwanie "wifi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Mam nadzieję, że ten projekt będzie działać płynnie i masz uruchomię aplikację platformy Azure. Wiele podstawowych składników dla bardziej zaawansowanych wyszukiwań są zawarte w tej jednej aplikacji, więc jest to dobry pomysł, aby przejść przez nią i odtworzyć go krok po kroku.

Aby utworzyć ten projekt od podstaw, a tym samym pomóc wzmocnić składniki usługi Azure Cognitive Search w umyśle, przejdź przez następujące kroki.

## <a name="set-up-a-development-environment"></a>Konfigurowanie środowiska programistycznego

1. W programie Visual Studio 2017 lub nowszym wybierz pozycję **Nowy/Projekt,** a następnie **ASP.NET podstawową aplikację sieci Web**. Nadaj projektowi nazwę taką jak "FirstAzureSearchApp".

    ![Tworzenie projektu w chmurze](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Po kliknięciu **przycisku OK** dla tego typu projektu otrzymasz drugi zestaw opcji, które mają zastosowanie do tego projektu. Wybierz **aplikację sieci Web (model-view-controller)**.

    ![Tworzenie projektu MVC](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Następnie w menu **Narzędzia** wybierz pozycję **Menedżer pakietów NuGet,** a następnie **zarządzaj pakietami NuGet dla rozwiązania...**. Jest jeden pakiet, który musimy zainstalować. Wybierz kartę **Przeglądaj,** a następnie wpisz "Azure Cognitive Search" w polu wyszukiwania. Zainstaluj **microsoft.Azure.Search,** gdy pojawi się na liście (wersja 9.0.1 lub nowsza). Aby ukończyć instalację, należy kliknąć kilka dodatkowych okien dialogowych.

    ![Dodawanie bibliotek platformy Azure za pomocą nuget](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Inicjowanie usługi Azure Cognitive Search

W tym przykładzie używamy publicznie dostępnych danych hotelowych. Dane te są dowolnym zbiorem 50 fikcyjnych nazw hoteli i opisów, stworzonych wyłącznie w celu dostarczenia danych demonstracyjnych. Aby uzyskać dostęp do tych danych, należy określić nazwę i klucz dla niego.

1. Otwórz plik appsettings.json w nowym projekcie i zastąp domyślne linie następującą nazwą i kluczem. Pokazany tutaj klucz API nie jest przykładem klucza, jest _to dokładnie_ klucz potrzebny do uzyskania dostępu do danych hotelu. Plik appsettings.json powinien teraz wyglądać następująco.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Nie skończyliśmy jeszcze z tym plikiem, wybierz właściwości tego pliku i zmień ustawienie **Kopiuj na Katalog wyjściowy** na **Kopiuj, jeśli nowsze**.

    ![Kopiowanie ustawień aplikacji na dane wyjściowe](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Struktury danych modelu

Modele (klasy C#) są używane do przekazywania danych między klientem (widok), serwerem (kontrolerem), a także chmurą Platformy Azure przy użyciu architektury MVC (model, widok, kontroler). Zazwyczaj te modele będą odzwierciedlać strukturę danych, które są dostępne. Ponadto potrzebujemy modelu do obsługi komunikacji widoku/kontrolera.

1. Otwórz folder **Modele** projektu, używając Eksploratora rozwiązań, a zobaczysz tam jeden domyślny model: **ErrorViewModel.cs**.

2. Kliknij prawym przyciskiem myszy folder **Modele** i wybierz polecenie **Dodaj,** a następnie **wybierz pozycję Nowy element**. Następnie w wyświetlonym oknie dialogowym wybierz **ASP.NET Core,** a następnie pierwszą opcję **Klasa**. Zmień nazwę pliku cs, aby Hotel.cs, a następnie kliknij przycisk **Dodaj**. Zastąp całą zawartość Hotel.cs następującym kodem. Zwróć uwagę **na adres** i **pokój** członków klasy, te pola są same klasy, więc będziemy potrzebować modeli dla nich też.

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

3. Wykonaj ten sam proces tworzenia modelu dla **Address** klasy, z wyjątkiem nazwy pliku Address.cs. Zastąp zawartość na poniższą.

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

4. I ponownie, wykonaj ten sam proces, aby utworzyć **Room** klasy, nazywając plik Room.cs. Ponownie zastąp zawartość następującym.

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

5. Zestaw **zajęć Hotel**, **Address**i **Room** są znane na platformie Azure jako [_typy złożone_](search-howto-complex-data-types.md), ważną cechą usługi Azure Cognitive Search. Typy złożone mogą być wiele poziomów głębokie klas i podklas i umożliwić znacznie bardziej złożone struktury danych, które mają być reprezentowane niż przy użyciu _typów prostych_ (klasa zawierająca tylko podstawowe elementy członkowskie). Potrzebujemy jeszcze jednego modelu, więc przejdź przez proces tworzenia nowej klasy modelu ponownie, choć tym razem wywołać klasę SearchData.cs i zastąpić kod domyślny z następujących.

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

    Ta klasa zawiera dane wejściowe użytkownika **(searchText)** i dane wyjściowe wyszukiwania (**resultList**). Typ wyjścia jest krytyczny, **DocumentSearchResult&lt;&gt;Hotel**, ponieważ ten typ dokładnie pasuje do wyników wyszukiwania i musimy przekazać to odniesienie do widoku.



## <a name="create-a-web-page"></a>Tworzenie strony sieci Web

Utworzony projekt domyślnie utworzy liczbę widoków klienta. Dokładne widoki zależą od używanej wersji Core .NET (w tym przykładzie używamy wersji 2.1). Wszystkie znajdują się w folderze **Widoki** projektu. Wystarczy zmodyfikować plik Index.cshtml (w folderze **Widoki/Strona główna).**

Usuń zawartość index.cshtml w całości i odbuduj plik w poniższych krokach.

1. Używamy dwóch małych obrazów w widoku. Można użyć własnego lub skopiować obrazy z projektu GitHub: azure-logo.png i search.png. Te dwa obrazy powinny być umieszczone w folderze **wwwroot/images.**

2. Pierwszy wiersz Index.cshtml powinien odwoływać się do modelu, którego będziemy używać do przekazywania danych między klientem (widok) a serwerem (kontrolerem), który jest modelem **SearchData,** który utworzyliśmy. Dodaj ten wiersz do pliku Index.cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Standardową praktyką jest wprowadzanie tytułu widoku, więc następne wiersze powinny być:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Po tytule wprowadź odwołanie do arkusza stylów HTML, który wkrótce utworzymy.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Teraz do mięsa widoku. Kluczową rzeczą do zapamiętania jest to, że widok musi obsługiwać dwie sytuacje. Po pierwsze, musi obsługiwać wyświetlacz, gdy aplikacja jest uruchamiana po raz pierwszy, a użytkownik nie wprowadził jeszcze żadnego tekstu wyszukiwania. Po drugie, musi obsługiwać wyświetlanie wyników, oprócz pola tekstowego wyszukiwania, do wielokrotnego użytku przez użytkownika. Aby obsłużyć te dwie sytuacje, musimy sprawdzić, czy model dostarczony do widoku jest null, czy nie. Model null wskazuje, że znajdujemy się w pierwszej z dwóch sytuacji (początkowe uruchomienie aplikacji). Dodaj następujące elementy do pliku Index.cshtml i przeczytaj komentarze.

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
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Na koniec dodajemy arkusz stylów. W programie Visual Studio w menu **Plik** wybierz pozycję **Nowy/Plik,** a następnie **arkusz stylów** (z wyróżnioną wyróżnioną funkcją **Ogólne).** Zastąp domyślny kod na następujący. Nie będziemy wchodzić w ten plik bardziej szczegółowo, style są standardowe HTML.

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

7. Zapisz plik arkusza stylów jako hotels.css, w folderze wwwroot/css, obok domyślnego pliku site.css.

To dopełnia nasz pogląd. Robimy dobre postępy. Modele i widoki są kompletne, tylko kontroler pozostaje powiązać wszystko razem.

## <a name="define-methods"></a>Definiowanie metod

Musimy zmodyfikować zawartość jednego kontrolera (**Kontroler domowy**), który jest tworzony domyślnie.

1. Otwórz plik HomeController.cs i zastąp **instrukcje using** następującymi.

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

### <a name="add-index-methods"></a>Dodawanie metod indeksu

Potrzebujemy dwóch metod **indeksu,** jednej bez parametrów (w przypadku pierwszego otwarcia aplikacji) i jednej przy modelu jako parametru (dla gdy użytkownik wprowadził tekst wyszukiwania). Pierwsza z tych metod jest tworzona domyślnie. 

1. Dodaj następującą metodę po domyślnej metodzie **Index().**

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

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Zwróć uwagę na deklarację **asynchroniczna** metody i **wywołania await** do **RunQueryAsync**. Te słowa kluczowe zajmują się wykonywanie naszych połączeń asynchroniczne, a więc uniknąć blokowania wątków na serwerze.

    Catch **catch** bloku używa modelu błędu, który został utworzony dla nas domyślnie.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Zanotuj obsługę błędów i inne widoki i metody domyślne

W zależności od używanej wersji programu .NET Core domyślnie tworzony jest nieco inny zestaw widoków domyślnych. W przypadku platformy .NET Core 2.1 domyślnymi widokami są Indeks, Informacje, Kontakt, Prywatność i Błąd. Na przykład w przypadku platformy .NET Core 2.2 domyślne widoki to Indeks, Prywatność i Błąd. W obu przypadkach można wyświetlić te strony domyślne podczas uruchamiania aplikacji i sprawdzić, jak są one obsługiwane w kontrolerze.

Będziemy testować widok błędu w dalszej części tego samouczka.

W przykładzie GitHub usunęliśmy nieużywane widoki i skojarzone z nimi akcje.

### <a name="add-the-runqueryasync-method"></a>Dodaj RunQueryAsync metody

Wywołanie usługi Azure Cognitive Search jest hermetyzowane w naszej **metodzie RunQueryAsync.**

1. Najpierw dodaj niektóre zmienne statyczne, aby skonfigurować usługę platformy Azure, i wywołanie ich zainicjowania.

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

2. Teraz dodaj samą metodę **RunQueryAsync.**

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

    W tej metodzie najpierw upewnij się, że nasza konfiguracja platformy Azure jest inicjowana, a następnie ustawić niektóre parametry wyszukiwania. Nazwy pól w **Select** parametru dokładnie nazwy właściwości w **klasie hotelu.** Istnieje możliwość pominąć **Select** parametru, w którym to przypadku wszystkie właściwości są zwracane. Jednak ustawienie nie **Wybierz** parametry jest nieefektywne, jeśli jesteśmy zainteresowani tylko podzbiór danych. Określając właściwości, które nas interesują, zwracane są tylko te właściwości.

    Asynchroniczne wywołanie wyszukiwania **(model.resultList = await _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parametry);**) jest tym, o co chodzi w tym samouczku i aplikacji. **DocumentSearchResult** Klasa jest interesująca, a dobrym pomysłem (gdy aplikacja jest uruchomiona) jest ustawienie punktu przerwania w tym miejscu i użyć debugera, aby sprawdzić zawartość **model.resultList**. Powinieneś stwierdzić, że jest intuicyjny, dostarczając danych, o które prosiłeś, i niewiele więcej.

Teraz na chwilę prawdy.

### <a name="test-the-app"></a>Testowanie aplikacji

Teraz sprawdźmy, czy aplikacja działa poprawnie.

1. Wybierz **debugowanie/start bez debugowania** lub naciśnij klawisz F5. Jeśli masz kodowane rzeczy poprawnie, otrzymasz początkowy widok indeksu.

     ![Otwieranie aplikacji](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Wprowadź tekst, taki jak "plaża" (lub dowolny tekst, który przychodzi na myśl), i kliknij ikonę wyszukiwania. Powinieneś uzyskać pewne wyniki.

     ![Wyszukiwanie "plaży"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Spróbuj wprowadzić "pięć gwiazdek". Zwróć uwagę, jak uzyskać żadnych wyników. Bardziej wyrafinowane wyszukiwanie potraktuje "pięć gwiazdek" jako synonim "luksusu" i zwróci te wyniki. Użycie synonimów jest dostępne w usłudze Azure Cognitive Search, ale nie będziemy go omawiać w pierwszych samouczkach.
 
4. Spróbuj wprowadzić "hot" jako tekst wyszukiwania. _Nie_ zwraca wpisów ze słowem "hotel" w nich. Nasze wyszukiwanie lokalizowanie tylko całych słów, choć kilka wyników są zwracane.

5. Spróbuj innych słów: "basen", "słońce", "widok" i cokolwiek innego. Zobaczysz, że usługa Azure Cognitive Search działa na najprostszym, ale wciąż przekonującym poziomie.

## <a name="test-edge-conditions-and-errors"></a>Warunki i błędy krawędzi testowej

Ważne jest, aby sprawdzić, czy nasze funkcje obsługi błędów działają tak, jak powinny, nawet wtedy, gdy wszystko działa idealnie. 

1. W **index** metody, po **try {** wywołania, wprowadź wiersz **zgłosić nowy Exception()**. Ten wyjątek wymusi błąd podczas wyszukiwania tekstu.

2. Uruchom aplikację, wprowadź "pasek" jako tekst wyszukiwania i kliknij ikonę wyszukiwania. Wyjątek powinien spowodować widok błędu.

     ![Wymuszanie błędu](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Jest uważany za zagrożenie bezpieczeństwa, aby zwrócić wewnętrzne numery błędów na stronach błędów. Jeśli aplikacja jest przeznaczona do ogólnego użytku, wykonaj pewne badania dotyczące bezpiecznych i najlepszych rozwiązań dotyczących tego, co ma zostać odesłane w przypadku wystąpienia błędu.

3. Usuń **zrzuć nowy Exception(),** gdy użytkownik jest spełniony, obsługa błędów działa tak, jak powinna.

## <a name="takeaways"></a>Wnioski

Rozważmy następujące dania na wynos z tego projektu:

* Wywołanie usługi Azure Cognitive Search jest zwięzłe i można łatwo zinterpretować wyniki.
* Wywołania asynchroniczne dodać niewielką złożoność do kontrolera, ale są najlepszym rozwiązaniem, jeśli zamierzasz tworzyć aplikacje wysokiej jakości.
* Ta aplikacja przeprowadziła proste wyszukiwanie tekstu, zdefiniowane przez to, co jest skonfigurowane w **searchParameters**. Jednak ta jedna klasa może być wypełniona wieloma członkami, które dodają wyrafinowania do wyszukiwania. Niewiele dodatkowej pracy jest potrzebne, aby ta aplikacja znacznie bardziej wydajne.

## <a name="next-steps"></a>Następne kroki

Aby zapewnić najlepsze środowisko użytkownika przy użyciu usługi Azure Cognitive Search, musimy dodać więcej funkcji, w szczególności stronicowania (przy użyciu numerów stron lub nieskończone przewijanie) i autouzupełniania/sugestie. Powinniśmy również wziąć pod uwagę bardziej zaawansowane parametry wyszukiwania (na przykład wyszukiwania geograficzne w hotelach w określonym promieniu danego punktu i kolejność wyników wyszukiwania).

Te następne kroki zostały omówione w serii samouczków. Zacznijmy od stronicowania.

> [!div class="nextstepaction"]
> [Samouczek C#: Podział wyników wyszukiwania na strony — usługa Azure Cognitive Search](tutorial-csharp-paging.md)


