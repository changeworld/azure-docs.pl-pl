---
title: Odczytywanie i zapisywanie danych przestrzennych | Mapy Microsoft Azure
description: Dowiedz się, jak odczytywać i zapisywać dane przy użyciu przestrzennego modułu we/wy dostarczonego przez Azure Maps Web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 458b307cf1158c467100e032e3f789462e8fdcca
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370910"
---
# <a name="read-and-write-spatial-data"></a>Odczytuj i zapisuj dane przestrzenne

W poniższej tabeli przedstawiono formaty plików przestrzennych, które są obsługiwane w przypadku operacji odczytu i zapisu w module operacji we/wy przestrzennym.

| Format danych       | Odczyt | Zapis |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| Przestrzenny CSV       | ✓  |  ✓  |
| Dobrze znany tekst   | ✓  |  ✓  |

W następnych sekcjach opisano różne narzędzia do odczytywania i zapisywania danych przestrzennych przy użyciu przestrzennego modułu we/wy.

## <a name="read-spatial-data"></a>Odczytaj dane przestrzenne

Funkcja `atlas.io.read` jest główną funkcją służącą do odczytywania wspólnych formatów danych przestrzennych, takich jak KML, GPX, GeoRSS, GEOJSON i pliki CSV z danymi przestrzennymi. Ta funkcja umożliwia również odczytywanie skompresowanych wersji tych formatów jako pliku zip lub pliku KMZ. Format pliku KMZ to skompresowana wersja KML, która może zawierać również zasoby, takie jak obrazy. Alternatywnie funkcja Read może przyjmować adres URL wskazujący plik w dowolnym z tych formatów. Adresy URL powinny być hostowane w punkcie końcowym z obsługą mechanizmu CORs lub usługa serwera proxy powinna być dostępna w opcjach odczytu. Usługa serwera proxy służy do ładowania zasobów w domenach, które nie są włączone przy użyciu mechanizmu CORs. Funkcja Read zwraca obietnicę, aby dodać ikony obrazu do mapy, i asynchronicznie przetwarza dane, aby zminimalizować wpływ na wątek interfejsu użytkownika.

Podczas odczytywania skompresowanego pliku, jako zip lub KMZ, zostanie on rozpakowany i zeskanowany jako pierwszy prawidłowy plik. Na przykład doc. KML lub plik z innymi prawidłowymi rozszerzeniami, takimi jak:. KML,. XML, GEOJSON,. JSON,. csv,. tsv lub. txt. Następnie obrazy, do których odwołuje się plik KML i GeoRSS są wstępnie załadowane, aby upewnić się, że są dostępne. Dane niedostępnego obrazu mogą ładować alternatywny obraz rezerwowy lub zostaną usunięte ze stylów. Obrazy wyodrębnione z plików KMZ zostaną przekonwertowane na identyfikatory URI danych.

Wynik funkcji Read jest obiektem `SpatialDataSet`. Ten obiekt rozszerza klasę funkcji GEOJSON. Można je łatwo przekazywać do `DataSource` jako-to, aby renderować jego funkcje na mapie. `SpatialDataSet` zawiera nie tylko informacje o funkcji, ale może również obejmować nakładki KML, metryki przetwarzania oraz inne szczegóły, jak opisano w poniższej tabeli.

| Nazwa właściwości | Typ | Opis | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Pole ograniczenia dotyczące wszystkich danych w zestawie danych. |
| `features` | `Feature[]` | Funkcje GEOJSON w zestawie danych. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Tablica KML GroundOverlays. |
| `icons` | Rejestruj&lt;ciąg, ciąg&gt; | Zestaw adresów URL ikon. Key = nazwa ikony, wartość = URL. |
| właściwości | ile | Informacje o właściwościach podane na poziomie dokumentu w zestawie danych przestrzennych. |
| `stats` | `SpatialDataSetStats` | Statystyka dotycząca zawartości i czasu przetwarzania przestrzennego zestawu danych. |
| `type` | `'FeatureCollection'` | Tylko do odczytu wartość typu GEOJSON. |

## <a name="examples-of-reading-spatial-data"></a>Przykłady odczytywania danych przestrzennych

Poniższy kod ilustruje sposób odczytywania prostego zestawu danych przestrzennych i renderowania go na mapie przy użyciu klasy `SimpleDataLayer`. Kod używa pliku GPX wskazywanego przez adres URL.

<br/>

<iframe height='500' scrolling='no' title='Proste ładowanie danych przestrzennych' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>dane przestrzenne ładowania</a> piórem proste przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W następnym pokazie kodu pokazano, jak czytać i ładować KML lub KMZ do mapy. KML mogą zawierać nakładki gruntowe, które będą miały postać `ImageLyaer` lub `OgcMapLayer`. Te nakładki muszą być dodawane do mapy niezależnie od funkcji. Ponadto, jeśli zestaw danych zawiera niestandardowe ikony, te ikony muszą zostać załadowane do zasobów mapy przed załadowaniem funkcji.

<br/>

<iframe height='500' scrolling='no' title='Załaduj KML na mapie' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>KML obciążenia</a> piórem na mapie według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

Opcjonalnie możesz zapewnić usłudze proxy dostęp do zasobów między domenami, które mogą nie mieć włączonego mechanizmu CORs. Ten fragment kodu pokazuje, że można udostępnić usługę proxy:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

//Read a KML file from a URL or pass in a raw KML string.
atlas.io.read('https://s3-us-west-2.amazonaws.com/s.cdpn.io/1717245/2007SanDiegoCountyFires.kml', {
    //Provide a proxy service
    proxyService: proxyServiceUrl
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

W ostatniej demonstracyjnej części pokazano, jak odczytać rozdzielony plik i renderować go na mapie. W takim przypadku kod używa pliku CSV, który zawiera kolumny danych przestrzennych.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie rozdzielanego pliku' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>Dodaj plik rozdzielony</a> przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Zapisz dane przestrzenne

Istnieją dwie główne funkcje zapisu w module operacji we/wy przestrzennym. Funkcja `atlas.io.write` generuje ciąg, podczas gdy funkcja `atlas.io.writeCompressed` generuje skompresowany plik zip. Skompresowany plik zip będzie zawierać plik tekstowy zawierający dane przestrzenne. Obie te funkcje zwracają obietnicę, aby dodać dane do pliku. I, oba te elementy mogą zapisywać dowolne z następujących danych: `SpatialDataSet`, `DataSource`, `ImageLayer`, `OgcMapLayer`, kolekcja funkcji, funkcja, geometria lub tablica dowolnej kombinacji tych typów danych. Podczas pisania przy użyciu którejkolwiek z tych funkcji można określić żądany format pliku. Jeśli format pliku nie zostanie określony, dane będą zapisywane jako KML.

Poniższe narzędzie pokazuje większość opcji zapisu, które mogą być używane z funkcją `atlas.io.write`.

<br/>

<iframe height='700' scrolling='no' title='Opcje zapisu danych przestrzennych' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>opcjami zapisu danych przestrzennych</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Przykład zapisywania danych przestrzennych

Poniższy przykład umożliwia przeciąganie i upuszczanie, a następnie ładowanie plików przestrzennych na mapie. Dane GEOJSON można wyeksportować z mapy i zapisać je w jednym z obsługiwanych formatów danych przestrzennych jako ciąg lub skompresowany plik.

<br/>

<iframe height='700' scrolling='no' title='Przeciągnij i upuść pliki przestrzenne na mapie' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>Przeciąganie i upuszczanie plików przestrzennych</a> piórem na mapie według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Opcjonalnie możesz zapewnić usłudze proxy dostęp do zasobów między domenami, które mogą nie mieć włączonego mechanizmu CORs. Ten fragment kodu pokazuje, że można dołączyć usługę proxy:

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

function readData(data, fileName) {
    loadingIcon.style.display = '';
    fileCount++;
    //Attempt to parse the file and add the shapes to the map.
    atlas.io.read(data, {
        //Provide a proxy service
        proxyService: proxyServiceUrl
    }).then(
        //Success
        function(r) {
            //some code goes here ...
        }
    );
}
```

## <a name="read-and-write-well-known-text-wkt"></a>Odczytaj i Zapisz dobrze znany tekst (WKT)

[Dobrze znany tekst](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) to Open GEOSPATIAL Consortium (OGC) standard służący do reprezentowania geometrie przestrzennego jako tekst. Wiele systemów geoprzestrzennych obsługuje WKT, takie jak Azure SQL i Azure PostgreSQL, przy użyciu wtyczki PostGIS. Podobnie jak w przypadku większości standardów OGC, współrzędne są sformatowane jako "Szerokość geograficzna", aby dostosować ją do Konwencji "x y". Na przykład punkt o długości co najwyżej 110 i Latitude 45 można napisać jako `POINT(-110 45)` przy użyciu formatu WKT.

Dobrze znany tekst może zostać odczytany przy użyciu funkcji `atlas.io.ogc.WKT.read` i zapisany przy użyciu funkcji `atlas.io.ogc.WKT.write`.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Przykłady odczytu i pisania dobrze znanego tekstu (WKT)

Poniższy kod ilustruje sposób odczytywania dobrze znanego ciągu tekstowego `POINT(-122.34009 47.60995)` i renderowania go na mapie przy użyciu warstwy bąbelkowej.

<br/>

<iframe height='500' scrolling='no' title='Odczytaj dobrze znany tekst' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>dobrze znany tekst</a> dla pióra za pomocą Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

Poniższy kod ilustruje odczytywanie i pisanie dobrze znanego tekstu.

<br/>

<iframe height='700' scrolling='no' title='Odczytaj i Zapisz dobrze znany tekst' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>Odczytaj i napisz dobrze znany tekst</a> , Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Odczyt i zapis GML

GML jest przestrzenną specyfikacją pliku XML, która jest często używana jako rozszerzenie do innych specyfikacji XML. Dane GEOJSON można zapisać jako XML za pomocą tagów GML przy użyciu funkcji `atlas.io.core.GmlWriter.write`. KOD XML zawierający GML można odczytać za pomocą funkcji `atlas.io.core.GmlReader.read`. Funkcja Read ma dwie opcje:

- Opcja `isAxisOrderLonLat` — kolejność osi współrzędnej "szerokości geograficznej, długości geograficznej" lub "Długość geograficzna" może się różnić w zależności od zestawów danych i nie zawsze jest prawidłowo zdefiniowana. Domyślnie czytnik GML odczytuje dane współrzędnych jako "Latitude, Długość geograficzna", ale ustawienie tej opcji na wartość true spowoduje jej odczytanie jako "Długość geograficzna".
- Opcja `propertyTypes` — ta opcja jest tabelą wyszukiwania wartości klucza, w której klucz jest nazwą właściwości w zestawie danych. Wartość jest typem obiektu, do którego ma zostać rzutowana wartość podczas analizowania. Obsługiwane wartości typu to: `string`, `number`, `boolean`i `date`. Jeśli właściwość nie znajduje się w tabeli wyszukiwania lub typ nie jest zdefiniowany, właściwość zostanie przeanalizowana jako ciąg.

Funkcja `atlas.io.read` domyślnie będzie funkcją `atlas.io.core.GmlReader.read`, gdy wykryje, że dane wejściowe są XML, ale dane nie są jednym z innych obsługiwanych formatów XML.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [atlas.io funkcje statyczne](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [funkcje Atlas. IO. OGC. WKT](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodaj warstwę mapy OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Korzystanie z podstawowych operacji](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)
