---
title: Pokaż ruch z Azure Maps | Microsoft Docs
description: Jak wyświetlać dane o ruchu na mapie JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 55bfc434082b2d5b7de193e969fc34f710657cdb
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638636"
---
# <a name="show-traffic-on-the-map"></a>Pokaż ruch na mapie

W tym artykule przedstawiono sposób wyświetlania informacji o ruchu i zdarzeniach na mapie.

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='456' scrolling='no' title='Pokaż ruch na mapie' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Pokaż ruch na mapie</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu konstruuje obiekt mapy. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](map-create.md) .

Drugi blok kodu używa funkcji settraffic w funkcji [detektora zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) mapy do renderowania przepływów ruchu i zdarzeń na mapie. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zapoznaj się z następującymi artykułami dotyczącymi pełnych przykładów kodu:

> [!div class="nextstepaction"]
> [Przykładowa strona kodowa](https://aka.ms/AzureMapsSamples)

Ulepsz środowisko użytkownika:

> [!div class="nextstepaction"]
> [Interakcja mapy ze zdarzeniami myszy](./map-events.md)

> [!div class="nextstepaction"]
> [Kompilowanie dostępnej mapy](./map-accessibility.md)
