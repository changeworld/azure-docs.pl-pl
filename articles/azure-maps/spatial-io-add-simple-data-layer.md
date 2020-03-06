---
title: Dodawanie prostej warstwy danych | Mapy Microsoft Azure
description: Dowiedz się, jak dodać prostą warstwę danych przy użyciu przestrzennego modułu we/wy dostarczonego przez Azure Maps Web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3fa54e3227496c11fcafc2f42e980daa5c716365
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370988"
---
# <a name="add-a-simple-data-layer"></a>Dodawanie prostej warstwy danych

Moduł operacji we/wy przestrzenny zawiera klasę `SimpleDataLayer`. Ta klasa ułatwia renderowanie funkcji stylów na mapie. Może nawet renderować zestawy danych, które mają właściwości stylu i zestawy danych, które zawierają mieszane typy geometrii. Prosta warstwa danych uzyskuje tę funkcję przez zapakowanie wielu warstw renderingu i użycie wyrażeń stylów. Wyrażenia stylu wyszukują wspólne właściwości stylu funkcji w tych warstwach opakowanych. Funkcja `atlas.io.read` i funkcja `atlas.io.write` używają tych właściwości do odczytu i zapisu stylów do obsługiwanego formatu pliku. Po dodaniu właściwości do obsługiwanego formatu pliku można użyć tego pliku do różnych celów. Na przykład plik może służyć do wyświetlania funkcji stylów na mapie.

Oprócz funkcji tworzenia stylów `SimpleDataLayer` zapewnia wbudowaną funkcję podręczną z szablonem podręcznym. Zostanie wyświetlone okno podręczne, gdy zostanie kliknięta funkcja. W razie potrzeby można wyłączyć domyślną funkcję podręczną. Ta warstwa obsługuje również dane klastrowane. Po kliknięciu klastra mapa zostanie powiększana do klastra i rozwinie do poszczególnych punktów i klastrów.

Klasa `SimpleDataLayer` jest przeznaczona do użycia w dużych zestawach danych z wieloma typami geometrii i wieloma stylami zastosowanymi w funkcjach. Gdy jest używana, ta klasa dodaje narzuty na sześć warstw zawierających wyrażenia stylów. Dlatego istnieją przypadki, w których bardziej wydajne jest korzystanie z podstawowych warstw renderowania. Na przykład użyj warstwy Podstawowa do renderowania kilku typów geometrii i kilku stylów dla funkcji

## <a name="default-supported-style-properties"></a>Domyślne obsługiwane właściwości stylu

Jak wspomniano wcześniej, prosta warstwa danych otacza kilka podstawowych warstw renderowania: bąbelki, symbole, linie, Wielokąt i wytłaczany wielokąt. Następnie używa wyrażeń do wyszukiwania prawidłowych właściwości stylu dla poszczególnych funkcji.

Azure Maps i właściwości stylu GitHub to dwa główne zestawy obsługiwanych nazw właściwości. Większość nazw właściwości różnych opcji warstwy usługi Azure Maps jest obsługiwana jako właściwości stylu funkcji w prostej warstwie danych. Do niektórych opcji warstwy dodano wyrażenia obsługujące nazwy właściwości stylu, które są często używane przez witrynę GitHub. Te nazwy właściwości są definiowane przez [obsługę mapy GEOjson usługi GitHub](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)i są używane do stylu plików GeoJSON, które są przechowywane i renderowane na platformie. Wszystkie właściwości stylu usługi GitHub są obsługiwane w prostej warstwie danych, z wyjątkiem właściwości `marker-symbol` style.

Jeśli czytnik napotyka właściwość o mniej typowym stylu, przekonwertuje ją na najbliższy Azure Maps Właściwość stylu. Ponadto domyślne wyrażenia stylów można przesłonić przy użyciu funkcji `getLayers` prostej warstwy danych i aktualizowania opcji na dowolnej z warstw.

W następnej sekcji znajdują się szczegółowe informacje o domyślnych właściwościach stylów, które są obsługiwane przez prostą warstwę danych. Kolejność nazwy obsługiwanej właściwości jest również priorytetem właściwości. Jeśli zdefiniowano dwie właściwości stylu dla tej samej opcji warstwy, pierwsza z nich na liście ma wyższy priorytet.

## <a name="simple-data-layer-options"></a>Proste Opcje warstwy danych

### <a name="bubble-layer-style-properties"></a>Właściwości bąbelków warstwowych

Jeśli funkcja jest `Point` lub `MultiPoint`, a funkcja nie ma właściwości `image`, która byłaby używana jako ikona niestandardowa, aby renderować punkt jako symbol, funkcja będzie renderowana przy użyciu `BubbleLayer`.

| Opcja warstwy | Obsługiwane nazwy właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>, `marker-size`<sup>2</sup>, `scale`<sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size` i `scale` wartości są uznawane za wartości skalarne i będą mnożone przez `8`

\[2\] Jeśli określono opcję `marker-size` serwisu GitHub, dla usługi RADIUS będą używane następujące wartości.

| Rozmiar znacznika | promienie |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Klastry są również renderowane przy użyciu warstwy bąbelki. Domyślnie promień klastra jest ustawiony na `16`. Kolor klastra różni się w zależności od liczby punktów w klastrze, zgodnie z definicją poniżej:

| Liczba punktów | Kolor    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>Właściwości stylu symbolu

Jeśli funkcja jest `Point` lub `MultiPoint`i ma właściwość `image`, która byłaby używana jako ikona niestandardowa, aby renderować punkt jako symbol, funkcja będzie renderowana przy użyciu `SymbolLayer`.

| Opcja warstwy | Obsługiwane nazwy właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size`<sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] Jeśli określono opcję `marker-size` serwisu GitHub, dla opcji rozmiar ikony zostaną użyte następujące wartości.

| Rozmiar znacznika | Rozmiar symbolu |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Jeśli punkt jest klastrem, właściwość `point_count_abbreviated` będzie renderowana jako etykieta tekstowa. Obraz nie będzie renderowany.

### <a name="line-style-properties"></a>Właściwości stylu linii

Jeśli funkcja jest `LineString`, `MultiLineString`, `Polygon`lub `MultiPolygon`, funkcja będzie renderowana przy użyciu `LineLayer`.

| Opcja warstwy | Obsługiwane nazwy właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Właściwości stylu wielokąta

Jeśli funkcja jest `Polygon` lub `MultiPolygon`, a funkcja nie ma właściwości `height` lub właściwość `height` jest równa zero, funkcja będzie renderowana przy użyciu `PolygonLayer`.

| Opcja warstwy | Obsługiwane nazwy właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, "`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Właściwości wyciągania stylu wielokątu

Jeśli funkcja jest `Polygon` lub `MultiPolygon`i ma właściwość `height` o wartości większej niż 0, funkcja będzie renderowana przy użyciu `PolygonExtrusionLayer`.

| Opcja warstwy | Obsługiwane nazwy właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="use-a-simple-data-layer"></a>Korzystanie z prostej warstwy danych

Klasa `SimpleDataLayer` jest używana, podobnie jak inne warstwy renderowania. Poniższy kod pokazuje, jak używać prostej warstwy danych na mapie:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Dodaj funkcje do źródła danych. Następnie prosta warstwa danych będzie dowiedzieć się, jak najlepiej renderować funkcje. Style dla poszczególnych funkcji można ustawić jako właściwości funkcji. Poniższy kod pokazuje funkcję punktu GEOJSON z właściwością `color` ustawioną na `red`. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

Poniższy kod renderuje funkcję powyższych punktów przy użyciu prostej warstwy danych. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Korzystanie z prostej warstwy danych" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Zobacz, jak <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>korzystać z prostej warstwy danych</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Rzeczywista moc prostej warstwy danych to:

- Istnieje kilka różnych typów funkcji w źródle danych. oraz
- Funkcje w zestawie danych mają ustawione na nich kilka właściwości stylu; oraz
- Nie masz pewności, co zestaw danych dokładnie zawiera.

Na przykład podczas analizowania strumieniowych źródeł danych XML nie można znać dokładnych stylów i typów geometrii funkcji. Poniższy przykład pokazuje moc prostej warstwy danych przez renderowanie funkcji pliku KML. Przedstawiono w nim również różne opcje zapewniane przez klasę prostej warstwy danych.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Proste Opcje warstwy danych" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>opcjami proste warstwy danych</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Odczytuj i zapisuj dane przestrzenne](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę mapy OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Korzystanie z podstawowych operacji](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)
