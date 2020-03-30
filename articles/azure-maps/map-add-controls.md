---
title: Dodawanie kontrolek do mapy | Mapy platformy Microsoft Azure
description: Jak dodać formant powiększania, kontrolkę skoku, sterowanie obracaniem i selektor stylu do mapy w Usługach Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 094dc9fd01ec71f378a173a2b4fa64cc672d7c97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334560"
---
# <a name="add-controls-to-a-map"></a>Dodawanie kontrolek do mapy

W tym artykule pokazano, jak dodać formanty do mapy. Dowiesz się również, jak utworzyć mapę ze wszystkimi kontrolkami i [selektorem stylu.](https://docs.microsoft.com/azure/azure-maps/choose-map-style)

## <a name="add-zoom-control"></a>Dodawanie kontrolki powiększenia

Kontrolka powiększenia dodaje przyciski służące do powiększania i pomniejszania mapy. Poniższy przykład kodu tworzy wystąpienie [Klasy ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) i dodaje go w prawym dolnym rogu mapy.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Poniżej znajduje się kompletny przykładowy kod uruchomiony powyższej funkcji.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie kontrolki powiększenia' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>pióro Dodawanie kontrolki powiększenia</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Dodawanie kontroli wysokości

Regulacja wysokości dodaje przyciski do przechylania skoku do mapy względem horyzontu. Poniższy przykład kodu tworzy wystąpienie [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) klasy. Dodaje PitchControl do prawego górnego rogu mapy.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Poniżej znajduje się kompletny przykładowy kod uruchomiony powyższej funkcji.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie kontrolki skoku' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Dodawanie formantu skoku</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Dodawanie kontrolki kompasu

Kontrolka kompasu dodaje przycisk do obracania mapy. Poniższy przykład kodu tworzy wystąpienie [Compass Control](/javascript/api/azure-maps-control/atlas.control.compasscontrol) klasy i dodaje go w lewym dolnym rogu mapy.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Poniżej znajduje się kompletny przykładowy kod uruchomiony powyższej funkcji.

<br/>

<iframe height='500' scrolling='no' title='Dodawanie kontrolki obrotu' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>pióro Dodawanie sterowania obracania</a> przez usługę Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Mapa ze wszystkimi elementami sterującymi

Wiele formantów można umieścić w tablicy i dodać do mapy wszystkie na raz i umieszczone w tym samym obszarze mapy, aby uprościć rozwój. Poniżej dodaje standardowe formanty nawigacji do mapy przy użyciu tej metody.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

Poniższy przykład kodu dodaje powiększenie, kompas, podziałka i styl kontrolki selektora w prawym górnym rogu mapy. Zwróć uwagę, jak automatycznie stosują się. Kolejność obiektów kontrolnych w skrypcie decyduje o kolejności, w jakiej pojawiają się na mapie. Aby zmienić kolejność formantów na mapie, można zmienić ich kolejność w tablicy.

<br/>

<iframe height='500' scrolling='no' title='Mapa ze wszystkimi elementami sterującymi' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>mapę Pióro A ze wszystkimi formantami</a> azure maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

Formant selektora stylu jest definiowany przez [klasę StyleControl.](/javascript/api/azure-maps-control/atlas.control.stylecontrol) Aby uzyskać więcej informacji na temat używania kontrolki selektora stylów, zobacz [wybieranie stylu mapy](choose-map-style.md).

## <a name="customize-controls"></a>Dostosowywanie kontrolek

Oto narzędzie do testowania różnych opcji dostosowywania formantów.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opcje sterowania nawigacją" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>opcje sterowania nawigacją</a> piórem według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

Jeśli chcesz utworzyć niestandardowe formanty nawigacji, należy `atlas.Control` utworzyć klasę, która rozciąga się od klasy lub utworzyć element HTML i umieścić go nad div mapy. Czy ten interfejs użytkownika `setCamera` kontroli wywołać funkcję map, aby przenieść mapę. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Sterowanie kompasem](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl (Kontrola pitchcontrol)](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [Kontrola stylu](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [Kontrola zoomu](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Pełny kod można znaleźć w następujących artykułach:

> [!div class="nextstepaction"]
> [Dodawanie numeru PIN](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie menu podręcznego](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)

