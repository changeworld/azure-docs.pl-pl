---
title: Dodaj warstwę wielokątów wytłoczenia do Azure Maps | Microsoft Docs
description: Jak dodać warstwę wielokątów wytłoczenia do zestawu SDK sieci Web Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e6858359549f6a54513eda7bc692adcbc7d7e71b
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484339"
---
# <a name="add-an-extrusion-polygon-layer-to-the-map"></a>Dodaj warstwę wielokątów wytłoczenia do mapy

W tym artykule pokazano, jak za pomocą warstwy wytłoczenia wielokąta renderować obszary `Polygon` i `MultiPolygon` funkcji geometrie jako kształty wyciągnięcia na mapie. Zestaw SDK sieci Web Azure Maps obsługuje również tworzenie okręgów geometrie zgodnie z definicją w [schemacie rozszerzonego kodu JSON](extend-geojson.md#circle). Te okręgi są przekształcane na wielokąty, gdy są renderowane na mapie. Wszystkie funkcje geometrie można także łatwo aktualizować, jeśli zawinięte z [Atlasem. Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) — Klasa.


## <a name="use-a-polygon-extrusion-layer"></a>Użyj warstwy wytłoczenia wielokątu

Gdy [warstwa wytłoczenia wielokąta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) jest połączona ze źródłem danych i załadowana na mapie, renderuje obszary `Polygon` i `MultiPolygon` funkcji jako kształty wyciągnięcia. Właściwości `height` i `base` warstwy wytłoczenia wielokąta definiują odległość bazową od powierzchni i wysokości naciągnięcia kształtu w **licznikach**. Poniższy kod przedstawia sposób tworzenia wielokątów, dodawania go do źródła danych i renderowania przy użyciu klasy warstwy wytłoczenia wielokąta.

> [!Note]
> Wartość `base` zdefiniowana w warstwie wytłoczenia wielokąta powinna być mniejsza lub równa liczbie `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wytłaczany Wielokąt" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>Wielokąt</a> wyciągać się z piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-multipolygons"></a>Dodawanie wielokątów opartych na danych

Mapę na mapie choropleth można renderować przy użyciu warstwy wytłoczenia Wielokąt, ustawiając jej `height` i `fillColor` właściwości proporcjonalnie do miary zmiennej statystycznej w `Polygon` i `MultiPolygon` funkcji geometrie. Poniższy przykładowy kod przedstawia wytłaczaną mapę na mapie choropleth U. S na podstawie pomiaru gęstości populacji według stanu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa na mapie choropleth wytłaczana" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/eYYYNox'>na mapie choropleth Azure Maps map</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Dodaj okrąg do mapy

Azure Maps używa rozszerzonej wersji schematu GEOJSON, który zawiera definicję okręgów, jak pokazano [tutaj](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). Wykreślone koło może być renderowane na mapie, tworząc `point` funkcję z właściwością `subType` `Circle` oraz numerowaną Właściwość `Radius` reprezentującą promień w **licznikach**. Na przykład:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps Web SDK konwertuje te funkcje `Point` na funkcje `Polygon` pod okapem i mogą być renderowane na mapie przy użyciu warstwy wytłoczenia Wielokąt, jak pokazano w poniższym przykładzie kodu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wielokąt drona miejsca" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>wielokąta dronaego miejsca w miejscu</a> dla pióra Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Dostosuj warstwę wytłoczenia wielokąta

Warstwa wytłoczenia wielokątów kilka opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [warstwa wytłoczenia wielokątu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Zasoby dodatkowe:

> [!div class="nextstepaction"]
> [Rozszerzenie specyfikacji GEOJSON Azure Maps](extend-geojson.md#circle)
