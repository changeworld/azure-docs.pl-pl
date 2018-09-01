---
title: Dodawanie kształtu przy użyciu usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać kształt do mapy Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5315e7d45ef3af838f26422655cf6971af6f903e
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382554"
---
# <a name="add-a-shape-to-a-map"></a>Dodawanie kształtu do mapy

W tym artykule przedstawiono sposób dodawania wielokąta linię i okrąg do mapy. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Dodaj wiersz

<iframe height='500' scrolling='no' title='Dodaj wiersz do mapy' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/qomaKv/'>dodać wiersz do mapy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu zostanie wyświetlona linia. Wiersz jest [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z LineString z LineStringProperties jako właściwość jego funkcji. Użyj `new atlas.data.Feature(new atlas.data.LineString())` utworzyć linię i określeniu jego właściwości. 

Warstwa linii jest tablicą wierszy. Ostatni blok kodu używa [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings) funkcji klasy mapy, aby dodać warstwę linii do mapy oraz zdefiniować właściwości warstwy linii. Zobacz właściwości warstwy linii w [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Dodaj koło

<iframe height='500' scrolling='no' title='Dodaj koło z mapą' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Dodaj koło z mapą</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu tworzona jest okrąg. Okrąg jest [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) z [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.circleproperties?view=azure-iot-typescript-latest) jako właściwość jego funkcji. Użyj `new atlas.data.Feature(new atlas.data.Point())` utworzyć koło i określeniu jego właściwości.

Warstwa okrąg jest tablicą koła. Ostatni blok kodu używa [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcircles) funkcji klasy mapy, aby dodać warstwę okrąg do mapy oraz zdefiniować właściwości warstwy okrąg. Zobacz właściwości warstwy okrąg w [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Dodaj wielokąta
<iframe height='500' scrolling='no' title='Dodawanie wielokąta do mapy ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Dodawanie wielokąta do mapy </a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu tworzona jest wielokąta. Wielokąt jest [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [wielokąta](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) z [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonproperties?view=azure-iot-typescript-latest) jako właściwość jego funkcji. Użyj `new atlas.data.Feature(new atlas.data.Polygon())` utworzenie wielokąta i zdefiniowanie jego właściwości. Podaj uporządkowane współrzędne ścieżki wielokąta w Konstruktorze wielokąta.

Warstwa wielokątów jest tablicą wielokątów. Ostatni blok kodu używa [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpolygons) funkcji klasy mapy Dodaj warstwę wielokątów do mapy i określeniu jego właściwości. Zobacz właściwości warstwy wielokątów w [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.polygonlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły:
* [Dodawanie niestandardowego kodu HTML](./map-add-custom-html.md)
* [Wyświetlanie wyników wyszukiwania](./map-search-location.md)


