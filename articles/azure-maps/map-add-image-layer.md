---
title: Dodawanie warstwy obrazu do Azure Maps | Microsoft Docs
description: Jak dodać warstwę obrazu do Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fadaaf7c64b11a6d6d94c68234f8288d1b3f8d07
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480492"
---
# <a name="add-an-image-layer-to-a-map"></a>Dodawanie warstwy obrazu do mapy

W tym artykule pokazano, jak można nałożyć obraz na stały zestaw współrzędnych na mapie. Istnieje wiele scenariuszy, w których zostanie wykonany nałożenie obrazu na mapie. Poniżej przedstawiono kilka przykładów typu obrazów, które często są nałożone na mapy;

* Obrazy przechwycone z dronom.
* Kompilowanie Floorplans.
* Historyczne i inne wyspecjalizowane obrazy map.
* Plany dotyczące lokacji zadań.
* Obrazy radarowe.

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) to prosty sposób na rozłożenie obrazu na mapie. Jeśli jednak obraz jest duży, przeglądarka może ulec załadowaniu. W takim przypadku Rozważ przerwanie obrazu do kafelków i załadowanie ich do mapy jako [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

Warstwa obrazu obsługuje następujące formaty obrazów:

- JPEG
- PNG
- BMP
- GIF (bez animacji)

## <a name="add-an-image-layer"></a>Dodawanie warstwy obrazów

Poniższy kod nakłada obraz [mapy Newark nowej Jersey z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapie. [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) jest tworzony przez przekazanie adresu URL do obrazu i współrzędne dla czterech rogów w formacie `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

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

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Prosta warstwa obrazu' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/eQodRo/'>prostą warstwą obrazu</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-ground-overlay"></a>Importowanie nakładki uziemienia KML

Ten przykład pokazuje, jak nałożyć KMLe informacje o nakładki naziemnej jako warstwę obrazu na mapie. Nakładki naziemne KML zapewniają współrzędne północne, Południowe, wschodnie i zachodnie oraz obrót z ruchem przeciwnym, natomiast warstwa obrazu oczekuje współrzędnych dla każdego rogu obrazu. KML nakładki naziemnej w tym przykładzie jest Chartres Cathedral i pochodzący z [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

Poniższy kod używa funkcji statycznej `getCoordinatesFromEdges` klasy [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) w celu obliczenia czterech rogów obrazu z poziomu nakładki naziemnej, na terenie północy, Południowo-Wschodnia, zachodniej i rotacji.

<br/>

<iframe height='500' scrolling='no' title='KML nakładki jako warstwa obrazu' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z KML piórem, <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>jak warstwa obrazu</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Dostosowywanie warstwy obrazu

Warstwa obrazu ma wiele opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy obrazu' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Opcje warstwy obrazu</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodaj warstwę kafelków](./map-add-tile-layer.md)