---
title: Pobieranie danych z kształtów na mapie | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak uzyskać dane kształtu rysowane na mapie przy użyciu zestawu Microsoft Azure Web SDK Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ca1b9854b118d359167873313f1fcb11bfecee52
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911008"
---
# <a name="get-shape-data"></a>Pobieranie danych kształtów

W tym artykule pokazano, jak uzyskać informacje o kształtach, które zostały narysowane na mapie przy użyciu funkcji rysowania [Menedżera rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) **. GetSource ()** . Mogą istnieć różne scenariusze, w których warto wyodrębnić dane GEOJSON rysowanego kształtu i korzystać z niego w innym miejscu.  


## <a name="get-data-from-drawn-shape"></a>Pobierz dane z rysowanego kształtu

Poniższa funkcja pobiera dane źródłowe rysowanego kształtu i wyprowadza je na ekranie. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Poniżej znajduje się kompletny przykładowy kod, w którym można narysować kształt w celu przetestowania funkcjonalności:

<br/>

<iframe height="686" title="Pobieranie danych kształtów" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Pobierz dane kształtu</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać dodatkowych funkcji modułu narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Reagowanie na zdarzenia rysowania](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Typy interakcji i skróty klawiaturowe](drawing-tools-interactions-keyboard-shortcuts.md)

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Menedżer rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rysowanie paska narzędzi](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
