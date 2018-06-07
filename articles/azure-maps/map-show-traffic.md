---
title: Pokaż ruchu przy użyciu map Azure | Dokumentacja firmy Microsoft
description: Jak wyświetlać dane o ruchu na mapie kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6ff7a0270509c244fc97bd04d8ba648fd262dc58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600123"
---
# <a name="show-traffic-on-the-map"></a>Pokaż ruchu na mapie

W tym artykule przedstawiono sposób wyświetlania informacji ruch i zdarzenia na mapie. 

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='456' scrolling='no' title='Pokaż ruchu na mapie' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Pokaż ruchu na mapie</a> mapach Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Widać [Tworzenie mapy](map-create.md) instrukcje.

Drugi bloku kodu używa [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic) funkcja klasy mapy do renderowania przepływu ruchu i zdarzeń na mapie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 
* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic)

Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 
* [Interakcja z mapą — zdarzenia myszy](./map-events.md)
* [Tworzenie mapy dostępny](./map-accessibility.md)

Zapoznaj się z naszym [kod przykładową stronę](http://aka.ms/AzureMapsSamples) dla scenariuszy mapowania.
