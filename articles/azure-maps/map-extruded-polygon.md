---
title: Dodawanie warstwy wyciągnięcia wielokąta do mapy | Mapy platformy Microsoft Azure
description: Jak dodać warstwę wyciągnięcia wielokąta do sdk Microsoft Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334540"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Dodawanie warstwy wyciągnięcia wielokąta do mapy

W tym artykule pokazano, jak używać warstwy wyciągnięcia `Polygon` `MultiPolygon` wielokąta do renderowania obszarów geometrii i operacji jako kształtów wyciągniętych. Zestaw Azure Maps Web SDK obsługuje renderowanie geometrii okręgu zgodnie z definicją w [rozszerzonym schemacie GeoJSON](extend-geojson.md#circle). Te okręgi mogą być przekształcane w wielokąty, gdy są renderowane na mapie. Wszystkie geometrie operacji mogą być łatwo aktualizowane po zawinięciu [atlasem. Klasa kształtu.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)

## <a name="use-a-polygon-extrusion-layer"></a>Używanie warstwy wyciągnięcia wielokąta

Połącz [warstwę wyciągnięcia wielokąta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) ze źródłem danych. Następnie załadowano go na mapę. Warstwa wyciągnięcia wielokąta renderuje obszary `Polygon` `MultiPolygon` a i operacje jako kształty wyciągane. Właściwości `height` `base` i właściwości warstwy wyciągnięcia wielokąta definiują odległość bazową od podłoża i wysokość wyciągniętego kształtu w **metrach**. Poniższy kod pokazuje, jak utworzyć wielokąt, dodać go do źródła danych i renderować za pomocą klasy warstwy wyciągnięcia wielokąta.

> [!Note]
> Wartość `base` zdefiniowana w warstwie wyciągnięcia wielokąta powinna być mniejsza `height`lub równa wartości .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wielokąt wytłaczany" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>wielokąt wyciągnięcia pióra</a> przez<a href='https://codepen.io/azuremaps'>@azuremaps</a>usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.</iframe>


## <a name="add-data-driven-polygons"></a>Dodawanie wielokątów opartych na danych

Mapę choropleth można renderować za pomocą warstwy wyciągnięcia wielokąta. Ustaw `height` i `fillColor` właściwości warstwy wyciągnięcia na pomiar zmiennej statystycznej w geometrii `Polygon` i `MultiPolygon` operacji. Poniższy przykład kodu przedstawia wyciągniętą mapę choropleth USA na podstawie pomiaru gęstości zaludnienia według stanu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wytłaczana mapa choropleth" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/eYYYNox'>mapę rysu pióra wytłaczane przez</a> usługi Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Dodawanie okręgu do mapy

Usługa Azure Maps używa rozszerzonej wersji schematu GeoJSON, która zawiera definicję okręgów, jak wspomniano [tutaj.](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle) Wyciągnięty okrąg można renderować na `point` mapie, `subType` tworząc `Circle` obiekt z `Radius` właściwością i ponumerowanym właściwością reprezentującą promień w **metrach**. Przykład:

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

Zestaw Azure Maps Web SDK konwertuje te `Point` funkcje na `Polygon` funkcje pod maską. Obiekty `Point` te mogą być renderowane na mapie przy użyciu warstwy wyciągnięcia wielokąta, jak pokazano w poniższym przykładzie kodu.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Wielokąt przestrzeni powietrznej drona" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>wielokąt przestrzeni powietrznej</a> Pen<a href='https://codepen.io/azuremaps'>@azuremaps</a>Drone przez Usługi Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Dostosowywanie warstwy wytłaczania wielokąta

Warstwa Wycięcia wielokątów ma kilka opcji stylizacji. Oto narzędzie, aby je wypróbować.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ ( PoogBRJ )' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> przez<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Wielokąt](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [warstwa wytłaczania wielokąta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Dodatkowe zasoby:

> [!div class="nextstepaction"]
> [Rozszerzenie specyfikacji usługi Azure Maps GeoJSON](extend-geojson.md#circle)
