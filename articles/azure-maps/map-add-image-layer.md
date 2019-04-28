---
title: Dodaj warstwę obrazu do usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać warstwę obrazu do mapy Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5396fefca3a60dea7a503f8b4e84cc575753ea30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769570"
---
# <a name="add-an-image-layer-to-a-map"></a>Dodaj warstwę obrazu do mapy

W tym artykule pokazano, jak można nakładki obraz stały zestaw współrzędnych na mapie. Istnieje wiele scenariuszy, w których odbywa się nałożenie obrazu na mapie. Poniżej przedstawiono kilka przykładów typów obrazów często nałożony na mapach;

* Obrazy przechwycone dronom.
* Tworzenie floorplans.
* Historyczne lub innych obrazów wyspecjalizowanych mapy.
* Schematy placach.
* Obrazy radarowy o pogodzie.

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) jest szybkie, łatwe nakładki obrazów na mapie. Jednak jeśli obraz jest duży, przeglądarka może mieć trudności z go załadować. W takim przypadku należy wziąć pod uwagę podzielenie obrazu na kafelkach i ładuje je do mapy jako [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

## <a name="add-an-image-layer"></a>Dodawanie warstwy obrazów

W tym przykładzie pokazano, jak nakładki obraz [mapy Newark New Jersey z 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) na mapie.

<br/>

<iframe height='500' scrolling='no' title='Warstwa obrazu prostego' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/eQodRo/'>warstwy obrazu prostego</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) jest tworzony przez przekazanie adresu URL do obrazu i współrzędne dowiedzą w formacie `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

## <a name="import-a-kml-ground-overlay"></a>Importowanie w nakładce podstaw KML

W tym przykładzie przedstawiono sposób nakładki nakładki podstaw KML informacji jako warstwa obrazu na mapie. Nakładki podstaw KML zapewniają północ, południe, Wschód i współrzędne Zachodnia i obrót przeciwnie do ruchu wskazówek zegara, gdzie jako obraz warstwy oczekuje współrzędnych dla każdego rogu obrazu. Nakładka podstaw KML, w tym przykładzie jest Chartres cathedral i pochodzące z wyszukiwania z [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

<br/>

<iframe height='500' scrolling='no' title='Nakładka podstaw KML jako warstwa obrazu' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML prowadzić nakładki jako warstwa obrazu</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Powyższy kod używa statycznego `getCoordinatesFromEdges` funkcji [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) klasy do obliczania dowiedzą o obraz z północ, południe, wschód zachód i wymiany informacji z KML prowadzić nakładki.


## <a name="customize-an-image-layer"></a>Dostosowywanie warstwa obrazu

Warstwa obrazu ma wiele opcji stylów. W tym miejscu to narzędzie ich wypróbowanie.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstw obrazu' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>opcji warstwy obrazu</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać więcej przykładów kodu dodać do map:

> [!div class="nextstepaction"]
> [Dodaj warstwę kafelków](./map-add-tile-layer.md)