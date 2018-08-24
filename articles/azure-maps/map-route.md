---
title: Wyświetlanie instrukcji dotyczących trasy z usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak wyświetlić kierunkach między dwiema lokalizacjami na mapie kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 52462c1c5a2a1a9698a2b51708e63b1bb1664f6e
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745540"
---
# <a name="show-directions-from-a-to-b"></a>Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B 

W tym artykule pokazano, jak utworzyć żądanie trasy i wyświetlania trasy na mapie. 

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Wyświetlanie instrukcji dotyczących trasy od A do B na mapie' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Wyświetlanie instrukcji dotyczących trasy od A do B na mapie</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu tworzy i dodaje pinezki na mapie, aby reprezentować początkowy i punkt końcowy trasy. Możesz zobaczyć [Dodawanie numeru pin na mapie](map-add-pin.md) instrukcje.

Trzeci blok kodu używa [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) funkcji klasy mapy, aby ustawić pole mapy oparte na początkowy i punkt końcowy trasy.

Czwarty bloku kodu wysyła [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [interfejsu API usługi Azure Maps Route](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Ostatni blok kodu analizuje przychodzącą odpowiedź. Pomyślnej odpowiedzi zbiera szerokości i długości geograficznej informacje dotyczące każdego punkt nawigacyjny. Tworzy tablicę wierszy, łącząc każdy punkt nawigacyjny do jego kolejnych punkt nawigacyjny. Dodaje te wiersze na mapę do renderowania trasy. Możesz zobaczyć [Dodaj wiersz na mapie](./map-add-shape.md#addALine) instrukcje.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 

* [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 
* [Wyświetlanie ruchu na mapie](./map-show-traffic.md)
* [Interakcja z mapą — zdarzenia myszy](./map-events.md)
