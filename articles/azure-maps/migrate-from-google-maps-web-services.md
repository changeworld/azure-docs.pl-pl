---
title: Migrowanie usług internetowych z usługi Google Maps | Microsoft Docs
description: Samouczek dotyczący sposobu migrowania usług sieci Web z usługi Mapy Google do Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8fc3270d15f77576ff353a110d8a3a9a17c7144d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480023"
---
# <a name="migrate-web-service-from-google-maps"></a>Migruj usługę sieci Web z usługi Google Maps

Zarówno platforma Azure, jak i usługa Google Maps zapewniają dostęp do przestrzennych interfejsów API za pomocą usług sieci Web REST. Interfejsy interfejsu API dla tych platform wykonują podobne funkcje, ale używają różnych konwencji nazewnictwa i obiektów odpowiedzi.

W poniższej tabeli przedstawiono interfejsy API usługi Azure Maps, które zapewniają podobną funkcjonalność do listy interfejsów API usługi Mapy Google.

| Interfejs API usługi Mapy Google | Interfejs API usługi Azure Maps                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Wskazówki dojazdu              | [Route](https://docs.microsoft.com/rest/api/maps/route)                               |
| Macierz odległości         | [Macierz trasy](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geokodowanie               | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)                             |
| Wyszukiwanie miejsc           | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)                             |
| Umieść Autouzupełnianie      | [Wyszukiwanie](https://docs.microsoft.com/rest/api/maps/search)                             |
| Mapa statyczna              | [Renderowanie](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Strefa czasowa               | [Strefa czasowa](https://docs.microsoft.com/rest/api/maps/timezone)                        |

Następujące interfejsy API usługi nie są obecnie dostępne w Azure Maps:

- Noszeniu
- Geolokalizacja
- Umieszcza szczegóły i zdjęcia. Numery telefonów i adresy URL witryn internetowych są dostępne w interfejsie API wyszukiwania Azure Maps.
- Adresy URL map
- Drogi — dane limitu szybkości są dostępne za pomocą interfejsów API trasy i odwrócenia geokodowania w Azure Maps.
- Widok statyczny

Azure Maps ma kilka dodatkowych usług sieci Web REST, które mogą być interesujące:

- [Operacje przestrzenne](https://docs.microsoft.com/rest/api/maps/spatial): odciążać złożone obliczenia przestrzenne i operacje, takie jak geoogrodzenia, do usługi.
- [Ruch](https://docs.microsoft.com/rest/api/maps/traffic): dostęp do przepływu ruchu i danych zdarzeń w czasie rzeczywistym.

## <a name="geocoding-addresses"></a>Adresy geokodowania

Geokodowanie jest procesem konwertowania adresu (takiego jak "1 Microsoft Way, Redmond, WA") na współrzędną (na przykład Długość geograficzna:-122,1298, Szerokość geograficzna: 47,64005). Współrzędne są następnie często używane do pozycjonowania znacznika na mapie lub Wyśrodkuj mapę.

Azure Maps udostępnia kilka metod adresów geokodowania:

- [**Geokodowanie adresów w formie Geokodowanej**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Określ pojedynczy ciąg adresu (na przykład "1 Microsoft Way, REDMOND, WA") i natychmiast przetwórz żądanie. Jest to zalecane, jeśli trzeba szybko odkodować poszczególne adresy.
- [**Geokodowanie adresów strukturalnych**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Określ części pojedynczego adresu, takie jak nazwa ulicy, miasto, kraj i kod pocztowy, i natychmiast przetwórz żądanie. Jest to zalecane, jeśli trzeba szybko odkodować poszczególne adresy, a dane są już analizowane w poszczególnych częściach adresu.
- [**Geokodowanie adresów partii**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów i przetworzy je w danym okresie czasu. Wszystkie adresy będą kodowane geokodowowo na serwerze i po ukończeniu można pobrać pełny zestaw wyników. Jest to zalecane w przypadku dużych zestawów danych geokodowania.
- [**Wyszukiwanie rozmyte**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ten interfejs API łączy geokodowanie adresów z punktem wyszukiwania zainteresowania. Ten interfejs API przyjmuje ciąg o dowolnej postaci, który może być adresem, miejscem, punktem orientacyjnym lub kategorią punktu zainteresowania, i natychmiastowo przetworzyć żądanie. Ten interfejs API jest zalecany w przypadku aplikacji, w których użytkownicy mogą wyszukiwać adresy lub punkty istotne z tego samego pola tekstowego.
- [**Wyszukiwanie rozmytej partii**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów, miejsc, punktów orientacyjnych lub punktu zainteresowania i przetworzyć je w określonym czasie. Wszystkie dane zostaną przetworzone równolegle na serwerze i po ukończeniu można pobrać pełny zestaw wyników.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` i `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` — miasto/miejscowość<br/>`municipalitySubdivision` — kluby, sub/Super miasto<br/>`countrySubdivision` — Województwo<br/>`countrySecondarySubdivision` — powiat<br/>`countryTertiarySubdivision` — region<br/>`countryCode` — dwuliterowy kod kraju |
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `region`                    | `countrySet`                       |

Przykład użycia usługi wyszukiwania opisano [tutaj](how-to-search-for-address.md). Pamiętaj, aby zapoznać się z [najlepszymi rozwiązaniami](how-to-use-best-practices-for-search.md) dotyczącymi dokumentacji wyszukiwania.

> [!TIP]
> W trybie Autouzupełnianie można używać interfejsów API swobodnego kodowania adresów i wyszukiwania rozmytego, dodając `&amp;typeahead=true` do adresu URL żądania. Poinformuje o tym, że tekst wejściowy jest prawdopodobnie częściowy i przejdzie do trybu predykcyjnego.

## <a name="reverse-geocode-a-coordinate"></a>Odwróć współrzędne geokodowe

Odwrócenie geokodowania to proces konwersji współrzędnych geograficznych (takich jak długość geograficzna:-122,1298, Szerokość geograficzna: 47,64005) na adres przybliżony (na przykład "1 Microsoft Way, Redmond, WA").

Azure Maps oferuje kilka metod odwrotnego kodu geokodowania:

- [**Odwróć kod**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)geograficzny: Określ jedną współrzędną geograficzną, aby uzyskać przybliżony adres, i natychmiast przetworzyć żądanie.
- [**Odwrotny kod**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)geograficzny Cross-Street: Określ jedną współrzędną geograficzną, aby uzyskać informacje o skrzyżowaniu krzyżowym (na przykład 1st & Main) i natychmiast przetworzyć żądanie.
- [**Odwrotny geokod dotyczący adresu partii**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 współrzędnych i przetworzy je w określonym czasie. Wszystkie dane zostaną przetworzone równolegle na serwerze i po ukończeniu można pobrać pełny zestaw wyników.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps   | Porównywalny parametr interfejsu API Azure Maps   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `latlng`                    | `query`  |
| `location_type`             | *Nie dotyczy*     |
| `result_type`               | `entityType`    |

Pamiętaj, aby zapoznać się z [najlepszymi rozwiązaniami](how-to-use-best-practices-for-search.md) dotyczącymi dokumentacji wyszukiwania.

Interfejs API odwrotnego geokodowania Azure Maps ma niektóre dodatkowe funkcje niedostępne w usłudze Google Maps, które mogą być przydatne do integracji podczas migracji aplikacji:

- Pobierz dane limitu szybkości.
- Pobierz informacje o użyciu dróg: droga lokalna, Arterial, ograniczony dostęp, Pochylnia itp.
- Strona ulica, na której znajduje się Współrzędna.

## <a name="search-for-points-of-interest"></a>Wyszukiwanie punktów orientacyjnych

Punkt danych zainteresowania można wyszukiwać w usłudze Google Maps przy użyciu interfejsu API wyszukiwania miejsc. Ten interfejs API udostępnia trzy różne sposoby wyszukiwania interesujących punktów:

- **Znajdź miejsce z tekstu:** Wyszukuje punkt orientacyjny na podstawie jego nazwy, adresu lub numeru telefonu.
- **Wyszukiwanie w pobliżu**: wyszukuje punkty zainteresowań, które znajdują się w określonej odległości od lokalizacji.
- **Wyszukiwanie tekstu:** Wyszukuje miejsca przy użyciu swobodnego tekstu, który zawiera punkt orientacyjny i informacje o lokalizacji. Na przykład "Pizza w Nowym Jorku" lub "Restauracje blisko głównego St".

Azure Maps udostępnia kilka interfejsów API wyszukiwania dla interesujących punktów:

- [**Wyszukiwanie punkt POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Wyszukiwanie punktów zainteresowania według nazwy. Na przykład "Starbucks".
- [**Wyszukiwanie kategorii punkt POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Wyszukiwanie punktów zainteresowania według kategorii. Na przykład "restauracji".
- [**Wyszukiwanie w pobliżu**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): wyszukuje punkty zainteresowań, które znajdują się w określonej odległości od lokalizacji.
- [**Wyszukiwanie rozmyte**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): ten interfejs API łączy geokodowanie adresów z punktem wyszukiwania zainteresowania. Ten interfejs API przyjmuje ciąg o dowolnej postaci, który może być adresem, miejscem, punktem orientacyjnym lub kategorią punktu zainteresowania, i natychmiastowo przetworzyć żądanie. Ten interfejs API jest zalecany w przypadku aplikacji, w których użytkownicy mogą wyszukiwać adresy lub punkty istotne z tego samego pola tekstowego.
- [**Wyszukaj w obrębie geometrii**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Wyszukaj punkty zainteresowań, które znajdują się w określonej geometrii (Wielokąt).
- [**Wyszukiwanie wzdłuż trasy**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Wyszukiwanie punktów zainteresowań, które znajdują się w określonej ścieżce trasy.
- [**Wyszukiwanie rozmytej partii**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Utwórz żądanie zawierające maksymalnie 10 000 adresów, miejsc, punktów orientacyjnych lub punktu zainteresowania i przetworzyć je w określonym czasie. Wszystkie dane zostaną przetworzone równolegle na serwerze i po ukończeniu można pobrać pełny zestaw wyników.

Obecnie Azure Maps nie ma porównywalnego interfejsu API dla interfejsu API wyszukiwania tekstu usługi Google Maps.

> [!TIP]
> W trybie Autouzupełnianie można używać interfejsów wyszukiwania punkt POI, wyszukiwania kategorii punkt POI i wyszukiwania rozmytego, dodając `&amp;typeahead=true` do adresu URL żądania. Poinformuje o tym, że tekst wejściowy jest prawdopodobnie częściowy i przejdzie do trybu predykcyjnego.

Pamiętaj, aby zapoznać się z [najlepszymi rozwiązaniami](how-to-use-best-practices-for-search.md) dotyczącymi dokumentacji wyszukiwania.

### <a name="find-place-from-text"></a>Znajdź miejsce z tekstu

Aby wyszukać punkty zainteresowania według nazwy lub adresu, można użyć interfejsów API [wyszukiwania Azure Maps punkt POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) i [wyszukiwania rozmytego](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) .

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps |
|---------------------------|-------------------------------------|
| `fields`                  | *Nie dotyczy*                               |
| `input`                   | `query`                             |
| `inputtype`               | *Nie dotyczy*                               |
| `key`                     | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md) dokumentacji. |
| `language`                | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .  |
| `locationbias`            | `lat`, `lon` i `radius`<br/>`topLeft` i `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Wyszukiwanie w pobliżu

Bliskie punkty zainteresowań można pobrać w Azure Maps przy użyciu interfejsu API [wyszukiwania w pobliżu](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) .

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

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

Azure Maps może służyć do obliczania tras i wskazówek. Azure Maps ma wiele z tych samych funkcji co usługa routingu Google Maps, na przykład:

- czasy przybycia i wyjazdu.
- trasy ruchu w czasie rzeczywistym i predykcyjne.
- różne tryby transportu; kierowanie, idące i dwucykliczne.

> [!NOTE]
> Azure Maps wymaga współrzędnych wszystkich waypoints. Adresy muszą mieć najpierw kod geokodowania.

Usługa routingu Azure Maps udostępnia następujące interfejsy API do obliczania tras:

- [**Obliczanie trasy**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Obliczanie trasy i natychmiastowe przetworzenie żądania. Ten interfejs API obsługuje zarówno żądania GET, jak i POST. Żądania POST są zalecane w przypadku określenia dużej liczby waypoints lub użycia wielu opcji trasy, aby zapewnić, że żądanie adresu URL nie stanie się zbyt długie i powoduje problemy.
- [**Trasa wsadowa**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): Utwórz żądanie zawierające maksymalnie 1 000 żądanie trasy i przetworzy je w danym okresie czasu. Wszystkie dane zostaną przetworzone równolegle na serwerze i po ukończeniu można pobrać pełny zestaw wyników.
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
| `region`                       | Nie *dotyczy — jest* to funkcja powiązana z geokodowaniem. Użyj parametru *countrySet* w przypadku korzystania z interfejsu API geokodowania Azure Maps.  |
| `traffic_model`               | Nie *dotyczy — można* określić tylko wtedy, gdy dane o ruchu mają być używane z parametrem *ruchu* . |
| `transit_mode`                | Zobacz [dokumentację usług mobilności](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Zobacz [dokumentację usług mobilności](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | Nie *dotyczy — Azure Maps* używa tylko systemu metryk.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Domyślnie interfejs API trasy Azure Maps zwraca tylko podsumowanie (odległość i czas) i współrzędne ścieżki trasy. Użyj parametru `instructionsType`, aby pobrać instrukcje włączania i wyłączania. Za pomocą parametru `routeRepresentation` można odfiltrować podsumowanie i ścieżkę trasy.

Interfejs API routingu Azure Maps ma wiele dodatkowych funkcji dostępnych w usłudze Google Maps, które mogą być przydatne do integracji podczas migracji aplikacji:

- Obsługa typu trasy: najkrótszy, najszybszy, TRILLING i większość wydajnych paliw.
- Obsługa dodatkowych trybów podróży: Magistrala, motocykl, taksówka, ciężarówka i Van.
- Obsługa 150 waypoints.
- Obliczanie wielu czasów podróży w jednym żądaniu; ruch historyczny, ruch na żywo, brak ruchu.
- Unikaj dodatkowych typów dróg: Carpool dróg, unpaved dróg, już używanych dróg.
- Określ obszary niestandardowe, które mają być stosowane.
- Ogranicz podniesienie uprawnień w ramach tej trasy, do której może się odistnieć.
- Routing oparty na specyfikacji aparatu. Oblicz trasy dla spalania lub pojazdów elektrycznych w oparciu o pozostałe wymagania dotyczące paliwa/opłaty i aparatu.
- Obsługa parametrów trasy pojazdu komercyjnego; wymiary pojazdu, waga, liczba axels i typ ładunku.
- Określ maksymalną prędkość pojazdu.

Oprócz tego usługa Route w Azure Maps obsługuje także [Obliczanie zakresów routingu](https://docs.microsoft.com/rest/api/maps/route/getrouterange), znanych również jako izochron, które generują Wielokąt obejmujący obszar, do którego można podróżować w dowolnym kierunku od punktu początkowego w określonym czasie lub w ilości paliwa/opłaty.

## <a name="retrieve-a-map-image"></a>Pobieranie obrazu mapy

Azure Maps udostępnia interfejs API do renderowania obrazów mapy statycznej z przełożonymi danymi. Interfejs API [renderowania obrazu mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) Azure Maps jest porównywalny z interfejsem API mapy statycznej w usłudze Google Maps.

> [!NOTE]
> Azure Maps wymaga, aby wszystkie lokalizacje w centrum, znaczniki i ścieżki były współrzędnych w formacie "Długość geograficzna", podczas gdy usługi Google Maps używają formatu "Szerokość geograficzna". Adresy muszą mieć najpierw kod geokodowania.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

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
> Azure Maps używa systemu kafelków z kafelkami, które są dwa razy większym rozmiarem kafelków mapy używanych w usłudze mapy Google. W takiej postaci wartość poziomu powiększenia w Azure Maps będzie wyglądać o jeden poziom powiększenia bliżej Azure Maps w porównaniu do usługi Google Maps. Aby wyrównać tę wartość, Obniż poziom powiększenia w żądaniach, które są migrowane przez jeden.

Aby uzyskać więcej informacji, zobacz [Przewodnik dotyczący korzystania z interfejsu API renderowania obrazu mapy](how-to-render-custom-data.md).

Oprócz możliwości generowania obrazu mapy statycznej, usługa renderowania Azure Maps renderuje również możliwość bezpośredniego dostępu do kafelków mapy w formacie rastrowym (PNG) i wektorowym:

- [**Kafelek mapy**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): pobieranie kafelków rastrowych (PNG) i wektorów dla map bazowych (dróg, granic, tła).
- [**Kafelek mapy zdjęć**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): pobieranie obrazów anten i satelity.

> [!TIP]
> Wiele aplikacji usługi Google Maps, które przełączają się z interaktywnego środowiska mapy do obrazów mapy statycznej kilka lat temu jako metody oszczędności kosztów. W Azure Maps często wiele tańsze jest użycie interaktywnej kontrolki mapy w zestawie SDK sieci Web, ponieważ opłaty są naliczane na podstawie obciążeń kafelków mapy. Kafelki mapy w Azure Maps są duże i często trwają tylko kilka razy, aby ponownie utworzyć ten sam widok mapy jako mapę statyczną, a kafelki mapy są buforowane automatycznie przez przeglądarkę. W związku z tym interaktywna kontrolka mapy często generuje część transakcji tylko podczas odtwarzania widoku mapy statycznej. Kadrowanie i powiększanie spowoduje załadowanie kolejnych kafelków, ale w razie potrzeby w kontrolce mapy można wyłączyć to zachowanie. Interaktywna kontrolka mapy udostępnia również wiele opcji wizualizacji niż usługi map statycznych.

### <a name="marker-url-parameter-format-comparison"></a>Porównanie formatu parametru adresu URL znacznika

**Wcześniej: Google Maps**

Znaczniki mapy Google można dodać do obrazu mapy statycznej przy użyciu `markers` parametru w adresie URL. `markers` parametr przyjmuje styl i listę lokalizacji, które mają być renderowane na mapie z tym stylem, jak pokazano poniżej:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Dodatkowe style mogą być używane przez dodanie dodatkowych parametrów `markers` do adresu URL o innym stylu i zestawie lokalizacji.

Lokalizacje znaczników są określane przy użyciu formatu "szerokości geograficznej".

Style znaczników w usłudze Google Maps są dodawane z formatem `optionName:value`, z wieloma stylami oddzielonymi przez potok (\|), takimi jak "optionName1: wartość1\|optionName2: wartość2". Zwróć uwagę, że nazwy i wartości opcji są oddzielone dwukropkiem (:). Następujące nazwy opcji stylu mogą służyć do znaczników stylów w usłudze Google Maps:

- `color` — kolor ikony znacznika domyślnego. Może to być 24-bitowy kolor szesnastkowy (`0xrrggbb`) lub jedna z następujących wartości: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` — pojedynczy znak alfanumeryczny pisany wielkimi literami, który ma być wyświetlany na górze ikony.
- `size` — rozmiar znacznika. Może być `tiny`, `mid`lub `small`.

Niestandardowych ikon można także używać w usłudze mapy Google przy użyciu następujących nazw opcji stylu:

- `anchor` — określa sposób wyrównania obrazu ikony do współrzędnych. Może to być wartość pikseli (x, y) lub jedna z następujących wartości: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`lub `bottomright`.
- `icon` — adres URL wskazujący obraz ikony.

Na przykład w usłudze Google Maps do mapy można dodać czerwony znacznik o rozmiarze średnim (Długość geograficzna: 110, Szerokość geograficzna: 45) przy użyciu następującego parametru adresu URL:

```
&markers=color:red|size:mid|45,-110
```

<center>

znacznik ![Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Po: Azure Maps**

W Azure Maps znacznikach można również dodać do obrazu mapy statycznej, określając parametr `pins` w adresie URL. Podobnie jak w przypadku usługi Google Maps, styl i Lista lokalizacji można określić w tym parametrze, a parametr `pins` można określić wiele razy, aby obsługiwać znaczniki z różnymi stylami.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Dodatkowe style mogą być używane przez dodanie dodatkowych parametrów `pins` do adresu URL o innym stylu i zestawie lokalizacji.

Gdy powróci do lokalizacji przypinania, Azure Maps wymaga, aby współrzędne były w formacie "szerokości geograficznej", podczas gdy w usłudze Google Maps jest stosowany format "Latitude, Długość geograficzna". Należy również zauważyć, że w Azure Maps występuje spacja, a nie rozdzielona przecinkami i szerokością geograficzną.

Wartość `iconType` określa typ kodu PIN do utworzenia i może mieć następujące wartości:

- `default` — ikona domyślnego numeru PIN.
- `none` — nie jest wyświetlana ikona, tylko etykiety będą renderowane.
- `custom` — określa, że ma zostać użyta ikona niestandardowa. Adres URL wskazujący ikonę obrazu można dodać na końcu parametru `pins` po określeniu informacji o lokalizacji numeru PIN.
- `{udid}` — unikatowy identyfikator danych (UDID) dla ikony przechowywanej na platformie Azure Maps magazynu danych.

Style kodu PIN w Azure Maps są dodawane z `optionNameValue`formatu, z wieloma stylami oddzielonymi znakami potoku (\|), takimi jak ten `iconType|optionName1Value1|optionName2Value2`. Zwróć uwagę na to, że nazwy i wartości opcji nie są rozdzielone. Do znaczników stylów w Azure Maps można używać następujących nazw opcji stylu:

- `al` — określa nieprzezroczystość (alfa) znacznika. Może być liczbą z przedziału od 0 do 1.
- `an` — określa zakotwiczenie numeru PIN. Wartości x i y pikseli określone w formacie "x y".
- `co` — kolor kodu PIN. Musi być 24-bitowym kolorem szesnastkowym: `000000` do `FFFFFF`.
- `la` — określa zakotwiczenie etykiety. Wartości x i y pikseli określone w formacie "x y".
- `lc` — kolor etykiety. Musi być kolorem 24-lub szesnastkowym: `000000`, aby `FFFFFF`.
- `ls` — rozmiar etykiety (w pikselach). Może być liczbą większą niż 0.
- `ro` — wartość w stopniach, aby obrócić ikonę. Może być liczbą z przedziału od-360 do 360.
- `sc` — wartość skali dla ikony pinezki. Może być liczbą większą niż 0.

Wartości etykiet są określane dla każdej lokalizacji kodu PIN, a nie mają wartości pojedynczej etykiety, która ma zastosowanie do wszystkich znaczników na liście lokalizacji. Wartość etykiety może być ciągiem zawierającym wiele znaków i być opakowana pojedynczymi cudzysłowami, aby upewnić się, że nie zostanie ona pomylona jako wartość stylu lub lokalizacji.

Na przykład w Azure Maps, dodając czerwoną (`FF0000`) ikonę domyślną z etykietą "obszar etykietka" umieszczoną poniżej (15 50), na współrzędnej (Długość geograficzna:-122,349300, Szerokość geograficzna: 47,620180) można wykonać przy użyciu następującego parametru adresu URL:

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

![Azure Maps wielu znaczników](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Porównanie formatu parametru adresu URL

**Wcześniej: Google Maps**

W przypadku wierszy i wielokątów usługi Mapy Google można dodać do obrazu mapy statycznej przy użyciu `path` parametru w adresie URL. `path` parametr przyjmuje styl i listę lokalizacji, które mają być renderowane na mapie z tym stylem, jak pokazano poniżej:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Dodatkowe style mogą być używane przez dodanie dodatkowych parametrów `path` do adresu URL o innym stylu i zestawie lokalizacji.

Lokalizacje ścieżki w usłudze mapy Google są określane przy użyciu formatu `latitude1,longitude1|latitude2,longitude2|…`. Ścieżki mogą być kodowane lub zawierać adresy dla punktów.

Style ścieżki w usłudze mapy Google są dodawane z formatem `optionName:value`, z wieloma stylami oddzielonymi znakami potoku (\|), takimi jak ten `optionName1:value1|optionName2:value2`. Zwróć uwagę, że nazwy i wartości opcji są oddzielone dwukropkiem (:). Następujące nazwy opcji stylu mogą służyć do nadawania stylu ścieżkom w usłudze Google Maps:

- `color` — kolor ścieżki lub konturu wielokąta. Może to być 24-bitowy kolor szesnastkowy (`0xrrggbb`), 32-bitowy kolor szesnastkowy (`0xrrggbbbaa`) lub jedna z następujących wartości: czarny, brązowy, zielony, purpurowy, żółty, niebieski, szary, pomarańczowy, czerwony, biały.
- `fillColor` — kolor wypełnienia obszaru ścieżki przy użyciu (Wielokąt). Może to być 24-bitowy kolor szesnastkowy (`0xrrggbb`), 32-bitowy kolor szesnastkowy (`0xrrggbbbaa`) lub jedna z następujących wartości: czarny, brązowy, zielony, purpurowy, żółty, niebieski, szary, pomarańczowy, czerwony, biały.
- `geodesic` — wskazuje, czy ścieżka powinna być linią, która jest zgodna z krzywizną ziemi.
- `weight` — grubość linii ścieżki w pikselach.

Na przykład w usłudze Google Maps czerwona linia z nieprzezroczystością 50% i grubością 4 pikseli można dodać do mapy między współrzędnymi (Długość geograficzna: 110, Szerokość geograficzna: 45 i Długość geograficzna:-100, Latitude: 50) za pomocą następującego parametru adresu URL:

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![linię łamaną w usłudze Google Maps](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Po: Azure Maps**

W Azure Maps linie i wielokąty można również dodać do obrazu mapy statycznej, określając parametr `path` w adresie URL. Podobnie jak w przypadku usługi Google Maps, styl i Lista lokalizacji można określić w tym parametrze, a parametr `path` można określić wiele razy, aby renderować wiele okręgów, linii i wielokątów o różnych stylach.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Gdy dojdzie do lokalizacji ścieżki, Azure Maps wymaga, aby współrzędne były w formacie "Długość geograficzna", podczas gdy w usłudze Google Maps jest stosowany format "Szerokość geograficzna". Należy również zauważyć, że w Azure Maps występuje spacja, a nie rozdzielona przecinkami i szerokością geograficzną. Azure Maps nie obsługuje zakodowanych ścieżek ani adresów dla punktów. Większe zestawy danych można przekazać jako plik GEOJSON do interfejsu API magazynu danych Azure Maps, jak opisano [tutaj](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Style ścieżki w Azure Maps są dodawane z `optionNameValue`formatu, z wieloma stylami oddzielonymi znakami potoku (\|), takimi jak ten `optionName1Value1|optionName2Value2`. Zwróć uwagę na to, że nazwy i wartości opcji nie są rozdzielone. Następujące nazwy opcji stylu mogą służyć do nadawania stylu ścieżkom w Azure Maps:

- `fa` — nieprzezroczystość koloru wypełnienia (alfa) użyta podczas renderowania wielokątów. Może być liczbą z przedziału od 0 do 1.
- `fc` — kolor wypełnienia używany do renderowania obszaru wielokąta.
- `la` — nieprzezroczystość koloru linii (alfa) używana podczas renderowania linii i konspektu wielokątów. Może być liczbą z przedziału od 0 do 1.
- `lc` — kolor linii używany do renderowania linii i konspektu wielokątów.
- `lw` — szerokość linii w pikselach.
- `ra` — określa promień okręgów w metrach.

Na przykład w Azure Maps, czerwona linia z nieprzezroczystością 50% i grubością 4 pikseli można dodać do mapy między współrzędnymi (Długość geograficzna: 110, Szerokość geograficzna: 45 i Długość geograficzna:-100, Szerokość geograficzna: 50) przy użyciu następującego parametru adresu URL:

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps łamaną](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Obliczanie macierzy odległości

Azure Maps udostępnia interfejs API do obliczania czasów podróży i odległości między zestawem lokalizacji jako matrycą odległości. Interfejs API macierzy Azure Mapsej odległości jest porównywalny z interfejsem API macierzy odległości w usłudze Google Maps;

- [**Macierz trasy**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): asynchronicznie oblicza czasy podróży i odległości dla zestawu źródeł i miejsc docelowych. Obsługiwane są maksymalnie 700 komórki na żądanie (liczba źródeł pomnożona przez liczbę miejsc docelowych). Mając to na uwadze, przykłady możliwych wymiarów macierzy to: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Żądanie interfejsu API macierzy odległości można wykonać tylko przy użyciu żądania POST z informacjami o lokalizacji źródłowej i docelowej w treści żądania. Ponadto Azure Maps wymaga współrzędnych wszystkich źródeł i miejsc docelowych. Adresy muszą mieć najpierw kod geokodowania.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

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
| `region`                       | Nie *dotyczy — jest* to funkcja powiązana z geokodowaniem. Użyj parametru `countrySet`, gdy korzystasz z interfejsu API Azure Maps geokodowania. |
| `traffic_model`                | Nie *dotyczy — można* określić tylko wtedy, gdy dane o ruchu mają być używane z parametrem `traffic`. |
| `transit_mode`                 | *Macierze na odległość* nie są obecnie obsługiwane.  |
| `transit_routing_preference`   | *Macierze na odległość* nie są obecnie obsługiwane.  |
| `units`                        | Nie *dotyczy — Azure Maps* używa tylko systemu metryk. |

> [!TIP]
> Wszystkie zaawansowane opcje routingu dostępne w interfejsie API routingu Azure Maps (Routing ciężarówki, Specyfikacja aparatu, unikanie...) są obsługiwane w interfejsie API macierzy na platformie Azure Maps.

## <a name="get-a-time-zone"></a>Pobierz strefę czasową

Azure Maps udostępnia interfejs API do pobierania strefy czasowej, w której znajduje się Współrzędna. Interfejs API strefy czasowej Azure Maps jest porównywalny z interfejsem API strefy czasowej w usłudze Google Maps:

- [**Strefa czasowa według współrzędnych**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Określ współrzędną i uzyskaj szczegóły dla strefy czasowej, w której się znajduje.

W poniższej tabeli odwołuje się do parametrów interfejsu API usługi Google Maps przy użyciu porównywalnych parametrów interfejsu API w Azure Maps.

| Parametr interfejsu API usługi Google Maps | Porównywalny parametr interfejsu API Azure Maps   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` — Zobacz też [uwierzytelnianie za pomocą Azure Maps](azure-maps-authentication.md) dokumentacji.       |
| `language`                  | `language` — Zobacz dokumentację [obsługiwanych języków](supported-languages.md) .    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Oprócz tego platforma Azure Maps udostępnia również kilka dodatkowych interfejsów API strefy czasowej, które ułatwiają konwersje z nazwami stref czasowych i identyfikatorami:

- [**Strefa czasowa według identyfikatora**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): zwraca bieżące, historyczne i przyszłe informacje o strefie czasowej dla określonego identyfikatora strefy czasowej organizacji IANA.
- [**Wyliczenie strefy czasowej Iana**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): zwraca pełną listę identyfikatorów stref czasowych organizacji IANA. Aktualizacje usługi IANA są uwzględniane w systemie w ciągu jednego dnia.
- [**Okna wyliczania strefy czasowej**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): zwraca pełną listę identyfikatorów strefy czasowej systemu Windows.
- [**Wersja programu Iana strefy czasowej**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): zwraca bieżący numer wersji programu Iana używany przez Azure Maps.
- [**System Windows strefy czasowej dla organizacji Iana**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): zwraca odpowiedni identyfikator Iana, uwzględniając prawidłowy identyfikator strefy czasowej systemu Windows. Dla pojedynczego identyfikatora systemu Windows można zwrócić wiele identyfikatorów IANA.

## <a name="client-libraries"></a>Biblioteki klienckie

Azure Maps udostępnia biblioteki klienckie dla następujących języków programowania:

- JavaScript, TypeScript, Node. js — [dokumentacja](how-to-use-services-module.md) \| [pakiet npm](https://www.npmjs.com/package/azure-maps-rest)

Biblioteki klienckie Open Source dla innych języków programowania:

- .NET Standard 2,0 — [pakiet NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/) [projektu usługi GitHub](https://github.com/perfahlen/AzureMapsRestServices) \|

## <a name="additional-resources"></a>Zasoby dodatkowe

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