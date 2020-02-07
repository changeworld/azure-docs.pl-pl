---
title: 'Samouczek: Migrowanie usług internetowych z usługi Google Maps | Mapy Microsoft Azure'
description: Jak przeprowadzić migrację usług sieci Web z usługi Google Maps do Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 50bdc0722328f857279b2cbd9a6e4cee740b9df8
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048930"
---
# <a name="migrate-web-service-from-google-maps"></a>Migruj usługę sieci Web z usługi Google Maps

Azure Maps i Google Maps zapewniają dostęp do przestrzennych interfejsów API za poorednictwem usług sieci Web REST. Interfejsy interfejsu API tych dwóch platform wykonują podobne funkcje, ale używają różnych konwencji nazewnictwa i obiektów odpowiedzi.

W poniższej tabeli przedstawiono interfejs API usługi Azure Maps, który zapewnia podobną funkcjonalność interfejsu API usługi Mapy Google.

| Interfejs API usługi Mapy Google | Interfejs API usługi Azure Maps                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Ze              | [Szlak](https://docs.microsoft.com/rest/api/maps/route)                               |
| Macierz odległości         | [Macierz trasy](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geokodowanie               | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)                             |
| Wyszukiwanie miejsc           | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)                             |
| Umieść Autouzupełnianie      | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)                             |
| Mapa statyczna              | [Renderowania](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Strefa czasowa               | [Strefa czasowa](https://docs.microsoft.com/rest/api/maps/timezone)                        |

Następujące interfejsy API usługi nie są obecnie dostępne w Azure Maps:

- Noszeniu
- Geolokalizacja
- Umieść szczegóły i umieść zdjęcia. Numery telefonów i adresy URL witryn internetowych są dostępne w interfejsie API wyszukiwania Azure Maps.
- Adresy URL map
- Drogach. Dane limitu szybkości są dostępne za pomocą interfejsów API trasy i odwrócenia geokodowania w Azure Maps.
- Widok statyczny

Azure Maps ma kilka dodatkowych usług sieci Web REST, które mogą być interesujące:

- [Operacje przestrzenne](https://docs.microsoft.com/rest/api/maps/spatial): odciążać złożone obliczenia przestrzenne i operacje, takie jak geoogrodzenia, do usługi.
- [Ruch](https://docs.microsoft.com/rest/api/maps/traffic): dostęp do przepływu ruchu i danych zdarzeń w czasie rzeczywistym.

## <a name="geocoding-addresses"></a>Adresy geokodowania

Geokodowanie jest procesem konwertowania adresu na współrzędną. Na przykład "1 Microsoft Way, Redmond, WA" jest konwertowane na "Długość geograficzna:-122,1298, Latitude: 47,64005". Współrzędne są niezbędne do pozycjonowania znacznika na mapie lub Wyśrodkuj mapę.

Azure Maps udostępnia kilka metod adresów geokodowania:

- [**Geokodowanie adresów w formie Geokodowanej**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Określ pojedynczy ciąg adresu i natychmiast przetwórz żądanie. Pojedynczy ciąg adresu to "1 Microsoft Way, Redmond, WA". Ta metoda jest zalecana, gdy trzeba szybko odkodować poszczególne adresy.
- [**Geokodowanie adresów strukturalnych**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Określ części pojedynczego adresu i przetwórz żądanie niemal w czasie rzeczywistym. Części adresu obejmują nazwę ulicy, miasto, kraj i kod pocztowy. Ta metoda jest zalecana w przypadku dwóch głównych scenariuszy. Dane są już analizowane jako poszczególne części adresu. Lub należy szybko odkodować poszczególne adresy.
- [**Geokodowanie adresów partii**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów i przetwórz żądanie w danym okresie czasu. Wszystkie adresy będą geokodować równolegle na serwerze. Po zakończeniu geokodowania cały zestaw wyników zostanie pobrany. Ta metoda jest zalecana w przypadku geokodowania dużych zestawów danych.
- [**Wyszukiwanie rozmyte**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ten interfejs API łączy geokodowanie adresów z punktem wyszukiwania zainteresowania. Ten interfejs API przyjmuje ciąg o dowolnym formacie i przetwarza żądanie niemal w czasie rzeczywistym. Ciąg o dowolnej postaci może być adresem, miejscem, punktem orientacyjnym lub kategorią punktu zainteresowania. Ten interfejs API jest zalecany, gdy do zapytania dotyczącego adresów i punktów orientacyjnych jest używane to samo pole tekstowe.
- [**Wyszukiwanie rozmytej partii**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów i przetwórz żądanie w danym okresie czasu. Możesz zażądać miejsc, punktów orientacyjnych lub punktu zainteresowania. Wszystkie dane zostaną przetworzone na serwerze i równolegle. Po zakończeniu cały zestaw wyników zostanie pobrany do pobrania.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` i `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` — miasto/miejscowość<br/>`municipalitySubdivision` — kluby, sub/Super miasto<br/>`countrySubdivision` — Województwo<br/>`countrySecondarySubdivision` — powiat<br/>`countryTertiarySubdivision` — region<br/>`countryCode` — dwuliterowy kod kraju |
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `region`                    | `countrySet`                       |

Przykład użycia usługi wyszukiwania opisano [tutaj](how-to-search-for-address.md). Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wyszukiwania](how-to-use-best-practices-for-search.md).

> [!TIP]
> W trybie Autouzupełnianie można używać interfejsów API swobodnego kodowania adresów i wyszukiwania rozmytego, dodając `&amp;typeahead=true` do adresu URL żądania. Poinformuje o tym, że tekst wejściowy jest prawdopodobnie częściowy, a wyszukiwanie przejdzie do trybu predykcyjnego.

## <a name="reverse-geocode-a-coordinate"></a>Odwróć współrzędne geokodowe

Odwrócenie geokodowania to proces konwersji współrzędnych geograficznych na przybliżony adres. Koordynuje ze znakiem "Długość geograficzna:-122,1298, Latitude: 47,64005" Konwertuj na "1 Microsoft Way, Redmond, WA".

Azure Maps oferuje kilka metod odwrotnego kodu geokodowania:

- [**Odwróć kod**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)geograficzny: Określ jedną współrzędną geograficzną, aby uzyskać przybliżony adres odpowiadający tej współrzędności. Przetwórz żądanie niemal w czasie rzeczywistym.
- [**Odwrotny kod**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)geograficzny Cross-Street: Określ jedną współrzędną geograficzną i Pobierz informacje o pobliskiej wartości krzyżowej. Przetwórz żądanie niemal w czasie rzeczywistym.
- [**Odwrotny geokod dotyczący adresu partii**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 współrzędnych i przetworzył żądanie w określonym czasie. Wszystkie dane będą przetwarzane równolegle na serwerze. Po zakończeniu cały zestaw wyników zostanie pobrany do pobrania.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps   | Porównywalny parametr interfejsu API Azure Maps   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `latlng`                    | `query`  |
| `location_type`             | *Nie dotyczy*     |
| `result_type`               | `entityType`    |

Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wyszukiwania](how-to-use-best-practices-for-search.md).

Interfejs API odtwarzania geokodowanej Azure Maps ma kilka dodatkowych funkcji, które nie są dostępne w usłudze Google Maps. Te funkcje mogą być przydatne do integracji z aplikacją podczas migracji aplikacji:

- Pobieranie danych limitu szybkości
- Pobierz informacje o użyciu dróg: droga lokalna, Arterial, ograniczony dostęp, Pochylnia i tak dalej
- Pobierz bok ulicy, w którym znajduje się Współrzędna

## <a name="search-for-points-of-interest"></a>Wyszukiwanie punktów orientacyjnych

Punkt danych zainteresowania można wyszukiwać w usłudze Google Maps przy użyciu interfejsu API wyszukiwania miejsc. Ten interfejs API udostępnia trzy różne sposoby wyszukiwania interesujących punktów:

- **Znajdź miejsce z tekstu:** Wyszukuje punkt orientacyjny na podstawie jego nazwy, adresu lub numeru telefonu.
- **Wyszukiwanie w pobliżu**: wyszukuje punkty zainteresowań, które znajdują się w określonej odległości od lokalizacji.
- **Wyszukiwanie tekstu:** Wyszukuje miejsca przy użyciu swobodnego tekstu, który obejmuje punkt orientacyjny i informacje o lokalizacji. Na przykład "Pizza w Nowym Jorku" lub "Restauracje blisko głównego St".

Azure Maps udostępnia kilka interfejsów API wyszukiwania dla interesujących punktów:

- [**Wyszukiwanie punkt POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Wyszukiwanie punktów zainteresowania według nazwy. Na przykład "Starbucks".
- [**Wyszukiwanie kategorii punkt POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Wyszukiwanie punktów zainteresowania według kategorii. Na przykład "restauracji".
- [**Wyszukiwanie w pobliżu**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): wyszukuje punkty zainteresowań, które znajdują się w określonej odległości od lokalizacji.
- [**Wyszukiwanie rozmyte**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ten interfejs API łączy geokodowanie adresów z punktem wyszukiwania zainteresowania. Ten interfejs API zajmuje ciąg o dowolnej postaci. Adres, miejsce, dzielnica, punkt orientacyjny, Kategoria punktu orientacyjnego itd. Ten interfejs API może przetworzyć żądanie niemal w czasie rzeczywistym. Ten interfejs API jest zalecany, gdy użytkownicy szukają adresów lub punktów zainteresowań przy użyciu tego samego pola tekstowego.
- [**Wyszukaj w obrębie geometrii**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Wyszukaj punkty zainteresowań, które znajdują się w określonej geometrii (Wielokąt).
- [**Wyszukiwanie wzdłuż trasy**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Wyszukiwanie punktów zainteresowań, które znajdują się w określonej ścieżce trasy.
- [**Wyszukiwanie rozmytej partii**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów, miejsc, punktów orientacyjnych lub punktu zainteresowania i przetworzyć je w określonym czasie. Wszystkie dane będą przetwarzane równolegle na serwerze. Po zakończeniu przetwarzania żądania Pobierz pełny zestaw wyników.

Obecnie Azure Maps nie ma porównywalnego interfejsu API do interfejsu API wyszukiwania tekstu w usłudze Google Maps.

> [!TIP]
> Interfejs API wyszukiwania punkt POI, interfejs API wyszukiwania kategorii punkt POI i interfejsy API wyszukiwania rozmytego mogą być używane w trybie Autouzupełnianie. Dodaj `&amp;typeahead=true` do adresu URL żądania. Spowoduje to wyświetlenie serwera, który jest najprawdopodobniej częściowym tekstem wejściowym i wyszukiwanie zostanie wykonane w trybie predykcyjnym.

Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wyszukiwania](how-to-use-best-practices-for-search.md).

### <a name="find-place-from-text"></a>Znajdź miejsce z tekstu

Użyj Azure Maps interfejsu API [wyszukiwania punkt POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) lub interfejsu API [wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) , aby wyszukać punkty zainteresowań według nazwy lub adresu.

W poniższej tabeli przedstawiono parametry interfejsu API usługi Google Maps z odpowiednimi parametrami interfejsu API Azure Maps.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps |
|---------------------------|-------------------------------------|
| `fields`                  | *Nie dotyczy*                               |
| `input`                   | `query`                             |
| `inputtype`               | *Nie dotyczy*                               |
| `key`                     | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `locationbias`            | `lat`, `lon` i `radius`<br/>`topLeft` i `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Wyszukiwanie w pobliżu

W Azure Maps Pobierz bliskie punkty zainteresowań przy użyciu interfejsu API [wyszukiwania w pobliżu](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) .

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps z parametrami interfejsu API Azure Maps.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `keyword`                   | `categorySet` i `brandSet`        |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `location`                  | `lat` i `lon`                     |
| `maxprice`                  | *Nie dotyczy*                               |
| `minprice`                  | *Nie dotyczy*                               |
| `name`                      | `categorySet` i `brandSet`        |
| `opennow`                   | *Nie dotyczy*                               |
| `pagetoken`                 | `ofs` i `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *Nie dotyczy*                               |
| `type`                      | `categorySet –` Zobacz sekcję [obsługiwane kategorie wyszukiwania](supported-search-categories.md) .   |

## <a name="calculate-routes-and-directions"></a>Obliczanie tras i wskazówek

Obliczaj trasy i wskazówki przy użyciu Azure Maps. Azure Maps ma wiele z tych samych funkcji co usługa routingu Google Maps, na przykład:

- czasy przybycia i wyjazdu.
- trasy ruchu w czasie rzeczywistym i predykcyjne.
- Różne tryby transportu. Takie jak kierowanie, idącie i przechodzenie.

> [!NOTE]
> Azure Maps wymaga współrzędnych wszystkich waypoints. Adresy muszą mieć najpierw kod geokodowania.

Usługa routingu Azure Maps udostępnia następujące interfejsy API do obliczania tras:

- [**Oblicz trasę**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Oblicz trasę i przetwórz żądanie niemal w czasie rzeczywistym. Ten interfejs API obsługuje zarówno żądania GET, jak i POST. Żądania POST są zalecane do określenia dużej liczby waypoints lub użycia wielu opcji trasy. Użycie polecenia POST gwarantuje, że żądanie adresu URL nie stanie się zbyt długie i powoduje problemy.
- [**Trasa wsadowa**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): Utwórz żądanie zawierające maksymalnie 1 000 żądanie trasy. Procesy żądań w danym okresie czasu. Wszystkie procesy danych równolegle na serwerze. Pobierz zestaw zaraz po zakończeniu żądania, a wyniki są gotowe.
- [**Usługi mobilności**](https://docs.microsoft.com/rest/api/maps/mobility): obliczanie tras i wskazówek przy użyciu tranzytu publicznego.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps    | Porównywalny parametr interfejsu API Azure Maps  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` — współrzędne w formacie `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                     | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | Nie *dotyczy — ta* funkcja jest związana z geokodowaniem. Użyj parametru *countrySet* w przypadku korzystania z interfejsu API geokodowania Azure Maps.  |
| `traffic_model`               | Nie *dotyczy — można* określić tylko wtedy, gdy dane o ruchu mają być używane z parametrem *ruchu* . |
| `transit_mode`                | Zobacz [dokumentację usług mobilności](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Zobacz [dokumentację usług mobilności](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | Nie *dotyczy — Azure Maps* używa tylko systemu metryk.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Domyślnie interfejs API trasy Azure Maps zwraca tylko podsumowanie (odległość i czas) i współrzędne ścieżki trasy. Użyj parametru `instructionsType`, aby pobrać instrukcje włączania i wyłączania. Użyj parametru `routeRepresentation`, aby odfiltrować podsumowanie i ścieżkę trasy.

Interfejs API routingu Azure Maps ma wiele dodatkowych funkcji, które nie są dostępne w usłudze Google Maps. Warto zintegrować te funkcje w trakcie migracji aplikacji:

- Obsługa typu trasy: najkrótszy, najszybszy, TRILLING i większość wydajnych paliw.
- Obsługa dodatkowych trybów podróży: Magistrala, motocykl, taksówka, ciężarówka i Van.
- Obsługa 150 waypoints.
- Obliczanie wielu czasów podróży w jednym żądaniu; ruch historyczny, ruch na żywo, brak ruchu.
- Unikaj dodatkowych typów dróg: Carpool dróg, unpaved dróg, już używanych dróg.
- Określ obszary niestandardowe, które mają być stosowane.
- Ogranicz podniesienie uprawnień, które trasa może wyznaczać.
- Trasa oparta na specyfikacjach silnika. Obliczaj trasy dla spalania lub pojazdów elektrycznych w oparciu o specyfikacje silnika oraz pozostałe paliwo lub opłaty.
- Obsługa komercyjnych parametrów trasy pojazdu. Takie jak wymiary pojazdu, waga, liczba axels i typ ładunku.
- Określ maksymalną prędkość pojazdu.

Oprócz tych funkcji Usługa Route w Azure Maps obsługuje [Obliczanie zakresów routingu](https://docs.microsoft.com/rest/api/maps/route/getrouterange). Obliczanie zakresów routingu jest również znane jako izochron. Wiąże się to z generowaniem obszaru pokrytego wielokąta. Następnie Obliczaj podróże w dowolnym kierunku od punktu początkowego. Wszystkie podczas rozważania określonego czasu i ilości paliwa lub opłaty.

## <a name="retrieve-a-map-image"></a>Pobieranie obrazu mapy

Azure Maps udostępnia interfejs API do renderowania obrazów mapy statycznej z przełożonymi danymi. Interfejs API [renderowania obrazu mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) w Azure Maps jest porównywalny z interfejsem API mapy statycznej w usłudze mapy Google.

> [!NOTE]
> Azure Maps wymaga, aby środkowe, wszystkie znaczniki i lokalizacje ścieżki były współrzędnych w formacie "Długość geograficzna". Z drugiej strony usługi Google Maps używają formatu "szerokości geograficznej". Adresy muszą mieć najpierw kod geokodowania.

W poniższej tabeli przedstawiono parametry interfejsu API usługi Google Maps z porównywalnymi parametrami interfejsu API Azure Maps.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` — określony jako część ścieżki URL. Obecnie obsługiwane są tylko pliki PNG. |
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `maptype`                   | `layer` i `style` — Zobacz dokumentację [mapy obsługiwanych stylów](supported-map-styles.md) . |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | Nie *dotyczy — jest* to funkcja powiązana z geokodowaniem. Użyj parametru `countrySet`, gdy korzystasz z interfejsu API Azure Maps geokodowania.  |
| `scale`                     | *Nie dotyczy*                              |
| `size`                      | `width` i `height` — może mieć rozmiar maksymalnie 8192x8192. |
| `style`                     | *Nie dotyczy*                              |
| `visible`                   | *Nie dotyczy*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure Maps używa systemu kafelków z kafelkami, które są dwa razy większym rozmiarem kafelków mapy używanych w usłudze mapy Google. Tak jak w przypadku wartości poziomu powiększenia występuje jeden poziom powiększenia zbliżony do Azure Maps w porównaniu do usługi Google Maps. Zmniejsz poziom powiększenia o jeden, w migrowanych żądaniach. Zmniejszenie wartości poziomu powiększenia umożliwia skompensowanie zmian w systemach kafelków.

Aby uzyskać więcej informacji, zobacz [Przewodnik dotyczący korzystania z interfejsu API renderowania obrazu mapy](how-to-render-custom-data.md).

Oprócz generowania obrazu mapy statycznej usługa renderowania Azure Maps zapewnia możliwość bezpośredniego dostępu do kafelków mapy w formacie rastrowym (PNG) i wektorowym:

- [**Kafelek mapy**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): pobieranie kafelków rastrowych (PNG) i wektorów dla map bazowych (dróg, granic, tła).
- [**Kafelek mapy zdjęć**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): pobieranie obrazów anten i satelity.

> [!TIP]
> Kilka lat temu wiele aplikacji usługi Google Maps zostało przełączone z interaktywnych funkcji mapy do obrazów mapy statycznej jako metody oszczędności kosztów. W Azure Maps często jest znacznie bardziej oszczędne korzystanie z kontrolki mapy interaktywnej w zestawie SDK sieci Web. Opłata za usługę jest oparta na liczbie kafelków mapy ładowanych przez aplikację. Kafelki mapy w Azure Maps są duże. Często tylko kilka kafelków umożliwia odtworzenie tego samego widoku mapy jako mapy statycznej. Kafelki mapy są buforowane automatycznie przez przeglądarkę. W związku z tym interaktywna kontrolka mapy często generuje ułamek transakcji podczas odtwarzania widoku mapy statycznej. Kadrowanie i powiększanie spowoduje załadowanie większej liczby kafelków, jednak w formancie mapy można wyłączyć to zachowanie. Interaktywna kontrolka mapy udostępnia również wiele opcji wizualizacji niż usługi map statycznych.

### <a name="marker-url-parameter-format-comparison"></a>Porównanie formatu parametru adresu URL znacznika

**Wcześniej: Google Maps**

W usłudze mapy Google można dodać znaczniki do obrazu mapy statycznej przy użyciu `markers` parametru w adresie URL. `markers` parametr przyjmuje styl i listę lokalizacji, które mają być renderowane na mapie z tym stylem, jak pokazano poniżej:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Dodatkowe style mogą być używane przez dodanie dodatkowych parametrów `markers` do adresu URL o innym stylu i zestawie lokalizacji.

Lokalizacje znaczników są określane przy użyciu formatu "szerokości geograficznej".

Style znaczników w usłudze Google Maps są dodawane z formatem `optionName:value`, z wieloma stylami oddzielonymi znakami potoku (\|). Podobnie: "optionName1: wartość1\|optionName2: wartość2". Zwróć uwagę, że nazwy i wartości opcji są oddzielone dwukropkiem (:). Następujące nazwy opcji stylu mogą służyć do znaczników stylów w usłudze Google Maps:

- `color` — kolor ikony znacznika domyślnego. Może to być 24-bitowy kolor szesnastkowy (`0xrrggbb`) lub jedna z następujących wartości: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` — pojedynczy znak alfanumeryczny pisany wielkimi literami, który ma być wyświetlany na górze ikony.
- `size` — rozmiar znacznika. Może być `tiny`, `mid`lub `small`.

Niestandardowe ikony można dodawać w usłudze Google Maps przy użyciu następujących nazw opcji stylu:

- `anchor` — określa sposób wyrównania obrazu ikony do współrzędnych. Może to być wartość pikseli (x, y) lub jedna z następujących wartości: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`lub `bottomright`.
- `icon` — adres URL wskazujący obraz ikony.

Na przykład w usłudze Google Maps do mapy można dodać czerwony znacznik o rozmiarze średnim (Długość geograficzna: 110, Szerokość geograficzna: 45) przy użyciu następującego parametru adresu URL:

```
&markers=color:red|size:mid|45,-110
```

<center>

znacznik ![Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Po: Azure Maps**

W Azure Maps Dodaj znaczniki do obrazu mapy statycznej, określając parametr `pins` w adresie URL. Podobnie jak w przypadku usługi Google Maps, określ styl i listę lokalizacji w tym parametrze. Określ wiele razy parametr `pins`, aby obsługiwał znaczniki o różnych stylach.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Aby użyć dodatkowych stylów, Dodaj dodatkowe `pins` parametry do adresu URL przy użyciu innego stylu i zestawu lokalizacji.

W przypadku lokalizacji numeru PIN Azure Maps wymaga, aby współrzędne były w formacie "szerokości geograficznej". Usługi Google Maps używają formatu "szerokości geograficznej". Spacja, a nie przecinek, oddziela długość i szerokość geograficzną w formacie Azure Maps.

`iconType` określa typ kodu PIN do utworzenia. Może mieć następujące wartości:

- `default` — ikona domyślnego numeru PIN.
- `none` — nie jest wyświetlana ikona, tylko etykiety będą renderowane.
- `custom` — określa, że ma zostać użyta ikona niestandardowa. Adres URL wskazujący ikonę obrazu można dodać na końcu parametru `pins` po określeniu informacji o lokalizacji numeru PIN.
- `{udid}` — unikatowy identyfikator danych (UDID) dla ikony przechowywanej na platformie Azure Maps magazynu danych.

Dodaj style kodu PIN w Azure Maps z formatem `optionNameValue`. Oddziel wiele stylów za pomocą znaków potoku (\|). Na przykład: `iconType|optionName1Value1|optionName2Value2`. Nazwy i wartości opcji nie są oddzielone. Użyj następujących nazw opcji stylu do znaczników stylu w Azure Maps:

- `al` — określa nieprzezroczystość (alfa) znacznika. Wybierz liczbę z zakresu od 0 do 1.
- `an` — określa zakotwiczenie numeru PIN. Wartości x i y pikseli określone w formacie "x y".
- `co` — kolor kodu PIN. Musi być 24-bitowym kolorem szesnastkowym: `000000` do `FFFFFF`.
- `la` — określa zakotwiczenie etykiety. Wartości x i y pikseli określone w formacie "x y".
- `lc` — kolor etykiety. Musi być 24-bitowym kolorem szesnastkowym: `000000` do `FFFFFF`.
- `ls` — rozmiar etykiety (w pikselach). Wybierz liczbę większą niż 0.
- `ro` — wartość w stopniach, aby obrócić ikonę. Wybierz liczbę z przedziału od-360 do 360.
- `sc` — wartość skali dla ikony pinezki. Wybierz liczbę większą niż 0.

Wartości etykiet są określone dla każdej lokalizacji numeru PIN. Takie podejście jest bardziej wydajne niż stosowanie pojedynczej wartości etykiety do wszystkich znaczników na liście lokalizacji. Wartość etykiety może być ciągiem z wieloma znakami. Zawiń ciąg z pojedynczymi cudzysłowami, aby upewnić się, że nie zostanie on pomylony jako wartość stylu lub lokalizacji.

Na przykład w Azure Maps, dodając czerwoną (`FF0000`) ikonę domyślną z etykietą "obszar etykietka" umieszczoną poniżej (15 50), z ikoną na współrzędne (Długość geograficzna: 122,349300, Szerokość geograficzna: 47,620180) jest wykonywane przy użyciu następującego parametru adresu URL:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![znacznik Azure Maps](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Poniższy przykład dodaje trzy numery PIN z wartościami etykiet "1", "2" i "3":

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps wiele znaczników](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Porównanie formatu parametru adresu URL

**Wcześniej: Google Maps**

W usłudze mapy Google można dodawać linie i wielokąty do obrazu mapy statycznej przy użyciu `path` parametru w adresie URL. `path` parametr przyjmuje styl i listę lokalizacji, które mają być renderowane na mapie, jak pokazano poniżej:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Dodatkowe style mogą być używane przez dodanie dodatkowych parametrów `path` do adresu URL o innym stylu i zestawie lokalizacji.

Lokalizacje ścieżki w usłudze mapy Google są określane przy użyciu formatu `latitude1,longitude1|latitude2,longitude2|…`. Ścieżki mogą być kodowane lub zawierać adresy dla punktów.

Style ścieżki w usłudze mapy Google są dodawane z formatem `optionName:value`, z wieloma stylami oddzielonymi znakami potoku (\|). Jak to zrobić: `optionName1:value1|optionName2:value2`. Zwróć uwagę, że nazwy i wartości opcji są oddzielone dwukropkiem (:). Następujące nazwy opcji stylu mogą służyć do nadawania stylu ścieżkom w usłudze Google Maps:

- `color` — kolor ścieżki lub konturu wielokąta. Może to być 24-bitowy kolor szesnastkowy (`0xrrggbb`), 32-bitowy kolor szesnastkowy (`0xrrggbbbaa`) lub jedna z następujących wartości: Black, brązowy, zielony, purpurowy, żółty, niebieski, szary, pomarańczowy, czerwony, biały.
- `fillColor` — kolor wypełnienia obszaru ścieżki przy użyciu (Wielokąt). Może to być 24-bitowy kolor szesnastkowy (`0xrrggbb`), 32-bitowy kolor szesnastkowy (`0xrrggbbbaa`) lub jedna z następujących wartości: Black, brązowy, zielony, purpurowy, żółty, niebieski, szary, pomarańczowy, czerwony, biały.
- `geodesic` — wskazuje, czy ścieżka powinna być linią, która jest zgodna z krzywizną ziemi.
- `weight` — grubość linii ścieżki w pikselach.

W usłudze mapy Google do mapy między współrzędnymi można dodać nieprzezroczystą czerwoną linię i grubość pikseli, w parametrze adresu URL. W poniższym przykładzie linia ma nieprzezroczystość 50% i grubość czterech pikseli. Współrzędne to długość geograficzna:-110, Szerokość geograficzna: 45 i Długość geograficzna:-100, Szerokość geograficzna: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![linię łamaną w usłudze Google Maps](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Po: Azure Maps**

W Azure Maps Dodaj linie i wielokąty do obrazu mapy statycznej, określając parametr `path` w adresie URL. Podobnie jak w przypadku usługi Google Maps, określ styl i listę lokalizacji w tym parametrze. Określ parametr `path` wiele razy, aby renderować wiele okręgów, linii i wielokątów o różnych stylach.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Gdy powróci do lokalizacji ścieżki, Azure Maps wymaga, aby współrzędne były w formacie "szerokości geograficznej". Usługi Google Maps używają formatu "szerokości geograficznej". Spacja, a nie przecinek, oddziela długość i szerokość geograficzną w formacie Azure Maps. Azure Maps nie obsługuje zakodowanych ścieżek ani adresów dla punktów. Przekaż większe zestawy danych jako GEOJSON wypełnienia do Azure Maps interfejsu API magazynu danych zgodnie z opisem w [tym miejscu](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

W Azure Maps Dodaj style ścieżki przy użyciu formatu `optionNameValue`. Oddziel wiele stylów według potoku (\|), takich jak `optionName1Value1|optionName2Value2`. Nazwy i wartości opcji nie są oddzielone. Użyj następujących nazw opcji stylu do ścieżek stylów w Azure Maps:

- `fa` — nieprzezroczystość koloru wypełnienia (alfa) użyta podczas renderowania wielokątów. Wybierz liczbę z zakresu od 0 do 1.
- `fc` — kolor wypełnienia używany do renderowania obszaru wielokąta.
- `la` — nieprzezroczystość koloru linii (alfa) używana podczas renderowania linii i konspektu wielokątów. Wybierz liczbę z zakresu od 0 do 1.
- `lc` — kolor linii używany do renderowania linii i konspektu wielokątów.
- `lw` — szerokość linii w pikselach.
- `ra` — określa promień okręgów w metrach.

W Azure Maps Dodaj nieprzezroczystość czerwoną linii i grubość pikseli między współrzędnymi, w parametrze adresu URL. W poniższym przykładzie linia ma wartość nieprzezroczystość 50% i grubość czterech pikseli. Współrzędne mają następujące wartości: Długość geograficzna:-110, Szerokość geograficzna: 45 i Długość geograficzna:-100, Szerokość geograficzna: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps łamaną](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Obliczanie macierzy odległości

Azure Maps udostępnia interfejs API macierzy odległości. Ten interfejs API umożliwia obliczanie czasów podróży i odległości między zestawem lokalizacji przy użyciu macierzy odległości. Jest on porównywalny z interfejsem API macierzy odległości w usłudze mapy Google.

- [**Macierz trasy**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): asynchronicznie oblicza czasy podróży i odległości dla zestawu źródeł i miejsc docelowych. Obsługuje do 700 komórek na żądanie (liczba źródeł pomnożona przez liczbę miejsc docelowych. Mając to na uwadze, przykłady możliwych wymiarów macierzy to: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Żądanie interfejsu API macierzy odległości można wykonać tylko przy użyciu żądania POST z informacjami o lokalizacji źródłowej i docelowej w treści żądania. Ponadto Azure Maps wymaga współrzędnych wszystkich źródeł i miejsc docelowych. Adresy muszą mieć najpierw kod geokodowania.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów Azure Maps interfejsu API.

| Parametr interfejsu API usługi Google Maps      | Porównywalny parametr interfejsu API Azure Maps  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` — Określ w treści żądania POST jako GEOJSON. |
| `key`                          | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                     | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` — Określ w treści żądania POST jako GEOJSON.  |
| `region`                       | Nie *dotyczy — ta* funkcja jest związana z geokodowaniem. Użyj parametru `countrySet`, gdy korzystasz z interfejsu API Azure Maps geokodowania. |
| `traffic_model`                | Nie *dotyczy — można* określić tylko wtedy, gdy dane o ruchu mają być używane z parametrem `traffic`. |
| `transit_mode`                 | Macierze *oparte na* trasach nie są obecnie obsługiwane.  |
| `transit_routing_preference`   | Macierze *oparte na* trasach nie są obecnie obsługiwane.  |
| `units`                        | Nie *dotyczy — Azure Maps* używa tylko systemu metryk. |

> [!TIP]
> Wszystkie zaawansowane opcje routingu dostępne w interfejsie API routingu Azure Maps są obsługiwane w interfejsie API macierzy na odległość Azure Maps. Zaawansowane opcje routingu obejmują: Ruck Routing, specyfikacje aparatu i tak dalej.

## <a name="get-a-time-zone"></a>Pobierz strefę czasową

Azure Maps udostępnia interfejs API do pobierania strefy czasowej współrzędnych. Interfejs API strefy czasowej Azure Maps jest porównywalny z interfejsem API strefy czasowej w usłudze Google Maps:

- [**Strefa czasowa według współrzędnych**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Określ współrzędną i Odbierz szczegóły strefy czasowej współrzędnych.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md) dokumentacji.       |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Oprócz tego interfejsu API platforma Azure Maps udostępnia wiele interfejsów API strefy czasowej. Te interfejsy API konwertują czas na podstawie nazw lub identyfikatorów strefy czasowej:

- [**Strefa czasowa według identyfikatora**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): zwraca bieżące, historyczne i przyszłe informacje o strefie czasowej dla określonego identyfikatora strefy czasowej organizacji IANA.
- [**Wyliczenie strefy czasowej Iana**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): zwraca pełną listę identyfikatorów stref czasowych organizacji IANA. Aktualizacje usługi IANA są uwzględniane w systemie w ciągu jednego dnia.
- [**Okna wyliczania strefy czasowej**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): zwraca pełną listę identyfikatorów strefy czasowej systemu Windows.
- [**Wersja programu Iana strefy czasowej**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): zwraca bieżący numer wersji programu Iana używany przez Azure Maps.
- [**System Windows strefy czasowej dla organizacji Iana**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): zwraca odpowiedni identyfikator Iana, uwzględniając prawidłowy identyfikator strefy czasowej systemu Windows. Dla pojedynczego identyfikatora systemu Windows można zwrócić wiele identyfikatorów IANA.

## <a name="client-libraries"></a>Biblioteki klienta

Azure Maps udostępnia biblioteki klienckie dla następujących języków programowania:

- JavaScript, TypeScript, Node. js — [dokumentacja](how-to-use-services-module.md) \| [pakiet npm](https://www.npmjs.com/package/azure-maps-rest)

Biblioteki klienckie Open Source dla innych języków programowania:

- .NET Standard 2,0 — [pakiet NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/) [projektu usługi GitHub](https://github.com/perfahlen/AzureMapsRestServices) \|

## <a name="additional-resources"></a>Dodatkowe zasoby

Poniżej przedstawiono dodatkową dokumentację i zasoby dotyczące Azure Maps usług REST.

- [Najlepsze rozwiązania dotyczące wyszukiwania](how-to-use-best-practices-for-search.md)
- [Wyszukaj adres](how-to-search-for-address.md)
- [Dokumentacja interfejsu API usługi REST Azure Maps](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat Azure Maps usług REST.

> [!div class="nextstepaction"]
> [Najlepsze rozwiązania dotyczące korzystania z usługi wyszukiwania](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Jak używać modułu usług (Web SDK)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)