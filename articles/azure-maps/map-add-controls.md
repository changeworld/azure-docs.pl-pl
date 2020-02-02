---
title: Dodawanie formantów do mapy | Mapy Microsoft Azure
description: Jak dodać kontrolkę powiększenia, kontrolkę gęstość, obrócić formant i selektor stylu do mapy w Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e2ad8eb181685d3ac3de8b1e0ed7ef8ddfa4e224
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933282"
---
# <a name="add-controls-to-a-map"></a>Dodawanie formantów do mapy

W tym artykule opisano sposób dodawania formantów do mapy. Dowiesz się również, jak utworzyć mapę ze wszystkimi kontrolkami i [selektorem stylów](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Dodaj kontrolkę powiększenia

Kontrolka powiększenia dodaje przyciski umożliwiające powiększanie mapy w i na zewnątrz. Poniższy przykład kodu tworzy wystąpienie klasy [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) i dodaje ją do prawego dolnego rogu mapy.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie kontrolki powiększenia' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>dodając kontrolkę powiększenia</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Dodaj kontrolkę gęstość

Kontrolka gęstość dodaje przyciski umożliwiające przechylenie skoku na mapę względem zakresu. Poniższy przykład kodu tworzy wystąpienie klasy [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) . Dodaje PitchControl do prawego górnego rogu mapy.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie kontrolki skoku' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>dodając kontrolkę gęstość</a> przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Dodaj kontrolkę kompas

Kontrolka kompas dodaje przycisk służący do obracania mapy. Poniższy przykład kodu tworzy wystąpienie klasy [kontrolki kompasu](/javascript/api/azure-maps-control/atlas.control.compasscontrol) i dodaje ją do lewego dolnego rogu mapy.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie kontrolki Obróć' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro, <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>dodając kontrolkę obróć</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Mapa ze wszystkimi kontrolkami

Poniższy przykład kodu dodaje kontrolki selektor stylu, powiększenie, gęstość i kompas do prawego dolnego rogu mapy. Zwróć uwagę, jak automatycznie stosują się do nich stosy. Kolejność obiektów formantów w skrypcie określa kolejność, w jakiej są wyświetlane na mapie. Aby zmienić kolejność formantów na mapie, można zmienić ich kolejność w skrypcie.

<br/>

<iframe height='500' scrolling='no' title='Mapa ze wszystkimi kontrolkami' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>Mapa ze wszystkimi kontrolkami</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

Kontrolka selektora stylów jest definiowana przez klasę [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) . Aby uzyskać więcej informacji na temat używania kontrolki selektora stylów, zobacz [Wybieranie stylu mapy](choose-map-style.md).

## <a name="customize-controls"></a>Dostosuj kontrolki

Oto narzędzie do testowania różnych opcji dostosowywania kontrolek.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opcje kontrolki nawigacji" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>Opcje kontrolki Nawigacja</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Jeśli chcesz utworzyć niestandardowe kontrolki nawigacji, Utwórz klasę, która rozciąga się od klasy `atlas.Control` lub Utwórz element HTML i umieść go powyżej bloku DIV mapy. Ta kontrolka interfejsu użytkownika wywołuje funkcję Maps `setCamera`, aby przenieść mapę. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Kontrolka kompasu](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Pełny kod można znaleźć w następujących artykułach:

> [!div class="nextstepaction"]
> [Dodaj numer PIN](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie okna podręcznego](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę wielokątów](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)

