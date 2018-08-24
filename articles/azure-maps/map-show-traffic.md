---
title: Pokaż ruchu przy użyciu usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak wyświetlać dane o ruchu na mapie kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2499822db587dbf47dccedf6039d0fb5823c58b5
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746082"
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
* [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic)

Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 
* [Interakcja z mapą — zdarzenia myszy](./map-events.md)
* [Tworzenie mapy dostępne](./map-accessibility.md)

Zapoznaj się z naszym [kodu przykładowej strony](http://aka.ms/AzureMapsSamples) do scenariuszy mapowania.
