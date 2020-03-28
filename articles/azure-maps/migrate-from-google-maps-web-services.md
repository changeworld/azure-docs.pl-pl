---
title: 'Samouczek: Migracja usług internetowych z Map Google | Mapy platformy Microsoft Azure'
description: Jak przeprowadzić migrację usług internetowych z Map Google do Map Platformy Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d2f25f2b786686b8af9bad4ea8ce3c8aea9b589f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371457"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrowanie usługi internetowej z Map Google

Zarówno platforma Azure, jak i Mapy Google zapewniają dostęp do przestrzennych interfejsów API za pośrednictwem usług sieci web REST. Interfejsy interfejsu API tych platform wykonują podobne funkcje. Ale każdy z nich używa różnych konwencji nazewnictwa i obiektów odpowiedzi.

W tabeli przedstawiono interfejsy API usługi Azure Maps, które mają podobną funkcjonalność do wymienionych interfejsów API usługi Mapy Google.

| Interfejs API usługi Mapy Google | Interfejs API usługi Usługi Azure Maps                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Wskazówki dojazdu              | [Trasa](https://docs.microsoft.com/rest/api/maps/route)                                     |
| Matryca odległości         | [Macierz trasy](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| Geokodowanie               | [Wyszukaj](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Wyszukiwanie miejsc           | [Wyszukaj](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Umieść autouzupełnienie      | [Wyszukaj](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Przyciągaj do drogi            | Zobacz [Sekcja Obliczanie tras i wskazówek dojazdu.](#calculate-routes-and-directions)            |
| Ograniczenia prędkości            | Zobacz [Odwróć sekcję współrzędnych geokodu.](#reverse-geocode-a-coordinate)                  |
| Mapa statyczna              | [Renderowanie](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| Strefa czasowa               | [Strefa czasowa](https://docs.microsoft.com/rest/api/maps/timezone)                              |

Następujące interfejsy API usługi nie są obecnie dostępne w usłudze Azure Maps:

- Wysokość
- Geolokalizacja
- Szczegóły miejsc i zdjęcia — numery telefonów i adres URL witryny sieci Web są dostępne w interfejsie API wyszukiwania usługi Azure Maps.
- Adresy URL map
- Najbliższe drogi — jest to osiągalne przy użyciu sdk sieci Web, jak pokazano [tutaj](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
), ale obecnie nie jest dostępne jako usługa.
- Statyczny widok ulicy

Usługa Azure Maps ma kilka dodatkowych usług sieci web REST, które mogą być interesujące:

- [Operacje przestrzenne:](https://docs.microsoft.com/rest/api/maps/spatial)Odciążanie złożonych obliczeń przestrzennych i operacji, takich jak geofencing, do usługi.
- [Ruch:](https://docs.microsoft.com/rest/api/maps/traffic)dostęp do przepływu ruchu w czasie rzeczywistym i danych zdarzeń.

## <a name="geocoding-addresses"></a>Adresy geokodowania

Geokodowanie to proces przekształcania adresu w współrzędną. Na przykład "1 Microsoft way, Redmond, WA" konwertuje na długość geograficzną: -122.1298, szerokość geograficzna: 47.64005. Następnie współrzędne mogą być używane do różnych celów, takich jak pozycjonowanie znacznika wyśrodkowania na mapie.

Usługa Azure Maps udostępnia kilka metod kodowania geograficznego:

- [**Geokodowanie adresu swobodnego:**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)Określ pojedynczy ciąg adresu i natychmiast przetworzyj żądanie. "1 Microsoft way, Redmond, WA" jest przykładem pojedynczego ciągu adresu. Ten interfejs API jest zalecany, jeśli trzeba szybko geokodować poszczególne adresy.
- [**Geokodowanie adresu strukturalnego**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Określ części pojedynczego adresu, takie jak nazwa ulicy, miasto, kraj i kod pocztowy, i natychmiast przetwórz żądanie. Ten interfejs API jest zalecany, jeśli trzeba szybko geokodować poszczególne adresy, a dane są już analizowane w poszczególnych częściach adresów.
- [**Geokodowanie adresów wsadowych:**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)Utwórz żądanie zawierające do 10 000 adresów i poproś o ich przetwarzanie w określonym czasie. Wszystkie adresy będą geokodowane równolegle na serwerze i po zakończeniu pełny zestaw wyników można pobrać. Jest to zalecane w przypadku geokodowania dużych zestawów danych.
- [**Wyszukiwanie rozmyte:**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)Ten interfejs API łączy geokodowanie adresów z wyszukiwaniem punktów zainteresowania. Ten interfejs API przyjmuje w ciągu swobodnej formy. Ten ciąg może być adresem, miejscem, punktem orientacyjnym, punktem zainteresowania lub kategorią punktu zainteresowania. Ten interfejs API przetwarza żądanie w czasie zbliżonym do rzeczywistego. Ten interfejs API jest zalecany dla aplikacji, w których użytkownicy wyszukują adresy lub punkty szczególne w tym samym polu tekstowym.
- [**Wyszukiwanie wsadowe rozmyte**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Utwórz żądanie zawierające do 10 000 adresów, miejsc, punktów orientacyjnych lub punktów zainteresowania i poproś o ich przetwarzanie w określonym czasie. Wszystkie dane będą przetwarzane równolegle na serwerze i po zakończeniu pełny zestaw wyników można pobrać.

W poniższej tabeli odsyła parametry interfejsu API Map Google z porównywalnymi parametrami interfejsu API w usłudze Azure Maps.

| Parametr interfejsu API Map Google | Porównywalny parametr interfejsu API usługi Azure Maps  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` i `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`- miasto/ miasto<br/>`municipalitySubdivision`– sąsiedztwo, sub / super miasto<br/>`countrySubdivision`- województwo<br/>`countrySecondarySubdivision`- powiat<br/>`countryTertiarySubdivision`- dzielnica<br/>`countryCode`- dwuliterowy kod kraju |
| `key`                       | `subscription-key`– Zobacz także [uwierzytelniania z usługi Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                  | `language`– Zobacz [dokumentację obsługiwanych języków.](supported-languages.md)  |
| `region`                    | `countrySet`                       |

Przykład sposobu korzystania z usługi wyszukiwania jest udokumentowany [tutaj](how-to-search-for-address.md). Pamiętaj, aby zapoznać się [z najlepszymi praktykami wyszukiwania](how-to-use-best-practices-for-search.md).

> [!TIP]
> Geokodowanie adresów swobodnych i interfejsy API wyszukiwania rozmytego mogą `&typeahead=true` być używane w trybie autouzupełniania przez dodanie do adresu URL żądania. Spowoduje to, że serwer, że tekst wejściowy jest prawdopodobnie częściowe, a wyszukiwanie przejdzie w tryb predykcyjny.

## <a name="reverse-geocode-a-coordinate"></a>Odwróć geokodowanie współrzędnej

Geokodowanie wsteczne to proces przekształcania współrzędnych geograficznych w przybliżony adres. Współrzędne z "długość geograficzna: -122.1298, szerokość geograficzna: 47.64005" konwertowane na "1 Microsoft way, Redmond, WA".

Usługa Azure Maps udostępnia kilka metod odwrotnego geokodowania:

- [**Geokoder odwrotny adres:**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)Określ pojedynczą współrzędną geograficzną, aby uzyskać przybliżony adres odpowiadający tej współrzędnej. Przetwarza żądanie w czasie zbliżonym do rzeczywistego.
- [**Geokoder odwrotny do ulicy :**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)Określ jedną współrzędną geograficzną, aby uzyskać informacje o pobliskiej ulicy krzyżowej i natychmiast przetworzyć żądanie. Na przykład, możesz otrzymać następujące ulice krzyżowe 1st Ave i Main St.
- [**Geokoder odwrotny adresu wsadowego:**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview)Utwórz żądanie zawierające maksymalnie 10 000 współrzędnych i złóż je na przetwarzanie w określonym czasie. Wszystkie dane będą przetwarzane równolegle na serwerze. Po zakończeniu żądania można pobrać pełny zestaw wyników.

Ta tabela odsyła parametry interfejsu API Map Google z porównywalnymi parametrami interfejsu API w usłudze Azure Maps.

| Parametr interfejsu API Map Google   | Porównywalny parametr interfejsu API usługi Azure Maps   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Zobacz także [uwierzytelniania z usługi Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                  | `language`– Zobacz [dokumentację obsługiwanych języków.](supported-languages.md)  |
| `latlng`                    | `query`  |
| `location_type`             | *Nie dotyczy*     |
| `result_type`               | `entityType`    |

Zapoznaj się z [najlepszymi praktykami wyszukiwania](how-to-use-best-practices-for-search.md).

Interfejs API odwrotnego geokodowania usługi Azure Maps zawiera pewne dodatkowe funkcje, które nie są dostępne w Mapach Google. Te funkcje mogą być przydatne do integracji z aplikacją podczas migracji aplikacji:

- Pobieranie danych o ograniczeniu prędkości
- Pobierz informacje o użytkowaniu dróg: droga lokalna, tętnica, ograniczony dostęp, rampa itd.
- Pobieranie strony ulicy, przy której znajduje się współrzędna

## <a name="search-for-points-of-interest"></a>Wyszukiwanie punktów orientacyjnych

Dane o punktach szczególnych można wyszukiwać w Mapach Google za pomocą interfejsu API wyszukiwania miejsc. Ten interfejs API udostępnia trzy różne sposoby wyszukiwania punktów szczególnych:

- **Znajdź miejsce z tekstu:** Wyszukuje punkt zainteresowania na podstawie jego nazwy, adresu lub numeru telefonu.
- **Wyszukiwanie w pobliżu:** Wyszukuje punkty zainteresowania, które znajdują się w pewnej odległości od lokalizacji.
- **Wyszukiwanie tekstu:** Wyszukuje miejsca przy użyciu tekstu w formie swobodnej, który zawiera informacje o punktach szczególnych i lokalizacji. Na przykład "pizza w Nowym Jorku" lub "restauracje w pobliżu głównej st".

Usługa Azure Maps udostępnia kilka interfejsów API wyszukiwania dla punktów szczególnych:

- [**Wyszukiwanie PUNKTÓW USU**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Wyszukiwanie punktów zainteresowań według nazwy. Na przykład "Starbucks".
- [**Wyszukiwanie kategorii PUNKTÓW**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Wyszukiwanie punktów zainteresowań według kategorii. Na przykład "restauracja".
- [**Wyszukiwanie w pobliżu:**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)Wyszukuje punkty zainteresowania znajdujące się w pewnej odległości od lokalizacji.
- [**Wyszukiwanie rozmyte:**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)Ten interfejs API łączy geokodowanie adresów z wyszukiwaniem punktów zainteresowania. Ten interfejs API przyjmuje ciąg swobodny, który może być adresem, miejscem, punktem orientacyjnym, punktem zainteresowania lub kategorią punktu zainteresowania. Przetwarza żądanie w czasie zbliżonym do rzeczywistego. Ten interfejs API jest zalecany dla aplikacji, w których użytkownicy wyszukują adresy lub punkty szczególne w tym samym polu tekstowym.
- [**Wyszukiwanie w obrębie geometrii**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Wyszukiwanie punktów zainteresowania, które znajdują się w określonej geometrii. Na przykład wyszukaj punkt zainteresowania w obrębie wielokąta.
- [**Wyszukiwanie wzdłuż trasy:**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute)Wyszukiwanie punktów zainteresowania, które znajdują się wzdłuż określonej ścieżki trasy.
- [**Wyszukiwanie wsadowe rozmyte:**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)Utwórz żądanie zawierające do 10 000 adresów, miejsc, punktów orientacyjnych lub punktów zainteresowania. Przetworzono żądanie przez pewien czas. Wszystkie dane będą przetwarzane równolegle na serwerze. Po zakończeniu przetwarzania żądania można pobrać pełny zestaw wyników.

Obecnie usługa Azure Maps nie ma porównywalnego interfejsu API z interfejsem API wyszukiwania tekstu w Mapach Google.

> [!TIP]
> Wyszukiwanie UZ, wyszukiwanie kategorii UZ i interfejsy API wyszukiwania rozmytego mogą być `&typeahead=true` używane w trybie autouzupełniania przez dodanie do adresu URL żądania. Spowoduje to, że serwer, że tekst wejściowy jest prawdopodobnie częściowe. Interfejs API przeprowadzi wyszukiwanie w trybie predykcyjnym.

Zapoznaj się z [najlepszymi rozwiązaniami w zakresie](how-to-use-best-practices-for-search.md) dokumentacji wyszukiwania.

### <a name="find-place-from-text"></a>Znajdowanie miejsca z tekstu

Użyj wyszukiwania [usu poi](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) usługi Azure Maps i [wyszukiwania rozmytego, aby wyszukać](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) punkty zainteresowania według nazwy lub adresu.

Tabela odsyła parametry interfejsu API Map Google z porównywalnych parametrów interfejsu API usługi Azure Maps.

| Parametr interfejsu API Map Google | Porównywalny parametr interfejsu API usługi Azure Maps |
|---------------------------|-------------------------------------|
| `fields`                  | *Nie dotyczy*                               |
| `input`                   | `query`                             |
| `inputtype`               | *Nie dotyczy*                               |
| `key`                     | `subscription-key`– Zobacz także [uwierzytelniania z usługi Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                | `language`– Zobacz [dokumentację obsługiwanych języków.](supported-languages.md)  |
| `locationbias`            | `lat`, `lon` oraz`radius`<br/>`topLeft` i `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Wyszukiwanie w pobliżu

Użyj interfejsu API [wyszukiwania w pobliżu,](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) aby pobrać pobliskie punkty zainteresowania w usłudze Azure Maps.

W tabeli przedstawiono parametry interfejsu API Map Google z porównywalnymi parametrami interfejsu API usługi Azure Maps.

| Parametr interfejsu API Map Google | Porównywalny parametr interfejsu API usługi Azure Maps  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key`– Zobacz także [uwierzytelniania z usługi Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `keyword`                   | `categorySet` i `brandSet`        |
| `language`                  | `language`– Zobacz [dokumentację obsługiwanych języków.](supported-languages.md)  |
| `location`                  | `lat` i `lon`                     |
| `maxprice`                  | *Nie dotyczy*                               |
| `minprice`                  | *Nie dotyczy*                               |
| `name`                      | `categorySet` i `brandSet`        |
| `opennow`                   | *Nie dotyczy*                               |
| `pagetoken`                 | `ofs` i `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *Nie dotyczy*                               |
| `type`                      | `categorySet –`Zobacz [dokumentację obsługiwanych kategorii wyszukiwania.](supported-search-categories.md)   |

## <a name="calculate-routes-and-directions"></a>Obliczanie tras i wskazówek dojazdu

Oblicz trasy i wskazówki dojazdu za pomocą usługi Azure Maps. Usługa Azure Maps ma wiele takich samych funkcji jak usługa routingu Map Google, na przykład:

- Godziny przyjazdu i wyjazdu.
- Trasy ruchu w czasie rzeczywistym i predykcyjne.
- Różne środki transportu. Takie jak jazda samochodem, chodzenie, jazda na rowerze.

> [!NOTE]
> Usługa Azure Maps wymaga, aby wszystkie punkty trasy były współrzędne. Adresy muszą być najpierw geokodowane.

Usługa routingu usługi Azure Maps udostępnia następujące interfejsy API do obliczania tras:

- [**Oblicz trasę:**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)Oblicz trasę i natychmiast przetworzyj żądanie. Ten interfejs API obsługuje zarówno żądania GET, jak i POST. Żądania POST są zalecane podczas określania dużej liczby punktów trasy lub podczas korzystania z wielu opcji trasy, aby upewnić się, że żądanie adresu URL nie staje się zbyt długie i powoduje problemy. Kierunek trasy POST w usłudze Azure Maps ma opcję, która zajmuje tysiące [punktów pomocniczych](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) i użyje ich do odtworzenia logicznej ścieżki trasy między nimi (przyciąganie do drogi). 
- [**Marszruta wsadowa:**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview)Utwórz żądanie zawierające maksymalnie 1000 żądań marszruty i poproś je o ich przetwarzanie w określonym czasie. Wszystkie dane będą przetwarzane równolegle na serwerze i po zakończeniu pełny zestaw wyników można pobrać.
- [**Usługi mobilności**](https://docs.microsoft.com/rest/api/maps/mobility): Oblicz trasy i wskazówki dojazdu za pomocą transportu publicznego.

Tabela odsyła parametry interfejsu API Map Google z porównywalnych parametrów interfejsu API w usłudze Azure Maps.

| Parametr interfejsu API Map Google    | Porównywalny parametr interfejsu API usługi Azure Maps  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query`– współrzędne w formacie`"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key`– Zobacz także [uwierzytelniania z usługi Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                     | `language`– Zobacz [dokumentację obsługiwanych języków.](supported-languages.md)   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *Nie dotyczy* — ta funkcja jest związana z geokodowaniem. Użyj *countrySet* parametr podczas korzystania z interfejsu API geokodowania usługi Azure Maps.  |
| `traffic_model`               | *Nie dotyczy* — można określić tylko, czy dane o ruchu powinny być używane z parametrem *ruchu.* |
| `transit_mode`                | Zobacz [dokumentację usług mobilności](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Zobacz [dokumentację usług mobilności](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *Nie dotyczy* — usługa Azure Maps używa tylko systemu metryki.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Domyślnie interfejs API marszruty usługi Azure Maps zwraca tylko podsumowanie. Zwraca odległość i czas oraz współrzędne ścieżki trasy. Użyj `instructionsType` parametru, aby pobrać instrukcje turn-by-turn. I użyj `routeRepresentation` parametru, aby odfiltrować podsumowanie i ścieżkę trasy.

Interfejs API routingu usługi Azure Maps ma dodatkowe funkcje, które nie są dostępne w Mapach Google. Podczas migracji aplikacji, należy rozważyć użycie tych funkcji, może okazać się przydatne.

- Obsługa typu trasy: najkrótsza, najszybsza, trylująca i najbardziej oszczędna.
- Wsparcie dla dodatkowych trybów podróży: autobus, motocykl, taksówka, ciężarówka i van.
- Obsługa 150 punktów trasy.
- Obliczanie wielu czasów podróży w jednym żądaniu; historyczny ruch, ruch na żywo, brak ruchu.
- Unikaj dodatkowych rodzajów dróg: dróg carpool, dróg nieutwardzonych, już używanych dróg.
- Określ obszary niestandardowe, których należy unikać.
- Ogranicz rzędną, którą trasa może wznieść.
- Trasa na podstawie specyfikacji silnika. Oblicz trasy dla pojazdów spalinowych lub elektrycznych na podstawie specyfikacji silnika oraz pozostałego paliwa lub ładunku.
- Obsługa parametrów trasy pojazdu użytkowego. Takie jak wymiary pojazdu, masa, liczba osi i typ ładunku.
- Określ maksymalną prędkość pojazdu.

Oprócz tego usługa tras w usłudze Azure Maps obsługuje [obliczanie zakresów routingu.](https://docs.microsoft.com/rest/api/maps/route/getrouterange) Obliczanie zakresów routingu jest również znany jako izochrones. Wiąże się to z generowaniem wielokąta obejmującego obszar, do którego można podróżować w dowolnym kierunku z punktu początkowego. Wszystko po określonym czasie lub ilości paliwa lub opłaty.

Zapoznaj się z najlepszymi rozwiązaniami w zakresie dokumentacji [routingu.](how-to-use-best-practices-for-routing.md)

## <a name="retrieve-a-map-image"></a>Pobieranie obrazu mapy

Usługa Azure Maps udostępnia interfejs API do renderowania statycznych obrazów map z nałożone dane. Interfejs API [renderowania obrazu mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) w usłudze Azure Maps jest porównywalny z interfejsem API mapy statycznej w Mapach Google.

> [!NOTE]
> Usługa Azure Maps wymaga centrum, wszystkie znaczniki i lokalizacje ścieżki, które mają być współrzędne w formacie "długość geograficzna,szerokość geograficzna". Podczas gdy Mapy Google używają formatu "szerokość, długość geograficzna". Adresy muszą być najpierw geokodowane.

Tabela odsyła parametry interfejsu API Map Google z porównywalnych parametrów interfejsu API w usłudze Azure Maps.

| Parametr interfejsu API Map Google | Porównywalny parametr interfejsu API usługi Azure Maps  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format`– określone jako część ścieżki adresu URL. Obecnie obsługiwane tylko PNG. |
| `key`                       | `subscription-key`– Zobacz także [uwierzytelniania z usługi Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                  | `language`– Zobacz [dokumentację obsługiwanych języków.](supported-languages.md)  |
| `maptype`                   | `layer`oraz `style` — zobacz [dokumentację obsługiwanych stylów mapy.](supported-map-styles.md) |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *Nie dotyczy* — jest to funkcja związana z geokodowaniem. Użyj `countrySet` tego parametru podczas korzystania z interfejsu API geokodowania usługi Azure Maps.  |
| `scale`                     | *Nie dotyczy*                              |
| `size`                      | `width`i `height` – może mieć rozmiar do 8192x8192. |
| `style`                     | *Nie dotyczy*                              |
| `visible`                   | *Nie dotyczy*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> W systemie kafelków usługi Azure Maps kafelki są dwukrotnie większe od kafelków map używanych w Mapach Google. W związku z tym wartość poziomu powiększenia w usłudze Azure Maps będzie wyświetlana o jeden poziom powiększenia bliżej w usłudze Azure Maps w porównaniu z Mapami Google. Aby zrekompensować tę różnicę, należy zmienić poziom powiększenia w migrowane żądania.

Aby uzyskać więcej informacji, zobacz [przewodnik instrukcje dotyczące interfejsu API renderowania obrazu mapy](how-to-render-custom-data.md).

Oprócz możliwości generowania statycznego obrazu mapy usługa renderowania usługi Usługi Azure Maps zapewnia możliwość bezpośredniego dostępu do kafelków mapy w formacie rastrowym (PNG) i wektorowym:

- [**Kafelek Mapy**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): Pobierz płytki rastrowe (PNG) i wektorowe dla map bazowych (drogi, granice, tło).
- [**Kafelek zdjęć map :**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)Pobierz płytki zdjęć lotniczych i satelitarnych.

> [!TIP]
> Wiele aplikacji Google Maps, w których kilka lat temu przesiadywały się z interaktywnych doświadczeń z mapami na statyczne obrazy map. Dokonano tego jako metoda oszczędzania kosztów. W usłudze Azure Maps jest zwykle bardziej opłacalne użycie interaktywnego formantu mapy w sdk sieci Web. Opłaty za kontrolę mapy interaktywnej na podstawie liczby obciążeń kafelków. Kafelki map w usłudze Azure Maps są duże. Często odtwarzanie tego samego widoku mapy co mapa statyczna zajmuje tylko kilka kafelków. Kafelki mapy są buforowane automatycznie przez przeglądarkę. W związku z tym formant mapy interaktywnej często generuje ułamek transakcji podczas odtwarzania statycznego widoku mapy. Przesuwanie i powiększanie spowoduje załadowanie większej liczby kafelków; Jednak istnieją opcje w formancie mapy, aby wyłączyć to zachowanie. Interaktywna kontrola mapy zapewnia również o wiele więcej opcji wizualizacji niż statyczne usługi map.

### <a name="marker-url-parameter-format-comparison"></a>Porównanie formatu parametru adresu URL znacznika

**Przed: Mapy Google**

Dodaj znaczniki `markers` przy użyciu parametru w adresie URL. Parametr `markers` przyjmuje styl i listę lokalizacji, które mają być renderowane na mapie w tym stylu, jak pokazano poniżej:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Aby dodać dodatkowe style, użyj `markers` parametrów do adresu URL z innym stylem i zestawem lokalizacji.

Określ lokalizacje znaczników w formacie "szerokość, długość geograficzna".

Dodaj style znaczników z formatem, `optionName:value` z\|wieloma stylami oddzielonymi przez potok\|( ) znaków, takich jak ten "optionName1:value1 optionName2:value2". Należy zauważyć, że nazwy opcji i wartości są oddzielone dwukropkiem (:). Użyj następującej opcji stylizowania znaczników w Mapach Google:

- `color`– Kolor domyślnej ikony znacznika. Może to być 24-bitowy`0xrrggbb`kolor sześciokątny ( ) lub jedna z następujących wartości; `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label`– Pojedyncza wielka litera alfanumeryczna do wyświetlenia na górze ikony.
- `size`- Rozmiar znacznika. Może `tiny`być `mid`, `small`lub .

Użyj następujących nazw opcji stylu ikon niestandardowych w Mapach Google:

- `anchor`– Określa sposób wyrównywania obrazu ikony do współrzędnej. Może to być wartość piksela (x,y) lub jedna z następujących wartości; `top`, `bottom` `left`, `right` `center`, `topleft` `topright`, `bottomleft`, `bottomright`, , , lub .
- `icon`– Adres URL wskazujący obraz ikony.

Na przykład dodajmy czerwony, średniej wielkości znacznik do mapy na długości geograficznej: -110, szerokość geograficzna: 45:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Znacznik Map Google](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Po: Mapy platformy Azure**

Dodaj znaczniki do statycznego obrazu mapy, określając `pins` parametr w adresie URL. Podobnie jak Mapy Google, określ styl i listę lokalizacji w parametrze. Parametr `pins` można określić wiele razy, aby obsługiwać znaczniki z różnymi stylami.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Aby użyć dodatkowych stylów, dodaj dodatkowe `pins` parametry do adresu URL o innym stylu i zestawie lokalizacji.

W usłudze Azure Maps lokalizacja pinezki musi być w formacie "szerokość geograficzna". Mapy Google używają formatu "szerokość geograficzna, długość geograficzna". Spacja, a nie przecinek, oddziela długość i szerokość geograficzną w formacie usługi Azure Maps.

Określa `iconType` typ pinezki do utworzenia. Może mieć następujące wartości:

- `default`– Domyślna ikona pinezki.
- `none`– Nie jest wyświetlana żadna ikona, renderowane będą tylko etykiety.
- `custom`– Określa, że ma być używana ikona niestandardowa. Adres URL wskazujący obraz ikony można dodać `pins` na końcu parametru po informacji o lokalizacji pinezki.
- `{udid}`– Unikatowy identyfikator danych (UDID) dla ikony przechowywanej na platformie Usługi Azure Maps Data Storage.

Dodaj style pinezki w `optionNameValue` formacie. Oddziel wiele stylów\|znakami potoku ( ). Na przykład: `iconType|optionName1Value1|optionName2Value2`. Nazwy i wartości opcji nie są rozdzielone. Użyj następujących nazw opcji stylu, aby stylizować znaczniki:

- `al`– Określa krycie (alfa) znacznika. Wybierz liczbę z 0 do 1.
- `an`– Określa zakotwiczenie styku. Określ wartości pikseli X i y w formacie "x y".
- `co`– Kolor szpilki. Określ 24-bitowy kolor `000000` `FFFFFF`szesnastkowy: do .
- `la`– Określa zakotwiczenie etykiety. Określ wartości pikseli X i y w formacie "x y".
- `lc`– Kolor etykiety. Określ 24-bitowy kolor `000000` `FFFFFF`szesnastkowy: do .
- `ls`– Rozmiar etykiety w pikselach. Wybierz liczbę większą niż 0.
- `ro`– Wartość w stopniach, aby obrócić ikonę. Wybierz liczbę z -360 do 360.
- `sc`– Wartość skali dla ikony pinezki. Wybierz liczbę większą niż 0.

Określ wartości etykiet dla każdej lokalizacji pinezki. Takie podejście jest bardziej wydajne niż stosowanie pojedynczej wartości etykiety do wszystkich znaczników na liście lokalizacji. Wartość etykiety może być ciągiem wielu znaków. Zawiń ciąg pojedynczymi cudzysłowami, aby upewnić się, że nie jest mylony jako wartość stylu lub lokalizacji.

Dodajmy czerwoną (`FF0000`) domyślną ikonę z etykietą "Space Needle", pozycjonowana poniżej (15 50). Ikona jest na długości geograficznej: -122.349300, szerokość geograficzna: 47.620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Znacznik Usługi Azure Maps](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Dodać trzy szpilki z etykietami "1", "2" i "3":

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps wiele znaczników](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Porównanie formatu parametru adresu URL ścieżki

**Przed: Mapy Google**

Dodaj linie i wielokąt do statycznego `path` obrazu mapy przy użyciu parametru w adresie URL. Parametr `path` przyjmuje styl i listę lokalizacji, które mają być renderowane na mapie, jak pokazano poniżej:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Użyj dodatkowych stylów, dodając dodatkowe `path` parametry do adresu URL z innym stylem i zestawem lokalizacji.

Lokalizacje ścieżek są `latitude1,longitude1|latitude2,longitude2|…` określone w formacie. Ścieżki mogą być kodowane lub zawierać adresy dla punktów.

Dodaj style ścieżek z formatem, `optionName:value` oddziel wiele stylów znakami potoku .\| I, oddzielne nazwy opcji i wartości z dwukropkiem (:). Jak to: `optionName1:value1|optionName2:value2`. Do stylowania ścieżek w Mapach Google można używać następujących nazw opcji stylu:

- `color`– Kolor ścieżki lub konturu wielokąta. Może to być 24-bitowy`0xrrggbb`kolor sześciokątny ( ),`0xrrggbbbaa`32-bitowy kolor sześciokątny ( ) lub jedna z następujących wartości: czarny, brązowy, zielony, fioletowy, żółty, niebieski, szary, pomarańczowy, czerwony, biały.
- `fillColor`– Kolor, aby wypełnić obszar ścieżki (wielokąt). Może to być 24-bitowy`0xrrggbb`kolor sześciokątny ( ),`0xrrggbbbaa`32-bitowy kolor sześciokątny ( ) lub jedna z następujących wartości: czarny, brązowy, zielony, fioletowy, żółty, niebieski, szary, pomarańczowy, czerwony, biały.
- `geodesic`– Wskazuje, czy ścieżka powinna być linią, która podąża za krzywizną ziemi.
- `weight`– Grubość linii ścieżki w pikselach.

Dodaj krycie czerwonej linii i grubość pikseli do mapy między współrzędnymi w parametrze URL. W poniższym przykładzie linia ma 50% krycia i grubość czterech pikseli. Współrzędne to długość geograficzna: -110, szerokość geograficzna: 45 i długość geograficzna: -100, szerokość geograficzna: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Polilinia Map Google](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Po: Mapy platformy Azure**

Dodaj linie i wielokąty do statycznego obrazu `path` mapy, określając parametr w adresie URL. Podobnie jak Mapy Google, określ styl i listę lokalizacji w tym parametrze. Określ `path` parametr wiele razy, aby renderować wiele okręgów, linii i wielokątów z różnymi stylami.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Jeśli chodzi o lokalizacje ścieżek, usługa Azure Maps wymaga współrzędne w formacie "szerokość geograficzna długości". Mapy Google używają formatu "szerokość geograficzna, długość geograficzna". Spacja, a nie przecinek, oddziela długość i szerokość geograficzną w formacie usługi Azure Maps. Usługa Azure Maps nie obsługuje zakodowanych ścieżek ani adresów dla punktów. Przekaż większe zestawy danych jako plik GeoJSON do interfejsu API magazynu danych usługi Azure Maps, zgodnie z tym, co udokumentowano [poniżej.](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)

Dodawanie stylów `optionNameValue` ścieżek w formacie. Oddziel wiele stylów według znaków potoku (\|), takich jak ten `optionName1Value1|optionName2Value2`. Nazwy i wartości opcji nie są rozdzielone. Użyj następujących nazw opcji stylu, aby stylować ścieżki w usłudze Azure Maps:

- `fa`- Krycie koloru wypełnienia (alfa) używane podczas renderowania wielokątów. Wybierz liczbę z 0 do 1.
- `fc`- Kolor wypełnienia używany do renderowania obszaru wielokąta.
- `la`– Krycie kolorów linii (alfa) używane podczas renderowania linii i kontur wielokątów. Wybierz liczbę z 0 do 1.
- `lc`– Kolor linii używany do renderowania linii i kontur wielokątów.
- `lw`– Szerokość linii w pikselach.
- `ra`– Określa promień okręgów w metrach.

Dodaj krycie czerwonej linii i grubość piksela między współrzędnymi w parametrze URL. W poniższym przykładzie linia ma 50% krycia i grubość czterech pikseli. Współrzędne mają następujące wartości: długość geograficzna: -110, szerokość geograficzna: 45 i długość geograficzna: -100, szerokość geograficzna: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Polilinia usługi Azure Maps](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Obliczanie macierzy odległości

Usługa Azure Maps udostępnia interfejs API macierzy odległości. Ten interfejs API służy do obliczania czasów podróży i odległości między zestawem lokalizacji z macierzą odległości. Jest to porównywalne z interfejsem API matrycy odległości w Mapach Google.

- [**Macierz trasy:**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)Asynchronicznie oblicza czasy i odległości podróży dla zestawu źródeł i miejsc docelowych. Obsługuje do 700 komórek na żądanie. To liczba początkowych pomnożona przez liczbę miejsc docelowych. Mając to na uwadze, przykłady możliwych wymiarów macierzy to: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Żądanie do interfejsu API macierzy odległości można zrobić tylko przy użyciu żądania POST z informacjami o źródle pochodzenia i miejscu docelowym w treści żądania. Ponadto usługa Azure Maps wymaga współrzędnych wszystkich źródeł i miejsc docelowych. Adresy muszą być najpierw geokodowane.

Ta tabela odsyła parametry interfejsu API Map Google z porównywalnymi parametrami interfejsu API usługi Azure Maps.

| Parametr interfejsu API Map Google      | Porównywalny parametr interfejsu API usługi Azure Maps  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination`– określić w treści żądania POST jako GeoJSON. |
| `key`                          | `subscription-key`– Zobacz także [uwierzytelniania z usługi Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                     | `language`– Zobacz [dokumentację obsługiwanych języków.](supported-languages.md)  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins`– określić w treści żądania POST jako GeoJSON.  |
| `region`                       | *Nie dotyczy* — ta funkcja jest związana z geokodowaniem. Użyj `countrySet` tego parametru podczas korzystania z interfejsu API geokodowania usługi Azure Maps. |
| `traffic_model`                | *Nie dotyczy* — można określić tylko, czy `traffic` dane o ruchu powinny być używane z parametrem. |
| `transit_mode`                 | *N/A* — macierze odległościowe oparte na tranzycie nie są obecnie obsługiwane.  |
| `transit_routing_preference`   | *N/A* — macierze odległościowe oparte na tranzycie nie są obecnie obsługiwane.  |
| `units`                        | *Nie dotyczy* — usługa Azure Maps używa tylko systemu metryki. |

> [!TIP]
> Wszystkie zaawansowane opcje routingu dostępne w interfejsie API routingu usługi Azure Maps są obsługiwane w interfejsie API macierzy odległości usługi Azure Maps. Zaawansowane opcje wyznaczania tras obejmują: routing samochodów ciężarowych, specyfikacje silnika i tak dalej.

Zapoznaj się z najlepszymi rozwiązaniami w zakresie dokumentacji [routingu.](how-to-use-best-practices-for-routing.md)

## <a name="get-a-time-zone"></a>Uzyskaj strefę czasową

Usługa Azure Maps udostępnia interfejs API do pobierania strefy czasowej współrzędnej. Interfejs API strefy czasowej usługi Azure Maps jest porównywalny z interfejsem API strefy czasowej w Mapach Google:

- [**Strefa czasowa według współrzędnych**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Określ współrzędną i odbierz szczegóły strefy czasowej współrzędnej.

Ta tabela odsyła parametry interfejsu API Map Google z porównywalnymi parametrami interfejsu API w usłudze Azure Maps.

| Parametr interfejsu API Map Google | Porównywalny parametr interfejsu API usługi Azure Maps   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Zobacz także [uwierzytelniania z usługi Azure Maps](azure-maps-authentication.md) dokumentacji.       |
| `language`                  | `language`– Zobacz [dokumentację obsługiwanych języków.](supported-languages.md)    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Oprócz tego interfejsu API usługa Azure Maps udostępnia szereg interfejsów API strefy czasowej. Te interfejsy API konwertuje czas na podstawie nazw lub identyfikatorów strefy czasowej:

- [**Strefa czasowa według identyfikatora**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): Zwraca bieżące, historyczne i przyszłe informacje o strefie czasowej dla określonego identyfikatora strefy czasowej IANA.
- [**Enum IANA strefy czasowej**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): Zwraca pełną listę identyfikatorów stref czasowych IANA. Aktualizacje usługi IANA są odzwierciedlane w systemie w ciągu jednego dnia.
- [**Wyliczenia strefy czasowej systemu Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): Zwraca pełną listę identyfikatorów stref czasowych systemu Windows.
- [**Wersja IANA strefy czasowej**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): zwraca bieżący numer wersji usługi IANA używany przez usługi Azure Maps.
- [**Strefa czasowa Windows do IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): Zwraca odpowiedni identyfikator IANA, o której napewniesz prawidłowy identyfikator strefy czasowej systemu Windows. Dla jednego identyfikatora systemu Windows może zostać zwróconych wiele identyfikatorów IANA.

## <a name="client-libraries"></a>Biblioteki klienta

Usługa Azure Maps udostępnia biblioteki klienckie dla następujących języków programowania:

- JavaScript, TypeScript, Node.js – [pakiet NPM](https://www.npmjs.com/package/azure-maps-rest) [dokumentacji](how-to-use-services-module.md) \|

Te biblioteki klienta open source są przeznaczone dla innych języków programowania:

- .NET Standard 2.0 – \| [Pakiet NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/) [projektu GitHub](https://github.com/perfahlen/AzureMapsRestServices)

## <a name="additional-resources"></a>Zasoby dodatkowe

Poniżej przedstawiono dodatkową dokumentację i zasoby dla usług REST usługi Azure Maps.

- [Najlepsze rozwiązania dotyczące wyszukiwania](how-to-use-best-practices-for-search.md)
- [Wyszukiwanie adresu](how-to-search-for-address.md)
- [Najważniejsze wskazówki dotyczące routingu](how-to-use-best-practices-for-routing.md)
- [Dokumentacja referencyjna interfejsu API usługi USŁUGI AZURE Maps REST](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach REST usługi Azure Maps.

> [!div class="nextstepaction"]
> [Najważniejsze wskazówki dotyczące korzystania z usługi wyszukiwania](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Najważniejsze wskazówki dotyczące korzystania z usługi routingu](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Jak korzystać z modułu usług (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
