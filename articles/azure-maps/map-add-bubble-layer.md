---
title: Dodawanie warstwy bąbelkowej do mapy | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak dodać warstwę Bąbelków do mapy przy użyciu zestawu SDK w sieci Web map platformy Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7ae11734eb804715f3eb1b5edcb02fc328dafec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77208560"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Dodawanie warstwy bąbelkowej do mapy

W tym artykule pokazano, jak renderować dane punktowe ze źródła danych jako warstwę bąbelkową na mapie. Warstwy bąbelkowe renderują punkty jako okręgi na mapie o stałym promieniu pikseli. 

> [!TIP]
> Warstwy bąbelkowe domyślnie renderują współrzędne wszystkich geometrii w źródle danych. Aby ograniczyć warstwę w taki sposób, aby `filter` renderować tylko `['==', ['geometry-type'], 'Point']` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` obiekty geometrii punktowej, należy ustawić właściwość warstwy lub jeśli mają być również obiekty MultiPoint.

## <a name="add-a-bubble-layer"></a>Dodawanie warstwy bąbelkowej

Poniższy kod ładuje tablicę punktów do źródła danych. Następnie łączy punkty danych z [warstwą bąbelkową](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest). Warstwa bąbelkowa renderuje promień każdego bąbelka z pięcioma pikselami i białym kolorem wypełnienia. I kolor obrysu niebieskiego i szerokość obrysu sześciu pikseli. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Poniżej znajduje się kompletny przykładowy kod uruchomiony powyższej funkcji.

<br/>

<iframe height='500' scrolling='no' title='Źródło danych warstwy bąbelkej' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz Źródło <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>danych warstwy bąbelkowej pióra</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Pokazywanie etykiet z warstwą bąbelkową

Ten kod pokazuje, jak używać warstwy bąbelkowej do renderowania punktu na mapie. I jak użyć warstwy symbolu do renderowania etykiety. Aby ukryć ikonę warstwy `image` symboli, ustaw `'none'`właściwość opcji ikony na .

<br/>

<iframe height='500' scrolling='no' title='Źródło danych multiwarstwowych' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>Pióro MultiLayer DataSource</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Dostosowywanie warstwy bąbelkowej

Warstwa Bąbelkowa ma tylko kilka opcji stylizacji. Oto narzędzie, aby je wypróbować.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy bąbelkowej' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>opcje warstwy bąbelkowej</a> pióra według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Warstwa bąbelkowa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions (BubbleLayerOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
