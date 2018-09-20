---
title: Słownik usługi Azure Maps | Dokumentacja firmy Microsoft
description: Słownik powszechnie używanych terminów związanych z usługi Azure Maps, usług Location Based Services i GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: resource
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: d220fea64f860ebe5b660f7ebe5ad7db7aec4534
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368757"
---
# <a name="glossary"></a>Słownik

Oto lista popularnych wyrazów, używany przy użyciu usługi Azure Maps.

## <a name="a"></a>A

<a name="address-validation"></a> **Sprawdzanie poprawności adresu**: proces sprawdzania, czy istnienie adresu.

<a name="advanced-routing"></a> **Zaawansowany routing**: kolekcja usług, które wykonują operacje zaawansowane przy użyciu routingu danych podróży, takich jak obliczanie dostępne zakresy (isochrones) odległości macierzy i kierowanie żądań wsadowych.

<a name="aerial-imagery"></a> **Zdjęcia lotnicze**: zobacz [Satellite obrazach](#satellite-imagery). 

<a name="along-a-route-search"></a> **Wzdłuż wyszukiwania trasy**: przestrzenne kwerendę, która szuka danych, który znajduje się w określonym przekierowania, czasu lub odległości od ścieżka trasy.

<a name="altitude"></a> **Wysokość**: wysokości lub pionowy podniesienie poziomu punktu nawierzchni odwołania. Wysokość pomiarów opierają się na danym odniesienia, takie jak średnia poziomem morza. Zobacz też podniesienia uprawnień.

<a name="ambiguous"></a> **Niejednoznaczny**: stan niepewność co do klasyfikacji danych, który istnieje, gdy obiekt odpowiednio przypisane dwa lub więcej wartości danego atrybutu. Na przykład, gdy geokodowania "CA" dwóch Niejednoznaczne wyniki są zwracane; "Kanada" i "Kalifornia" jako "CA" to kod kraju i stanu dla każdego odpowiednio. 

<a name="annotation"></a> **Adnotacja**: tekst lub grafiki wyświetlanych na mapie, do dostarczania informacji użytkownika. Adnotacja może zidentyfikować opisują jednostki mapowania określonych, podawanie ogólnych informacji o obszar na mapie lub podać informacje dotyczące mapowania, sam.

<a name="antimeridian"></a> **Antimeridian**: znany także jako 180<sup>th</sup> południków to punkt, gdy spełniają stopnie-180 oraz 180 stopni długości geograficznej. Jest to przeciwne południków prime na całym świecie.

<a name="application-programming-interface-api"></a> **Interfejs programowania aplikacji (API)**: specyfikacja, która umożliwia deweloperom tworzenie aplikacji.

<a name="api-key"></a> **Klucz interfejsu API**: Zobacz klucza usługi mapy platformy Azure.

<a name="area-of-interest-aoi"></a> **Obszar zainteresowań (AOI)**: zakres używany do definiowania obszar koncentracji uwagi dla mapy albo bazy danych środowiska produkcyjnego.

<a name="asset-tracking"></a> **Śledzenie zasobów**: proces śledzenie lokalizacji elementu zawartości, takich jak osoby, pojazdu lub innego obiektu.

<a name="asynchronous-request"></a> **Żądania asynchronicznego**: żądanie HTTP, które otwiera połączenie i zgłasza żądanie do serwera, który zwraca identyfikator żądania asynchronicznego, a następnie zamknięcie połączenia. Serwer kontynuuje przetwarzanie żądania i sprawdzić stan przy użyciu identyfikatora użytkownika. Jeśli żądanie jest Zakończono przetwarzanie, użytkownik może następnie pobierania odpowiedzi. Ten typ żądania jest zwykle używany dla długotrwałych uruchomionych procesów.

<a name="autocomplete"></a> **Autouzupełnianie**: funkcja w aplikacji przewiduje pozostałą część wyrazu wpisywany przez użytkownika. 

<a name="autosuggest"></a> **Automatyczne sugerowanie**: funkcja w aplikacji przewiduje logiczne możliwości co wpisywany przez użytkownika.

<a name="azure-location-based-services-lbs"></a> **Usługa Azure lokalizacji Based Services (LBS)**: poprzednia nazwa usługi Azure Maps podczas w wersji zapoznawczej.

<a name="azure-maps-key"></a> **Klucz usługi Azure Maps**: klucz map Azure jest unikatowy ciąg, który jest używany do uwierzytelniania aplikacji usługi Azure Maps przez użytkownika lub żądania obsługi. 

## <a name="b"></a>B

<a name="base-map"></a> **Mapą podstawową**: część aplikacji mapy, która wyświetla tła informacje takie jak dróg, charakterystycznych elementów krajobrazu i granice polityczne.

<a name="batch-request"></a> **Żądanie wsadowe**: proces łączenia wielu żądań w pojedynczym żądaniu.

<a name="bearing"></a> **Mając**: kierunek poziomy punktu w odniesieniu do innego punktu. To wymaganie jest wyrażone jako wartość kąta względem Północna z 0-stopni 360-stopni w prawo. 

<a name="boundary"></a> **Granica**: wiersza lub wielokąta oddzielenie sąsiadujących polityczne jednostki, takie jak kraje, okręgi i właściwości. Granica jest wiersz, który może być lub może nie być zgodne z fizycznego funkcje, takie jak rzek górach oraz ścian.

<a name="bounds"></a> **Granice**: zobacz [pole Bounding](#bounding-box).

<a name="bounding-box"></a> **Pole ograniczenia**: zestaw współrzędnych używana do reprezentowania prostokątny obszar na mapie. 

## <a name="c"></a>C

<a name="cadastre"></a> **Katastrem**: rekord zarejestrowanych ziemi i właściwości. Zobacz też [działka](#parcel).

<a name="camera"></a> **Aparat fotograficzny**: W kontekście kontrolkę mapy interakcyjnej aparatu definiuje mapy pole widzenia. Okienko ekranu kamery jest określana na podstawie kilku parametrów mapy; Centrum, poziomu powiększenia pomysłu, żadnego wpływu. 

<a name="centroid"></a> **Centroida — oś**: geometrycznych środek funkcji. Centroida — oś wiersza będzie punktu środkowego podczas centroida — oś wielokąta byłoby środek obszaru.

<a name="choropleth-map"></a> **Mapy na mapie Choropleth**: tematycznych mapy, w którym obszary są zacieniowane proporcjonalnie do pomiaru zmiennej statystyczne są wyświetlane na mapie. Na przykład Pokoloruj granicy każdego stanu USA na podstawie jego względne populacji dla wszystkich innych Państw.

<a name="concave-hull"></a> **Wklęsłych kadłuba**: kształt, który reprezentuje możliwe geometrię wklęsłych, która zawiera wszystkie kształty w określonym zestawie danych. Wygenerowany kształt jest podobny do opakowywanie dane przy użyciu zawinięcie i następnie ogrzewania go, powodując duży rozciąga się między punktami do cave kierunku innych punktów danych.

<a name="consumption-model"></a> **Model użycia**: informacje, które określa szybkość jaką pojazdu zużywa paliwa lub energii elektrycznej. Zobacz też [dokumentacji modelu zużycia](consumption-model.md).

<a name="control"></a> **Kontrolka**: składnikiem niezależnym lub wielokrotnego użytku, składający się z graficznego interfejsu użytkownika, który definiuje zestaw zachowań dla interfejsu. Na przykład kontrolkę mapy zazwyczaj jest to część interfejsu użytkownika, który ładuje mapę interaktywną.

<a name="convex-hull"></a> **Wypukłe kadłuba**: wypukłe kadłuba jest kształt, który reprezentuje minimalne geometrię wypukłe, która zawiera wszystkie kształty w określonym zestawie danych. Wygenerowany kształt jest podobny do zawijania poza pasmem elastyczna baza danych w całym zestawie danych.

<a name="coordinate"></a> **Koordynowanie**: składa się z wartości długości i szerokości geograficznej, używany do reprezentowania lokalizacji na mapie.

<a name="coordinate-system"></a> **System współrzędnych**: punkt odniesienia używany do definiowania położenie punktów w przestrzeni w dwóch lub trzech wymiarach.

<a name="country-code"></a> **Numer kierunkowy kraju**: Unikatowy identyfikator dla kraju oparty na standardzie ISO. ISO2 to dwuznakowy kod kraju (na przykład US), który reprezentuje ISO3 3 znakowy kod (np. USA).

<a name="country-subdivision"></a> **Podpodział kraju**: podział pierwszego poziomu, kraj, powszechnie znane jako województwo.

<a name="country-secondary-subdivision"></a> **Podpodział dodatkowej kraju**: podział drugiego poziomu, kraj, powszechnie znane jako powiat.

<a name="country-tertiary-subdivision"></a> **Podpodział trzeciorzędny kraju**: podział trzeciego poziomu w kraju, zazwyczaj o nazwie obszaru, takiego jak tyłu.

<a name="cross-street"></a> **Ulica między**: punkt przecięcia się co najmniej dwóch Streets (ulice).

<a name="cylindrical-projection"></a> **Cylindryczne**: projekcji, który przekształca punktów z sferoidalny lub sphere na cylinder tangens lub secans. Następnie cylindra określonego przedziału od góry do dołu i jako spłaszczone do płaszczyzny.

## <a name="d"></a>D

<a name="datum"></a> **Datum**: do specyfikacji systemu pomiarowego, system współrzędnych umieszcza na powierzchni (poziomy datum) lub wysokości powyżej lub poniżej powierzchni (datum pionowych).

<a name="dbf-file"></a> **Plik DBF;**: format pliku bazy danych, który jest używany w połączeniu z pliki kształtów (SHP).

<a name="degree-minutes-seconds-dms"></a> **Stopień minut sekund (DMS)**: jednostka miary do opisywania szerokości i długości geograficznej. Stopień to 1/360<sup>th</sup> koła. Stopień jest podzielona na 60 minut, a minuta jest podzielony na 60 sekund.

<a name="delaunay-triangulation"></a> **Triangulacja Delaunay**: techniki tworzenia siatki trójkąty ciągłe, nienakładające się z zestawu danych punktów. Okrąg circumscribing każdego trójkąta nie zawiera punktów z zestawu danych w jego wewnętrznych.

<a name="demographics"></a> **Dane demograficzne**: statystyczne charakterystyki (np. wiek, szybkość urodzenia i przychody) populacji ludzi.

<a name="destination"></a> **Miejsce docelowe**: punkt końcowy lub lokalizacji, w którym jest ktoś podróżującym na.

<a name="digital-elevation-model-dem"></a> **Cyfrowy podniesienia uprawnień modelu (DEM)**: zestaw wartości podniesienia uprawnień związanych z powierzchni, przechwycone w regularnych odstępach czasu przy użyciu wspólnej podstawy wymiarowej przestrzeni. DEMs są zwykle używane do reprezentowania zwolnienia terenu.

<a name="dijkstra's-algorithm"></a> **Algorytm firmy Dijkstra**: algorytm, który sprawdza łączność sieci, aby najkrótsze ścieżki między dwoma punktami.

<a name="distance-matrix"></a> **Macierz odległości**: macierzy, która zawiera informacje dotyczące czasu i odległość podróży między zestaw źródeł i miejsc docelowych. 

## <a name="e"></a>E

<a name="elevation"></a> **Podniesienie poziomu**: pionową odległość punktu lub obiekt powyżej lub poniżej powierzchni odwołanie lub datum (zazwyczaj mean Morza poziom). Podniesienie poziomu zazwyczaj odnosi się do wysokość lądzie.

<a name="envelope"></a> **Koperta**: zobacz [pole Bounding](#bounding-box).

<a name="extended-postal-code"></a> **Rozszerzony kod pocztowy**: kod pocztowy, który może zawierać dodatkowe informacje. Na przykład w Stanach Zjednoczonych, kody pocztowe mają pięć cyfr, ale rozszerzonego kodu pocztowego, znane jako zip + 4, będzie zawierać cztery dodatkowe cyfry. Te dodatkowe cyfry są używane do identyfikowania geograficzne segmentu, w obszarze 5 cyfrowy dostarczania, takich jak blok miasta, grupa apartamentach lub skrytki, która pomaga w wydajny poczty, sortowanie i dostarczanie.

<a name="extent"></a> **Zakres**: zobacz [pole Bounding](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a> **Uwierzytelnianie federacyjne**: metody uwierzytelniania, która umożliwia mechanizmu pojedynczego logowania lub uwierzytelniania można stosować w przypadku wielu aplikacji internetowych i mobilnych. 

<a name="feature"></a> **Funkcja**: obiekt, który łączy geometrii z informacjami o dodatkowe metadane. 

<a name="feature-collection"></a> **Funkcji zbierania**: kolekcja obiektów funkcji.

<a name="find-along-route"></a> **Znajdź wzdłuż trasy**: przestrzenne kwerendę, która szuka danych, który znajduje się w określonym przekierowania, czasu lub odległości od ścieżka trasy.

<a name="find-nearby"></a> **Znajdź w pobliżu**: przestrzenne kwerendę, która wyszukuje Stała odległość liniową (tak jak w linii prostej) z punktu.

<a name="fleet-management"></a> **Grupy floty zarządzania**: Zarządzanie pojazdów użytkowych, takich jak samochody, ciężarówek, dostarczany i płaszczyzny. Zarządzanie flotą mogą obejmować szeroką gamę funkcji, takich jak finansowania pojazdu, obsługi, telematyka (śledzenie i Diagnostyka) także sterownika, szybkość, paliwa i bezpieczeństwo zarządzania. Zarządzanie flotą to proces używany przez firmy, które polegają na transport w działalności firmy, aby zminimalizować ryzyko i zmniejszenie swoje ogólne koszty transportu i personelu, przy jednoczesnym zapewnieniu zgodności z przepisami dla instytucji rządowych.

<a name="free-flow-speed"></a> **Bezpłatne szybkości przepływu**: oczekiwano w warunkach idealne szybkości swobodnego przepływu. Zwykle limit szybkości.

<a name="free-form-address"></a> **Adres w dowolnej postaci**: pełny adres, który jest reprezentowany jako pojedynczy wiersz tekstu.

<a name="fuzzy-search"></a> **Wyszukiwanie rozmyte**: wyszukiwania, który zajmuje się w dowolnym ciągiem tekstu, który może być adresem lub punktu orientacyjnego. 

## <a name="g"></a>G

<a name="geocode"></a> **Geocode**: adres lub lokalizacji, w której został przekształcony w współrzędnych, który może służyć do wyświetlania tej lokalizacji na mapie. 

<a name="geocoding"></a> **Geokodowanie**: również znane jako geokodowania do przodu, to proces konwertowania adres lokalizacji danych na współrzędnych. 

<a name="geodesic-path"></a> **Ścieżka geodezyjnych**: najkrótsze ścieżki między dwoma punktami krzywych powierzchni. Podczas renderowania na usługi Azure Maps ta ścieżka jest wyświetlana jako krzywej z powodu projekcji Mercator.

<a name="geofence"></a> **Wirtualnego ogrodzenia**: element zdefiniowany region geograficzny, która może służyć do wyzwolenia zdarzenia, gdy urządzenie wprowadza, czy istnieje regionu.

<a name="geojson"></a> **GeoJSON**: jest wspólny format plików opartych na formacie JSON używane do przechowywania danych geograficznych wektora, takich jak punkty, wierszy i wielokątów. **Uwaga**: korzysta z usługi Azure Maps rozszerzoną wersję GeoJSON jako [opisane tutaj](extend-geojson.md).

<a name="geometry"></a> **Geometria**: reprezentuje obiekt przestrzennych, takie jak punkt, linii lub wielokąta.

<a name="geometrycollection"></a> **GeometryCollection**: kolekcja obiektów geometrii.

<a name="geopol"></a> **GeoPol**: odwołuje się do kwestie geopolityczne poufnych danych, takich jak sporne obramowanie i nazw.

<a name="georeference"></a> **Geograficznego określania**: proces dostosowywania dane geograficzne lub obrazów do znanych współrzędnych. Ten proces może obejmować zmiany, obracanie, skalowania lub pochylanie danych.

<a name="georss"></a> **GeoRSS**: rozszerzenie XML dotyczący dodawania danych przestrzennych źródeł danych RSS.

<a name="gis"></a> **GIS**: akronim "System informacji geograficznej". Wspólny termin używany do opisania branży mapowania.

<a name="gml"></a> **GML**: również znane jako Geography Markup Language. Rozszerzenie pliku XML do przechowywania danych przestrzennych.

<a name="gps"></a> **GPS**: znany także jako globalnego systemu pozycjonowania, to system satelity, używany do określenia pozycji urządzeń, na ziemi. Orbiting satelity przesyłać sygnałów, które umożliwiają odbiornik GPS dowolne miejsce na ziemi. obliczyć lokalizacji za pośrednictwem trilateration.

<a name="gpx"></a> **GPX**: również znane jako format wymiany GPS, jest często tworzone na podstawie urządzenia GPS formacie pliku XML.  

<a name="great-circle-distance"></a> **Najkrótszej odległości**: najkrótszą odległość między dwoma punktami na powierzchni kuli.

<a name="greenwich-mean-time-gmt"></a> **Czas uniwersalny Greenwich (GMT)**: godzinie południków prime, który jest uruchamiany za pośrednictwem Centrum Royal w Greenwich w Anglii.

<a name="guid"></a> **Identyfikator GUID**: Unikatowy identyfikator globalny. Ciąg używany do jednoznacznego identyfikowania interfejsu, klasy, biblioteki typów, kategorii składników lub rekordu.

## <a name="h"></a>H

<a name="haversine-formula"></a> **Formuła Haversine**: typowe równania, służące do obliczania najkrótszej — odległość między dwoma punktami na kuli.

<a name="hd-maps"></a> **HD mapy**: również znane jako wysoki mapuje definicji, składa się z o dużej wierności drogowej sieci informacje, takie jak oznaczenia lane znakowania i kierunek światła wymagane do obsługi autonomicznych.

<a name="heading"></a> **Nagłówek**: kierunek coś wskazujący lub połączonego z. Zobacz też [mając](#heading).

<a name="heatmap"></a> **Mapy cieplnej**: wizualizacji danych, w którym zakresu kolorów reprezentuje gęstość punktów w określonym obszarze. Zobacz też [tematycznych mapy](#thermatic-map).

<a name="hybrid-imagery"></a> **Obrazach hybrydowych**: satelitarne i zdjęcia lotnicze, dane i etykiety nałożony na jego podstawie.

## <a name="i"></a>I

<a name="iana"></a> **Organizacja IANA**: akronim Internet Assigned Numbers Authority. Grupa organizacji nonprofit nadzoruje globalnego przydzielanie adresów IP.

<a name="isochrone"></a> **Isochrone**: isochrone definiuje obszar, w którym osoby mogą być przesyłane w określonym czasie dla trybu transportu w dowolnym kierunku z danej lokalizacji. Zobacz też [dostępny zakres](#reachable-range).

<a name="isodistance"></a> **Isodistance**: biorąc pod uwagę lokalizację, isochrone definiuje obszar, w którym osoby mogą być przesyłane w określonej odległości dla trybu transportu w dowolnym kierunku. Zobacz też [dostępny zakres](#reachable-range).

## <a name="k"></a>tys.

<a name="kml"></a> **KML**: znany również jako język znaczników dziurką od klucza, jest wspólny format plików XML do przechowywania danych geograficznych wektor takich jak punkty, wierszy i wielokątów. 

## <a name="l"></a>L

<a name="landsat"></a> **Landsat**: Wielospektralne, orbiting ziemi satelity opracowany przez NASA, w którym zbierać obrazach ziemi, która jest używana w wielu branżach, takich jak rolnictwo, leśnictwo i map.

<a name="latitude"></a> **Szerokość geograficzna**: odległość kątowa mierzony w stopniach od równika na północ lub kierunek południowo.

<a name="level-of-detail"></a> **Poziom szczegółowości**: zobacz [poziom powiększenia](#zoom-level).

<a name="lidar"></a> **LIDAR'owych**: akronim światła wykrywania i zakresu. Wykrywanie zdalnego technika, która używa laserami, aby zmierzyć odległość do odbijające powierzchni.

<a name="linear-interpolation"></a> **Interpolacji liniowej**: oszacowanie nieznaną wartość przy użyciu odstęp między znane wartości.

<a name="linestring"></a> **LineString**: geometry, używany do reprezentowania wiersza. Nazywane również wielokąt. 

<a name="localization"></a> **Lokalizacja**: obsługa innych języków i kultur.

<a name="logistics"></a> **Logistyka**: proces przenoszenia osób, pojazdów, dostawy lub zasobów w skoordynowany sposób.

<a name="longitude"></a> **Długość geograficzna**: odległość kątowa mierzony w stopniach południków prime w kierunku wschodnie lub zachodnie.

## <a name="m"></a>M

<a name="map-tile"></a> **Kafelek mapy**: prostokątny obraz, który reprezentuje partycji kanwy mapy. Aby uzyskać więcej informacji, zobacz [poziomy powiększenia i Kafelek dokumentacja grid](zoom-levels-and-tile-grid.md).

<a name="marker"></a> **Znacznik**: znany także jako numeru pin lub Pinezka jest ikonę która reprezentuje lokalizację punkt na mapie.

<a name="mercator-projection"></a> **Projekcja mercator**: rzutowania mapy cylindrycznych, który stał się rzutowania mapy standardowe celów morskich ze względu na możliwość reprezentować linie stałej kurs, znane jako wiersze rumbu jako bezpośrednio segmentami, które oszczędzać kąty z południków. Wszystkie projekcje prostego mapy zniekształcają kształty lub rozmiary mapy w porównaniu do true układu kuli ziemskiej. Projekcji Mercator exaggerates obszarów oni daleko od równika, w taki sposób, że mniejsze obszary są wyświetlane w większych na mapie, podejście biegunów. 

<a name="multilinestring"></a> **MultiLineString**: geometry, który reprezentuje kolekcję obiektów LineString. 

<a name="multipoint"></a> **MultiPoint**: geometry, który reprezentuje kolekcję obiektów punktów.

<a name="multipolygon"></a> **MultiPolygon**: geometry, który reprezentuje kolekcję obiektów wielokąta. Na przykład aby wyświetlić granicy Hawaje, czy obramowany każdego Wyspa wielokąta i granicy Hawaje w związku z tym będzie MultiPolygon.

<a name="municipality"></a> **Gmina**: miasta lub miejscowości. 

<a name="municipality-subdivision"></a> **Gmina podpodział**: podział gmina, na przykład otoczenie lub nazwy lokalne, takie jak "Centrum".

## <a name="n"></a>Nie

<a name="navigation-bar"></a> **Pasek nawigacyjny**: zestaw formantów na mapie, używane do dostosowywania poziom powiększenia, wysokość, obrotu i przełączanie warstwy podstawowej mapy.

<a name="nearby-search"></a> **W pobliżu wyszukiwania**: przestrzenne kwerendę, która wyszukuje Stała odległość liniową (tak jak w linii prostej) z punktu.

<a name="neutral-ground-truth"></a> **Prawdę niezależny od podstaw**: mapy, który renderuje etykiety w języku regionu, czyli przedstawia liczbę i skryptów lokalnych, jeśli jest dostępny.

## <a name="o"></a>O

<a name="origin"></a> **Pochodzenie**: punkt początkowy lub lokalizacji, w którym użytkownik jest.

## <a name="p"></a>P

<a name="panning"></a> **Przesuwanie**: proces przechodzenia na mapie w dowolnym kierunku, przy zachowaniu stałej powiększenia.

<a name="parcel"></a> **Paczka**: powierzchni ziemi lub właściwość granicy.

<a name="pitch"></a> **Gęstość**: ilość pochylenie mapa ma względem pionowy, gdzie 0 zagląda dół mapy.

<a name="point"></a> **Punkt**: geometrię, która reprezentuje pojedynczy położeniu na mapie. 

<a name="points-of-interest-poi"></a> **Punktów orientacyjnych (punktów POI)**: biznesowych, charakterystycznych elementów krajobrazu czy typowe miejsca.

<a name="polygon"></a> **Wielokąt**: geometry niezawodnej, który reprezentuje obszar na mapie. 

<a name="polyline"></a> **Wielokąt**: geometry, używany do reprezentowania wiersza. Znany także jako element LineString. 

<a name="position"></a> **Pozycja**: długość geograficzna, szerokość i wysokość (x, y, z współrzędne) punktu.

<a name="post-code"></a> **Kod pocztowy**: zobacz [kod pocztowy](#postal-code).

<a name="postal-code"></a> **Kod pocztowy**: szereg litery lub cyfry lub zarówno w określonym formacie używane przez usługi pocztowe kraju zostać podzielona obszarów geograficznych stref w celu uproszczenia dostarczania poczty.

<a name="prime-meridian"></a> **Pierwsze południków**: linię długość geograficzna, który reprezentuje 0 stopni długości geograficznej. Ogólnie rzecz biorąc, wartości długości geograficznej zmniejszyć podczas podróży w kierunku zachodnim aż do 180 stopni i zwiększyć podczas poruszania się we wschodnim kierunkach-180-stopni. 

<a name="prj"></a> **PRJ**: plik tekstowy, który często dołączony plik pliku kształtu, który zawiera informacje o przewidywanych współrzędnych jest zestaw danych.

<a name="projection"></a> **Projekcja**: przewidywany współrzędnych na podstawie rzutowania mapy takie Mercator poprzecznej, Albers równa obszaru i Robinson. Te zapewniają możliwość mapy kulistego kuli ziemskiej na płaszczyznę dwuwymiarowe współrzędne formułuje projektu. Przewidywany współrzędnych są czasami określane jako mapa projekcji.

## <a name="q"></a>PYTANIA I ODPOWIEDZI

<a name="quadkey"></a> **Quadkey**: indeks 4 podstawowy adres dla kafelków w obrębie obszaru quadtree fragmentacji systemu. Aby uzyskać więcej informacji, zobacz [poziomy powiększenia i siatka kafelków](zoom-levels-and-tile-grid.md) dokumentacji, aby uzyskać więcej informacji.

<a name="quadtree"></a> **Obszaru Quadtree**: struktura danych, w którym każdy węzeł ma dokładnie cztery elementy podrzędne. System fragmentacji, używane w usługi Azure Maps używa struktury obszaru quadtree taki sposób, że jako użytkownik powiększa się o jeden poziom, każdy Kafelek mapy dzielony na cztery Kafelki podrzędnych.  Aby uzyskać więcej informacji, zobacz [poziomy powiększenia i siatka kafelków](zoom-levels-and-tile-grid.md) dokumentacji, aby uzyskać więcej informacji.

<a name="queries-per-second-qps"></a> **Zapytania na drugim (QPS)**: liczba zapytań lub żądań, które mogą być wykonane w ciągu sekundy do usługi lub platformy. 

## <a name="r"></a>R

<a name="radial-search"></a> **Wyszukiwanie promieniowego**: przestrzenne kwerendę, która wyszukuje Stała odległość liniową (tak jak w linii prostej) z punktu. 

<a name="raster-data"></a> **Dane rastrowe**: macierz komórki (lub pikselach) zorganizowanych w wiersze i kolumny (lub siatkę) gdzie każda komórka zawiera wartość reprezentujących informacje, takie jak temperatury. Firmy rastrowych obejmują cyfrowych zdjęć lotniczych obrazów z satelity, obrazów cyfrowych i zeskanowane map.

<a name="raster-layer"></a> **Warstwy rastrowej**: warstwy Kafelek, który składa się z obrazów rastrowych.

<a name="reachable-range"></a> **Zakres dostępny**: osiągalny zakresu definiuje obszar, w którym osoby mogą być przesyłane w wyznaczonym okresie lub odległości dla trybu transportu przechodzić w dowolnym kierunku z lokalizacji. Zobacz też [Isochrone](#isochrone) i [Isodistance](#isodistance).

<a name="remote-sensing"></a> **Wykrywanie zdalnego**: proces zbierania i interpretowanie danych z czujników z dużej odległości.

<a name="rest-service"></a> **Usługa REST**: akronim REST oznacza Representational Transfer stanu. Usługa REST jest oparty na adresach URL usługi sieci web korzystającej z technologii internetowych podstawowe do komunikowania się najbardziej typowe metody są żądania HTTP GET i POST. Tego rodzaju usług zwykle do mnie znacznie szybszy i mniejszy niż tradycyjne usługi oparte na protokole SOAP.

<a name="reverse-geocode"></a> **Odwróć geocode**: proces pobierania Współrzędna i określenie adresu, w którym jest reprezentuje na mapie.

<a name="reproject"></a> **Reproject**: zobacz [przekształcania](#transformation).

<a name="rest-service"></a> **Usługa REST**: akronim Representational Transfer stanu. Architektura wymianę informacji między komputerami w środowisku zdecentralizowanego, rozproszonych. REST umożliwia programom na różnych komputerach do komunikowania się niezależnie od systemu operacyjnego lub platformy, wysyłając żądanie protokołu HTTP (Hypertext Transfer) do uniwersalnego lokatora zasobów (URL) i odzyskać dane.

<a name="route"></a> **Trasy**: ścieżka między co najmniej dwóch lokalizacjach, które również mogą zawierać dodatkowe informacje, takie jak instrukcje dotyczące waypoints wzdłuż trasy.

<a name="requests-per-second-rps"></a> **Żądań na sekundę (RPS)**: zobacz [zapytań na sekundę (QPS)](#queries-per-second-qps). 

<a name="rss"></a> **Funkcja RSS**: akronim naprawdę proste syndykacji, Podsumowanie witryny Framework opis zasobu (RDF) lub Podsumowanie witryny sformatowanego w zależności od źródła. Proste, ze strukturą format XML na potrzeby udostępniania zawartości między różnych witrynach sieci Web. Funkcja RSS dokumenty zawierają metadanych klucza elementów, takich jak tworzenie, Data, tytuł, krótki opis i łącze hipertekstowe. Dzięki temu informacje użytkownika (lub usługi wydawcy RSS) zdecydować, materiałów są warte dalszych badań.

## <a name="s"></a>S

<a name="satellite-imagery"></a> **Satelitarne obrazach**: obrazów, które zostały przechwycone przez płaszczyzn i satelity skierowana bezpośrednio w dół.

<a name="software-development-kit-sdk"></a> **Zestaw Software development kit (SDK)**: zbiór dokumentację, przykładowy kod i przykładowych aplikacji, aby ułatwić tworzenie aplikacji za pomocą interfejsu API dla deweloperów.

<a name="shapefile-shp"></a> **Plik kształtu (SHP)**: znany także jako pliku kształtu ESRI, jest wektor formatu przechowywania danych do przechowywania lokalizacji, kształt i atrybutów funkcji geograficznego. Plik kształtu, który jest przechowywany w zestawu powiązanych plików.

<a name="spherical-mercator-projection"></a> **Kulistego projekcji Mercator**: zobacz [Web Mercator](#web-mercator). 

<a name="spatial-query"></a> **Zapytań przestrzennych**: żądanie do usługi, który wykonuje operację przestrzennych. Przykład wyszukiwania promieniowego, lub wzdłuż wyszukiwania trasy.

<a name="spatial-reference"></a> **Odwołania przestrzennego**: Współrzędna lokalnego, regionalnego lub globalnego systemu używana do lokalizowania dokładnie geograficzne jednostki. Definiuje układ współrzędnych używany do tworzenia powiązań współrzędnych mapy do lokalizacji w świecie rzeczywistym. Odwołania przestrzennego upewnij się, można zintegrować wyświetlanie dokładnego lub analizy danych przestrzennych z różnych warstw lub źródeł. Usługa Azure Maps korzysta [EPSG:3857](https://epsg.io/3857) koordynacji systemu odniesienia i WGS 84 dla danych wejściowych geometrii. 

<a name="sql-spatial"></a> **Przestrzenne programu SQL**: odwołuje się do przestrzenne funkcje wbudowane w SQL Azure i programu SQL Server 2008 lub nowszym. Ta funkcja przestrzennego jest również dostępny jako bibliotekę .NET, który może służyć niezależnie od programu SQL Server. Aby uzyskać więcej informacji, zobacz [danych przestrzennych (SQL Server) dokumentacji](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) Aby uzyskać więcej informacji.

<a name="synchronous-request"></a> **Żądań synchronicznych**: żądanie HTTP otwiera połączenie i czeka na odpowiedź. Przeglądarki ograniczają liczbę jednoczesnych żądań HTTP, które mogą być wykonane ze strony. Jeśli wiele długotrwałych żądań synchronicznych zostaną wprowadzone w tym samym czasie, limit ten można z Tobą skontaktować i żądań opóźnione, dopóki nie zakończy się jedną z innych żądań.

## <a name="t"></a>T

<a name="telematics"></a> **Telematyka**: wysyłanie, odbieranie i przechowywania informacji za pośrednictwem usług telekomunikacyjnych urządzeń w połączeniu z kilkukrotnie zawieszenie sterowania zdalnego obiektów. 

<a name="temporal-data"></a> **Dane czasowe**: dane, które odwołuje się do godziny lub daty. Danych czasowych mogą odwoływać się do dyskretnych zdarzeń, takich jak ataki zaowocowało; Przenoszenie obiektów, takich jak pociągów; lub obserwacji powtarzane, np. liczby elementów z czujników ruchu.

<a name="terrain"></a> **Terenu**: obszar terenu o szczególne cechy, takie jak piaszczystej terenu lub po górach terenu.

<a name="thematic-maps"></a> **Mapy tematycznych**: tematycznych mapa jest proste mapy wprowadzone w celu odzwierciedlenia motywu o obszaru geograficznego. Typowy scenariusz dla tego typu mapy jest kolorów regionów administracyjnych, takich jak krajach, w oparciu o niektóre metryki danych.

<a name="tile-layer"></a> **Warstwa kafelków**: warstwa wyświetlane przez łączenie kafelków map (prostokątna sekcje) do ciągłego warstwy. Kafelki są albo rastrowych obrazów kafelków lub wektorowych kafelków. Warstwy kafelków rastrowych są zwykle renderowane wcześniejsze i przechowywane jako obrazy z serwera. Może to zająć dużo miejsca do magazynowania. Wektor kafelka warstwy są renderowane na bieżąco w aplikacji klienta, dlatego są mniejsze wymagania dotyczące magazynu po stronie serwera.

<a name="time-zone"></a> **Strefa czasowa**: regionie świata, która przestrzega jednolita postać czasu standardowego do celów prawnych, komercyjnych i społecznościowych. Stref czasowych zwykle postępuj zgodnie z granicami krajów i ich części.

<a name="transaction"></a> **Transakcja**: usługi Azure Maps używa transakcji modelu licencjonowania gdzie;

- Jedna transakcja jest tworzona dla każdego 15 kafelków mapy lub ruch żądanie.
- Jedna transakcja jest tworzona dla każdego wywołania interfejsu API do jednej z usług Azure Maps, takie jak wyszukiwanie lub routingu.

<a name="transformation"></a> **Przekształcenie**: proces konwersji danych między różnymi systemami współrzędnych geograficznych. Może na przykład mieć niektóre dane, które zostały przechwycone w Wielkiej Brytanii, a następnie oparte na układ współrzędnych geograficznych OSGB 1936. Usługa Azure Maps korzysta [EPSG:3857](https://epsg.io/3857) wariant system współrzędnych odwołanie WGS84. Jako takie do poprawnego wyświetlania danych, będzie musiała mieć współrzędnych przekształcone z jednego systemu do innego.

<a name="traveling-salesmen-problem-tsp"></a> **Problem sprzedawców Traveling (TSP)**: Hamiltonian problem obwodu, w którym sprzedawcy musi znaleźć najbardziej efektywny sposób, aby przejść do serii zostanie ono zatrzymane, następnie wróć do lokalizacja początkowa.  

<a name="trilateration"></a> **Trilateration**: proces określania położenie punktu na powierzchni ziemi w odniesieniu do dwóch innych punktów przez dokonywanie pomiarów odległości między wszystkie trzy punkty.

<a name="turn-by-turn-navigation"></a> **Włącz, wyłącz nawigacji**: dalej manewrów zbliża się do aplikacji, która zawiera instrukcje trasy dla każdego kroku trasę jako użytkownicy.

## <a name="v"></a>V

<a name="vector-data"></a> **Dane wektorowe**: Współrzędna na podstawie danych, która jest reprezentowana jako punkty, linie lub wielokątów.

<a name="vector-tile"></a> **Wektor, Kafelek**: specyfikacja otwartej obsługi danych do przechowywania danych wektorowych dane geograficzne przy użyciu tego samego systemu kafelków jako formantu mapy. Zobacz też [warstwę kafelków](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a> **Można przeprowadzić przy użyciu routingu Problem (VRP)**: klasę problemów, w których zestawu uporządkowanych tras dla pojazdów floty jest obliczana, biorąc pod uwagę jako zestaw ograniczeń. Te ograniczenia może obejmować elementy, takie jak okna czasowe dostarczania, wiele możliwości trasy, a ograniczenia czasu trwania podróży.

<a name="voronoi-diagram"></a> **Voronoi diagram**: partycji miejsca do obszarów lub komórki, otaczające zestaw obiektów geometrycznego (zazwyczaj funkcje punktów). Te komórki lub wiersze i wielokątów, musi spełniać kryteria Delaunay trójkąty. Wszystkie lokalizacje w regionie są bliżej obiektu, który otacza niż do jakiegokolwiek innego obiektu w zestawie. Diagramy Voronoi są często używane do odróżnić obszarów wpływ wokół funkcji geograficznego. 

## <a name="w"></a>W

<a name="waypoint"></a> **Punkt nawigacyjny**: punkt nawigacyjny jest w określonej lokalizacji geograficznej, zdefiniowane przez długość i szerokość geograficzną, która jest używana do celów nawigacyjne. Często używane do reprezentowania punktu, w której ktoś przechodzi Rozsyłanie za pomocą.

<a name="waypoint-optimization"></a> **Punkt nawigacyjny optymalizacji**: proces zmiany kolejności zbiór waypoints, aby zminimalizować czas podróży lub musieli przechodzić przez wszystkie waypoints podanej odległości. Często określane jako [Problem sprzedawców Traveling](#traveling-salesmen-problem-tsp) lub [problemu routingu pojazdu](#vehicle-routing-problem-vrp) w zależności od złożoności optymalizacji.

<a name="web-map-service-wms"></a> **Web Service Map (WMS)**: WMS jest standardem Otwórz konsorcjum geograficznych (OGC), który definiuje opartej na obrazie mapy usług. Usługi WMS do obsługi usługi zapewniają obrazy mapy dla szczególnych obszarów w obrębie mapy na żądanie. Obrazy zawierają wstępnie renderowana Symbolika i może być renderowana w jednym z kilku nazwanych stylów, zdefiniowane przez usługę.

<a name="web-mercator"></a> **Web Mercator**: to tak zwane kulistego Mercator jest niewielkie wariant projekcji Mercator, jeden używane głównie w programach mapowanie oparte na sieci Web. Używa tych samych formuł jako standardowa projekcji Mercator jako używane w przypadku map na małą skalę. Jednak używa Web Mercator kulistego formuły skaluje na wszystkich, natomiast na dużą skalę Mercator mapuje zwykle formularz ellipsoidal projekcji. Niezgodność jest imperceptible na skalę globalną, ale powoduje, że mapy lokalne obszary, które różni się nieco od wartość true, ellipsoidal Mercator mapy w tej samej skali.

<a name="wgs84"></a> **WGS84**: zbiór stałych używanych do wiązania przestrzenne współrzędne lokalizacjach na powierzchni mapy. WGS84 datum jest standardowa używane przez większość dostawców mapowania online i urządzenia GPS. Usługa Azure Maps korzysta [EPSG:3857](https://epsg.io/3857) wariant system współrzędnych odwołanie WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a> **Współrzędna Z**: zobacz [wysokość](#altitude). 

<a name="zip-code"></a> **Kod pocztowy**: zobacz [kod pocztowy](#postal-code).

<a name="Zoom level"></a> **Poziom powiększenia**: Określa poziom szczegółowości oraz ile mapy jest widoczna. Pomniejszeniu aż poziom 0, mapę świata pełną często będzie w widoku, ale pokaże ograniczone szczegóły, takie jak nazwy kraju i obramowanie i nazwy ocean. Po powiększeniu bliżej poziomu 17 mapy wyświetli obszar kilka bloków Miasto drogowej szczegółowe informacje. Aby uzyskać więcej informacji, zobacz [poziomy powiększenia i siatka kafelków](zoom-levels-and-tile-grid.md) dokumentacji.

