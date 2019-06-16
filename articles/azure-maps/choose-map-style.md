---
title: Mapowanie funkcji usługi Azure Maps stylu | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usługi Azure Maps style powiązane funkcje.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ffed12b9184c7b6a690c30db9826f031fe6c9f9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60795905"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Wybierz styl z mapy usługi Azure Maps

Usługi Azure Maps ma cztery style różnych mapowań do wyboru. Aby uzyskać więcej informacji o stylach mapy, zobacz [obsługiwane style mapy w usługi Azure Maps](./supported-map-styles.md). W tym artykule pokazano, jak za pomocą funkcji stylu związane ustawienie stylu przy ładowaniu mapy, ustawienie nowego stylu i korzystanie z kontrolki selektor stylu.

## <a name="set-style-on-map-load"></a>Ustaw styl przy ładowaniu mapy

<iframe height='500' scrolling='no' title='Ustawienia stylu przy ładowaniu mapy' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>ustawienia stylu przy ładowaniu mapy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Blok kodu powyżej Ustawia klucz subskrypcji i tworzy obiekt mapy ze stylem równa grayscale_dark. Zobacz [Utwórz mapę](./map-create.md) instrukcje dotyczące sposobu tworzenia mapy.

## <a name="update-the-style"></a>Aktualizacja stylu

<iframe height='500' scrolling='no' title='Aktualizacja stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>aktualizowanie styl</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Blok kodu powyżej Ustawia klucz subskrypcji i tworzy obiekt mapy bez wstępnie ustawienia stylu. Zobacz [Utwórz mapę](./map-create.md) instrukcje dotyczące sposobu tworzenia mapy.

Drugi blok kodu używa mapy [metody setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metodę, aby ustawić style mapy do satelity.

## <a name="add-the-style-picker"></a>Dodaj selektor stylu

<iframe height='500' scrolling='no' title='Dodawanie selektor stylu' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Dodawanie selektor stylu</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu w powyższym kodzie Ustawia klucz subskrypcji i tworzy obiekt mapy, style mapy wstępnie jest ustawiona na grayscale_dark. Zobacz [Utwórz mapę](./map-create.md) instrukcje dotyczące sposobu tworzenia mapy.

Drugi blok kodu tworzy selektor stylu, za pomocą atlas [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) konstruktora.

Selektor stylu umożliwia wybór stylu mapy. Trzeci blok kodu dodaje selektor stylu do mapy, przy użyciu mapy [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) metody. Selektor stylu mieści się w planie **odbiornik zdarzeń** zapewnienie ładuje po mapy ładuje pełni.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Dodawanie formantu do map:

> [!div class="nextstepaction"]
> [Dodawanie kontrolki mapy](./map-add-controls.md)

Dodaj mapę kodu pin:

> [!div class="nextstepaction"]
> [Dodawanie numeru pin](./map-add-pin.md)
