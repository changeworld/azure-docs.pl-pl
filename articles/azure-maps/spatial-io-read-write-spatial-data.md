---
title: Odczytywanie i zapisywanie danych przestrzennych | Mapy platformy Microsoft Azure
description: Dowiedz się, jak odczytywać i zapisywać dane przy użyciu modułu Usługi We/Wy przestrzennej udostępnianej przez zestaw SDK usługi Azure Maps Web.
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334151"
---
# <a name="read-and-write-spatial-data"></a>Odczytywanie i zapisywanie danych przestrzennych

W poniższej tabeli wymieniono formaty plików przestrzennych, które są obsługiwane do odczytu i zapisu operacji za pomocą modułu IO przestrzennego.

| Format danych       | Odczyt | Zapisywanie |
|-------------------|------|-------|
| Geojson           | ✓  |  ✓  |
| Georss            | ✓  |  ✓  |
| Gml               | ✓  |  ✓  |
| Gpx               | ✓  |  ✓  |
| Kml               | ✓  |  ✓  |
| Kmz               | ✓  |  ✓  |
| Przestrzenny CSV       | ✓  |  ✓  |
| Dobrze znany tekst   | ✓  |  ✓  |

W następnych sekcjach opisano wszystkie różne narzędzia do odczytywania i zapisywania danych przestrzennych za pomocą modułu IO przestrzennego.

## <a name="read-spatial-data"></a>Odczyt danych przestrzennych

Funkcja `atlas.io.read` ta jest główną funkcją używaną do odczytywania typowych formatów danych przestrzennych, takich jak pliki KML, GPX, GeoRSS, GeoJSON i CSV z danymi przestrzennymi. Ta funkcja może również odczytywać skompresowane wersje tych formatów, jako plik zip lub plik KMZ. Format pliku KMZ jest skompresowaną wersją KML, która może również zawierać zasoby, takie jak obrazy. Alternatywnie funkcja odczytu może przyjmować adres URL, który wskazuje plik w dowolnym z tych formatów. Adresy URL powinny być hostowane w punkcie końcowym obsługującym usługę CORS lub usługa proxy powinna być dostępna w opcjach odczytu. Usługa serwera proxy służy do ładowania zasobów w domenach, które nie są włączone CORS. Funkcja odczytu zwraca obietnicę, aby dodać ikony obrazu do mapy i przetwarza dane asynchronicznie, aby zminimalizować wpływ na wątek interfejsu użytkownika.

Podczas odczytywania skompresowanego pliku, jako zip lub KMZ, zostanie on rozpakowany i zeskanowany w poszukiwaniu pierwszego prawidłowego pliku. Na przykład doc.kml lub plik z innym prawidłowym rozszerzeniem, takim jak: .kml, xml, geojson, .json, csv, .tsv lub txt. Następnie obrazy, do których odwołuje się pliki KML i GeoRSS, są wstępnie załadowane, aby zapewnić ich dostępność. Niedostępne dane obrazu mogą załadować alternatywny obraz rezerwowy lub zostaną usunięte ze stylów. Obrazy wyodrębnione z plików KMZ zostaną przekonwertowane na identyfikatory URI danych.

Wynik z funkcji odczytu `SpatialDataSet` jest obiektem. Ten obiekt rozszerza klasę GeoJSON FeatureCollection. Można go łatwo przekazać `DataSource` do as-is, aby renderować jego obiekty na mapie. Informacje `SpatialDataSet` o obiektach zawierają nie tylko informacje o obiektach, ale mogą również obejmować nakładki gruntowe KML, metryki przetwarzania i inne szczegóły opisane w poniższej tabeli.

| Nazwa właściwości | Typ | Opis | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Obwiednia wszystkich danych w zestawie danych. |
| `features` | `Feature[]` | Funkcje GeoJSON w zestawie danych. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Tablica KML GroundOverlays. |
| `icons` | Nagraj&lt;ciąg, ciąg&gt; | Zestaw adresów URL ikon. Klucz = nazwa ikony, Wartość = ADRES URL. |
| properties | Wszelki | Informacje o właściwościach dostarczane na poziomie dokumentu zestawu danych przestrzennych. |
| `stats` | `SpatialDataSetStats` | Statystyki dotyczące zawartości i czasu przetwarzania przestrzennego zestawu danych. |
| `type` | `'FeatureCollection'` | Wartość typu GeoJSON tylko do odczytu. |

## <a name="examples-of-reading-spatial-data"></a>Przykłady odczytu danych przestrzennych

Poniższy kod pokazuje, jak odczytać zestaw danych przestrzennych i `SimpleDataLayer` renderować go na mapie przy użyciu klasy. Kod używa pliku GPX wskazywu przez adres URL.

<br/>

<iframe height='500' scrolling='no' title='Łatwe ładowanie danych przestrzennych' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>load dane przestrzenne proste</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Następne demo kodu pokazuje, jak odczytać i załadować KML lub KMZ, do mapy. KML może zawierać nakładki uziemienia, które `ImageLyaer` `OgcMapLayer`będą w postaci lub . Te nakładki muszą być dodawane na mapie oddzielnie od obiektów. Ponadto jeśli zestaw danych ma niestandardowe ikony, te ikony muszą być ładowane do zasobów map przed załadowaniem obiektów.

<br/>

<iframe height='500' scrolling='no' title='Załaduj KML na mapę' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pen <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>load KML Na</a> mapie<a href='https://codepen.io/azuremaps'>@azuremaps</a>przez Usługi Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Opcjonalnie można udostępnić usługę serwera proxy umożliwiającą dostęp do zasobów między domenami, które mogą nie mieć włączonej usługi CORS. Funkcja odczytu spróbuje najpierw uzyskać dostęp do plików w innej domenie przy użyciu usługi CORS. Po pierwszym uruchomieniu nie może uzyskać dostępu do dowolnego zasobu w innej domenie przy użyciu usługi CORS, zażąda ona dodatkowych plików tylko wtedy, gdy usługa proxy została dostarczona. Funkcja odczytu dołącza adres URL pliku na końcu podanego adresu URL serwera proxy. Ten fragment kodu pokazuje, jak przekazać usługę proxy do funkcji odczytu:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

Poniższe demo pokazuje, jak odczytać rozdzielony plik i renderować go na mapie. W takim przypadku kod używa pliku CSV, który ma kolumny danych przestrzennych.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie pliku rozdzielanego' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pen <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>Add a Delimited</a> File<a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Zapisywanie danych przestrzennych

Istnieją dwie główne funkcje zapisu w module we/wy przestrzennej. Funkcja `atlas.io.write` generuje ciąg, podczas `atlas.io.writeCompressed` gdy funkcja generuje skompresowany plik zip. Skompresowany plik zip będzie zawierał plik tekstowy z danymi przestrzennymi. Obie te funkcje zwracają obietnicę, aby dodać dane do pliku. I obaj mogą zapisywać dowolną z `SpatialDataSet` `DataSource`następujących `ImageLayer` `OgcMapLayer`danych: , , , , zbiór operacji, obiekt, geometria lub tablica dowolnej kombinacji tych typów danych. Podczas pisania przy użyciu jednej z tych funkcji można określić format pliku poszukiwanego. Jeśli format pliku nie jest określony, dane zostaną zapisane jako KML.

Poniższe narzędzie pokazuje większość opcji zapisu, które `atlas.io.write` mogą być używane z funkcją.

<br/>

<iframe height='700' scrolling='no' title='Opcje zapisu danych przestrzennych' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>opcje zapisu danych przestrzennych</a> pióra przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Przykład zapisywania danych przestrzennych

Poniższy przykład umożliwia przeciąganie i upuszczanie, a następnie ładowanie plików przestrzennych na mapie. Dane GeoJSON można wyeksportować z mapy i zapisać je w jednym z obsługiwanych formatów danych przestrzennych jako ciąg lub jako skompresowany plik.

<br/>

<iframe height='700' scrolling='no' title='Przeciąganie i upuszczanie plików przestrzennych na mapę' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>przeciąganie pióra i upuszczanie plików przestrzennych na mapę</a> przez usługę Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

Opcjonalnie można udostępnić usługę serwera proxy umożliwiającą dostęp do zasobów między domenami, które mogą nie mieć włączonej usługi CORS. Ten fragment kodu pokazuje, że można włączyć usługę proxy:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Odczyt i napisanie dobrze znanego tekstu (WKT)

[Well-Known Text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) jest standardem Open Geospatial Consortium (OGC) do reprezentowania geometrii przestrzennych jako tekstu. Wiele systemów geoprzestrzennych obsługuje wkt, takie jak Azure SQL i Azure PostgreSQL przy użyciu wtyczki PostGIS. Podobnie jak większość standardów OGC, współrzędne są sformatowane jako "szerokość geograficzna", aby wyrównać z konwencją "x y". Na przykład punkt na długości -110 i szerokości geograficznej 45 `POINT(-110 45)` można zapisać jako przy użyciu formatu WKT.

Dobrze znany tekst można odczytać `atlas.io.ogc.WKT.read` za pomocą funkcji `atlas.io.ogc.WKT.write` i zapisać za pomocą funkcji.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Przykłady czytania i pisania dobrze znanego tekstu (WKT)

Poniższy kod pokazuje, jak odczytać dobrze `POINT(-122.34009 47.60995)` znany ciąg tekstowy i renderować go na mapie przy użyciu warstwy bąbelkowej.

<br/>

<iframe height='500' scrolling='no' title='Przeczytaj dobrze znany tekst' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Przeczytaj dobrze znany</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>tekst przez usługi Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Poniższy kod pokazuje czytanie i pisanie dobrze znanego tekstu tam iz powrotem.

<br/>

<iframe height='700' scrolling='no' title='Czytanie i pisanie dobrze znanego tekstu' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>Odczytuj i napisz</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>dobrze znany tekst przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Odczyt i zapis GML

GML to przestrzenna specyfikacja pliku XML, która jest często używana jako rozszerzenie innych specyfikacji XML. Dane GeoJSON mogą być zapisywane jako XML `atlas.io.core.GmlWriter.write` z tagami GML przy użyciu funkcji. Kod XML zawierający GML można `atlas.io.core.GmlReader.read` odczytać za pomocą tej funkcji. Funkcja odczytu ma dwie opcje:

- `isAxisOrderLonLat` Opcja — kolejność współrzędnych "szerokość geograficzna, długość geograficzna" lub "długość geograficzna, szerokość geograficzna" może się różnić w zależności od zbiorów danych i nie zawsze jest dobrze zdefiniowana. Domyślnie czytnik GML odczytuje dane współrzędnych jako "szerokość geograficzna, długość geograficzna", ale ustawienie tej opcji na true odczyta je jako "długość geograficzna, szerokość geograficzna".
- Opcja `propertyTypes` — ta opcja jest tabelą wyszukiwania wartości klucza, w której kluczem jest nazwa właściwości w zestawie danych. Wartość jest typem obiektu, na który należy rzutować wartość podczas analizowania. Obsługiwane wartości typu `string`to: `number` `boolean`, `date`, , i . Jeśli właściwość nie znajduje się w tabeli odnośnych lub typ nie jest zdefiniowany, właściwość zostanie przeanalizowana jako ciąg.

Funkcja `atlas.io.read` domyślnie będzie `atlas.io.core.GmlReader.read` działać, gdy wykryje, że dane wejściowe to XML, ale dane nie są jednym z innych formatów przestrzennego XML.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [atlas.io funkcje statyczne](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet (Zestaw danych przestrzennych)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats (SpatialDataSetStats)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [Czytnik gml](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter (GmlWriter)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [funkcje atlas.io.ogc.WKT](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Dodawanie warstwy mapy OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Łączenie się z usługą WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Wykorzystanie podstawowych operacji](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)
