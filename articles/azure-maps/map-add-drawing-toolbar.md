---
title: Dodawanie paska narzędzi Rysowanie do Azure Maps | Microsoft Docs
description: Jak dodać pasek narzędzi rysowania do mapy za pomocą Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 6bc754c9a4f333da85e57c5ad9780da8df93e895
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309828"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Dodawanie paska narzędzi narzędzi do rysowania do mapy

W tym artykule pokazano, jak używać modułu narzędzi do rysowania i wyświetlać pasek narzędzi rysowania na mapie. Kontrolka [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) dodaje pasek narzędzi Rysowanie na mapie. Dowiesz się, jak tworzyć mapy za pomocą tylko jednego i wszystkich narzędzi do rysowania oraz jak dostosować renderowanie kształtów rysowania w Menedżerze rysowania.

## <a name="add-drawing-toolbar"></a>Dodaj pasek narzędzi rysowania

Poniższy kod tworzy wystąpienie Menedżera rysowania i wyświetla pasek narzędzi na mapie.

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Poniżej znajduje się kompletny przykładowy kod wykonywany w powyższych funkcjach:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Dodaj pasek narzędzi rysowania" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zapoznaj się z piórem <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>Dodawanie paska narzędzi Rysowanie</a> przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Ogranicz wyświetlane opcje paska narzędzi

Poniższy kod tworzy wystąpienie Menedżera rysowania i wyświetla pasek narzędzi z narzędziem rysowania wielokątów na mapie. 

```Javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Poniżej znajduje się kompletny przykładowy kod wykonywany w powyższych funkcjach:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Dodawanie narzędzia do rysowania wielokątów" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióro <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Dodaj narzędzie do rysowania wielokątów</a> , Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() w <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Zmień styl renderowania rysowania

Poniższy kod pobiera warstwy renderowania z Menedżera rysowania i modyfikuje ich opcje, aby zmienić styl renderowania dla rysowania. W takim przypadku punkty będą renderowane z niebieską ikoną znacznika, linie będą czerwone i cztery piksele szerokości, wielokąty będą miały zielony kolor wypełnienia i pomarańczowy konspekt.

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

Poniżej znajduje się kompletny przykładowy kod wykonywany w powyższych funkcjach:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Zmień styl renderowania rysowania" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz styl renderowania dla pióra <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>zmiana rysowania</a> według Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Rysowanie paska narzędzi](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Menedżer rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)