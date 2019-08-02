---
title: Dodawanie okna podręcznego z Azure Maps | Microsoft Docs
description: Jak dodać okno podręczne do mapy JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 92d44ef3d0db8e93d4babd7441238c7fa105dbd5
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639008"
---
# <a name="add-a-popup-to-the-map"></a>Dodawanie okna podręcznego do mapy

W tym artykule opisano sposób dodawania okna podręcznego do punktu na mapie.

## <a name="understand-the-code"></a>Zrozumienie kodu

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Dodawanie wyskakujących okienek przy użyciu Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Dodaj wyskakujące okienko przy użyciu Azure Maps</a> przez<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) . Tworzy również zawartość HTML, która będzie wyświetlana w menu podręcznym.

Drugi blok kodu tworzy obiekt źródła danych przy użyciu klasy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Punkt jest [funkcją](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) klasy [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) . Obiekt punktu o właściwościach nazwa i opis jest następnie tworzony i dodawany do źródła danych.

[Warstwa symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikon do renderowania danych opartych na punkcie [w postaci symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie.  Warstwa symboli jest tworzona w trzecim bloku kodu. Źródło danych jest dodawane do warstwy symboli, która jest następnie dodawana do mapy.

Czwarty blok kodu tworzy [obiekt podręczny](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) za pośrednictwem `new atlas.Popup()`. Właściwości podręczne, takie jak Position i pixelOffset, są częścią [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions). PopupOptions można zdefiniować w konstruktorze popup lub za [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) pomocą funkcji SetOptions klasy popup. Zostanie utworzony odbiornik zdarzeń dla warstwy symboli. `mouseover`

Ostatni blok kodu tworzy funkcję, która jest wyzwalana przez `mouseover` odbiornik zdarzeń. Ustawia zawartość i właściwości okna podręcznego i dodaje obiekt podręczny do mapy.

## <a name="reusing-a-popup-with-multiple-points"></a>Używanie okna podręcznego z wieloma punktami

Jeśli masz wiele punktów i chcesz wyświetlić tylko jedno podręczne, najlepszym rozwiązaniem jest utworzenie jednego podręcznego i ponowne użycie go zamiast tworzenia okna podręcznego dla każdej funkcji punktu. Dzięki temu liczba elementów DOM utworzonych przez aplikację jest znacznie zmniejszona, co może zapewnić lepszą wydajność. Ten przykład tworzy funkcje 3 punktów. Po kliknięciu dowolnego z nich zostanie wyświetlone okno podręczne z zawartością dla tej funkcji punktu.

<br/>

<iframe height='500' scrolling='no' title='Używanie podręcznego z wieloma numerami PIN' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobaczysz piórem, <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>korzystając z okna podręcznego z wieloma</a> numerami PIN przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Elementy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Zobacz następujące wspaniałe artykuły dotyczące pełnych przykładów kodu:

> [!div class="nextstepaction"]
> [Dodaj warstwę symboli](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Dodawanie znacznika HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Dodawanie kształtu](./map-add-shape.md)