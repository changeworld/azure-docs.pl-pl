---
title: Pokaż ruchu przy użyciu usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak wyświetlać dane o ruchu na mapie kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6d5c721ab84c28bae9415dceeaa09fd12cc05824
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45733026"
---
# <a name="show-traffic-on-the-map"></a>Wyświetlanie ruchu na mapie

W tym artykule przedstawiono sposób wyświetlania informacji o ruchu i zdarzeń na mapie.

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='456' scrolling='no' title='Wyświetlanie ruchu na mapie' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>wyświetlanie ruchu na mapie</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](map-create.md) instrukcje.

Drugi blok kodu używa [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic) funkcji klasy mapy do renderowania przepływów ruchu i zdarzeń na mapie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby wyświetlić przykłady pełnego kodu:

> [!div class="nextstepaction"]
> [Strona przykładowy kod](http://aka.ms/AzureMapsSamples)

Zwiększyć komfort pracy użytkowników:

> [!div class="nextstepaction"]
> [Mapy interakcji ze zdarzeniami myszy](./map-events.md)

> [!div class="nextstepaction"]
> [Tworzenie mapy dostępne](./map-accessibility.md)
