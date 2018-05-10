---
title: Dodawanie kształtu przy użyciu map Azure | Dokumentacja firmy Microsoft
description: Jak dodać kształt do mapy kodu Javascript
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: fb9ec0713d3db465cf835346465e70c4455b38ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-shape-to-a-map"></a>Dodawanie kształtów do mapy

W tym artykule przedstawiono sposób dodawania wielokąta wiersza i okrąg do mapy. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Dodawanie linii

<iframe height='500' scrolling='no' title='Dodaj linię do mapy' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/qomaKv/'>dodać wiersz do mapy</a> mapach Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Widać [Tworzenie mapy](./map-create.md) instrukcje.

Wiersz jest tworzony w drugim bloku kodu. Wiersz jest [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) z LineString z LineStringProperties jako jego właściwości funkcji. Użyj `new atlas.data.Feature(new atlas.data.LineString())` do tworzenia linii i określeniu jego właściwości. 

Warstwa wiersza jest tablicą wierszy. Korzysta z ostatniego bloku kodu [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings) funkcja klasy mapy, aby dodać warstwę wiersza do mapy oraz zdefiniować właściwości warstwy wiersza. Zobacz właściwości warstwy wiersza w [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Dodaj okręgu

<iframe height='500' scrolling='no' title='Dodawanie okręgu do mapy' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>dodać okrąg na mapę</a> mapach Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Widać [Tworzenie mapy](./map-create.md) instrukcje.

W drugim bloku kodu jest tworzony okręgu. Koło jest [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) z [punktu](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) z [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circleproperties?view=azure-iot-typescript-latest) jako jego właściwości funkcji. Użyj `new atlas.data.Feature(new atlas.data.Point())` tworzenie koło i określeniu jego właściwości.

Warstwa koło jest tablicą okręgi. Korzysta z ostatniego bloku kodu [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addcircles) funkcja klasy mapy, aby dodać warstwę okręgu do mapy oraz zdefiniować właściwości warstwy okręgu. Zobacz właściwości warstwy okrąg na [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Dodaj wielokąta
<iframe height='500' scrolling='no' title='Dodawanie do mapy wielokąta ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Dodawanie wielokąta do mapy </a> mapach Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Widać [Tworzenie mapy](./map-create.md) instrukcje.

W drugim bloku kodu jest tworzony wielokąta. Wielokąt jest [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) z [wielokąta](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygon?view=azure-iot-typescript-latest) z [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonproperties?view=azure-iot-typescript-latest) jako jego właściwości funkcji. Użyj `new atlas.data.Feature(new atlas.data.Polygon())` tworzenie wielokąta i określeniu jego właściwości. Podaj uporządkowanej współrzędne ścieżki wielokąta w Konstruktorze wielokąta.

Warstwa Wielokąt jest tablicą wielokątów. Korzysta z ostatniego bloku kodu [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpolygons) funkcji klasy mapy, aby dodać warstwę wielokąta do mapy i określeniu jego właściwości. Zobacz właściwości warstwy wielokąta w [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonlayeroptions?view=azure-iot-typescript-latest). 
