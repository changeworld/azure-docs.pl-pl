---
title: Dodawanie paska narzędzi do rysowania do mapy | Mapy platformy Microsoft Azure
description: Jak dodać pasek narzędzi do rysowania do mapy przy użyciu zestawu SDK usługi Azure Maps Web
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bebf1ddfbca3aec5a551193609381cf3510bc3ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334500"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Dodawanie paska narzędzi do rysowania do mapy

W tym artykule pokazano, jak korzystać z modułu Narzędzia do rysowania i wyświetlać pasek narzędzi rysowania na mapie. Formant [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) dodaje pasek narzędzi rysowania na mapie. Dowiesz się, jak tworzyć mapy za pomocą tylko jednego i wszystkich narzędzi do rysowania oraz jak dostosować renderowanie kształtów rysunku w menedżerze rysunków.

## <a name="add-drawing-toolbar"></a>Dodawanie paska narzędzi rysowania

Poniższy kod tworzy wystąpienie menedżera rysunku i wyświetla pasek narzędzi na mapie.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Poniżej znajduje się kompletny przykładowy kod uruchomiony powyższych funkcji:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Dodawanie paska narzędzi rysowania" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pasek narzędzi Dodaj <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>rysunek</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pióra według usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Ogranicz wyświetlane opcje paska narzędzi

Poniższy kod tworzy wystąpienie menedżera rysunku i wyświetla pasek narzędzi z narzędziem do rysowania wielokątów na mapie. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Poniżej znajduje się kompletny przykładowy kod uruchomiony powyższych funkcji:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Dodawanie narzędzia do rysowania wielokąta" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>narzędzie Dodawania wielokąta</a> przez usługę<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Zmienianie stylu renderowania rysunku

Styl rysowanych kształtów można dostosować, pobierając warstwy bazowe menedżera rysunków `drawingManager.getLayers()` za pomocą funkcji, a następnie ustawiając opcje na poszczególnych warstwach. Uchwyty przeciągania, które pojawiają się dla współrzędnych podczas edycji kształtu, są znacznikami HTML. Styl uchwytów przeciągania można dostosować, przekazując opcje `dragHandleStyle` `secondaryDragHandleStyle` znaczników HTML do i opcji menedżera rysunku.  

Poniższy kod pobiera warstwy renderowania z Menedżera rysunku i modyfikuje ich opcje, aby zmienić styl renderowania dla rysunku. W takim przypadku punkty będą renderowane za pomocą niebieskiej ikony znacznika. Linie będą czerwone i mają cztery piksele szerokości. Wielokąty będą miały zielony kolor wypełnienia i pomarańczowy kontur. Następnie zmienia style uchwytów przeciągania na kwadratowe ikony. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Poniżej znajduje się kompletny przykładowy kod uruchomiony powyższych funkcji:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zmienianie stylu renderowania rysunku" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>styl renderowania rysunku zmiany</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pióra według usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak korzystać z dodatkowych funkcji modułu narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Pobieranie danych kształtów](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagowanie na zdarzenia rysowania](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Typy interakcji i skróty klawiaturowe](drawing-tools-interactions-keyboard-shortcuts.md)

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Mapę](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Pasek narzędzi rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Kierownik rysunku](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
