---
title: Dodaj warstwę bąbelkową do Azure Maps | Microsoft Docs
description: Jak dodać warstwę bąbelkową do mapy JavaScript
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 516e4f35c88ae9c0e2d63e8a4ee40eb57c05ac29
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639041"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Dodawanie warstwy bąbelkowej do mapy

W tym artykule opisano sposób renderowania danych punktu ze źródła danych jako warstwy bąbelkowej na mapie. Warstwy bąbelków renderują punkty jako okręgi na mapie o stałym promieniu pikseli. 

> [!TIP]
> Domyślnie warstwy bąbelków będą renderować współrzędne wszystkich geometrie w źródle danych. Aby ograniczyć warstwę w taki sposób, że renderuje tylko funkcje geometrii punktów `filter` , ustawia właściwość warstwy na `['==', ['geometry-type'], 'Point']` lub `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Jeśli chcesz również uwzględnić funkcje MultiPoint.

## <a name="add-a-bubble-layer"></a>Dodawanie warstwy bąbelkowej

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>Źródło danych BubbleLayer</a> dla pióra według<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

W drugim bloku kodu tablica obiektów [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) jest definiowana i dodawana do obiektu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) .

[Warstwa bąbelkowa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) renderuje dane oparte na punktach opakowane w [źródle danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako okręgi na mapie. Ostatni blok kodu tworzy warstwę bąbelkową i dodaje ją do mapy. Zobacz właściwości warstwy bąbelkowej w [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Tablica obiektów punktów, źródło danych i warstwa bąbelków są tworzone i dodawane do mapy w funkcji [detektora zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby upewnić się, że okrąg jest wyświetlany po całkowitym załadowaniu mapy.

## <a name="show-labels-with-a-bubble-layer"></a>Wyświetlanie etykiet z warstwą bąbelkową

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>Źródło danych MultiLayer</a> dla pióra według<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Powyższy kod przedstawia sposób wizualizacji i etykietowania danych na mapie. Pierwszy blok kodu powyżej konstruuje obiekt mapy. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

Drugi blok kodu tworzy obiekt [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) . Następnie tworzy obiekt źródła danych przy użyciu klasy [źródła danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) i dodaje punkt do źródła danych.

[Warstwa bąbelkowa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) renderuje dane oparte na punktach opakowane w [źródle danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako okręgi na mapie. Trzeci blok kodu tworzy warstwę bąbelkową i dodaje ją do mapy. Zobacz właściwości warstwy bąbelkowej w [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

[Warstwa symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikon do renderowania danych opartych na punkcie [w postaci symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie. Ostatni blok kodu tworzy i dodaje warstwę symboli do mapy, która renderuje etykietę tekstową bąbelka. Zobacz właściwości warstwy symboli w [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Źródło danych i warstwy są tworzone i dodawane do mapy w ramach funkcji detektora [zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby upewnić się, że dane są wyświetlane po całkowitym załadowaniu mapy.

## <a name="customize-a-bubble-layer"></a>Dostosowywanie warstwy bąbelków

Warstwa bąbelków zawiera tylko kilka opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy bąbelkowej' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Opcje warstwy bąbelków</a> pióra według Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)