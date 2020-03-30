---
title: Dodawanie warstwy linii do mapy | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak dodać warstwę linii do mapy przy użyciu zestawu SDK sieci Web microsoft azure maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8503b12be628fe7d5651221c9d0379bee3e292bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76933481"
---
# <a name="add-a-line-layer-to-the-map"></a>Dodawanie warstwy linii do mapy

Warstwa liniowa może `LineString` służyć `MultiLineString` do renderowania i obiektów jako ścieżek lub tras na mapie. Warstwa liniowa może być również `Polygon` używana `MultiPolygon` do renderowania konturu i operacji. Źródło danych jest połączone z warstwą linii, aby zapewnić jej dane do renderowania. 

> [!TIP]
> Warstwy liniowe domyślnie renderują współrzędne wielokątów, a także wierszy w źródle danych. Aby ograniczyć warstwę w taki sposób, że `filter` renderuje tylko `['==', ['geometry-type'], 'LineString']` `['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]` funkcje LineString ustawić właściwość warstwy lub jeśli chcesz dołączyć MultiLineString obiektów, jak również.

Poniższy kod pokazuje, jak utworzyć wiersz. Dodaj wiersz do źródła danych, a następnie renderuj ją za pomocą warstwy liniowej przy użyciu klasy [LineLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a line and add it to the data source.
dataSource.add(new atlas.data.LineString([[-73.972340, 40.743270], [-74.004420, 40.756800]]));
  
//Create a line layer to render the line to the map.
map.layers.add(new atlas.layer.LineLayer(dataSource, null, {
    strokeColor: 'blue',
    strokeWidth: 5
}));
```

Poniżej znajduje się kompletny przykładowy kod uruchomiony powyższej funkcji.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie wiersza do mapy' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/qomaKv/'>pióro Dodawanie wiersza do</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>mapy przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

Warstwy linii można stylizować za pomocą [funkcji LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) i [użyj wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md).

## <a name="add-symbols-along-a-line"></a>Dodawanie symboli wzdłuż linii

W tym przykładzie pokazano, jak dodać ikony strzałek wzdłuż linii na mapie. W przypadku korzystania z warstwy symbolu należy ustawić opcję "położenie" na "linia". Ta opcja spowoduje renderowanie symboli wzdłuż linii i obracanie ikon (0 stopni = w prawo).

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pokaż strzałkę wzdłuż linii" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/drBJwX/'>strzałkę Pokaż pióro</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>wzdłuż linii przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

> [!TIP]
> Zestaw SDK sieci Web usługi Azure Maps udostępnia kilka konfigurowalnych szablonów obrazów, których można używać z warstwą symboli. Aby uzyskać więcej informacji, zobacz [dokument Jak używać szablonów obrazów.](how-to-use-image-templates-web-sdk.md)

<a name="line-stroke-gradient"></a>

## <a name="add-a-stroke-gradient-to-a-line"></a>Dodawanie gradientu obrysu do linii

Do linii można zastosować kolor pojedynczego obrysu. Można również wypełnić linię gradientem kolorów, aby wyświetlić przejście z jednego segmentu linii do następnego segmentu linii. Na przykład gradienty linii mogą być używane do reprezentowania zmian w czasie i odległości lub różnych temperatur w połączonej linii obiektów. Aby zastosować tę funkcję do wiersza, źródło `lineMetrics` danych musi mieć ustawioną opcję true, a `strokeColor` następnie wyrażenie gradientu kolorów może zostać przekazane do opcji wiersza. Wyrażenie gradientu obrysu `['line-progress']` musi odwoływać się do wyrażenia danych, które udostępnia obliczone metryki linii do wyrażenia.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Linia z gradientem obrysu" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz wiersz pióra <a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>z gradientem obrysu</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-line-layer"></a>Dostosowywanie warstwy linii

Warstwa Linia ma kilka opcji stylizacji. Oto narzędzie, aby je wypróbować.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy linii' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>opcje warstwy linii</a> pióra według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Warstwa linii](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 

> [!div class="nextstepaction"]
> [LineLayerOptions (LineLayerOptions)](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie menu podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Jak używać szablonów obrazów](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)
