---
title: Pobierz dane z kształtów na mapie | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak uzyskać dane kształtu rysowane na mapie przy użyciu microsoft azure maps web SDK.
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 88db018575f92e777223f383c65cd6db51ba515a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334409"
---
# <a name="get-shape-data"></a>Pobieranie danych kształtów

W tym artykule pokazano, jak uzyskać dane kształtów rysowane na mapie. Używamy funkcji **drawingManager.getSource()** wewnątrz [menedżera rysunku](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--). Istnieją różne scenariusze, w których chcesz wyodrębnić dane geojson rysowanego kształtu i użyć go w innym miejscu.  


## <a name="get-data-from-drawn-shape"></a>Pobierz dane z rysowanych kształtów

Następująca funkcja pobiera dane źródłowe rysowane kształtu i wyprowadza go na ekranie. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Poniżej znajduje się kompletny przykład kodu uruchamiania, gdzie można narysować kształt, aby przetestować funkcjonalność:

<br/>

<iframe height="686" title="Pobieranie danych kształtów" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Zobacz pióro Pobierz dane<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>kształtu</a> przez usługi Azure Maps ( ) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak korzystać z dodatkowych funkcji modułu narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Reagowanie na zdarzenia rysowania](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Typy interakcji i skróty klawiaturowe](drawing-tools-interactions-keyboard-shortcuts.md)

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Mapę](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Kierownik rysunku](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Pasek narzędzi rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
