---
title: C# samouczek na temat zamawiania wyników
titleSuffix: Azure Cognitive Search
description: W tym samouczku pokazano, jak zamówić wyniki wyszukiwania. Opiera się na poprzednim projekcie hoteli, zamawiania według właściwości podstawowej, właściwości pomocniczej i zawiera profil punktacji, aby dodać kryteria zwiększania.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 812085a5a4b3e8d1233f19c947d2fd5e433f6ab7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121549"
---
# <a name="c-tutorial-order-the-results---azure-cognitive-search"></a>Samouczek C#: Zamów wyniki — Azure Cognitive Search

Do tego momentu w naszej serii samouczków wyniki są zwracane i wyświetlane w domyślnej kolejności. Może to być kolejność, w której znajdują się dane lub ewentualnie zdefiniowano domyślny _profil oceniania,_ który będzie używany, gdy nie określono parametrów zamawiania. W tym samouczku przejdziemy do sposobu zamawiania wyników na podstawie właściwości podstawowej, a następnie dla wyników, które mają tę samą właściwość podstawową, jak zamówić ten wybór na właściwości pomocniczej. Jako alternatywę dla zamawiania na podstawie wartości liczbowych, w końcowym przykładzie pokazano, jak porządkować na podstawie niestandardowego profilu oceniania. Będziemy również nieco głębiej do wyświetlania _złożonych typów_.

Aby łatwo porównać zwrócone wyniki, ten projekt tworzy na nieskończone przewijanie projektu utworzonego w [samouczku C#: Wyniki wyszukiwania na podziale na strony — Azure Cognitive Search](tutorial-csharp-paging.md) samouczek.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Wyniki zamówień na podstawie jednej właściwości
> * Wyniki zamówień na podstawie wielu właściwości
> * Filtrowanie wyników na podstawie odległości od punktu geograficznego
> * Kolejność wyników na podstawie profilu oceniania

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

Mieć nieskończoną wersję przewijania [samouczka języka C#: Wyświetlanie na podziale na strony wyników wyszukiwania —](tutorial-csharp-paging.md) projekt usługi Azure Cognitive Search jest uruchomiony. Ten projekt może być własną wersją lub zainstalować ją z GitHub: [Utwórz pierwszą aplikację.](https://github.com/Azure-Samples/azure-search-dotnet-samples)

## <a name="order-results-based-on-one-property"></a>Wyniki zamówień na podstawie jednej właściwości

Kiedy zamawiamy wyniki na podstawie jednej nieruchomości, powiedzmy, ocena hotelu, chcemy nie tylko uporządkowane wyniki, chcemy również potwierdzenia, że zamówienie jest poprawne. Innymi słowy, jeśli zamówimy na ratingu, powinniśmy wyświetlić ocenę w widoku.

W tym samouczku dodamy również nieco więcej do wyświetlania wyników, najtańszej ceny pokoju i najdroższej ceny pokoju dla każdego hotelu. Jak zagłębiamy się w zamawianiu, będziemy również dodawać wartości, aby upewnić się, że to, co zamawiamy na jest również wyświetlany w widoku.

Nie ma potrzeby modyfikowania żadnego z modeli, aby umożliwić zamawianie. Widok i kontroler wymagają aktualizacji. Zacznij od otwarcia kontrolera domowego.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Dodawanie właściwości OrderBy do parametrów wyszukiwania

1. Wszystko, czego potrzeba, aby zamówić wyniki na podstawie pojedynczej właściwości numerycznej, jest ustawienie **OrderBy** parametr na nazwę właściwości. W **index(Model SearchData)** metoda, dodać następujący wiersz do parametrów wyszukiwania.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > Kolejność domyślna jest rosnąco, choć można dodać **asc** do właściwości, aby to wyjaśnić. Kolejność malejąca jest określona przez dodanie **desc**.

2. Teraz uruchom aplikację i wprowadź dowolny wspólny wyszukiwany termin. Wyniki mogą lub nie mogą być w odpowiedniej kolejności, ponieważ ani ty, ani ty, ani ty, ani ty, ani użytkownik, nie ma łatwego sposobu weryfikacji wyników!

3. Wyjaśnijmy, że wyniki są uporządkowane na podstawie oceny. Najpierw zastąp klasy **box1** i **box2** w pliku hotels.css następującymi klasami (te klasy są wszystkie nowe, których potrzebujemy w tym samouczku).

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
    >Przeglądarki zazwyczaj buforują pliki css, co może prowadzić do użycia starego pliku css, a zmiany są ignorowane. Dobrym sposobem jest dodanie ciągu zapytania z parametrem wersji do łącza. Przykład:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Zaktualizuj numer wersji, jeśli uważasz, że stary plik css jest używany przez przeglądarkę.

4. Dodaj **właściwość Ocena** do parametru **Select** w metodzie **Index(SearchData model).**

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Otwórz widok (index.cshtml) i zastąp pętlę renderowania**&lt;(!--&gt;Pokaż dane hotelu. --**) następującym kodem.

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

6. Ocena musi być dostępna zarówno na pierwszej wyświetlanej stronie, jak i na kolejnych stronach, które są wywoływane za pomocą nieskończonego przewijania. W przypadku tych dwóch sytuacji musimy zaktualizować zarówno **akcję Next** w kontrolerze, jak i **przewijaną** funkcję w widoku. Począwszy od kontrolera, zmień **Next** metody na następujący kod. Ten kod tworzy i komunikuje tekst klasyfikacji.

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

7. Teraz **zaktualizuj przewijaną** funkcję w widoku, aby wyświetlić tekst klasyfikacji.

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

8. Teraz ponownie uruchom aplikację. Wyszukaj dowolne typowe terminy, takie jak "wifi", i sprawdź, czy wyniki są uporządkowane według malejącej kolejności oceny hotelu.

    ![Zamawianie na podstawie oceny](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Zauważysz, że kilka hoteli ma identyczną ocenę, a więc ich pojawienie się na wyświetlaczu jest ponownie kolejnością, w jakiej dane są znalezione, co jest arbitralne.

    Zanim przyjrzymy się dodaniu drugiego poziomu zamawiania, dodajmy kod, aby wyświetlić zakres stawek za pokój. Dodajemy ten kod zarówno do pokazywania wyodrębniania danych ze _złożonego typu,_ a także, abyśmy mogli dyskutować o zamawianiu wyników na podstawie ceny (najpierw najtańsze).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Dodaj zakres stawek za pokój do widoku

1. Dodaj właściwości zawierające najtańszą i najdroższą cenę pokoju do Hotel.cs modelu.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Oblicz stawki pomieszczeń na końcu akcji **Index(SearchData model)** w kontrolerze domowym. Dodaj obliczenia po przechowywaniu danych tymczasowych.

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

3. Dodaj **Rooms** właściwość **select** parametr w **index(SearchData model model) metody kontrolera.**

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Zmień pętlę renderowania w widoku, aby wyświetlić zakres szybkości dla pierwszej strony wyników.

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

5. Zmień **Next** metody w kontrolerze macierzystym do komunikowania zakresu szybkości, dla kolejnych stron wyników.

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

6. Zaktualizuj **przewijaną** funkcję w widoku, aby obsłużyć tekst stawek pokoju.

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

7. Uruchom aplikację i sprawdź, czy są wyświetlane zakresy stawek za pomieszczenia.

    ![Wyświetlanie zakresów stawek w pomieszczeniu](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

**Obiekt OrderBy** parametrów wyszukiwania nie akceptuje wpisu, takiego jak **Rooms.BaseRate,** aby zapewnić najtańszą cenę pokoju, nawet jeśli pokoje zostały już posortowane według stawki. W takim przypadku pokoje nie są sortowane według stawki. Aby wyświetlić hotele w przykładowym zestawie danych, uporządkowanym według ceny pokoju, należy posortować wyniki w kontrolerze domowym i wysłać te wyniki do widoku w żądanej kolejności.

## <a name="order-results-based-on-multiple-values"></a>Wyniki zamówień na podstawie wielu wartości

Teraz pytanie brzmi, jak odróżnić hotele o tej samej ocenie. Dobrym sposobem byłoby zamówienie na podstawie ostatniego remontu hotelu. Innymi słowy, im niedawno hotel został odnowiony, tym wyższy hotel pojawia się w wynikach.

1. Aby dodać drugi poziom zamawiania, zmień **właściwości OrderBy** i **Select** w metodzie **Index(SearchData model),** aby uwzględnić właściwość **LastRenovationDate.**

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Dowolną liczbę właściwości można wprowadzić na liście **OrderBy.** Gdyby hotele miały taką samą ocenę i datę renowacji, można by wprowadzić trzecią nieruchomość, aby je rozróżnić.

2. Ponownie, musimy zobaczyć datę renowacji w widoku, aby mieć pewność, że kolejność jest poprawna. Dla czegoś takiego jak remont, prawdopodobnie potrzebny jest tylko rok. Zmień pętlę renderowania w widoku na następujący kod.

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

3. Zmień **Następną** metodę w kontrolerze domowym, aby przesłać składnik roku ostatniej daty renowacji.

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

4. Zmień **przewijaną** funkcję w widoku, aby wyświetlić tekst renowacji.

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

5. Uruchom aplikację. Wyszukaj wspólny termin, taki jak "basen" lub "widok", i sprawdź, czy hotele o tej samej ocenie są teraz wyświetlane w kolejności malejącej daty renowacji.

    ![Zamawianie w dniu renowacji](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtrowanie wyników na podstawie odległości od punktu geograficznego

Ocena i data renowacji to przykłady właściwości, które najlepiej są wyświetlane w porządku malejącym. Alfabetyczna aukcja byłaby przykładem dobrego użycia porządku rosnącego (na przykład, jeśli istniała tylko jedna właściwość **OrderBy** i została ustawiona na **HotelName,** zostanie wyświetlona kolejność alfabetyczna). Jednak w przypadku naszych przykładowych danych bardziej odpowiednia byłaby odległość od punktu geograficznego.

Aby wyświetlić wyniki na podstawie odległości geograficznej, wymagane jest kilka kroków.

1. Odfiltruj wszystkie hotele, które znajdują się poza określonym promieniem od danego punktu, wprowadzając filtr z parametrami długości geograficznej, szerokości i promienia. Długość geograficzna jest podawana najpierw funkcji POINT. Promień jest w kilometrach.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Powyższy filtr _nie_ porządkuje wyników na podstawie odległości, po prostu usuwa odstające. Aby zamówić wyniki, wprowadź ustawienie **OrderBy** określające metodę geoDistance.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Mimo że wyniki zostały zwrócone przez usługę Azure Cognitive Search przy użyciu filtru odległości, obliczona odległość między danymi a określonym punktem _nie_ jest zwracana. Przelicz tę wartość ponownie w widoku lub kontrolerze, jeśli chcesz ją wyświetlić w wynikach.

    Poniższy kod obliczy odległość między dwoma punktami łot/lon.

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

4. Teraz musicie związać te koncepcje. Jednak te fragmenty kodu są tak daleko, jak nasz samouczek idzie, tworzenie aplikacji opartej na mapie pozostaje jako ćwiczenie dla czytelnika. Aby wziąć ten przykład dalej, należy rozważyć wprowadzenie nazwy miasta o promieniu lub zlokalizowanie punktu na mapie i wybranie promienia. Aby dokładniej zbadać te opcje, zobacz następujące zasoby:

* [Dokumentacja usługi Azure Maps](https://docs.microsoft.com/azure/azure-maps/)
* [Znajdowanie adresu za pomocą usługi wyszukiwania usługi Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Kolejność wyników na podstawie profilu oceniania

Przykłady podane w samouczku do tej pory pokazują, jak zamówić na wartości liczbowe (ocena, data renowacji, odległość geograficzna), zapewniając _dokładny_ proces zamawiania. Jednak niektóre wyszukiwania i niektóre dane nie nadają się do takiego łatwego porównania między dwoma elementami danych. Usługa Azure Cognitive Search zawiera koncepcję _oceniania._ _Profile oceniania_ można określić dla zestawu danych, które mogą służyć do dostarczania bardziej złożonych i jakościowych porównań, które powinny być najbardziej wartościowe, gdy, powiedzmy, porównanie danych tekstowych, aby zdecydować, które powinny być wyświetlane w pierwszej kolejności.

Profile oceniania nie są definiowane przez użytkowników, ale zazwyczaj przez administratorów zestawu danych. Na podstawie danych hoteli utworzyliśmy kilka profili punktacji. Przyjrzyjmy się, jak profil oceniania jest zdefiniowany, a następnie spróbuj napisać kod, aby go wyszukać.

### <a name="how-scoring-profiles-are-defined"></a>Jak definiowane są profile oceniania

Przyjrzyjmy się trzem przykładom profilów oceniania i zastanówmy się, jak każdy z nich _powinien_ wpływać na kolejność wyników. Jako deweloper aplikacji nie piszesz tych profili, są one zapisywane przez administratora danych, jednak warto przyjrzeć się składni.

1. Jest to domyślny profil oceniania dla zestawu danych hoteli, używany, gdy nie określono żadnego **parametru OrderBy** lub **ScoringProfile.** Ten profil zwiększa _wynik_ hotelu, jeśli wyszukiwany tekst znajduje się w nazwie hotelu, opisie lub liście tagów (udogodnień). Zwróć uwagę, jak wagi punktacji faworyzują niektóre pola. Jeśli wyszukiwany tekst pojawi się w innym polu, niewymienione poniżej, będzie miał wagę 1. Oczywiście, im wyższy wynik, tym wcześniej wynik pojawia się w widoku.

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

2. Poniższy profil punktacji znacznie zwiększa wynik, jeśli dostarczony parametr zawiera jedną lub więcej z listy tagów (które nazywamy "udogodnieniami"). Kluczowym punktem tego profilu jest to, że _należy_ podać parametr zawierający tekst. Jeśli parametr jest pusty lub nie jest podany, zostanie zgłoszony błąd.
 
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

3. W tym trzecim przykładzie ocena daje znaczny wzrost wyniku. Ostatnia odnowiona data również zwiększy wynik, ale tylko wtedy, gdy dane te mieszczą się w ciągu 730 dni (2 lat) od bieżącej daty.

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

    Teraz zobaczmy, czy te profile działają tak, jak uważamy, że powinny!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Dodawanie kodu do widoku w celu porównania profili

1. Otwórz plik index.cshtml i &lt;zastąp sekcję treści&gt; następującym kodem.

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

2. Otwórz plik SearchData.cs i zastąp **klasę SearchData** następującym kodem.

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

3. Otwórz plik hotels.css i dodaj następujące klasy HTML.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Dodawanie kodu do kontrolera w celu określenia profilu oceniania

1. Otwórz plik kontrolera macierzystego. Dodaj następującą **instrukcję za pomocą** instrukcji (aby ułatwić tworzenie list).

    ```cs
    using System.Linq;
    ```

2.  W tym przykładzie potrzebujemy początkowego wywołania **index** zrobić nieco więcej niż tylko zwrócić widok początkowy. Metoda wyszukuje teraz do 20 udogodnień do wyświetlenia w widoku.

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

3. Potrzebujemy dwóch prywatnych metod, aby zapisać aspekty do magazynu tymczasowego i odzyskać je z magazynu tymczasowego i wypełnić model.

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

4. W razie potrzeby musimy ustawić parametry **OrderBy** i **ScoringProfile.** Zastąp istniejącą metodę **Index(SearchData model)** następującą.

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

    Zapoznaj się z komentarzami dla każdego z wyborów **przełączników.**

5. Nie musimy wprowadzać żadnych zmian w **Next** akcji, jeśli ukończono dodatkowy kod dla poprzedniej sekcji na zamówienie na podstawie wielu właściwości.

### <a name="run-and-test-the-app"></a>Uruchamianie i testowanie aplikacji

1. Uruchom aplikację. W widoku powinien pan zobaczyć pełen zestaw udogodnień.

2. Do zamawiania, wybierając "Według oceny numerycznej" daje numeryczne zamawiania już zaimplementowane w tym samouczku, z datą renowacji decyduje wśród hoteli o równej ocenie.

![Zamawianie "plaży" na podstawie oceny](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Teraz wypróbuj profil "Przez udogodnienia". Dokonaj różnych wyborów udogodnień i sprawdź, czy hotele z tymi udogodnieniami są promowane na liście wyników.

![Zamawianie "plaży" na podstawie profilu](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Wypróbuj "By Renovated date/Rating profile", aby sprawdzić, czy masz to, czego oczekujesz. Dopiero niedawno odnowione hotele powinny uzyskać zastrzyk _świeżości._

### <a name="resources"></a>Zasoby

Aby uzyskać więcej informacji, zobacz następujące [Dodawanie profili oceniania do indeksu usługi Azure Cognitive Search](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Wnioski

Rozważmy następujące dania na wynos z tego projektu:

* Użytkownicy będą oczekiwać, że wyniki wyszukiwania zostaną uporządkowane, najbardziej odpowiednie w pierwszej kolejności.
* Dane wymagają uporządkowanej struktury, dzięki czemu zamawianie jest łatwe. Nie byliśmy w stanie sortować na "najtańsze" pierwszy łatwo, jak dane nie jest skonstruowany, aby umożliwić zamawiania, które mają być wykonane bez dodatkowego kodu.
* Może istnieć wiele poziomów do zamawiania, aby odróżnić wyniki, które mają taką samą wartość na wyższym poziomie zamawiania.
* To naturalne, że niektóre wyniki są uporządkowane w porządku rosnącym (powiedzmy, odległość od punktu), a niektóre w porządku malejącym (powiedzmy, ocena gościa).
* Profile oceniania można zdefiniować, gdy porównania liczbowe nie są dostępne lub nie są wystarczająco inteligentne dla zestawu danych. Ocenianie każdego wyniku pomoże inteligentnie uporządkować i wyświetlić wyniki.

## <a name="next-steps"></a>Następne kroki

Ukończono tę serię samouczków języka C# — powinieneś zdobyć cenną wiedzę na temat interfejsów API usługi Azure Cognitive Search.

Aby uzyskać więcej informacji i samouczków, należy zapoznać się z przeglądaniem programu [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure)lub innych samouczków w dokumentacji usługi Azure [Cognitive Search](https://docs.microsoft.com/azure/search/).
