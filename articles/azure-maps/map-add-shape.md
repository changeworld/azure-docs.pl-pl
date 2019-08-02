---
title: Dodawanie kształtu z Azure Maps | Microsoft Docs
description: Jak dodać kształt do mapy JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0696eba4f3cca7beedc2efcda0182ab82b3d69d9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638693"
---
# <a name="add-a-shape-to-a-map"></a>Dodawanie kształtu do mapy

W tym artykule pokazano, jak renderować geometrie na mapie przy użyciu warstw linii i wielokątów. Zestaw SDK sieci Web Azure Maps obsługuje również tworzenie okręgów geometrie zgodnie z definicją w [schemacie rozszerzonego kodu JSON](extend-geojson.md#circle). Wszystkie funkcje geometrie można także łatwo aktualizować, jeśli są opakowane z klasą [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>Dodawanie wierszy do mapy

`LineString`i `MultiLineString` funkcje są używane do reprezentowania ścieżek i konspektów na mapie.

### <a name="add-a-line"></a>Dodaj wiersz

<iframe height='500' scrolling='no' title='Dodawanie linii do mapy' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Dodaj linię do mapy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pierwszy blok kodu w kodzie powyżej konstruuje obiekt mapy. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

W drugim bloku kodu obiekt źródła danych jest tworzony przy użyciu klasy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Obiekt [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) jest tworzony i dodawany do źródła danych.

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) renderuje obiekty liniowe otoczone w [źródle danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest). Ostatni blok kodu tworzy i dodaje warstwę linii do mapy. Zobacz właściwości warstwy linii pod adresem [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Źródło danych i warstwa linii są tworzone i dodawane do mapy w ramach [programu obsługi zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby upewnić się, że linia jest wyświetlana po całkowitym załadowaniu mapy.

### <a name="add-symbols-along-a-line"></a>Dodaj symbole wzdłuż linii

Ten przykład pokazuje, jak dodać ikony strzałek wzdłuż linii na mapie. W przypadku korzystania z warstwy symboli ustaw opcję "położenie" na "wiersz", co spowoduje renderowanie symboli wzdłuż linii i obrócenie ikon (0 stopni = Right).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pokaż strzałkę wzdłuż linii" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/drBJwX/'>strzałka Pokaż strzałkę obok pozycji</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="line-stroke-gradient"></a>Dodawanie gradientu obrysu do linii

Oprócz możliwości zastosowania jednego koloru pociągnięcia do linii można także wypełnić linię gradientem kolorów, aby pokazać przejście z jednego segmentu linii do następnego. Na przykład gradienty linii mogą służyć do reprezentowania zmian w czasie i odległości lub w różnych temperaturach w połączonej linii obiektów. Aby można było zastosować tę funkcję do wiersza, do źródła danych musi `lineMetrics` być ustawiona opcja true, a następnie wyrażenie gradientu koloru może być przesyłane `strokeColor` do opcji wiersza. Wyrażenie gradientu obrysu musi odwoływać `['line-progress']` się do wyrażenia danych, które uwidacznia obliczane metryki linii dla wyrażenia.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linia z gradientem pociągnięcia" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz linię pióra <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>z gradientem pociągnięcia</a> ,<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-line-layer"></a>Dostosowywanie warstwy liniowej

Warstwa linii kilka opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy linii' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>Opcje warstwy</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>Dodawanie wielokąta do mapy

`Polygon`funkcje `MultiPolygon` i są często używane do reprezentowania obszaru na mapie. 

### <a name="use-a-polygon-layer"></a>Użyj warstwy wielokąta 

Warstwa wielokątów renderuje obszar wielokąta. 

<iframe height='500' scrolling='no' title='Dodawanie wielokąta do mapy ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Dodaj wielokąt do mapy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

W drugim bloku kodu obiekt źródła danych jest tworzony przy użyciu klasy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . [Wielokąt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) jest tworzony na podstawie tablicy współrzędnych i dodanych do źródła danych. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderuje dane opakowane w [źródle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) danych na mapie. Ostatni blok kodu tworzy i dodaje warstwę wielokątów do mapy. Zobacz właściwości warstwy wielokąta w [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Źródło danych i warstwa wielokątów są tworzone i dodawane do mapy w ramach [programu obsługi zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby mieć pewność, że Wielokąt jest wyświetlany po całkowitym załadowaniu mapy.

### <a name="use-a-polygon-and-line-layer-together"></a>Użyj jednocześnie wielokąta i warstwy liniowej

Warstwa linii może służyć do renderowania konturu wielokąta. 

<iframe height='500' scrolling='no' title='Wielokąt i warstwa linii w celu dodania wielokąta' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz Wielokąt pióra <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>i warstwę linii, aby dodać Wielokąt</a> o Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() w <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

W drugim bloku kodu obiekt źródła danych jest tworzony przy użyciu klasy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . [Wielokąt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) jest tworzony na podstawie tablicy współrzędnych i dodanych do źródła danych. 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderuje dane opakowane w [źródle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) danych na mapie. Zobacz właściwości warstwy wielokąta w [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) jest tablicą wierszy. Zobacz właściwości warstwy linii pod adresem [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest). Trzeci blok kodu tworzy Wielokąt i linie warstwowe.

Ostatni blok kodu dodaje Wielokąt i warstwy linii do mapy. Źródło danych i warstwy są tworzone i dodawane do mapy w ramach [programu obsługi zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby mieć pewność, że Wielokąt zostanie wyświetlony po całkowitym załadowaniu mapy.

> [!TIP]
> Warstwy liniowe domyślnie będą renderować współrzędne wielokątów, jak również linie w źródle danych. Aby ograniczyć warstwę, która jest renderowana tylko w celu renderowania funkcji `filter` LineString, ustaw właściwość warstwy `['==', ['geometry-type'], 'LineString']` na `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` lub, jeśli chcesz również uwzględnić funkcje MultiLineString.

### <a name="fill-a-polygon-with-a-pattern"></a>Wypełnienie wielokąta z wzorcem

Oprócz wypełniania wielokątów kolorem można również użyć wzorca obrazu. Załaduj wzorzec obrazu do zasobów ikon obrazu mapy, a następnie odwołuje się do tego `fillPattern` obrazu za pomocą właściwości warstwy Wielokąt.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Deseń wypełnienia wielokąta" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>wzorzec wypełnienia wielokąta</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="customize-a-polygon-layer"></a>Dostosowywanie warstwy wielokątów

Warstwa wielokątów zawiera tylko kilka opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Dodaj okrąg do mapy

Azure Maps używa rozszerzonej wersji schematu GEOJSON, który zawiera definicję okręgów, jak pokazano [tutaj](extend-geojson.md#circle). Okrąg może być renderowany `Point` na mapie przez utworzenie funkcji, która `subType` ma `"Circle"` właściwość o wartości i `radius` właściwość, która ma liczbę reprezentującą promień w licznikach. Na przykład:

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Zestaw SDK sieci Web Azure Maps konwertuje `Point` te funkcje `Polygon` na funkcje w ramach okładek i mogą być renderowane na mapie przy użyciu warstw wielokątów i linii, jak pokazano poniżej.

<iframe height='500' scrolling='no' title='Dodaj okrąg do mapy' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Dodaj okrąg do mapy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pierwszy blok kodu w kodzie powyżej konstruuje obiekt mapy. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

W drugim bloku kodu obiekt źródła danych jest tworzony przy użyciu klasy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Okrąg jest [funkcją](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) `subType` i ma właściwość ustawioną na `"Circle"` i `radius` wartość właściwości w licznikach. Gdy funkcja punktu z `subType` elementu `"Circle"` jest dodawana do źródła danych, jest konwertowana na cykliczny Wielokąt w obrębie mapy.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderuje dane opakowane w [źródle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) danych na mapie. Ostatni blok kodu tworzy i dodaje warstwę wielokątów do mapy. Zobacz właściwości warstwy wielokąta w [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Źródło danych i warstwa wielokątów są tworzone i dodawane do mapy w ramach [programu obsługi zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby mieć pewność, że okrąg jest wyświetlany po całkowitym załadowaniu mapy.

## <a name="make-a-geometry-easy-to-update"></a>Łatwa aktualizacja geometrii

Klasa otacza geometrię lub [funkcję](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) i ułatwia ich aktualizowanie i konserwowanie. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) `Shape`
`new Shape(data: Feature<data.Geometry, any>)`konstruuje obiekt Shape i inicjuje go z określoną funkcją.

<br/>

<iframe height='500' scrolling='no' title='Aktualizowanie właściwości kształtu' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>właściwościami kształtu</a> Dodaj pióro według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pierwszy blok kodu powyżej konstruuje obiekt mapy. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Punkt jest [funkcją](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) klasy. Drugi blok kodu inicjuje wartość promienia elementu Slider HTML, a następnie konstruuje i otacza obiekt punktu w obiekcie klasy [kształtu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

Trzeci blok kodu tworzy funkcję, która przyjmuje wartość z elementu suwaka zakresu HTML i zmienia wartość promienia przy użyciu metody AddProperty klasy [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) Shape.

W czwartym bloku kodu obiekt źródła danych jest tworzony przy użyciu klasy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Punkt jest następnie dodawany do źródła danych.

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) renderuje dane opakowane w [źródle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) danych na mapie. Trzeci blok kodu tworzy warstwę wielokątów. Zobacz właściwości warstwy wielokąta w [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest). Źródło danych, program obsługi zdarzeń kliknięcia i warstwa wielokątów są tworzone i dodawane do mapy w ramach [programu obsługi zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby upewnić się, że punkt jest wyświetlany po całkowitym załadowaniu mapy.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów kodu do dodania do usługi Maps, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)
