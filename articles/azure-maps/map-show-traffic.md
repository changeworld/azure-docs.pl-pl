---
title: Pokazywale ruchu na mapie | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak wyświetlić dane o ruchu na mapie przy użyciu microsoft azure maps web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534818"
---
# <a name="show-traffic-on-the-map"></a>Pokazywal ruchu na mapie

Istnieją dwa typy danych o ruchu dostępne w usłudze Azure Maps:

- Dane o zdarzeniach — składa się z danych opartych na punktach i liniach dotyczących takich rzeczy, jak budowa, zamknięcia dróg i wypadki.
- Dane przepływu — zapewnia metryki dotyczące przepływu ruchu na drogach. Często dane o przepływie ruchu są używane do kolorowania dróg. Kolory są oparte na tym, ile ruchu spowalnia przepływ, w stosunku do ograniczenia prędkości lub innej metryki. Dane przepływu ruchu w usłudze Azure Maps mają trzy różne metryki pomiaru:
    - `relative`- jest w stosunku do prędkości swobodnego przepływu drogi.
    - `absolute`- to absolutna prędkość wszystkich pojazdów na drodze.
    - `relative-delay`- wyświetla obszary, które są wolniejsze niż średnie oczekiwane opóźnienie.

Poniższy kod pokazuje, jak wyświetlić dane o ruchu drogowym na mapie.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Poniżej znajduje się kompletny przykładowy kod uruchomiony powyższej funkcji.

<br/>

<iframe height='500' scrolling='no' title='Pokazywale ruchu na mapie' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Pokaż ruch na</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>mapie przez usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="traffic-overlay-options"></a>Opcje nakładki ruchu

Poniższe narzędzie umożliwia przełączanie między różnymi ustawieniami nakładki ruchu, aby zobaczyć, jak zmienia się renderowanie. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opcje nakładki ruchu" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>opcje nakładek ruchu</a> piórowego według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Mapę](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Opomowania ruchu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Zwiększ komfort użytkowania:

> [!div class="nextstepaction"]
> [Interakcja mapy ze zdarzeniami myszy](map-events.md)

> [!div class="nextstepaction"]
> [Tworzenie mapy z ułatwieniami dostępu](map-accessibility.md)

> [!div class="nextstepaction"]
> [Przykładowa strona kodu](https://aka.ms/AzureMapsSamples)
