---
title: Pokaż kierunki przy użyciu map Azure | Dokumentacja firmy Microsoft
description: Jak wyświetlić między dwiema lokalizacjami na mapie kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 5e9ab73ddc16517e17894cddd9bc102f3941f00c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "35759799"
---
# <a name="show-directions-from-a-to-b"></a>Wyświetlanie instrukcji dotyczących trasy z punktu A do punktu B 

W tym artykule przedstawiono sposób tworzenia żądania trasy i wyświetlenie trasy na mapie. 

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='500' scrolling='no' title='Pokaż ona instrukcje od A do B na mapie' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Pokaż instrukcjami z zakresu od A do B na mapie</a> mapach Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Widać [Tworzenie mapy](./map-create.md) instrukcje.

W drugim bloku kodu tworzy i dodaje numerów PIN na mapie, aby reprezentować początkowy i punkt końcowy trasy. Widać [dodać numer pin na mapie](map-add-pin.md) instrukcje.

Trzeci bloku kodu używa [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) funkcji klasy mapy, aby ustawić obwiedni mapy na podstawie początkowy i punkt końcowy trasy.

Wysyła czwarty blok kodu [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [interfejsu API Azure mapy trasy](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Ostatni blok kodu analizuje przychodzące odpowiedzi. Dla pomyślnej odpowiedzi zbiera informacje współrzędne geograficzne dla każdego waypoint. Łącząc każdego waypoint do jego kolejne waypoint tworzy tablicę wierszy. Dodaje wszystkie te wiersze na mapę do renderowania trasy. Widać [Dodaj wiersz na mapie](./map-add-shape.md#addALine) instrukcje.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 

* [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)

Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 
* [Pokaż ruchu na mapie](./map-show-traffic.md)
* [Interakcja z mapą - zdarzenia myszy](./map-events.md)
