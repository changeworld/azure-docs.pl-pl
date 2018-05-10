---
title: Pokaż kierunki przy użyciu map Azure | Dokumentacja firmy Microsoft
description: Jak wyświetlić między dwiema lokalizacjami na mapie kodu Javascript
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: 9007afd1bc4d2361addc2a554fab1330174e88e7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="show-directions-from-a-to-b"></a>Pokaż instrukcjami z zakresu od A do B 

W tym artykule przedstawiono sposób tworzenia żądania trasy i wyświetlenie trasy na mapie. 

## <a name="understand-the-code"></a>Kodu

<iframe height='500' scrolling='no' title='Pokaż ona instrukcje od A do B na mapie' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Pokaż instrukcjami z zakresu od A do B na mapie</a> mapach Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Widać [Tworzenie mapy](./map-create.md) instrukcje.

W drugim bloku kodu tworzy i dodaje numerów PIN na mapie, aby reprezentować początkowy i punkt końcowy trasy. Widać [dodać numer pin na mapie](map-add-pin.md) instrukcje.

Trzeci bloku kodu używa [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) funkcji klasy mapy, aby ustawić obwiedni mapy na podstawie początkowy i punkt końcowy trasy.

Wysyła czwarty blok kodu [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [interfejsu API Azure mapy trasy](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

Ostatni blok kodu analizuje przychodzące odpowiedzi. Dla pomyślnej odpowiedzi zbiera informacje współrzędne geograficzne dla każdego waypoint. Łącząc każdego waypoint do jego kolejne waypoint tworzy tablicę wierszy. Dodaje wszystkie te wiersze na mapę do renderowania trasy. Widać [Dodaj wiersz na mapie](./map-add-shape.md#addALine) instrukcje.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 

* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
