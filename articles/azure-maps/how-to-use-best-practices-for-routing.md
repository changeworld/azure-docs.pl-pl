---
title: Najważniejsze wskazówki dotyczące usługi Azure Maps Route Service | Mapy platformy Microsoft Azure
description: Dowiedz się, jak efektywnie kierować trasę, korzystając z usługi tras z usług Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 85ce29d088b8fbd110988db67776d89346215e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335418"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Najważniejsze wskazówki dotyczące usługi Azure Maps Route

Interfejsy API wskazówki dojazdu i macierzy tras w [usłudze Azure](https://docs.microsoft.com/rest/api/maps/route) Maps Route Service mogą służyć do obliczania szacowanych czasów przybycia (ETA) dla każdej żądanej trasy. Interfejsy API trasy uwzględniają takie czynniki, jak informacje o ruchu drogowym w czasie rzeczywistym i historyczne dane o ruchu drogowym, takie jak typowe prędkości drogowe w żądanym dniu tygodnia i porze dnia. Interfejsy API zwracają najkrótsze lub najszybsze trasy dostępne do wielu miejsc docelowych w czasie w kolejności lub w zoptymalizowanej kolejności, na podstawie czasu lub odległości. Użytkownicy mogą również poprosić o specjalistyczne trasy i szczegóły dla spacerowiczów, rowerzystów i pojazdów użytkowych, takich jak ciężarówki. W tym artykule udostępnimy najlepsze rozwiązania, aby wywołać usługę Azure Maps [Route Service,](https://docs.microsoft.com/rest/api/maps/route)a dowiesz się, jak to zrobić:

* Wybierz między interfejsami API wskazówki dojazdu a interfejsem API routingu macierzy
* Żądanie historycznych i przewidywanych czasów podróży na podstawie danych o ruchu drogowym w czasie rzeczywistym i historycznych
* Zażądaj szczegółów trasy, takich jak czas i odległość, dla całej trasy i każdego odcinka trasy
* Zamów trasę dla pojazdu użytkowego, takiego jak ciężarówka
* Prośba o informacje drogowe na trasie, takie jak korki i informacje o opłatach drogowych
* Poproś o trasę, która składa się z jednego lub więcej przystanków (punktów trasy)
* Optymalizacja trasy jednego lub więcej przystanków, aby uzyskać najlepszą kolejność, aby odwiedzić każdy przystanek (punkt trasy)
* Optymalizacja tras alternatywnych przy użyciu punktów pomocniczych. Na przykład zaoferuj alternatywne trasy, które przechodzą przez stację ładowania pojazdów elektrycznych.
* Korzystanie z [usługi marszruty](https://docs.microsoft.com/rest/api/maps/route) za pomocą zestawu SDK usługi Azure Maps Web

## <a name="prerequisites"></a>Wymagania wstępne

Aby nawiązywać połączenia z interfejsami API usługi Azure Maps, potrzebujesz konta usługi Azure Maps i klucza. Aby uzyskać więcej informacji, zobacz [Tworzenie konta](quick-demo-map-app.md#create-an-account-with-azure-maps) i Uzyskaj [klucz podstawowy](quick-demo-map-app.md#get-the-primary-key-for-your-account). Klucz podstawowy jest również znany jako podstawowy klucz subskrypcji lub klucz subskrypcji.

Aby uzyskać informacje dotyczące uwierzytelniania w usłudze Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w usłudze Azure Maps](./how-to-manage-authentication.md). Aby uzyskać więcej informacji na temat zasięgu usługi trasy, zobacz [Zasięg routingu](routing-coverage.md).

W tym artykule używa [aplikacji Postman](https://www.postman.com/downloads/) do tworzenia wywołań REST, ale można wybrać dowolne środowisko programistyczne interfejsu API.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Wybierz kierunki trasy i routing macierzowy

Interfejsy API wskazówek api dojazdu, w tym czas podróży i współrzędne ścieżki trasy. Interfejs API macierzy trasy umożliwia obliczanie czasu i odległości podróży dla zestawu tras zdefiniowanych przez lokalizacje początkowe i docelowe. Dla każdego źródła interfejs API macierzy oblicza koszt (czas podróży i odległość) routingu z tego źródła do każdego miejsca docelowego. Wszystkie te interfejsy API umożliwiają określenie parametrów, takich jak żądany czas odjazdu, czas przybycia i typ pojazdu, takie jak samochód lub ciężarówka. Wszystkie wykorzystują dane o ruchu drogowym w czasie rzeczywistym lub predykcyjnym, aby zwrócić najbardziej optymalne trasy.

Należy rozważyć wywołanie interfejsów API wskazówki dojazdu, jeśli scenariusz jest:

* Poproś o najkrótszą lub najszybszą trasę jazdy między dwoma lub więcej znanymi lokalizacjami, aby uzyskać dokładne godziny przyjazdu pojazdów dostawczych.
* Żądanie szczegółowego prowadzenia trasy, w tym geometrii trasy, w celu wizualizacji tras na mapie
* Biorąc pod uwagę listę lokalizacji klientów, oblicz możliwie najkrótszą trasę, aby odwiedzić każdą lokalizację klienta i powrócić do źródła. Ten scenariusz jest powszechnie znany jako problem sprzedawcy podróży. W jednym żądaniu można przekazać maksymalnie 150 punktów trasy (przystanków).
* Wysyłaj partie kwerend do interfejsu API partii wskazówki dojazdu trasy przy użyciu tylko jednego wywołania interfejsu API.

Należy rozważyć wywołanie interfejsu API routingu macierzy, jeśli scenariusz jest do:

* Oblicz czas podróży lub odległość między zestawem miejsc początkowych i miejsc docelowych. Na przykład masz 12 kierowców i musisz znaleźć najbliższego dostępnego kierowcę, aby odebrać dostawę jedzenia z restauracji.
* Sortuj potencjalne trasy według ich rzeczywistej odległości lub czasu podróży. Interfejs API macierzy zwraca tylko czasy podróży i odległości dla każdej kombinacji pochodzenia i miejsca docelowego.
* Dane klastra na podstawie czasu podróży lub odległości. Na przykład firma zatrudnia 50 pracowników, znajdź wszystkich pracowników, którzy mieszkają w ciągu 20 minut czasu jazdy od biura.

Oto porównanie, aby pokazać niektóre możliwości kierunków trasy i macierzy interfejsów API:

| Azure Maps API | Maksymalna liczba zapytań w żądaniu | Unikaj obszarów | Wyznaczanie tras dla samochodów ciężarowych i pojazdów elektrycznych | punkty trasy i optymalizacja podróży sprzedawcy | Punkty pomocnicze |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Uzyskaj wskazówki dojazdu | 1 | | X | X | |
| Publikowanie wskazówek dojazdu | 1 | X | X | X | X |
| Księgowanie partii wskazówki dojazdu | 700 | | X | X | |
| Macierz postu | 700 | | X | | |

Aby dowiedzieć się więcej o możliwościach wyznaczania tras pojazdów elektrycznych, zapoznaj się z naszym samouczkiem na temat [kierowania pojazdów elektrycznych za pomocą notesów platformy Azure z pythonem.](tutorial-ev-routing.md)

## <a name="request-historic-and-real-time-data"></a>Żądanie danych historycznych i w czasie rzeczywistym

Domyślnie usługa Trasa zakłada, że tryb podróżowania jest samochodem, a czas odjazdu jest teraz. Zwraca trasę na podstawie warunków ruchu w czasie rzeczywistym, chyba że żądanie obliczania trasy określa inaczej. Stałe ograniczenia ruchu zależne od czasu, takie jak "Lewe skręty nie są dozwolone między 16:00 a 18:00" są przechwytywane i będą uwzględniane przez silnik routingu. Zamknięcia dróg, takie jak roboty drogowe, będą brane pod uwagę, chyba że wyraźnie zażądasz trasy, która ignoruje bieżący ruch na żywo. Aby zignorować bieżący `traffic` `false` ruch, należy ustawić w żądaniu interfejsu API.

Wartość obliczania trasy **travelTimeInSekunds** obejmuje opóźnienie spowodowane ruchem. Jest generowany przez wykorzystanie bieżących i historycznych danych dotyczących czasu podróży, gdy czas odjazdu jest ustawiony na teraz. Jeśli czas odlotu jest ustawiony w przyszłości, interfejsy API zwracają przewidywane czasy podróży na podstawie danych historycznych.

Jeśli dołączysz **parametr computeTravelTimeFor=all** w żądaniu, element podsumowania w odpowiedzi będzie miał następujące dodatkowe pola, w tym historyczne warunki ruchu:

| Element | Opis|
| :--- | :--- |
| noTrafficTravelTimeInSekundy | Szacowany czas podróży obliczony tak, jakby na trasie nie było opóźnień z powodu warunków drogowych, na przykład z powodu zatorów |
| historicTrafficTravelTimeInSekundy | Szacowany czas podróży obliczony na podstawie danych o historycznym ruchu zależnym od czasu |
| liveTrafficIncidentsTravelTimeInSekundy | Szacowany czas podróży obliczony na podstawie danych prędkości w czasie rzeczywistym |

W następnych sekcjach pokazano, jak nawiązywać połączenia z interfejsami API trasy przy użyciu omawianych parametrów.

### <a name="sample-query"></a>Przykładowe zapytanie

W pierwszym przykładzie poniżej czas odjazdu jest ustawiony na przyszłość, w momencie pisania.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

Odpowiedź zawiera element podsumowania, jak ten poniżej. Ponieważ czas odjazdu jest ustawiony na przyszłość, **trafficDelayInSeconds** wartość wynosi zero. Wartość **travelTimeInSeconds** jest obliczana przy użyciu zależnych od czasu danych o historycznym ruchu drogowym. Tak więc w tym przypadku **travelTimeInSeconds** wartość jest równa **historicTrafficTravelTimeInSeconds** wartość.

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Przykładowe zapytanie

W drugim przykładzie poniżej mamy żądanie routingu w czasie rzeczywistym, gdzie jest teraz czas odlotu. Nie jest jawnie określony w adresie URL, ponieważ jest to wartość domyślna.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

Odpowiedź zawiera podsumowanie, jak pokazano poniżej. Z powodu przeciążeń **trafficDelaysInSeconds** wartość jest większa niż zero. Jest również większa niż **historycznaTrafficTravelTimeInSeconds**.

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>Zażądaj szczegółów trasy i odcinka

Domyślnie usługa Trasa zwróci tablicę współrzędnych. Odpowiedź będzie zawierać współrzędne, które składają `points`się na ścieżkę na liście o nazwie . Odpowiedź trasy obejmuje również odległość od początku trasy i szacowany czas, jaki upłynął. Wartości te mogą służyć do obliczania średniej prędkości dla całej trasy.

Na poniższej `points` ilustracji przedstawiono element.

<center>

![lista punktów](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

</center>

Rozwiń `point` element, aby wyświetlić listę współrzędnych ścieżki:

<center>

![lista punktów](media/how-to-use-best-practices-for-routing/points-list-img.png)

</center>

Interfejsy API wskazówki dojazdu obsługują różne formaty instrukcji, których można używać, określając parametr **InstructionsType.** Aby sformatować instrukcje łatwego przetwarzania komputera, użyj **instrukcjiType=coded**. Użyj **instructionsType=tagged,** aby wyświetlić instrukcje jako tekst dla użytkownika. Ponadto instrukcje mogą być formatowane jako tekst, w którym niektóre elementy instrukcji są oznaczone, a instrukcja jest przedstawiona ze specjalnym formatowaniem. Aby uzyskać więcej informacji, zobacz [listę obsługiwanych typów instrukcji](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype).

Gdy wymagane są instrukcje, odpowiedź zwraca `guidance`nowy element o nazwie . Element `guidance` zawiera dwie informacje: wskazówki "zakręt po kroku" i skrócone instrukcje.

<center>

![Typ instrukcji](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

</center>

Element `instructions` zawiera wskazówki zakręt po zakręcie `instructionGroups` dla podróży, a ma podsumowane instrukcje. Każde podsumowanie instrukcji obejmuje segment podróży, który może obejmować wiele dróg. Interfejsy API mogą zwracać szczegóły dotyczące odcinków trasy. takich jak zakres współrzędnych korka lub aktualna prędkość ruchu.

<center>

![Instrukcje z kolei po kolei](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

</center>

<center>

![Skrócone instrukcje](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

</center>

## <a name="request-a-route-for-a-commercial-vehicle"></a>Poproś o trasę dla pojazdu użytkowego

Interfejsy API routingu usługi Azure Maps obsługują routing pojazdów użytkowych, obejmujący routing samochodów ciężarowych. Interfejsy API uwzględnić określone limity. Takie jak wysokość i masa pojazdu oraz w przypadku przewozu ładunku niebezpiecznego. Na przykład, jeśli pojazd przewozi łatwopalne, silnik wyznaczający unikać niektórych tuneli, które znajdują się w pobliżu obszarów mieszkalnych.

### <a name="sample-query"></a>Przykładowe zapytanie

Poniższe przykładowe żądanie dotyczy trasy dla samochodu ciężarowego. Ciężarówka przewozi niebezpieczne odpady klasy 1.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Interfejs API trasy zwraca wskazówki, które uwzględnią wymiary samochodu ciężarowego i odpadów niebezpiecznych. Instrukcje dotyczące trasy można odczytać, `guidance` rozwijając element.

<center>

![Samochód ciężarowy z hazwaste klasy 1](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

</center>

### <a name="sample-query"></a>Przykładowe zapytanie

Zmiana klasy us Hazmat, z powyższej kwerendy, spowoduje inną trasę, aby pomieścić tę zmianę.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Poniższa odpowiedź dotyczy ciężarówki przewożącej materiały niebezpieczne klasy 9, które są mniej niebezpieczne niż materiał niebezpieczny klasy 1. Po rozwinięciu `guidance` elementu, aby odczytać wskazówki dojazdu, można zauważyć, że wskazówki nie są takie same. Istnieje więcej instrukcji trasy dla ciężarówki przewożącej materiały niebezpieczne klasy 1.

<center>

![Samochód ciężarowy z hazwaste klasy 9](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)

</center>

## <a name="request-traffic-information-along-a-route"></a>Żądanie informacji o ruchu drogowym wzdłuż trasy

Za pomocą interfejsów API kierunku trasy usługi Azure Maps deweloperzy `sectionType` mogą żądać szczegółów dla każdego typu sekcji, dołączając parametr w żądaniu. Na przykład można zażądać informacji o prędkości dla każdego segmentu korka. Zapoznaj się [z listą wartości dla sekcjiType klucz,](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) aby dowiedzieć się o różnych szczegółów, które można zażądać.

### <a name="sample-query"></a>Przykładowe zapytanie

Następująca kwerenda `sectionType` `traffic`ustawia na . Żąda sekcji, które zawierają informacje o ruchu drogowym z Seattle do San Diego.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

Odpowiedź zawiera sekcje, które są odpowiednie dla ruchu wzdłuż podanych współrzędnych.

<center>

![odcinki ruchu drogowego](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

</center>

Ta opcja może służyć do kolorowania sekcji podczas renderowania mapy, jak na poniższym obrazku: 

<center>

![odcinki ruchu drogowego](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

</center>

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Obliczanie i optymalizowanie trasy wieloprzełodowej

Usługa Azure Maps udostępnia obecnie dwie formy optymalizacji trasy:

* Optymalizacje na podstawie żądanego typu trasy, bez zmiany kolejności punktów trasy. [Obsługiwane typy tras](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype) można znaleźć tutaj

* Optymalizacja sprzedawcy podróży, która zmienia kolejność punktów trasy, aby uzyskać najlepsze zamówienie, aby odwiedzić każdy przystanek

W przypadku routingu wieloprzełodniczkowego można określić do 150 punktów trasy w jednym żądaniu trasy. Początkowe i końcowe lokalizacje współrzędnych mogą być takie same, jak w przypadku podróży w obie strony. Ale musisz podać co najmniej jeden dodatkowy punkt trasy, aby obliczyć trasę. Punkty trasy można dodać do zapytania między współrzędnymi początku i miejsca docelowego.

Jeśli chcesz zoptymalizować najlepszą kolejność, aby odwiedzić dane punkty trasy, musisz określić **computeBestOrder=true**. Ten scenariusz jest również znany jako problem optymalizacji sprzedawcy podróży.

### <a name="sample-query"></a>Przykładowe zapytanie

Następująca kwerenda żąda ścieżki dla sześciu `computeBestOrder` punktów trasy, z parametrem ustawionym na `false`. Jest to również wartość domyślna dla parametru. `computeBestOrder`

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

Odpowiedź opisuje długość ścieżki na 140 851 metrów i że podróż tą ścieżką zajmie 9991 sekund.

<center>

![Nieoptymalna reakcja](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

</center>

Poniższa ilustracja przedstawia ścieżkę wynikającą z tej kwerendy. Ta ścieżka jest jedną z możliwych tras. Nie jest to optymalna ścieżka oparta na czasie lub odległości.

<center>

![Obraz niestyptowany](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

</center>

Kolejność punktu trasy to: 0, 1, 2, 3, 4, 5 i 6.

### <a name="sample-query"></a>Przykładowe zapytanie

Następująca kwerenda żąda ścieżki dla tych samych sześciu punktów, jak w powyższym przykładzie. Tym razem `computeBestOrder` parametr ustawiony `true` na (optymalizacja sprzedawcy podróży).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

Odpowiedź opisuje długość ścieżki na 91 814 metrów i że podróż tą ścieżką zajmie 7797 sekund. Odległość podróży i czas podróży są tutaj niższe, ponieważ interfejs API zwrócił zoptymalizowaną trasę.

<center>

![Nieoptymalna reakcja](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

</center>

Poniższa ilustracja przedstawia ścieżkę wynikającą z tej kwerendy.

<center>

![Obraz niestyptowany](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

</center>

Optymalna trasa ma następującą kolejność punktową: 0, 5, 1, 2, 4, 3 i 6.

>[!TIP]
> Zoptymalizowane informacje o zamówieniu punktu trasy z usługi Routing zawiera zestaw indeksów. Nie obejmują one wskaźników pochodzenia i miejsca docelowego. Należy przyrost tych wartości o 1, aby uwzględnić początek. Następnie dodaj miejsce docelowe do końca, aby uzyskać pełną uporządkowaną listę punktów trasy.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Obliczanie i stronniczość tras alternatywnych przy użyciu punktów pomocniczych

Mogą wystąpić sytuacje, w których chcesz zrekonstruować trasę, aby obliczyć zero lub więcej alternatywnych tras dla trasy referencyjnej. Na przykład można wyświetlić alternatywne trasy klientów, które przechodzą przez sklep detaliczny. W takim przypadku należy stronniczość lokalizacji przy użyciu punktów pomocniczych. Oto kroki, aby bias lokalizacji:

1. Obliczanie trasy w stanie rzeczywistym i uzyskanie ścieżki z odpowiedzi trasy
2. Użyj ścieżki trasy, aby znaleźć żądane lokalizacje wzdłuż lub w pobliżu ścieżki trasy. Na przykład można użyć interfejsu API punktu zainteresowania usługi Azure Maps [Point of Interest](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) lub zbadać własne dane w bazie danych.  
3. Zamów lokalizacje na podstawie odległości od początku trasy
4. Dodaj te lokalizacje jako punkty pomocnicze w nowym żądaniu trasy do [interfejsu API Post Route Directions](https://docs.microsoft.com/rest/api/maps/route/postroutedirections). Aby dowiedzieć się więcej o punktach pomocniczych, zobacz [dokumentację interfejsu API Post Route Directions](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints). 

Podczas wywoływania [interfejsu API Post Route Directions](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)można ustawić minimalny czas odchylenia lub ograniczenia odległości wraz z punktami pomocniczymi. Użyj tych parametrów, jeśli chcesz zaoferować alternatywne trasy, ale chcesz również ograniczyć czas podróży. Gdy te ograniczenia są używane, alternatywne trasy będą podążać trasą odniesienia od punktu początkowego dla danego czasu lub odległości. Innymi słowy, inne trasy odbiegają od trasy odniesienia dla podanych ograniczeń.

Poniższy obraz eksponuje przykład renderowania tras alternatywnych z określonymi limitami odchylenia dla czasu i odległości.

<center>

![Alternatywne trasy](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

</center>

## <a name="use-the-routing-service-in-a-web-app"></a>Korzystanie z usługi Routing w aplikacji sieci web

Zestaw Azure Maps Web SDK udostępnia [moduł usługi](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest). Ten moduł jest biblioteką pomocniczą, która ułatwia korzystanie z interfejsów API REST usługi Azure Maps w aplikacjach sieci Web lub Node.js przy użyciu języka JavaScript lub TypeScript. Moduł Service może służyć do renderowania zwróconych tras na mapie. Moduł automatycznie określa, który interfejs API ma być używany z żądaniami GET i POST.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz:

> [!div class="nextstepaction"]
> [Usługa Marszruty usługi Azure Maps](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [Jak korzystać z modułu Serwis](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [Pokaż trasę na mapie](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Pakiet NPM usługi Azure Maps](https://www.npmjs.com/package/azure-maps-rest  )
