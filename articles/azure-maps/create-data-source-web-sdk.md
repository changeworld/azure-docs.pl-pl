---
title: Tworzenie źródła danych w Azure Maps | Microsoft Docs
description: Tworzenie źródła danych i używanie go z Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: b83a66296d54a179a56e37de199ec900ae23a1db
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433014"
---
# <a name="create-a-data-source"></a>Tworzenie źródła danych

Azure Maps Web SDK przechowuje dane w źródłach danych, które optymalizują dane do wykonywania zapytań i renderowania. Obecnie istnieją dwa typy źródeł danych:

**Źródło danych GEOJSON**

Źródło danych GEOJSON pozwala na ładowanie i przechowywanie danych lokalnie przy użyciu klasy `DataSource`. Dane GEOJSON można utworzyć ręcznie lub utworzyć przy użyciu klas pomocnika w przestrzeni nazw [Atlas. Data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) . Klasa `DataSource` udostępnia funkcje do importowania lokalnych lub zdalnych plików GeoJSON. Pliki zdalnego GEOJSON muszą być hostowane w punkcie końcowym z obsługą mechanizmu CORs. Klasa `DataSource` zapewnia funkcjonalność dla danych punktu klastrowania. Dane można łatwo dodać, usunąć i zaktualizować za pomocą klasy `DataSource`.


> [!TIP]
> Jeśli chcesz zastąpić wszystkie dane w `DataSource`, jeśli nastąpi wywołanie do `clear` następnie `add` funkcje, Mapa spróbuje ponownie renderować dwa razy, co może spowodować nieco opóźnienia. Zamiast tego należy użyć funkcji `setShapes`, która spowoduje usunięcie i zamianę wszystkich danych w źródle danych i wyzwolenie tylko jednego ponownego renderowania mapy.

**Źródło kafelków wektora**

Źródło kafelka wektorowego opisuje, jak uzyskać dostęp do warstwy kafelków wektorowych i można ją utworzyć za pomocą klasy [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource) . Azure Maps wyrównany do [specyfikacji kafelka wektora MapBox](https://github.com/mapbox/vector-tile-spec), która jest otwartym standardem. Warstwy kafelków wektorowych są podobne do warstw kafelków, jednak zamiast poszczególnych kafelków obrazu rastrowego są one skompresowanym plikiem (format PBF), który zawiera dane mapy wektorowej i co najmniej jedną warstwę, która może być renderowana na kliencie na podstawie stylu każdej warstwy. Dane w kafelku wektorowym zawierają funkcje geograficzne w postaci punktów, linii i wielokątów. Istnieją różne zalety warstw kafelków wektorowych za pośrednictwem warstw kafelków rastrowych;

 - Rozmiar pliku kafelka wektora jest zwykle znacznie mniejszy niż odpowiednik kafelka rastrowego. W związku z tym jest używana mniejsza przepustowość, co oznacza małe opóźnienia i szybszą mapę. Pozwala to zwiększyć komfort pracy użytkowników.
 - Ponieważ kafelki wektorowe są renderowane na kliencie, można je dostosować do rozdzielczości urządzenia, na którym są one wyświetlane. Dzięki temu renderowane mapy, które wyglądają znacznie bardziej dobrze, są definiowane i z użyciem Crystal Clear Label. 
 - Zmiana stylu danych w mapach wektorów nie wymaga ponownego pobierania danych, ponieważ na kliencie można zastosować nowy styl. Natomiast zmiana stylu warstwy kafelków rastrowych zazwyczaj wymaga załadowania kafelków z serwera, do którego zastosowano nowy styl.
 - Ze względu na to, że dane są dostarczane w formie wektorowej, do przygotowania danych nie jest wymagane przetwarzanie po stronie serwera, co oznacza, że nowsze dane można szybciej udostępnić.

Wszystkie warstwy używające źródła wektora muszą określać wartość `sourceLayer`. 

Po utworzeniu źródła danych można dodać do mapy za pomocą właściwości `map.sources`, która jest obiektem [sourcemanager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager). Poniższy kod przedstawia sposób tworzenia `DataSource` i dodawania go do mapy.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>Łączenie źródła danych z warstwą

Dane są renderowane na mapie przy użyciu warstw renderowania. Pojedynczemu źródle danych może być przywoływana jedna lub więcej warstw renderowania. Następujące warstwy renderowania wymagają od źródła danych, aby je włączyć:

- [Bąbelkowa warstwa](map-add-bubble-layer.md) renderuje dane punktowe jako skalowane kółka na mapie.
- [Warstwa symboli](map-add-pin.md) — renderuje dane punktu jako ikony i/lub tekst.
- [Warstwa mapy cieplnej](map-add-heat-map-layer.md) — renderuje dane punktu jako mapę cieplną gęstości.
- [Warstwa linii](map-add-shape.md) — może służyć do renderowania linii i lub konspektu wielokątów. 
- [Warstwa wielokątów](map-add-shape.md) — wypełnia obszar wielokąta przy użyciu pełnego koloru lub wzorca obrazu.

Poniższy kod przedstawia sposób tworzenia źródła danych, dodawania go do mapy i łączenia go z warstwą bąbelkową, a następnie importowania danych punktu GEOJSON z lokalizacji zdalnej do niej. 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Istnieją dodatkowe warstwy renderowania, które nie łączą się z tymi źródłami danych, ale zamiast tego ładują dane bezpośrednio renderowane. 

- [Warstwa obrazu](map-add-image-layer.md) — nakłada pojedynczy obraz na mapę i wiąże jego rogi z zestawem określonych współrzędnych.
- [Warstwa kafelków](map-add-tile-layer.md) nakłada warstwę kafelków rastrowych na podstawie mapy.

## <a name="one-data-source-with-multiple-layers"></a>Jedno źródło danych z wieloma warstwami

Wiele warstw można podłączyć do pojedynczego źródła danych. Może to być nieparzyste, ale istnieje wiele różnych scenariuszy, w których ta metoda jest przydatna. Zróbmy na przykład scenariusz tworzenia środowiska rysowania wielokątów. Gdy użytkownik umożliwia użytkownikowi narysowanie wielokątów, powinien renderować obszar Wielokąt wypełnienia, gdy użytkownik dodaje punkty do mapy. Dodanie linii z stylem, który zawiera Wielokąt, spowoduje, że zobaczysz krawędzie wielokąta w miarę rysowania. Na koniec Dodawanie dojścia, takiego jak numer PIN lub znacznik, nad każdą pozycją w wielokąta, ułatwia edytowanie poszczególnych pozycji. Poniżej znajduje się obraz pokazujący ten scenariusz.

![Mapa pokazująca wiele warstw renderowania danych z jednego źródła danych](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Aby zrealizować ten scenariusz na większości platform mapowania, należy utworzyć obiekt wielokątu, obiekt linii i numer PIN dla każdej pozycji w wielokąta. Gdy Wielokąt jest modyfikowany, trzeba ręcznie zaktualizować linię i numery PIN, co może szybko być złożone.

W przypadku Azure Maps wystarczy pojedynczy Wielokąt w źródle danych, jak pokazano w poniższym kodzie.

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodawanie okna podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę wielokątów](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodawanie mapy cieplnej](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)