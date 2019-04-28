---
title: Dodaj warstwę bąbelków do usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać warstwę bąbelków do mapy Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f2c4c6b8655d5efb993a2dedf536000ac94328c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769689"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Dodaj warstwę bąbelków do mapy

W tym artykule dowiesz się, jak można renderować punktu danych ze źródła danych jako warstwa Bąbelek na mapie. Warstwy bąbelków renderowane punktów jako okręgów na mapie z protokołem radius stały pikseli. 

> [!TIP]
> Warstwy bąbelków domyślnie będą renderowane współrzędne wszystkich geometrii w źródle danych. Aby ograniczyć warstwy w taki sposób, że tylko renderuje geometrii punktu funkcje zestawu `filter` właściwości warstwy, aby `['==', ['geometry-type'], 'Point']` lub `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` aby obejmują również funkcje MultiPoint.

## <a name="add-a-bubble-layer"></a>Dodawanie warstwy bąbelkowej

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu, tablicy [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) obiektów został zdefiniowany i jest dodawane do [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) obiektu.

A [warstwy bąbelków](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) renderuje oparta na punkcie danych, zapakowane w [źródła danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako kół na mapie. Ostatni blok kodu tworzy warstwę bąbelka i dodaje go do mapy. Zobacz właściwości warstwy bąbelek na [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

Tablica obiektów punktów, źródła danych i warstwy bąbelków są tworzone i dodawane do mapy w ramach [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkcję, aby upewnić się, że koła jest wyświetlany po mapy zawiera w pełni załadowany.

## <a name="show-labels-with-a-bubble-layer"></a>Pokaż etykiety z warstwą bąbelkowych

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>wielowarstwowych DataSource</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Powyższy kod dowiesz się, jak wizualizować i etykiety danych na mapie. Pierwszy blok powyższy kod tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Tworzy drugi blok kodu, [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) obiektu. Następnie tworzy źródła danych obiektu przy użyciu [źródła danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) i dodaje punkt do źródła danych.

A [warstwy bąbelków](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) renderuje oparta na punkcie danych, zapakowane w [źródła danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako kół na mapie. Trzeci bloku kodu tworzy warstwę bąbelka i dodaje go do mapy. Zobacz właściwości warstwy bąbelek na [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions).

A [warstwy symbol](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikony do renderowania oparta na punkcie danych, w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbole na mapie. Ostatni blok kodu tworzy i dodaje symbol warstwę do mapy, który renderuje Etykieta tekstowa bąbelek. Zobacz właściwości warstwy symboli w [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions).

Źródło danych i warstwy są tworzone i dodawane do mapy w ramach [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkcję, aby upewnić się, że dane są wyświetlane po mapy zawiera w pełni załadowany.

## <a name="customize-a-bubble-layer"></a>Dostosowywanie warstwy bąbelkowych

Warstwa bąbelkowy ma tylko kilka opcji stylów. W tym miejscu to narzędzie ich wypróbowanie.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy bąbelkowych' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>będą się pojawiać opcje warstwy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać więcej przykładów kodu dodać do map:

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](map-add-pin.md)

> [!div class="nextstepaction"]
> [Za pomocą wyrażeń opartych na danych stylu](data-driven-style-expressions-web-sdk.md)