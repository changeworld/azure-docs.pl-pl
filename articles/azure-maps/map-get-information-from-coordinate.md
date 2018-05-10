---
title: Pokaż informacje o współrzędnych przy użyciu map Azure | Dokumentacja firmy Microsoft
description: Jak wyświetlić informacje o adresie na mapie, gdy użytkownik wybierze współrzędnych
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
ms.openlocfilehash: bb8644724cc872a0a8bc331e76251218492fd93d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="get-information-from-a-coordinate"></a>Uzyskaj informacje z współrzędnych

W tym artykule przedstawiono sposób wyszukiwania wstecznego adresów, a po kliknięciu myszą Pokaż adres klikniętej lokalizacji, w menu podręcznym. 

## <a name="understand-the-code"></a>Kodu

<iframe height='500' scrolling='no' title='Uzyskaj informacje z współrzędnych' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Uzyskaj informacje z współrzędnych</a> mapach Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Widać [Tworzenie mapy](./map-create.md) instrukcje.

W drugim bloku kodu aktualizuje styl kursor myszy na wskaźnik.

Trzeci bloku kodu tworzy menu podręcznego. Widać [Dodaj menu podręcznego na mapie](./map-add-popup.md) instrukcje.

Ostatni blok kodu dodaje odbiornik zdarzeń dla kliknięcia myszą. Po kliknięciu myszą, wysyła [XMLHttpRequest](https://xhr.spec.whatwg.org/) do [Azure odwrotna adres wyszukiwania interfejsu API map](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Dla pomyślnej odpowiedzi, zbiera adres klikniętej lokalizacji, a definiuje menu podręczne zawartości i pozycji za pomocą [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) funkcja klasy podręcznego

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 
* [Wyszukiwanie wsteczne adresu](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Menu podręczne](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Otwórz](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Zamknij](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)
