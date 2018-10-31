---
title: Dodawanie symboli i znaczniki z usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodawać znaczniki i symboli do mapy Javascript
author: walsehgal
ms.author: v-musehg
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 92d80d9a4d39a3f21539e5e6e498fc52df213a19
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248816"
---
# <a name="add-symbols-and-markers-to-a-map"></a>Dodaj znaczniki i symboli do mapy

W tym artykule pokazano, jak dodawać znaczniki i symboli do mapy, przy użyciu źródła danych.

## <a name="add-a-symbol-marker"></a>Dodaj znacznik symboli

<iframe height='500' scrolling='no' title='Przełącznik Przypnij lokalizację' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>przełącznika Przypnij lokalizację</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok powyższy kod tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu obiektu źródła danych jest tworzony przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy. Punkt jest następnie tworzony i dodawany do źródła danych. Punkt znajduje się [funkcji](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) z [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest).

Trzeci bloku kodu tworzy [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) i aktualizacje współrzędne punktu od myszy, kliknij przycisk za pomocą klasy kształt [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#setcoordinates) metody.

A [warstwy symbol](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikony do renderowania oparta na punkcie danych, w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbole na mapie.  Źródła danych, kliknij odbiornik zdarzeń i warstwy symboli są tworzone i dodawane do mapy w ramach [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkcję, aby upewnić się, że punkt jest wyświetlany po mapy ładuje pełni.

## <a name="add-a-custom-symbol"></a>Dodawanie niestandardowego symbolu

<iframe height='500' scrolling='no' title='Źródło danych w formacie HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Dodaje drugi blok kodu [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) na mapie za pomocą [znaczniki](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) właściwość [mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) klasy. HtmlMarker zostanie dodany do mapy w ramach [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkcję, aby upewnić się, czy jest wyświetlana po mapy ładuje pełni.

## <a name="add-bubble-markers"></a>Dodaj znaczniki bąbelkowych

<iframe height='500' scrolling='no' title='BubbleLayer źródła danych' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu, Tablica pozycji jest zdefiniowana i [MultiPoint](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.multipoint?view=azure-iot-typescript-latest) obiekt zostanie utworzony. Obiekt źródła danych jest tworzony przy użyciu [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) klasy i MultiPoint obiekt zostanie dodany do źródła danych.

A [warstwy bąbelków](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) renderuje oparta na punkcie danych, zapakowane w [źródła danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako kół na mapie. Ostatni blok kodu tworzy warstwę bąbelka i dodaje go do mapy. Zobacz właściwości warstwy bąbelek na [BubblerLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.bubblelayeroptions?view=azure-iot-typescript-latest).

Obiekt MultiPoint, źródła danych i warstwie bąbelków są tworzone i dodawane do mapy w ramach [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkcję, aby upewnić się, czy koła jest wyświetlany po mapy ładuje pełni.

## <a name="add-bubble-markers-with-label"></a>Dodaj znaczniki bąbelków etykietą

<iframe height='500' scrolling='no' title='Wielowarstwowych źródła danych' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>wielowarstwowych DataSource</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Powyższy kod dowiesz się, jak wizualizować i etykiety danych na mapie. Pierwszy blok powyższy kod tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Tworzy drugi blok kodu, [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) obiektu. Następnie tworzy źródła danych obiektu przy użyciu [źródła danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) i dodaje punkt do źródła danych.

A [warstwy bąbelków](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest) renderuje oparta na punkcie danych, zapakowane w [źródła danych](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako kół na mapie. Trzeci bloku kodu tworzy warstwę bąbelka i dodaje go do mapy. Zobacz właściwości warstwy bąbelek na [BubblerLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.bubblelayeroptions?view=azure-iot-typescript-latest).

A [warstwy symbol](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikony do renderowania oparta na punkcie danych, w [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) jako symbole na mapie. Ostatni blok kodu tworzy i dodaje symbol warstwę do mapy, który renderuje Etykieta tekstowa bąbelek. Zobacz właściwości warstwy symboli w [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.symbollayeroptions?view=azure-iot-typescript-latest).

Źródło danych i warstwy są tworzone i dodawane do mapy w ramach [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) funkcję, aby upewnić się, czy dane są wyświetlane po mapy ładuje pełni.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać więcej przykładów kodu dodać do map:

> [!div class="nextstepaction"]
> [Dodawanie menu podręcznego](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Dodawanie kształtu](./map-add-shape.md)