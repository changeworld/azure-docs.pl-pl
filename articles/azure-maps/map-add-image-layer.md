---
title: Dodawanie warstwy obrazu do mapy | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak nałożyć obraz na mapę przy użyciu sdk Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209719"
---
# <a name="add-an-image-layer-to-a-map"></a>Dodawanie warstwy obrazu do mapy

W tym artykule pokazano, jak nałożyć obraz na stały zestaw współrzędnych. Oto kilka przykładów różnych typów obrazów, które można nałożone na mapy:

* Obrazy zrobione z dronów
* Plany budowy podłóg
* Historyczne lub inne specjalistyczne obrazy map
* Plany miejsc pracy
* Obrazy radarów pogodowych

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) to prosty sposób nałkowania obrazu na mapę. Należy pamiętać, że przeglądarki mogą mieć trudności z załadowaniem dużego obrazu. W takim przypadku należy rozważyć rozbicie obrazu na kafelki i załadowanie go do mapy jako [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Warstwa obrazu obsługuje następujące formaty obrazów:

- JPEG
- PNG
- BMP
- GIF (bez animacji)

## <a name="add-an-image-layer"></a>Dodawanie warstwy obrazów

Poniższy kod zawiera na mapie [mapę Newark w stanie New Jersey z 1922 roku.](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) [Warstwa obrazu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) jest tworzona przez przekazanie adresu URL do obrazu i `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`współrzędne czterech narożników w formacie .

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

Oto kompletny przykładowy kod uruchomiony poprzedniego kodu.

<br/>

<iframe height='500' scrolling='no' title='Prosta warstwa obrazu' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz prostą <a href='https://codepen.io/azuremaps/pen/eQodRo/'>warstwę obrazu</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pióra według usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importowanie pliku KML jako nakładki gruntu

W tym przykładzie pokazano, jak dodać informacje o nakładce gruntu KML jako warstwę obrazu na mapie. Nakładki naziemne KML zapewniają współrzędne północne, południowe, wschodnie i zachodnie oraz obrót w kierunku przeciwnym do ruchu wskazówek zegara. Ale warstwa obrazu oczekuje współrzędnych dla każdego rogu obrazu. Nakładka gruntu KML w tym przykładzie jest dla katedry Chartres, i to pochodzi z [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Kod używa funkcji statycznej `getCoordinatesFromEdges` z [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) klasy. Oblicza cztery narożniki obrazu przy użyciu informacji o północy, południu, wschodzie, zachodzie i rotacji nakładki gruntu KML.

<br/>

<iframe height='500' scrolling='no' title='Nakładka gruntu KML jako warstwa obrazu' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz nakładkę naziemną pióra <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML jako warstwę obrazu</a> według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Dostosowywanie warstwy obrazu

Warstwa obrazu ma wiele opcji stylizacji. Oto narzędzie do ich wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy obrazu' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>opcje warstwy obrazu</a> pióra według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Warstwa obrazu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions (Pliki graficzne)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Dodawanie warstwy kafelków](./map-add-tile-layer.md)