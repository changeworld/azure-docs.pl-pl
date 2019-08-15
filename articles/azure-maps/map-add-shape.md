---
title: Dodaj warstwę wielokąta do Azure Maps | Microsoft Docs
description: Jak dodać warstwę wielokąta do Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ca6c0f5e6fde5a31655ed17f4a016bf44216643f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976137"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Dodawanie warstwy wielokąta do mapy

W tym artykule pokazano, jak renderować obszary `Polygon` i `MultiPolygon` funkcje geometrie na mapie przy użyciu warstwy wielokąta. Zestaw SDK sieci Web Azure Maps obsługuje również tworzenie okręgów geometrie zgodnie z definicją w [schemacie rozszerzonego kodu JSON](extend-geojson.md#circle). Te okręgi są przekształcane na wielokąty, gdy są renderowane na mapie. Wszystkie funkcje geometrie można także łatwo aktualizować, jeśli zawinięte z [Atlasem. Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) — Klasa.

## <a name="use-a-polygon-layer"></a>Użyj warstwy wielokąta 

Gdy warstwa wielokątów jest połączona ze źródłem danych i załadowana na mapie, renderuje obszar `Polygon` funkcji i. `MultiPolygon` Poniższy kod przedstawia sposób tworzenia wielokątów, dodawania go do źródła danych i renderowania za pomocą warstwy wielokąta przy użyciu klasy [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) .

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

//Create and add a polygon layer to render the polygon to the map.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    opacaty: 0.5
}));
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie wielokąta do mapy ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Dodaj wielokąt do mapy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Użyj jednocześnie wielokąta i warstwy liniowej

Warstwa linii może służyć do renderowania konspektu wielokątów. Poniższy przykład kodu renderuje Wielokąt podobny do poprzedniego, ale teraz dodaje warstwę linii jako drugą warstwę połączoną ze źródłem danych.  

<iframe height='500' scrolling='no' title='Wielokąt i warstwa linii w celu dodania wielokąta' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz Wielokąt pióra <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>i warstwę linii, aby dodać Wielokąt</a> o Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Wypełnienie wielokąta z wzorcem

Oprócz wypełniania wielokątów kolorem można również użyć wzorca obrazu. Załaduj wzorzec obrazu do zasobów ikon obrazu mapy, a następnie odwołuje się do tego `fillPattern` obrazu za pomocą właściwości warstwy Wielokąt.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Deseń wypełnienia wielokąta" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>wzorzec wypełnienia wielokąta</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Zestaw SDK sieci Web Azure Maps udostępnia kilka dostosowywalnych szablonów obrazów, których można użyć jako wzorców wypełnienia. Aby uzyskać więcej informacji, zobacz dokument [jak korzystać z szablonów obrazów](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-polygon-layer"></a>Dostosowywanie warstwy wielokątów

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

Zestaw SDK sieci Web Azure Maps konwertuje `Point` te funkcje `Polygon` na funkcje w ramach okładek i mogą być renderowane na mapie przy użyciu warstw wielokątów i linii, jak pokazano w poniższym przykładzie kodu.

<br/>

<iframe height='500' scrolling='no' title='Dodaj okrąg do mapy' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Dodaj okrąg do mapy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Łatwa aktualizacja geometrii

Klasa otacza geometrię lub [funkcję](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) i ułatwia ich aktualizowanie i konserwowanie. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) `Shape` Kształt można utworzyć, przekazując dane geometryczne i zestaw właściwości lub przekazując funkcję, jak pokazano w poniższym kodzie.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Poniższy przykład kodu pokazuje, jak otoczyć obiekt GEOJSON okręgu z klasą Shape i łatwo zaktualizować jego właściwość RADIUS przy użyciu suwaka. Gdy wartość promień zmieni się w kształcie, renderowanie okręgu jest automatycznie aktualizowane na mapie.

<br/>

<iframe height='500' scrolling='no' title='Aktualizowanie właściwości kształtu' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>właściwościami kształtu</a> Dodaj pióro według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do usługi Maps, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie okna podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Jak używać szablonów obrazów](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę linii](map-add-line-layer.md)

Dodatkowe zasoby:

> [!div class="nextstepaction"]
> [Rozszerzenie specyfikacji GEOJSON Azure Maps](extend-geojson.md#circle)