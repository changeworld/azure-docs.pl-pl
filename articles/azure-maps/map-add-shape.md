---
title: Dodawanie warstwy wielokąta do mapy | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak renderować wielokąt i wielokąt na mapie w sdku microsoft azure maps web.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9191f054ca3c7374bcbc7bec46573289a512612c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535056"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Dodawanie warstwy wielokąta do mapy

W tym artykule pokazano, `Polygon` jak `MultiPolygon` renderować geometrie i geometrie operacji na mapie przy użyciu warstwy wielokąta. Zestaw Azure Maps Web SDK obsługuje również tworzenie geometrii okręgu zgodnie z definicją w [rozszerzonym schemacie GeoJSON](extend-geojson.md#circle). Okręgi te są przekształcane w wielokąty po renderowaniu na mapie. Wszystkie geometrie operacji można łatwo aktualizować po zawinięciu [atlasem. Klasa kształtu.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-layer"></a>Używanie warstwy wielokąta 

Gdy warstwa wielokąta jest połączona ze źródłem danych i załadowana na mapę, renderuje obszar `Polygon` i `MultiPolygon` obiekty. Aby utworzyć wielokąt, dodaj go do źródła danych i renderuj warstwą wielokąta przy użyciu klasy [PolygonLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Poniżej znajduje się kompletny i uruchomiony przykład powyższego kodu.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie wielokąta do mapy ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>pióro Dodawanie wielokąta do</a> mapy<a href='https://codepen.io/azuremaps'>@azuremaps</a>przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Używanie warstwy wielokąta i liniowej razem

Warstwa liniowa służy do renderowania konturu wielokątów. Poniższy przykład kodu renderuje wielokąt jak w poprzednim przykładzie, ale teraz dodaje warstwę linii. Ta warstwa liniowa jest drugą warstwą połączoną ze źródłem danych.  

<iframe height='500' scrolling='no' title='Wielokąt i warstwa linii w celu dodania wielokąta' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>wielokąt pióra i warstwę linii, aby dodać wielokąt</a> według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Wypełnianie wielokąta wzorem

Oprócz wypełnienia wielokąta kolorem można użyć wzoru obrazu, aby wypełnić wielokąt. Załaduj wzorzec obrazu do zasobów sprite'a obrazu map, a następnie odnieś się do tego obrazu `fillPattern` właściwością warstwy wielokąta.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wzór wypełnienia wielokątem" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>wzorzec wypełnienia polygon</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pióra przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>


> [!TIP]
> Zestaw SDK sieci Web usługi Azure Maps udostępnia kilka konfigurowalnych szablonów obrazów, których można używać jako wzorców wypełnienia. Aby uzyskać więcej informacji, zobacz [dokument Jak używać szablonów obrazów.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-polygon-layer"></a>Dostosowywanie warstwy wielokąta

Warstwa wielokąta ma tylko kilka opcji stylizacji. Oto narzędzie, aby je wypróbować.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Dodawanie okręgu do mapy

Usługa Azure Maps używa rozszerzonej wersji schematu GeoJSON, która zawiera definicję okręgów, jak wspomniano [tutaj.](extend-geojson.md#circle) Okrąg jest renderowany na `Point` mapie przez utworzenie obiektu. Ma `Point` `subType` właściwość o wartości `"Circle"` i `radius` właściwości z liczbą reprezentującą promień w metrach. 

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

Zestaw Azure Maps Web SDK konwertuje te `Point` funkcje na `Polygon` funkcje. Następnie te obiekty są renderowane na mapie przy użyciu warstw wielokątowych i liniowych, jak pokazano w poniższym przykładzie kodu.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie okręgu do mapy' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>pióro Dodawanie okręgu do</a> mapy<a href='https://codepen.io/azuremaps'>@azuremaps</a>przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Ułatwianie aktualizowania geometrii

Klasa `Shape` zawija [geometrię](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) lub [operację](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) i ułatwia aktualizowanie i utrzymywanie tych funkcji. Aby utworzyć wystąpienie zmiennej kształtu, przekaż geometrię lub zestaw właściwości do konstruktora kształtu.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Poniższy przykład kodu pokazuje, jak zawinąć okrąg GeoJSON obiektu z klasy kształtu. Wraz ze zmianą wartości promienia w kształcie okrąg jest renderowany automatycznie na mapie.

<br/>

<iframe height='500' scrolling='no' title='Aktualizowanie właściwości kształtu' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>właściwości kształtu Aktualizacji</a> pióra według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Wielokąt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Warstwa wielokąta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions (PolygonLayerOptions)](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

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
> [Dodawanie warstwy linii](map-add-line-layer.md)

Dodatkowe zasoby:

> [!div class="nextstepaction"]
> [Rozszerzenie specyfikacji usługi Azure Maps GeoJSON](extend-geojson.md#circle)