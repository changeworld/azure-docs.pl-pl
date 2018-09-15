---
title: Mapowanie funkcji usługi Azure Maps stylu | Dokumentacja firmy Microsoft
description: Więcej informacji na temat usługi Azure Maps style powiązane funkcje.
author: walsehgal
ms.author: v-musehg
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9122a349fd81cf723e21cd17e09e15d1d9f64503
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634765"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Wybierz styl z mapy usługi Azure Maps

Usługi Azure Maps ma cztery style różnych mapowań do wyboru. Aby uzyskać więcej informacji o stylach mapy, zobacz [obsługiwane style mapy w usługi Azure Maps](./supported-map-styles.md). W tym artykule pokazano, jak za pomocą funkcji stylu związane ustawienie stylu przy ładowaniu mapy, ustawienie nowego stylu i korzystanie z kontrolki selektor stylu.

## <a name="setting-style-on-map-load"></a>Ustawienia stylu przy ładowaniu mapy

<iframe height='500' scrolling='no' title='Ustawienia stylu przy ładowaniu mapy' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>ustawienia stylu przy ładowaniu mapy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Powyższy kod tworzy obiekt mapy ze stylem równa skali szarości. Zobacz [Utwórz mapę](./map-create.md) instrukcje dotyczące sposobu tworzenia mapy.

## <a name="updating-the-style"></a>Aktualizacja stylu

<iframe height='500' scrolling='no' title='Aktualizacja stylu' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>aktualizowanie styl</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu w powyższy kod tworzy obiekt mapy bez wstępnie ustawienia stylu. Zobacz [Utwórz mapę](./map-create.md) instrukcje dotyczące sposobu tworzenia mapy.

Drugi zegara kodu używa mapy [metody setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle) metodę, aby ustawić style mapy do satelity.

## <a name="adding-the-style-picker"></a>Dodawanie selektor stylu

<iframe height='500' scrolling='no' title='Dodawanie selektor stylu' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Dodawanie selektor stylu</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Pierwszy blok kodu w powyższy kod tworzy obiekt mapy bez wstępnie ustawienia stylu. Zobacz [Utwórz mapę](./map-create.md) instrukcje dotyczące sposobu tworzenia mapy.

Drugi blok kodu tworzy selektor stylu, za pomocą atlas [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) konstruktora.

Selektor stylu umożliwia wybór stylu mapy. Trzeci blok kodu dodaje selektor stylu do mapy, przy użyciu mapy [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) metody.

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
