---
title: C#samouczek dotyczący porządkowanie wyników — usługa Azure Search
description: Ten samouczek opiera się na projekcie "Podział na strony — Usługa Azure Search w wynikach wyszukiwania", aby dodawać, porządkowanie wyników wyszukiwania. Dowiedz się, jak kolejność wyników na właściwość podstawowego, a dla wyników, które mają tę samą właściwość podstawowy, sposób uporządkowania wyników dodatkowych właściwości. Na koniec sposób uporządkowania wyników na podstawie profilu oceniania.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/21/2019
ms.openlocfilehash: 32e253b4e131d753ab6937d0aa2a49bda471e091
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466531"
---
# <a name="c-tutorial-order-the-results---azure-search"></a>C#Samouczek: Kolejność wyników — usługa Azure Search

Do tego punktu w serii samouczków wyniki są zwracane, a następnie wyświetlane na domyślną kolejność. Może to być kolejność, w którym znajdują się dane, lub ewentualnie domyślną _profil oceniania_ została zdefiniowana, który będzie używany, gdy nie określono żadnych parametrów szeregowania. W tym samouczku przejdziemy będzie w sposób uporządkowania wyników na podstawie właściwości podstawowy, a następnie dla wyników, które mają tę samą właściwość podstawowy, jak kolejność wybieranie dodatkowych właściwości. Jako alternatywę do porządkowania oparte na wartości liczbowe końcowy przykład pokazuje, jak kolejność na podstawie niestandardowego profilu oceniania. Firma Microsoft będzie również bardziej szczegółowo nieco omawiają wyświetlanie _typy złożone_.

Aby można było łatwo porównać zwróconych wyników, ten projekt jest kompilowany na nieskończonej przewijania projektu utworzonego w [ C# samouczka: Podział na strony — wyniki wyszukiwania, usługa Azure Search](tutorial-csharp-paging.md) samouczka.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Wyniki na podstawie jednej właściwości.
> * Wyniki oparte na wiele właściwości atrybutu
> * Filtruj wyniki w oparciu odległości od punktu geograficznych
> * Wyniki oparte na profilu oceniania

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

Wersja nieskończonej przewijania [ C# samouczka: Podział na strony — wyniki wyszukiwania, usługa Azure Search](tutorial-csharp-paging.md) projektu działanie. Ten projekt może być własną wersję lub go zainstalować z witryny GitHub: [Tworzenie pierwszej aplikacji](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Wyniki na podstawie jednej właściwości.

Gdy firma Microsoft kolejność wyników na podstawie jednej właściwości, załóżmy, że hotelu klasyfikacji, nie ma być uruchamiany tylko wyniki uporządkowane, chcemy również potwierdzenie, że kolejność jest poprawna. Innymi słowy Jeśli firma Microsoft zamówienie na ocenę, firma Microsoft powinien być wyświetlany tę klasyfikację w widoku.

W tym samouczku także dodamy nieco bardziej do wyświetlenia wyników, najtańszej pokoju i najbardziej kosztowne stopa miejsca dla każdego hotelu. Jak możemy delve, w kolejności, będziemy również dodawać wartości, aby upewnić się, co to są możemy porządkowanie dla jest również wyświetlany w widoku.

Nie ma potrzeby, aby zmodyfikować dowolne modele umożliwiające określanie kolejności. Widok i potrzebują kontrolera zaktualizowanych. Rozpocznij, otwierając głównego kontrolera.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Dodaj właściwość OrderBy do parametrów wyszukiwania

1. Wszystko, co może potrwać do kolejności wyników na podstawie właściwości pojedynczej wartości liczbowych, polega na ustawieniu **OrderBy** parametru, aby nazwa właściwości. W **indeksu (SearchData model)** metody, Dodaj następujący wiersz do parametrów wyszukiwania.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > Domyślna kolejność jest rosnący, że można dodawać **asc** do właściwości, aby było jasne. Malejącej jest określony, dodając **desc**.

2. Teraz uruchom aplikację, a następnie wprowadź wszelkie wspólny termin wyszukiwania. Wyniki mogą lub mogą nie być w odpowiedniej kolejności jako deweloper, a nie użytkownika zrobiło tego też żadnych prosty sposób sprawdzania, czy wyniki!

3. Upewnijmy się je Wyczyść, wyniki są uporządkowane według klasyfikacji. Najpierw Zastąp **pole1** i **pole 2** klasy w pliku hotels.css za pomocą następujących klas (te klasy są nowe licencje potrzebujemy na potrzeby tego samouczka).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    >[!Tip]
    >Przeglądarki zwykle pamięci podręcznej plików css i może to prowadzić do starego pliku css, które są używane i edycji ignorowane. Dobrym sposobem round to jest dodanie ciągu zapytania za pomocą parametru wersji do łącza. Na przykład:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Jeśli uważasz, że stary plik css, jest on używany przez przeglądarkę, należy zaktualizować numer wersji.

4. Dodaj **ocena** właściwości **wybierz** parametru w **indeksu (SearchData model)** metody.

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Powoduje ono otwarcie widoku (index.cshtml) i Zastąp pętlą renderowania ( **&lt;! — Pokaż dane hotelu.--&gt;** ) z następującym kodem.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. Tę klasyfikację musi być dostępne zarówno na pierwszej stronie wyświetlane, jak i na kolejnych stronach, które są wywoływane za pośrednictwem nieskończonej przewijania. W przypadku drugiego nagłówka tych dwóch sytuacji należy zaktualizować obydwa **dalej** akcji w kontrolerze i **przewijane** funkcji w widoku. Począwszy od kontrolera, zmień **dalej** metody z następującym kodem. Ten kod tworzy i komunikuje się tekst klasyfikacji.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. Teraz zaktualizować **przewijane** funkcji w widoku, aby wyświetlić tekst klasyfikacji.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

8. Teraz ponownie uruchomić tę aplikację. Wyszukiwanie dowolnego wspólny termin, takie jak "Wi-Fi" i sprawdź, czy wyniki są uporządkowane według malejącej oceny hotelu.

    ![Kolejność na podstawie klasyfikacji](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Zauważysz, że kilka hotele mają identyczne klasyfikacji i dlatego ich wygląd na ekranie jest ponownie kolejność, w którym znajduje się dane, czyli dowolnego.

    Zanim spojrzymy na dodawanie drugiego poziomu, w kolejności, możemy dodać niektóre kod, aby wyświetlić zakres częstotliwości pokoju. Dodajemy ten kod do pokazania, zarówno wyodrębnianie danych z _typu złożonego_, a także dzięki czemu można omawiać porządkowanie wyników na podstawie ceny (najtańszej pierwszy przykład).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Dodaj zakres pokoju stawek do widoku

1. Dodaj właściwości zawierające szybkość najtańszej i najbardziej kosztowne pokoju w pliku Hotel.cs modelu.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Oblicz stawki miejsca na końcu **indeksu (SearchData model)** akcji na głównym kontrolerze. Po zapisaniu danych tymczasowych, należy dodać obliczeń.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

3. Dodaj **pokojów** właściwości **wybierz** parametru w **indeksu (SearchData model)** metody akcji kontrolera.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Zmień pętlą renderowania widoku, aby wyświetlić zakres stawki dla pierwszej strony wyników.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. Zmiana **dalej** metody w domu kontrolera do komunikowania się w zakresie szybkości dla kolejnych stron wyników.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. Aktualizacja **przewijane** funkcji w widoku, aby obsłużyć pokoju ocenia tekstu.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

7. Uruchom aplikację i sprawdź, czy zakresy współczynnik miejsca są wyświetlane.

    ![Wyświetlanie miejsca częstotliwość zakresów](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

**OrderBy** właściwości parametry wyszukiwania nie będzie akceptować wpis takich jak **Rooms.BaseRate** zapewnienie najtańszej pokoju, nawet wtedy, gdy pomieszczeniach już zostały posortowane według stawki, (które nie są one). Aby można było wyświetlić hoteli w zestawie danych przykładowych, uporządkowane według miejsca częstotliwość, trzeba posortować wyniki do poziomu głównego kontrolera i wysłać te wyniki do widoku w odpowiedni sposób.

## <a name="order-results-based-on-multiple-values"></a>Wyniki na podstawie wielu wartości

Teraz pytanie jest sposób odróżnić hotele przy użyciu tej samej klasyfikacji. Kolejność na podstawie czasu, gdy został renovated hotelu byłoby jeden dobry sposób. Innymi słowy niedawno został renovated hotelu, im większy hotelu pojawia się w wynikach.

1. Aby dodać drugiego poziomu, w kolejności, należy zmienić **OrderBy** i **wybierz** właściwości w **indeksu (SearchData model)** metodę, aby uwzględnić  **Zapytaniu LastRenovationDate** właściwości.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Można wprowadzić dowolną liczbę właściwości w **OrderBy** listy. Jeśli hotele tej samej klasyfikacji i odnawianie daty, trzeci właściwości można wprowadzić do rozróżnienia między nimi.

2. Ponownie musimy zobaczyć datę odnawianie w widoku, aby mieć pewność, że kolejność jest poprawna. Aby uzyskać takie samo jak odnawianie prawdopodobnie tylko rok jest wymagany. Zmień pętlą renderowania w widoku z następującym kodem.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. Zmiana **dalej** metoda głównego kontrolera, do przekazywania składnik roku z datą ostatniej odnawianie.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Zmiana **przewijane** funkcji w widoku do wyświetlania tekstu odnawianie.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

5. Uruchom aplikację. Wyszukiwanie wspólny termin, takie jak "puli" lub "view" i sprawdź, hotele, za pomocą tej samej klasyfikacji są teraz wyświetlane w porządku malejącym odnawianie daty.

    ![Kolejność w dniu odnawianie](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtruj wyniki w oparciu odległości od punktu geograficznych

Ocena i Data odnawianie są przykłady właściwości, które najlepiej są wyświetlane w kolejności malejącej. Alfabetyczna lista będzie przykładem stosowania kolejności rosnącej (na przykład, jeśli wystąpił co najmniej jeden **OrderBy** właściwość która została ustawiona na **HotelName** , a następnie będzie można wyświetlić w porządku alfabetycznym ). Jednak nasze przykładowe dane odległości od punktu geograficzne będzie bardziej odpowiednie.

Aby wyświetlić wyniki w oparciu położenia geograficznego, kilka kroki są wymagane.

1. Odfiltruj wszystkich hotele, które znajdują się poza wybranego promienia z danego punktu, wprowadzając filtr z długość geograficzna, szerokość i parametry usługi radius. Długość geograficzna otrzymuje najpierw funkcję punktu. RADIUS jest w kilometrach.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Powyższy Filtr spowodował jest _nie_ zamówienie wyniki w oparciu o odległość, po prostu usuwa elementy odstające. Aby uporządkować wyniki, należy wprowadzić **OrderBy** ustawienia określa metodę geoDistance.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Mimo że wyniki zostały zwrócone przez usługę Azure Search przy użyciu filtru odległość, obliczeniowych odległość między danych a określonym punktem jest _nie_ zwracane. Oblicz ponownie tę wartość w widoku lub kontroler, jeśli chcesz go wyświetlić w wynikach.

    Poniższy kod oblicza odległość między dwoma punktami lat/długa.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. Masz teraz powiązać te pojęcia razem. Jednak te fragmenty kodu są w zakresie, w jakim Nasz samouczek dotyczący przechodzi tworzenie aplikacją opartą na mapie pozostanie w charakterze ćwiczenia dla czytnika. Aby móc dalej w tym przykładzie, należy wziąć pod uwagę zarówno nazwę miasta z protokołem radius, wprowadzając lub lokalizowanie punkt na mapie, a następnie wybierając promień. Aby zbadać dalej te opcje, zobacz następujące zasoby:

* [Dokumentacja usługi Azure Maps](https://docs.microsoft.com/azure/azure-maps/)
* [Znajdowanie adresu przy użyciu usługi Azure Maps usługi wyszukiwania](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Wyniki oparte na profilu oceniania

Przykłady podane w tym samouczku do tej pory pokazują, jak kolejność według wartości liczbowych (Klasyfikacja, Data odnawianie, położenia geograficznego), zapewniając _dokładnie_ przetwarzanie kolejności. Jednak niektóre wyszukiwania i pewnymi danymi nie nadają się do takich łatwo porównać między elementami danych dwa. Usługa Azure Search obejmuje koncepcję _oceniania_. _Profile oceniania_ może zostać określony dla zestawu danych, który może służyć do zapewnienia bardziej złożone i jakościowe porównań, które powinny być najbardziej wartościowych when, powiedz, porównywanie danych tekstowych do określania, które powinny być wyświetlane pierwszy.

Profile oceniania nie są zdefiniowane przez użytkowników, ale zazwyczaj przez administratorów zestawu danych. Profile oceniania kilka skonfigurowano danych hotels. Możemy przyjrzeć się sposób definiowania profilu oceniania, a następnie spróbuj pisania kodu, aby wyszukać na nich.

### <a name="how-scoring-profiles-are-defined"></a>Sposób oceniania profile są zdefiniowane.

Możemy przyjrzeć się trzy przykłady profile oceniania i wziąć pod uwagę, jak każdy _powinien_ wpływa na kolejność wyników. Jako Deweloper aplikacji nie zapisuj, te profile są zapisywane przez administratora danych, jednak warto Przyjrzyj się składni.

1. Jest to domyślny profil oceniania dla zestawu danych hotele używany, gdy nie zostanie podana żadna **OrderBy** lub **ScoringProfile** parametru. Ten profil zwiększa _wynik_ dla hotelu, jeśli tekst wyszukiwania znajduje się w hotelu nazwę, opis lub listę tagów (pozwalającego). Zwróć uwagę, jak wagi oceny Preferuj określonych pól. Jeśli tekst wyszukiwania pojawi się w innym polu nie wymieniono poniżej, ma wagę 1. Oczywiście im większa wynik, wcześniej wynik zostanie wyświetlony w widoku.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. Poniższy profil oceniania znacząco zwiększa wynik, jeśli podany parametr zawiera co najmniej jedną z listy tagów, (które wywołania "pozwalającego"). Kluczowym punktem ten profil jest fakt, że parametr _musi_ @username, zawierający tekst. Jeśli parametr jest pusty lub nie jest podany, zostanie zgłoszony błąd.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. W tym przykładzie trzeci tę klasyfikację zapewnia znaczne zwiększenie wydajności na wynik. Ostatni dzień renovated będzie również zwiększyć wynik, ale tylko jeśli danych mieści się w 730 dni (2 lata) od bieżącej daty.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    Daj nam Zobaczmy, jeśli te profile działać zgodnie z naszym zdaniem powinny!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Dodaj kod do widoku, aby porównać profilów

1. Otwórz plik index.cshtml i Zastąp &lt;treści&gt; sekcję poniższym kodem.

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
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

2. Otwórz plik SearchData.cs i Zastąp **SearchData** klasy z następującym kodem.

    ```cs
    public class SearchData
    {
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

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

3. Otwórz plik hotels.css, i dodaj następujące klasy HTML.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Dodaj kod do kontrolera, aby określić profil oceniania

1. Otwórz plik głównego kontrolera. Dodaj następujący kod **przy użyciu** — instrukcja (w celu pomocy w tworzeniu listy).

    ```cs
    using System.Linq;
    ```

2.  W tym przykładzie potrzebujemy początkowe wywołanie **indeksu** nieco więcej niż tylko zwrócić widok początkowy. Metoda szuka teraz pozwalającego maksymalnie 20 wyświetlić w widoku.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

3. Potrzebujemy dwie metody prywatnej można zapisać aspektami do magazynu tymczasowego, a także sprawności magazynu tymczasowego i wypełnić modelu.

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

4. Musimy **OrderBy** i **ScoringProfile** parametrów, zgodnie z potrzebami. Zastąp istniejące **indeksu (SearchData model)** metoda następującym kodem.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
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
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Zapoznaj się z artykułem komentarzy dla każdego z **Przełącz** wybrane opcje.

5. Nie musimy wprowadzić zmiany w **dalej** akcji, jeśli wykonano dodatkowego kodu w poprzedniej sekcji, w kolejności, w oparciu o wiele właściwości atrybutu.

### <a name="run-and-test-the-app"></a>Uruchamianie i testowanie aplikacji

1. Uruchom aplikację. Powinieneś zobaczyć pełny zestaw pozwalającego w widoku.

2. Do ustalania kolejności, wybierając "według wartości liczbowych ocenę" zapewni kolejność wartości liczbowych, który już został zaimplementowany w tym samouczku z datą odnawianie wybierania hotele klasyfikacji równa.

![Kolejność "beach" na podstawie klasyfikacji](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Teraz spróbuj profilu "przez pozwalającego". Zaznaczania pozwalającego i sprawdź, czy hotele za pomocą tych obiektów są promowane listy wyników.

![Kolejność "beach" oparta na profilu](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Spróbuj "Przez Renovated Data/oceniania profilu" Aby zobaczyć, jeśli otrzymujesz oczekiwanych. Pobieraj tylko ostatnio renovated hotele _świeżości_ boost.

### <a name="resources"></a>Zasoby

Aby uzyskać więcej informacji, zobacz [dodać profile oceniania do indeksu usługi Azure Search](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Wnioski

Należy wziąć pod uwagę następujące wnioski z tego projektu:

* Użytkownicy będą oczekiwać, że wyniki wyszukiwania do zamówienia, najważniejsze najpierw.
* Danych wymaga, aby kolejność jest proste. Nie byliśmy w stanie sortowania na "najtańsze" najpierw łatwo, ponieważ dane nie mają strukturę umożliwiające określanie kolejności wykonać bez dodatkowego kodu.
* Może istnieć wiele poziomów zamawianie do rozróżnienia między wyniki, które mają taką samą wartość na wyższym poziomie kolejności.
* Naturalna dla niektórych wyników być uporządkowane w kolejności rosnącej kolejności (np. odległości od punktu), a niektóre w kolejności malejącej (powiedzmy, ocena użytkownika gościa).
* Profile oceniania może być zdefiniowana, jeśli porównanie numeryczne są niedostępne lub nie inteligentnych wystarczająco, dla zestawu danych. Ocenianie każdego wyniku pomocy kolejności i wyświetlić wyniki w sposób inteligentny.

## <a name="next-steps"></a>Kolejne kroki

Zostały wykonane w tej serii C# samouczków — użytkownik powinien weszły cenne wiedzę na temat interfejsów API usługi Azure Search.

Dalsze dokumentacja i samouczki należy wziąć pod uwagę przeglądania [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), lub inne samouczki w [dokumentacji wyszukiwania platformy Azure](https://docs.microsoft.com/azure/search/).
