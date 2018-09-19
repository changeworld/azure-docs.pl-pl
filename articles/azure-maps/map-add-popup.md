---
title: Dodawanie menu podręcznego za pomocą usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać okno podręczne do mapy Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 09/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 76a7e230491d5e524a1d73437a56d12594cfebe2
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127441"
---
# <a name="add-a-popup-to-the-map"></a>Dodawanie menu podręcznego do mapy

W tym artykule pokazano, jak dodać okno podręczne z mapą.  

## <a name="understand-the-code"></a>Zrozumienie kodu

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Dodawanie menu podręcznego do mapy' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>dodać okno podręczne z mapą</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu tworzy numeru pin i dodać je do mapy. Możesz zobaczyć [Dodawanie numeru pin do mapy](./map-add-pin.md) instrukcje.

Trzeci bloku kodu tworzy zawartość ma być wyświetlany w menu podręcznego. Zawartość okna podręcznego jest elementu HTML.

Czwarty bloku kodu tworzy [obiekt menu podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) za pośrednictwem `new atlas.Popup()`. Menu podręczne właściwości, takie jak zawartość i położenie są częścią [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest). PopupOptions można zdefiniować w Konstruktorze podręczny lub za pośrednictwem [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) funkcji klasy okna podręcznego.

Ostatni blok kodu używa [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener) funkcji klasy mapy do nasłuchiwania zdarzeń mouseover numerów PIN i używa [Otwórz](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) funkcji klasy okna podręcznego, aby otworzyć okno podręczne, jeśli wystąpi zdarzenie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Okno podręczne](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Zobacz następujące artykuły doskonałe przykłady pełnego kodu:

> [!div class="nextstepaction"]
> [Dodawanie kształtu](./map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodawanie niestandardowego kodu HTML](./map-add-custom-html.md)
