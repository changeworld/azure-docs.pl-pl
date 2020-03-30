---
title: Słowniczek usługi Azure Maps | Dokumenty firmy Microsoft
description: Słownik często używanych terminów skojarzonych z usługami Azure Maps, usługami opartymi na lokalizacji i usługami GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: c9d5c2efc14dec5beee0eaf81a4f628421ef3d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657048"
---
# <a name="glossary"></a>Słownik

Na poniższej liście opisano typowe słowa używane w usługach Azure Maps.

## <a name="a"></a>A

<a name="address-validation"></a>**Sprawdzanie poprawności adresu:** Proces weryfikacji istnienia adresu.

<a name="advanced-routing"></a>**Routing zaawansowany:** Zbiór usług, które wykonują operacje z wyprzedzeniem przy użyciu danych routingu drogowego; takie jak obliczanie osiągalnych zakresów (izochrony), macierzy odległości i żądań marszrut wsadowych.

<a name="aerial-imagery"></a>**Zdjęcia lotnicze**: Zobacz [zdjęcia satelitarne](#satellite-imagery). 

<a name="along-a-route-search"></a>**Wzdłuż wyszukiwania trasy:** kwerenda przestrzenna, która wyszukuje dane w określonym czasie objazdu lub odległości od ścieżki trasy.

<a name="altitude"></a>**Wysokość:** Wysokość lub pionowa rzędna punktu powyżej powierzchni odniesienia. Pomiary wysokości są oparte na danej referencyjnej podstawie, takiej jak średni poziom morza. Zobacz też elewacja.

<a name="ambiguous"></a>**Niejednoznaczne:** Stan niepewności w klasyfikacji danych, który istnieje, gdy obiekt może odpowiednio przypisać dwie lub więcej wartości dla danego atrybutu. Na przykład podczas geokodowania "CA", zwracane są dwa niejednoznaczne wyniki: "Kanada" i "Kalifornia". "CA" to kraj i kod stanu, odpowiednio dla "Kanada" i "Kalifornia". 

<a name="annotation"></a>**Adnotacja:** Tekst lub grafika wyświetlana na mapie w celu dostarczenia informacji użytkownikowi. Adnotacja może identyfikować lub opisywać określoną jednostkę mapy, dostarczać ogólne informacje o obszarze na mapie lub dostarczać informacji o samej mapie.

<a name="antimeridian"></a>**Antimeridian**: Znany również jako 180<sup>th</sup> Meridian. Jest to punkt, w którym spotykają się -180 stopni i 180 stopni długości geograficznej. Co jest przeciwieństwem południka głównego na świecie.

<a name="application-programming-interface-api"></a>**Interfejs programowania aplikacji (API)**: Specyfikacja, która umożliwia deweloperom tworzenie aplikacji.

<a name="api-key"></a>**Klucz interfejsu API**: Zobacz [Uwierzytelnianie klucza udostępnionego](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Obszar zainteresowania (AOI)**: Zakres używany do definiowania obszaru ostrości dla mapy lub produkcji bazy danych.

<a name="asset-tracking"></a>**Śledzenie zasobów:** proces śledzenia lokalizacji zasobu, takiego jak osoba, pojazd lub inny obiekt.

<a name="asynchronous-request"></a>**Żądanie asynchroniczne:** Żądanie HTTP, które otwiera połączenie i sprawia, że żądanie do serwera, który zwraca identyfikator dla żądania asynchronicznie, a następnie zamyka połączenie. Serwer kontynuuje przetwarzanie żądania, a użytkownik może sprawdzić stan przy użyciu identyfikatora. Po zakończeniu przetwarzania żądania użytkownik może pobrać odpowiedź. Ten typ żądania jest używany dla długotrwałych procesów.

<a name="autocomplete"></a>**Autouzupełnianie:** Funkcja w aplikacji, która przewiduje resztę słowa, które użytkownik wpisuje. 

<a name="autosuggest"></a>**Autosuggest:** Funkcja w aplikacji, która przewiduje logiczne możliwości tego, co użytkownik wpisuje.

<a name="azure-location-based-services-lbs"></a>**Usługi oparte na lokalizacji platformy Azure (LBS)**: poprzednia nazwa usługi Azure Maps, gdy była w wersji zapoznawczej.

<a name="azure-active-directory"></a>**Usługa Azure Active Directory (Azure AD)**: Usługa Azure AD to chmurowa usługa zarządzania tożsamościami i dostępem firmy Microsoft. Integracja usługi Azure Maps z usługą Azure AD jest obecnie dostępna w wersji zapoznawczej dla wszystkich interfejsów API usługi Azure Maps. Usługa Azure AD obsługuje kontrolę dostępu opartą na rolach (RBAC), aby umożliwić szczegółowy dostęp do zasobów usługi Azure Maps. Aby dowiedzieć się więcej o integracji usługi Azure Maps w usłudze Azure AD, zobacz [Usługi Azure Maps i usługi Azure AD](azure-maps-authentication.md) oraz Zarządzanie [uwierzytelnianiem w usłudze Azure Maps.](how-to-manage-authentication.md)

<a name="azure-maps-key"></a>**Klucz usługi Azure Maps**: zobacz [Uwierzytelnianie za pomocą klucza udostępnionego](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a>**Mapa bazowa:** Część aplikacji mapy, która wyświetla informacje o tle, takie jak drogi, punkty orientacyjne i granice polityczne.

<a name="batch-request"></a>**Żądanie wsadowe:** Proces łączenia wielu żądań w jedno żądanie.

<a name="bearing"></a>**Łożysko**: Kierunek poziomy punktu w stosunku do innego punktu. Jest to wyrażone jako kąt względem północy, od 0 stopni do 360 stopni w kierunku zgodnym z ruchem wskazówek zegara. 

<a name="boundary"></a>**Granica:** Linia lub wielokąt oddzielający sąsiednie jednostki polityczne, takie jak kraje/regiony, okręgi i właściwości. Granica to linia, która może, ale nie może, być zgodna z cechami fizycznymi, takimi jak rzeki, góry lub ściany.

<a name="bounds"></a>**Granice**: Patrz [Obwiednia .](#bounding-box)

<a name="bounding-box"></a>**Obwiednia:** Zestaw współrzędnych używanych do reprezentowania prostokątnego obszaru na mapie. 

## <a name="c"></a>C

<a name="cadastre"></a>**Katastru**: Rejestr zarejestrowanych gruntów i nieruchomości. Zobacz też [Paczka](#parcel).

<a name="camera"></a>**Kamera**: W kontekście interaktywnego sterowania mapą kamera definiuje pole widzenia mapy. Rzutnia kamery jest określana na podstawie kilku parametrów mapy: centrum, poziomu powiększenia, skoku, łożyska. 

<a name="centroid"></a>**Centroid:** Geometryczny środek operacji. Centroid linii byłby punktem środkowym, podczas gdy środek polipona byłby jego środkiem.

<a name="choropleth-map"></a>**Mapa Choropleth**: Mapa tematyczna, na której obszary są zacienione proporcjonalnie do pomiaru zmiennej statystycznej. Ta zmienna statystyczna jest wyświetlana na mapie. Na przykład kolorowanie granicy każdego stanu USA na podstawie jego względnej populacji do wszystkich innych stanów.

<a name="concave-hull"></a>**Kadłub**wklęsły: Kształt reprezentujący możliwą geometrię wklęsłą, która otacza wszystkie kształty w określonym zestawie danych. Wygenerowany kształt jest podobny do zawijania danych folią, a następnie podgrzewania go, powodując w ten sposób duże rozpiętości między punktami do jaskini w kierunku innych punktów danych.

<a name="consumption-model"></a>**Model zużycia**: Informacje określające szybkość, z jaką pojazd zużywa paliwo lub energię elektryczną. Zobacz także [dokumentację modelu zużycia](consumption-model.md).

<a name="control"></a>**Sterowanie:** Samodzielny lub wielokrotnego użytku składnik składający się z graficznego interfejsu użytkownika, który definiuje zestaw zachowań dla interfejsu. Na przykład formant mapy jest zazwyczaj częścią interfejsu użytkownika, która ładuje interaktywną mapę.

<a name="convex-hull"></a>**Wypukły kadłub**: Wypukły kadłub to kształt reprezentujący minimalną wypukłą geometrię, która otacza wszystkie kształty w określonym zestawie danych. Wygenerowany kształt jest podobny do zawijania gumki wokół zestawu danych.

<a name="coordinate"></a>**Współrzędna:** Składa się z wartości długości i szerokości geograficznej używanych do reprezentowania lokalizacji na mapie.

<a name="coordinate-system"></a>**Układ współrzędnych**: Struktura odniesienia używana do definiowania położenia punktów w przestrzeni w dwóch lub trzech wymiarach.

<a name="country-code"></a>**Kod kraju:** Unikatowy identyfikator kraju/regionu na podstawie normy ISO. ISO2 to dwuznakowy kod dla kraju (na przykład US), który ISO3 reprezentuje kod trzyznakowy (na przykład USA).

<a name="country-subdivision"></a>**Podział kraju**: Podpodział pierwszego poziomu kraju/regionu, powszechnie znany jako stan lub prowincja.

<a name="country-secondary-subdivision"></a>**Podział wtórny kraju**: Drugi poziom podziału kraju/regionu, powszechnie znany jako powiat.

<a name="country-tertiary-subdivision"></a>**Podział trzeciorzędowy kraj**: Trzeci poziom podziału kraju/regionu, zazwyczaj nazwanego obszaru, takiego jak okręg.

<a name="cross-street"></a>**Ulica krzyżowa**: Punkt, w którym przecinają się dwie lub więcej ulic.

<a name="cylindrical-projection"></a>**Rzut cylindryczny**: Rzut, który przekształca punkty ze sferoidy lub kuli w cylindra stycznego lub sekanowego. Cylinder jest następnie pokrojony od góry do dołu i spłaszczony w płaszczyźnie.

## <a name="d"></a>D

<a name="datum"></a>**Baza pomiarowa**: Specyfikacje referencyjne systemu pomiarowego, układu położenia współrzędnych na powierzchni (pozioma dataum) lub wysokości nad lub pod powierzchnią (pionowa dataum).

<a name="dbf-file"></a>**Plik DBF**: Format pliku bazy danych, który jest używany w połączeniu z Shapefiles (SHP).

<a name="degree-minutes-seconds-dms"></a>**Stopni minut sekund (DMS)**: Jednostka miary do opisywania szerokości i długości geograficznej. Stopień to 1/360<sup>th</sup> okręgu. Stopień jest dalej podzielony na 60 minut, a minuta jest podzielona na 60 sekund.

<a name="delaunay-triangulation"></a>**Triangulacji Delaunay**: Technika tworzenia siatki sąsiadujących, nienakładających trójkątów z zestawu danych punktów. Okrąg okrążający każdego trójkąta nie zawiera żadnych punktów z zestawu danych we wnętrzu.

<a name="demographics"></a>**Dane demograficzne**: Cechy statystyczne (takie jak wiek, wskaźnik urodzeń i dochód) populacji ludzkiej.

<a name="destination"></a>**Miejsce docelowe:** punkt końcowy lub miejsce, do którego ktoś podróżuje.

<a name="digital-elevation-model-dem"></a>**Cyfrowy model elewacji (DEM)**: Zestaw danych wartości elewacji związanych z powierzchnią, przechwycony nad obszarem w regularnych odstępach czasu przy użyciu wspólnej bazy pomiarowej. Dems są zazwyczaj używane do reprezentowania ulgi terenu.

<a name="dijkstra's-algorithm"></a>**Algorytm Dijkstra:** Algorytm, który bada łączność sieci, aby znaleźć najkrótszą ścieżkę między dwoma punktami.

<a name="distance-matrix"></a>**Macierz odległości:** Macierz zawierająca informacje o czasie podróży i odległości między zestawem źródeł i miejsc docelowych. 

## <a name="e"></a>E

<a name="elevation"></a>**Rzędna:** Pionowa odległość punktu lub obiektu powyżej lub poniżej powierzchni odniesienia lub bazy pomiarowej. Ogólnie rzecz biorąc, powierzchnia odniesienia jest średni poziom morza. Elewacja zazwyczaj odnosi się do pionowej wysokości ziemi.

<a name="envelope"></a>**Koperta**: Patrz [Obwiednia](#bounding-box).

<a name="extended-postal-code"></a>**Rozszerzony kod pocztowy:** Kod pocztowy, który może zawierać dodatkowe informacje. Na przykład w STANACH ZJEDNOCZONYCH kody pocztowe mają pięć cyfr. Ale rozszerzony kod pocztowy, znany jako zip + 4, zawiera cztery dodatkowe cyfry. Te dodatkowe cyfry służą do identyfikacji segmentu geograficznego w pięciocyfrowym obszarze dostawy, takim jak blok miejski, grupa mieszkań lub skrzynka pocztowa. Znajomość segmentu geograficznego pomaga w wydajnym sortowaniu i dostarczaniu poczty.

<a name="extent"></a>**Zakres**: Patrz [Obwiednia](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Uwierzytelnianie federacyjne**: Metoda uwierzytelniania umożliwiająca użycie pojedynczego mechanizmu logowania/uwierzytelniania w wielu aplikacjach internetowych i mobilnych. 

<a name="feature"></a>**Operacja**: Obiekt, który łączy geometrię z dodatkowymi informacjami o metadanych. 

<a name="feature-collection"></a>**Kolekcja obiektów**: Kolekcja obiektów charakterystycznych.

<a name="find-along-route"></a>**Znajdź wzdłuż trasy:** Kwerenda przestrzenna, która wyszukuje dane, które znajdują się w określonym czasie lub odległości od ścieżki trasy.

<a name="find-nearby"></a>**Znajdź w pobliżu:** Kwerenda przestrzenna, która przeszukuje stałą odległość linii prostej (w linii prostej) od punktu.

<a name="fleet-management"></a>**Zarządzanie flotą**: Zarządzanie pojazdami użytkowymi, takimi jak samochody, ciężarówki, statki i samoloty. Zarządzanie flotą może obejmować szereg funkcji, takich jak finansowanie pojazdów, konserwacja, telematyka (śledzenie i diagnostyka), a także zarządzanie kierowcami, prędkością, paliwem i bezpieczeństwem i higieną pracy. Zarządzanie flotą to proces stosowany przez firmy, które polegają na transporcie w swojej firmie. Firmy chcą zminimalizować ryzyko i zmniejszyć ogólne koszty transportu i personelu, zapewniając jednocześnie zgodność z przepisami rządowymi.

<a name="free-flow-speed"></a>**Prędkość swobodnego przepływu:** Prędkość swobodnego przepływu oczekiwana w idealnych warunkach. Zazwyczaj ograniczenie prędkości.

<a name="free-form-address"></a>**Adres formularza bezpłatnego:** Pełny adres, który jest reprezentowany jako pojedynczy wiersz tekstu.

<a name="fuzzy-search"></a>**Wyszukiwanie rozmyte:** Wyszukiwanie, które przyjmuje w swobodnym ciągu tekstu, który może być adresem lub punktem zainteresowania. 

## <a name="g"></a>G

<a name="geocode"></a>**Geokod:** Adres lub lokalizacja, która została przekonwertowana na współrzędną, która może służyć do wyświetlania tej lokalizacji na mapie. 

<a name="geocoding"></a>**Geokodowanie**: Znany również jako geokodowanie do przodu, jest procesem konwersji adresu danych lokalizacji na współrzędne. 

<a name="geodesic-path"></a>**Ścieżka geodezjowa**: Najkrótsza ścieżka między dwoma punktami na zakrzywionej powierzchni. Po renderowaniu w usłudze Azure Maps ta ścieżka jest wyświetlana jako linia zakrzywiona ze względu na projekcję Mercatora.

<a name="geofence"></a>**Geofence**: Zdefiniowany region geograficzny, który może służyć do wyzwalania zdarzeń, gdy urządzenie wchodzi lub istnieje region.

<a name="geojson"></a>**GeoJSON**: Jest to wspólny format pliku oparty na JSON używany do przechowywania geograficznych danych wektorowych, takich jak punkty, linie i wielokąty. **Uwaga:** Usługa Azure Maps używa rozszerzonej wersji usługi GeoJSON, zgodnie [z opisaną tutaj](extend-geojson.md).

<a name="geometry"></a>**Geometria:** Reprezentuje obiekt przestrzenny, taki jak punkt, linia lub wielokąt.

<a name="geometrycollection"></a>**GeometryCollection**: Zbiór obiektów geometrii.

<a name="geopol"></a>**GeoPol**: Odnosi się do danych wrażliwych geopolitycznie, takich jak sporne granice i nazwy miejsc.

<a name="georeference"></a>**Georeference**: Proces wyrównywania danych geograficznych lub obrazów do znanego układu współrzędnych. Proces ten może polegać na przesunięciu, obróceniu, skalowaniu lub wypaczeniu danych.

<a name="georss"></a>**GeoRSS**: Rozszerzenie XML do dodawania danych przestrzennych do kanałów informacyjnych RSS.

<a name="gis"></a>**GIS**: Skrót od "Geographic Information System". Wspólny termin używany do opisania branży mapowania.

<a name="gml"></a>**GML**: Znany również jako Język znaczników geografii. Rozszerzenie pliku XML do przechowywania danych przestrzennych.

<a name="gps"></a>**GPS**: Znany również jako Globalny System Pozycjonowania, to system satelitów używanych do określania pozycji urządzeń na ziemi. Orbitujące satelity przesyłają sygnały, które pozwalają odbiornikowi GPS w dowolnym miejscu na ziemi obliczyć własną lokalizację poprzez triatrację.

<a name="gpx"></a>**GPX**: Znany również jako format GPS eXchange, jest formatem pliku XML powszechnie tworzony z urządzeń GPS.  

<a name="great-circle-distance"></a>**Odległość dużego okręgu**: najkrótsza odległość między dwoma punktami na powierzchni kuli.

<a name="greenwich-mean-time-gmt"></a>**Greenwich Mean Time (GMT)**: Czas na południku głównym, który biegnie przez Royal Observatory w Greenwich, Anglia.

<a name="guid"></a>**Identyfikator GUID**: Unikatowy identyfikator globalnie. Ciąg używany do jednoznacznej identyfikacji interfejsu, klasy, biblioteki typów, kategorii składnika lub rekordu.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Formuła Haversine:** Wspólne równanie używane do obliczania odległości dużego okręgu między dwoma punktami na kuli.

<a name="hd-maps"></a>**Mapy HD**: Znane również jako mapy wysokiej rozdzielczości, składa się z wysokiej jakości informacji o sieci drogowej, takich jak oznaczenia pasa ruchu, oznakowanie i światła kierunkowe wymagane do jazdy autonomicznej.

<a name="heading"></a>**Kierunek:** Kierunek, w którym coś wskazuje lub jest skierowany. Patrz także [Łożysko](#heading).

<a name="heatmap"></a>**Mapa cieplna**: Wizualizacja danych, w której zakres kolorów reprezentuje gęstość punktów w danym obszarze. Zobacz też Mapa tematyczna.

<a name="hybrid-imagery"></a>**Obrazy hybrydowe:** zdjęcia satelitarne lub lotnicze z danymi drogowymi i etykietami nałożone na nią.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: Skrót od Internet Assigned Numbers Authority . Grupa non-profit, która nadzoruje globalną alokację adresów IP.

<a name="isochrone"></a>**Isochrone**: Izochrone definiuje obszar, w którym ktoś może podróżować w określonym czasie dla środka transportu w dowolnym kierunku z danej lokalizacji. Zobacz też [Zasięg osiągalny](#reachable-range).

<a name="isodistance"></a>**Izrozadność**: Biorąc pod uwagę lokalizację, izochron definiuje obszar, w którym ktoś może podróżować w określonej odległości dla środka transportu w dowolnym kierunku. Zobacz też [Zasięg osiągalny](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: Znany również jako Keyhole Markup Language, jest typowym formatem pliku XML do przechowywania geograficznych danych wektorowych, takich jak punkty, linie i wielokąty. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: Wielospektralne, orbitujące wokół Ziemi satelity opracowane przez NASA, które zbierają zdjęcia ziemi. Te obrazy są używane w wielu branżach, takich jak rolnictwo, leśnictwo i kartografia.

<a name="latitude"></a>**Szerokość geograficzna:** Odległość kątowa mierzona w stopniach od równika w kierunku północnym lub południowym.

<a name="level-of-detail"></a>**Poziom szczegółowości**: Zobacz Poziom powiększenia.

<a name="lidar"></a>**Lidar**: Akronim do wykrywania światła i zakresu. Technika teledetekcji, która wykorzystuje lasery do pomiaru odległości do powierzchni odbijających światło.

<a name="linear-interpolation"></a>**Interpolacja liniowa**: Oszacowanie nieznanej wartości przy użyciu odległości liniowej między znanymi wartościami.

<a name="linestring"></a>**LineString**: Geometria używana do reprezentowania linii. Znany również jako polilinii. 

<a name="localization"></a>**Lokalizacja**: Obsługa różnych języków i kultur.

<a name="logistics"></a>**Logistyka**: Proces przenoszenia ludzi, pojazdów, dostaw lub aktywów w skoordynowany sposób.

<a name="longitude"></a>**Długość geograficzna**: Odległość kątowa mierzona w stopniach od południka głównego w kierunku wschodnim lub zachodnim.

## <a name="m"></a>M

<a name="map-tile"></a>**Kafelek mapy**: Prostokątny obraz reprezentujący partycję obszaru roboczego mapy. Aby uzyskać więcej informacji, zobacz [poziomy powiększenia i dokumentację siatki kafelków](zoom-levels-and-tile-grid.md).

<a name="marker"></a>**Znacznik:** Znany również jako pineka lub pineka, jest ikoną reprezentującą położenie punktu na mapie.

<a name="mercator-projection"></a>**Projekcja Mercatora:** Cylindryczna projekcja mapy, która stała się standardową projekcją mapy do celów morskich ze względu na zdolność do reprezentowania linii o stałym kursie, znanych jako linie rhumb, jako proste segmenty, które oszczędzają kąty z południkami. Wszystkie rzuty na płaskiej mapie zniekształcają kształty lub rozmiary mapy w porównaniu z rzeczywistym układem powierzchni Ziemi. Projekcja Mercatora wyolbrzymia obszary daleko od równika, tak że mniejsze obszary pojawiają się większe na mapie, gdy zbliżasz się do biegunów. 

<a name="multilinestring"></a>**MultiLineString**: Geometria reprezentująca kolekcję obiektów LineString. 

<a name="multipoint"></a>**MultiPoint**: Geometria reprezentująca kolekcję obiektów Punkt.

<a name="multipolygon"></a>**MultiPolygon:** Geometria reprezentująca kolekcję obiektów Wielokąta. Na przykład, aby pokazać granicę Hawajów, każda wyspa zostanie nakreślona wielokątem. Tak więc granicą Hawajów byłby zatem MultiPolygon.

<a name="municipality"></a>**Gmina**: Miasto lub miasto. 

<a name="municipality-subdivision"></a>**Pododdział gminy**: Podział gminy, taki jak nazwa dzielnicy lub obszaru lokalnego, taki jak "centrum".

## <a name="n"></a>Nie

<a name="navigation-bar"></a>**Pasek nawigacyjny:** Zestaw kontrolek na mapie używany do dostosowywania poziomu powiększenia, skoku, obrotu i przełączania podstawowej warstwy mapy.

<a name="nearby-search"></a>**Wyszukiwanie w pobliżu:** zapytanie przestrzenne, które przeszukuje stałą odległość linii prostej (w linii prostej) od punktu.

<a name="neutral-ground-truth"></a>**Neutralna prawda gruntu:** Mapa, która renderuje etykiety w języku urzędowym regionu, który reprezentuje i w skryptach lokalnych, jeśli są dostępne.

## <a name="o"></a>O

<a name="origin"></a>**Początek:** Punkt początkowy lub lokalizacja, w której znajduje się użytkownik.

## <a name="p"></a>P

<a name="panning"></a>**Przesuwanie:** Proces przesuwania mapy w dowolnym kierunku przy zachowaniu stałego poziomu powiększenia.

<a name="parcel"></a>**Działka**: Działka lub granica nieruchomości.

<a name="pitch"></a>**Skok:** Wysokość pochylenia mapy w stosunku do pionowej, gdzie 0 patrzy prosto w dół na mapie.

<a name="point"></a>**Punkt:** Geometria reprezentująca pojedynczą pozycję na mapie. 

<a name="points-of-interest-poi"></a>**Punkty szczególne (POI)**: Firma, punkt orientacyjny lub wspólne miejsce zainteresowania.

<a name="polygon"></a>**Wielokąt**: Geometria bryłowa reprezentująca obszar na mapie. 

<a name="polyline"></a>**Polilinia**: Geometria używana do reprezentowania linii. Znany również jako LineString. 

<a name="position"></a>**Położenie**: Długość geograficzna, szerokość geograficzna i wysokość (współrzędne x,y,z) punktu.

<a name="post-code"></a>**Kod pocztowy**: Patrz [Kod pocztowy](#postal-code).

<a name="postal-code"></a>**Kod pocztowy:** Seria liter lub cyfr lub obu tych liter w określonym formacie. Kod pocztowy jest używany przez usługę pocztową kraju/regionu do dzielenia obszarów geograficznych na strefy w celu uproszczenia dostawy poczty.

<a name="primary-key"></a>**Klucz podstawowy:** pierwszy z dwóch kluczy subskrypcji dostarczonych dla uwierzytelniania klucza udostępnionego usługi Azure Maps. Zobacz [Uwierzytelnianie za pomocą klucza udostępnionego](#shared-key-authentication).

<a name="prime-meridian"></a>**Południk główny:** Linia długości geograficznej, która reprezentuje długość geograficzną 0 stopni. Ogólnie rzecz biorąc, wartości długości geograficznej zmniejszają się podczas podróży w kierunku zachodnim do 180 stopni i zwiększają się podczas podróży w kierunkach wschodnich do -180 stopni. 

<a name="prj"></a>**PRJ**: Plik tekstowy, który często towarzyszy plik Shapefile, który zawiera informacje o przewidywanym układzie współrzędnych, w którym znajduje się zestaw danych.

<a name="projection"></a>**Projekcja:** Rzutowany układ współrzędnych oparty na projekcji mapy, takiej jak poprzeczny Mercator, równy obszar Albersa i Robinson. Zapewniają one możliwość projekcji map sferycznej powierzchni Ziemi na dwuwymiarową kartezjańską płaszczyznę współrzędnych. Rzutowane układy współrzędnych są czasami określane jako rzuty mapowe.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey:** Indeks adresów base-4 dla kafelka w systemie kafli quadtree. Aby uzyskać więcej informacji, zobacz [Poziomy powiększenia i dokumentacja siatki kafelków, aby](zoom-levels-and-tile-grid.md) uzyskać więcej informacji.

<a name="quadtree"></a>**Quadtree**: Struktura danych, w której każdy węzeł ma dokładnie cztery elementy podrzędne. System kafli używany w usłudze Azure Maps używa struktury quadtree, takiej jak powiększanie przez użytkownika na jednym poziomie, każdy kafelek mapy dzieli się na cztery podporządkowania.  Aby uzyskać więcej informacji, zobacz [Poziomy powiększenia i dokumentacja siatki kafelków, aby](zoom-levels-and-tile-grid.md) uzyskać więcej informacji.

<a name="queries-per-second-qps"></a>**Zapytania na sekundę (QPS)**: Liczba zapytań lub żądań, które można wysuwać do usługi lub platformy w ciągu jednej sekundy. 

## <a name="r"></a>R

<a name="radial-search"></a>**Wyszukiwanie promieniowe:** kwerenda przestrzenna, która przeszukuje stałą odległość linii prostej (w linii prostej) od punktu. 

<a name="raster-data"></a>**Dane rastrowe**: Macierz komórek (lub pikseli) zorganizowana w wiersze i kolumny (lub siatkę), w której każda komórka zawiera wartość reprezentującą informacje, takie jak temperatura. Raster to cyfrowe zdjęcia lotnicze, zdjęcia z satelitów, obrazy cyfrowe i zeskanowane mapy.

<a name="raster-layer"></a>**Warstwa rastrowa**: warstwa kafli, która składa się z obrazów rastrowych.

<a name="reachable-range"></a>**Zakres osiągalny:** Osiągalny zasięg określa obszar, w którym ktoś może podróżować w określonym czasie lub odległości, aby środek transportu mógł podróżować, w dowolnym kierunku z lokalizacji. Zobacz także [Isochrone](#isochrone) i [Isodistance](#isodistance).

<a name="remote-sensing"></a>**Teledetekcja**: Proces zbierania i interpretowania danych z czujników z daleka.

<a name="rest-service"></a>**Rest service**: Skrót REST oznacza reprezentacyjny transfer państwa. Usługa REST to usługa internetowa oparta na adresie URL, która opiera się na podstawowej technologii sieci web do komunikowania się, najczęściej spotykane metody są http get i post żądań. Tego typu usługi mają tendencję do mnie znacznie szybciej i mniejsze niż tradycyjne usługi oparte na MYDLE.

<a name="reverse-geocode"></a>**Odwróć geokod:** Proces przyjmowania współrzędnych i określania adresu, w którym jest on przedstawiany na mapie.

<a name="reproject"></a>**Reproject**: Zobacz [Transformacja](#transformation).

<a name="rest-service"></a>**Rest service**: Akronim dla reprezentacyjnego transferu państwa. Architektura wymiany informacji między elementami równorzędnymi w zdecentralizowanym, rozproszonym środowisku. REST umożliwia programom na różnych komputerach komunikowanie się niezależnie od systemu operacyjnego lub platformy. Usługa może wysłać żądanie protokołu HTTP (Hypertext Transfer Protocol) do jednolitego lokalizatora zasobów (URL) i odzyskać dane.

<a name="route"></a>**Trasa**: Ścieżka między dwiema lub więcej lokalizacjami, która może również zawierać dodatkowe informacje, takie jak instrukcje dotyczące punktów trasy wzdłuż trasy.

<a name="requests-per-second-rps"></a>**Żądania na sekundę (RPS)**: Zobacz [zapytania na sekundę (QPS)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: Skrót od Really Simple Syndication, Resource Description Framework (RDF) Site Summary lub Rich Site Summary, w zależności od źródła. Prosty, ustrukturyzowany format XML do udostępniania zawartości między różnymi witrynami sieci Web. Dokumenty RSS zawierają kluczowe elementy metadanych, takie jak autor, data, tytuł, krótki opis i łącze hipertekstowe. Te informacje pomagają użytkownikowi (lub usłudze wydawcy RSS) zdecydować, jakie materiały są warte dalszego zbadania.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Zdjęcia satelitarne**: Zdjęcia, które zostały zrobione przez samoloty i satelity skierowane prosto w dół.

<a name="secondary-key"></a>**Klucz pomocniczy:** drugi z dwóch kluczy subskrypcji dostarczonych dla uwierzytelniania klucza udostępnionego usługi Azure Maps. Zobacz [Uwierzytelnianie za pomocą klucza udostępnionego](#shared-key-authentication).

<a name="shapefile-shp"></a>**Shapefile (SHP)**: Znany również jako plik shapefile ESRI, jest formatem przechowywania danych wektorowych do przechowywania lokalizacji, kształtu i atrybutów funkcji geograficznych. Plik shape jest przechowywany w zestawie powiązanych plików.

<a name="shared-key-authentication"></a>**Uwierzytelnianie za pomocą klucza współdzielonego:** uwierzytelnianie za pomocą kluczy za pomocą konta Usługi Azure Maps zależy od przekazywania kluczy wygenerowanych przez konto usługi Azure Maps przy użyciu każdego żądania do usługi Azure Maps. Te klucze są często określane jako klucze subskrypcji. Zaleca się, że klucze są regularnie regenerowane dla bezpieczeństwa. Dwa klucze są dostarczane, dzięki czemu można zachować połączenia przy użyciu jednego klucza podczas ponownego generowania innych. Po ponownym wygenerowaniu kluczy należy zaktualizować wszystkie aplikacje uzyskujące dostęp do tego konta pod kątem używania nowych kluczy. Aby dowiedzieć się więcej o uwierzytelnianiu usługi Azure Maps, zobacz [Usługi Azure Maps i usługi Azure AD](azure-maps-authentication.md) oraz Zarządzanie [uwierzytelnianiem w usłudze Azure Maps](how-to-manage-authentication.md).

<a name="software-development-kit-sdk"></a>**Zestaw SDK (Software Development Kit)**: Zbiór dokumentacji, przykładowy kod i przykładowe aplikacje, aby ułatwić deweloperowi używanie interfejsu API do tworzenia aplikacji.

<a name="spherical-mercator-projection"></a>**Sferyczna projekcja Mercator:** Zobacz [Web Mercator](#web-mercator). 

<a name="spatial-query"></a>**Kwerenda przestrzenna:** Żądanie do usługi, która wykonuje operację przestrzenną. Takie jak wyszukiwanie promieniowe lub wyszukiwanie trasy.

<a name="spatial-reference"></a>**Odniesienie przestrzenne**: Współrzędny systemu lokalnego, regionalnego lub globalnego używanego do precyzyjnego lokalizowania jednostek geograficznych. Definiuje układ współrzędnych używany do powiązania współrzędnych mapy z lokalizacjami w świecie rzeczywistym. Odniesienia przestrzenne zapewniają, że dane przestrzenne z różnych warstw lub źródeł mogą być zintegrowane w celu dokładnego przeglądania lub analizy. Usługa Azure Maps używa układu referencyjnego współrzędnych [EPSG:3857](https://epsg.io/3857) i WGS 84 do wprowadzania danych geometrii.

<a name="sql-spatial"></a>**SQL spatial**: Odnosi się do funkcji przestrzennych wbudowanych w sql azure i SQL Server 2008 i powyżej. Ta funkcja przestrzenna jest również dostępna jako biblioteka .NET, która może być używana niezależnie od programu SQL Server. Aby uzyskać więcej informacji, zobacz [dokumentację danych przestrzennych (SQL Server), aby](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) uzyskać więcej informacji.

<a name="subscription-key"></a>**Klucz subskrypcji**: Zobacz [Uwierzytelnianie kluczem udostępnionym](#shared-key-authentication).

<a name="synchronous-request"></a>**Żądanie synchroniczne:** Żądanie HTTP otwiera połączenie i czeka na odpowiedź. Przeglądarki ograniczają liczbę równoczesnych żądań HTTP, które można nabrać na stronie. Jeśli wiele długotrwałych żądań synchronicznych są dokonywane w tym samym czasie, a następnie ten limit można osiągnąć. Żądania będą opóźnione do czasu zakończenia jednego z pozostałych żądań.

## <a name="t"></a>T

<a name="telematics"></a>**Telematyka**: Wysyłanie, odbieranie i przechowywanie informacji za pośrednictwem urządzeń telekomunikacyjnych w połączeniu z kontrolą na obiektach zdalnych. 

<a name="temporal-data"></a>**Dane czasowe**: Dane, które odnoszą się konkretnie do godzin lub dat. Dane czasowe mogą odnosić się do dyskretnych zdarzeń, takich jak uderzenia pioruna; poruszających się obiektów, takich jak pociągi; lub powtarzających się obserwacji, takich jak liczby z czujników ruchu.

<a name="terrain"></a>**Teren**: Obszar terenu o szczególnej charakterystyce, taki jak piaszczysty teren lub teren górski.

<a name="thematic-maps"></a>**Mapy tematyczne**: Mapa tematyczna to prosta mapa stworzona w celu odzwierciedlenia motywu o obszarze geograficznym. Typowym scenariuszem dla tego typu mapy jest kolor regionów administracyjnych, takich jak kraje/regiony na podstawie niektórych danych.

<a name="tile-layer"></a>**Warstwa kafli**: Warstwa wyświetlana przez złożenie kafelków mapy (przekrojów prostokątnych) w warstwę ciągłą. Kafelki są kafelkami obrazów rastrowych lub kafelkami wektorowymi. Warstwy kafli rastrowych są zazwyczaj renderowane z wyprzedzeniem i są przechowywane jako obrazy na serwerze. Warstwy kafli rastrowych mogą wykorzystywać dużą przestrzeń magazynową. Warstwy kafli wektorowych są renderowane w czasie zbliżonym do rzeczywistego w aplikacji klienckiej. W związku z tym wymagania dotyczące magazynu po stronie serwera są mniejsze dla warstw płytek wektorowych.

<a name="time-zone"></a>**Strefa czasowa**: Region świata, który przestrzega jednolitego czasu standardowego dla celów prawnych, handlowych i społecznych. Strefy czasowe mają tendencję do podążania za granicami krajów/regionów i ich podpodziałów.

<a name="transaction"></a>**Transakcja:** Usługa Azure Maps używa modelu licencjonowania transakcyjnego, w którym;

- Jedna transakcja jest tworzona dla każdej 15 map lub wymaganych kafelków ruchu.
- Jedna transakcja jest tworzona dla każdego wywołania interfejsu API do jednej z usług w usłudze Azure Maps. Wyszukiwanie i routing są przykładami usługi Azure Maps.

<a name="transformation"></a>**Transformacja**: Proces konwersji danych między różnymi systemami współrzędnych geograficznych. Możesz na przykład mieć pewne dane, które zostały przechwycone w Wielkiej Brytanii i oparte na układzie współrzędnych geograficznych OSGB 1936. Usługa Azure Maps używa wariantu układu współrzędnych [EPSG:3857](https://epsg.io/3857) WGS84. W związku z tym, aby poprawnie wyświetlić dane, będzie musiał mieć jego współrzędne przekształcone z jednego systemu do drugiego.

<a name="traveling-salesmen-problem-tsp"></a>**Travel Salesmen Problem (TSP)**: Hamiltonian problem obwodu, w którym sprzedawca musi znaleźć najbardziej efektywny sposób, aby odwiedzić serię przystanków, a następnie powrócić do miejsca rozpoczęcia.  

<a name="trilateration"></a>**Triantracja**: Proces określania położenia punktu na powierzchni ziemi, w odniesieniu do dwóch innych punktów, poprzez pomiar odległości między wszystkimi trzema punktami.

<a name="turn-by-turn-navigation"></a>**Nawigacja krok po kroku:** Aplikacja, która udostępnia instrukcje trasy dla każdego kroku trasy, gdy użytkownicy zbliżają się do następnego manewru.

## <a name="v"></a>V

<a name="vector-data"></a>**Dane wektorowe**: Współrzędne dane, które są reprezentowane jako punkty, linie lub wielokąty.

<a name="vector-tile"></a>**Kafelek wektorowy:** specyfikacja otwartych danych do przechowywania danych wektora geoprzestrzennego przy użyciu tego samego systemu kafelków co kontrolka mapy. Zobacz też [Warstwa kafli](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**Problem z wyznaczaniem tras pojazdów (VRP)**: Klasa problemów, w której oblicza się zestaw zamówionych tras dla floty pojazdów, biorąc pod uwagę jako zestaw ograniczeń. Ograniczenia te mogą obejmować okna czasu dostawy, wiele pojemności trasy i ograniczenia czasu trwania podróży.

<a name="voronoi-diagram"></a>**Diagram Voronoi:** Partycja przestrzeni na obszary lub komórki, które otaczają zestaw obiektów geometrycznych, zwykle wskazują obiekty. Komórki te, lub wielokąty, muszą spełniać kryteria trójkątów Delaunay. Wszystkie lokalizacje w obrębie obszaru znajdują się bliżej obiektu, który otacza, niż do jakiegokolwiek innego obiektu w zestawie. Diagramy Voronoi są często używane do wyznaczania obszarów oddziaływania wokół funkcji geograficznych. 

## <a name="w"></a>W

<a name="waypoint"></a>**Punkt trasy:** Punkt trasy jest określoną lokalizacją geograficzną zdefiniowaną przez długość geograficzną i szerokość geograficzną, która jest używana do celów nawigacyjnych. Często używane do reprezentowania punktu, w którym ktoś porusza się po trasie.

<a name="waypoint-optimization"></a>**Optymalizacja punktów trasy:** Proces ponownego kolejności zestawu punktów trasy w celu zminimalizowania czasu podróży lub odległości wymaganej do przejścia przez wszystkie podane punkty trasy. W zależności od złożoności optymalizacji, optymalizacja ta jest często określana jako [problem podróżujących sprzedawców](#traveling-salesmen-problem-tsp) lub [problem routingu pojazdów.](#vehicle-routing-problem-vrp)

<a name="web-map-service-wms"></a>**Usługa map internetowych (WMS)**: WMS jest standardem Open Geographic Consortium (OGC), który definiuje usługi map oparte na obrazie. Usługi WMS udostępniają obrazy map dla określonych obszarów na mapie na żądanie. Obrazy zawierają wstępnie renderowane symboliki i mogą być renderowane w jednym z kilku nazwanych stylów, jeśli są zdefiniowane przez usługę.

<a name="web-mercator"></a>**Web Mercator**: Znany również jako sferycznej projekcji Mercator. Jest to niewielki wariant projekcji Mercatora, używany głównie w programach mapowania opartych na sieci Web. Używa tych samych formuł co standardowa projekcja Mercatora, która jest używana w przypadku map na małą skalę. Jednak Web Mercator używa sferycznych formuł we wszystkich skalach, ale mapy Mercatora na dużą skalę zwykle używają elipsoidalnej formy projekcji. Rozbieżność jest niezauważalna w skali globalnej, ale powoduje, że mapy obszarów lokalnych nieco odbiegają od prawdziwych elipsoidalnych map Mercatora w tej samej skali.

<a name="wgs84"></a>**WGS84**: Zestaw stałych używanych do powiązania współrzędnych przestrzennych z lokalizacjami na powierzchni mapy. WGS84 datum jest standardem używanym przez większość dostawców map online i urządzeń GPS. Usługa Azure Maps używa wariantu układu współrzędnych [EPSG:3857](https://epsg.io/3857) WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Współrzędne Z**: Patrz [Wysokość .](#altitude) 

<a name="zip-code"></a>**Kod pocztowy**: Patrz [Kod pocztowy](#postal-code).

<a name="Zoom level"></a>**Poziom powiększenia**: Określa poziom szczegółowości i ilość mapy. Po powiększeniu do poziomu 0, pełna mapa świata będzie często widoczna. Mapa będzie jednak wyświetlać ograniczone szczegóły, takie jak nazwy krajów/regionów, granice i nazwy oceanów. Po powiększeniu bliżej poziomu 17 na mapie pojawi się obszar kilku bloków miejskich ze szczegółowymi informacjami o drogach. W mapach azure najwyższy poziom powiększenia wynosi 22. Aby uzyskać więcej informacji, zobacz poziomy [powiększenia i dokumentację siatki kafelków.](zoom-levels-and-tile-grid.md)

