---
title: Dodawanie prostej warstwy danych | Mapy platformy Microsoft Azure
description: Dowiedz się, jak dodać prostą warstwę danych przy użyciu modułu Usługi We/Wy przestrzennej udostępnianej przez zestaw SDK usługi Azure Maps Web.
author: philmea
ms.author: philmea
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7671d07a468a9f67a4851ec828fe18896d7a6c66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334278"
---
# <a name="add-a-simple-data-layer"></a>Dodawanie prostej warstwy danych

Przestrzenny moduł We/Wy zapewnia `SimpleDataLayer` klasę. Ta klasa ułatwia renderowanie stylizowanych obiektów na mapie. Może nawet renderować zestawy danych, które mają właściwości stylu i zestawy danych, które zawierają typy geometrii mieszanej. Prosta warstwa danych osiąga tę funkcję, owijając wiele warstw renderowania i używając wyrażeń stylu. Wyrażenia stylu wyszukują typowe właściwości stylu obiektów wewnątrz tych zawiniętych warstw. Funkcja `atlas.io.read` i `atlas.io.write` funkcja używają tych właściwości do odczytu i zapisu stylów w obsługiwanym formacie pliku. Po dodaniu właściwości do obsługiwanego formatu pliku plik może być używany do różnych celów. Na przykład plik może służyć do wyświetlania stylizowanych obiektów na mapie.

Oprócz funkcji stylizacji, `SimpleDataLayer` zapewnia wbudowaną funkcję wyskakującego z szablonem wyskakującym okienkiem. Okno podręczne jest wyświetlane po kliknięciu obiektu. Domyślna funkcja wyskakujących okienka może być wyłączona, jeśli to możliwe. Ta warstwa obsługuje również dane klastrowane. Po kliknięciu klastra mapa powiększy klaster i rozszerzy go na poszczególne punkty i podklastery.

Klasa `SimpleDataLayer` jest przeznaczona do użycia w dużych zestawach danych z wieloma typami geometrii i wieloma stylami zastosowanymi w elementów. Gdy jest używana, ta klasa dodaje obciążenie sześciu warstw zawierających wyrażenia stylu. Tak więc zdążyń, gdy bardziej efektywne jest użycie podstawowych warstw renderowania. Na przykład użyj warstwy rdzenia, aby renderować kilka typów geometrii i kilka stylów na elementów

## <a name="use-a-simple-data-layer"></a>Korzystanie z prostej warstwy danych

Klasa `SimpleDataLayer` jest używana podobnie jak inne warstwy renderowania są używane. Poniższy kod pokazuje, jak używać prostej warstwy danych na mapie:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Dodaj funkcje do źródła danych. Następnie prosta warstwa danych dowiesz się, jak najlepiej renderować obiekty. Style poszczególnych operacji można ustawić jako właściwości operacji. Poniższy kod przedstawia funkcję punktu GeoJSON z właściwością ustawioną `color` na `red`. 

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

Poniższy kod renderuje powyższą funkcję punktu przy użyciu prostej warstwy danych. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Korzystanie z prostej warstwy danych" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Zobacz pióro <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>Użyj prostej warstwy danych</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

Prawdziwa moc prostej warstwy danych pojawia się, gdy:

- Istnieje kilka różnych typów funkcji w źródle danych; Lub
- Funkcje w zestawie danych mają kilka właściwości stylu indywidualnie ustawione na nich; Lub
- Nie masz pewności, co dokładnie zawiera zestaw danych.

Na przykład podczas analizowania danych XML można nie znać dokładnych stylów i typów geometrii obiektów. W poniższym przykładzie przedstawiono moc prostej warstwy danych, renderując funkcje pliku KML. Pokazuje również różne opcje, które zapewnia klasa warstwy proste dane.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Proste opcje warstwy danych" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Zobacz <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>opcje warstwy danych</a> Pióro proste przez Usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Ta prosta warstwa danych używa klasy [szablonu wyskakujących](map-add-popup.md#add-popup-templates-to-the-map) do wyświetlania odnośników KML lub właściwości funkcji jako tabeli. Domyślnie cała zawartość renderowana w wyskakującym okienku zostanie włączona w trybie piaskownicy elementu iframe jako funkcja zabezpieczeń. Istnieją jednak ograniczenia:
>
> - Wszystkie skrypty, formularze, blokada wskaźnika i górna funkcja nawigacji są wyłączone. Linki mogą otwierać się na nowej karcie po kliknięciu. 
> - Starsze przeglądarki, które nie `srcdoc` obsługują parametru w elementów iframe, będą ograniczone do renderowania niewielkiej ilości zawartości.
> 
> Jeśli ufasz, że dane są ładowane do wyskakujących okienk i potencjalnie chcesz, aby te skrypty ładowane do wyskakujących okienka mogły uzyskać dostęp do aplikacji, możesz wyłączyć to, ustawiając opcję szablonów wyskakujących `sandboxContent` na false. 

## <a name="default-supported-style-properties"></a>Domyślne obsługiwane właściwości stylu

Jak wspomniano wcześniej, prosta warstwa danych zawija kilka warstw renderowania rdzenia: bąbelk, symbol, linię, wielokąt i wyciągnięty wielokąt. Następnie używa wyrażeń do wyszukiwania prawidłowych właściwości stylu dla poszczególnych obiektów.

Usługi Azure Maps i Właściwości stylu GitHub to dwa główne zestawy obsługiwanych nazw właściwości. Większość nazw właściwości różnych opcji warstwy map azure są obsługiwane jako właściwości stylu obiektów w prostej warstwie danych. Wyrażenia zostały dodane do niektórych opcji warstwy do obsługi nazw właściwości stylu, które są powszechnie używane przez GitHub. Te nazwy właściwości są definiowane przez [obsługę map GeoJSON usługi GitHub](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)i są używane do stylizacji plików GeoJSON, które są przechowywane i renderowane w ramach platformy. Wszystkie właściwości stylizowania gitHub są obsługiwane w prostej `marker-symbol` warstwie danych, z wyjątkiem właściwości stylizujących.

Jeśli czytelnik natknie się na właściwości mniej wspólnego stylu, przekonwertuje go do najbliższej właściwości stylu usługi Azure Maps. Ponadto domyślne wyrażenia stylu można zastąpić za pomocą `getLayers` funkcji prostej warstwy danych i aktualizując opcje na dowolnej warstwie.

W poniższych sekcjach znajdują się szczegółowe informacje na temat domyślnych właściwości stylu obsługiwanych przez prostą warstwę danych. Kolejność obsługiwanej nazwy właściwości jest również priorytetem właściwości. Jeśli dla tej samej opcji warstwy zdefiniowano dwie właściwości stylu, pierwsza z nich ma wyższy priorytet.

### <a name="bubble-layer-style-properties"></a>Właściwości stylu warstwy bąbelkowej

Jeśli obiekt jest `Point` lub `MultiPoint`, a funkcja nie `image` ma właściwości, która będzie używana jako niestandardowa ikona do renderowania punktu `BubbleLayer`jako symbolu, funkcja zostanie renderowana za pomocą pliku .

| Warstwa, opcja | Nazwa(y) obsługiwanych właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup> `marker-size`, <sup>2</sup>, `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size` Wartości `scale` i wartości są uważane za wartości skalarne i zostaną pomnożone przez`8`

\[2\] Jeśli opcja `marker-size` GitHub jest określona, następujące wartości będą używane dla promienia.

| Rozmiar znacznika | Promień |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Klastry są również renderowane przy użyciu warstwy bąbelkowej. Domyślnie promień klastra jest `16`ustawiony na . Kolor klastra różni się w zależności od liczby punktów w klastrze, zgodnie z poniższą definicją:

| Liczba punktów | Kolor    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt;10     | `green`  |

### <a name="symbol-style-properties"></a>Właściwości stylu symbolu

Jeśli obiekt jest `Point` lub `MultiPoint`lub , i `image` funkcja i ma właściwość, która będzie używana jako ikona niestandardowa do `SymbolLayer`renderowania punktu jako symbol, funkcja zostanie renderowana za pomocą .

| Warstwa, opcja | Nazwa(y) obsługiwanych właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] Jeśli określono `marker-size` opcję GitHub, dla opcji rozmiaru ikony zostaną użyte następujące wartości.

| Rozmiar znacznika | Rozmiar symboli |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Jeśli funkcja punktu jest klastrem, `point_count_abbreviated` właściwość będzie renderowana jako etykieta tekstowa. Żaden obraz nie zostanie renderowany.

### <a name="line-style-properties"></a>Właściwości stylu linii

Jeśli funkcja jest `LineString` `MultiLineString`, `Polygon`, `MultiPolygon`, lub , to funkcja `LineLayer`zostanie renderowana za pomocą .

| Warstwa, opcja | Nazwa(y) obsługiwanych właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Właściwości stylu wielokąta

Jeśli funkcja jest `Polygon` lub `MultiPolygon`a , a funkcja albo `height` nie ma `height` właściwości lub właściwość wynosi zero, `PolygonLayer`funkcja zostanie renderowana za pomocą .

| Warstwa, opcja | Nazwa(y) obsługiwanych właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Właściwości stylu trędowatego wielokąta

Jeśli funkcja jest `Polygon` lub `MultiPolygon`, i `height` ma właściwość o wartości większej niż 0, `PolygonExtrusionLayer`funkcja zostanie renderowana za pomocą .

| Warstwa, opcja | Nazwa(y) obsługiwanych właściwości | Wartość domyślna |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Warstwa simpledata](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions (Proste warstwy danych)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Odczytywanie i zapisywanie danych przestrzennych](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy mapy OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Łączenie się z usługą WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Wykorzystanie podstawowych operacji](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)
