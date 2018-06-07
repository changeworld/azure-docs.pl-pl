---
title: Dodaj menu podręczne przy użyciu map Azure | Dokumentacja firmy Microsoft
description: Jak dodać wyskakujące okienko z mapą kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d9eeac28b204af0bb7d8e204762aae854055b33a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599307"
---
# <a name="add-a-popup-to-the-map"></a>Dodawanie menu podręcznego do mapy

W tym artykule przedstawiono sposób dodawania elementu popup do mapy.  

## <a name="understand-the-code"></a>Zrozumienie kodu

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Dodawanie menu podręcznego do mapy' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>Dodawanie menu podręcznego do mapy</a> mapach Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Widać [Tworzenie mapy](./map-create.md) instrukcje.

W drugim bloku kodu tworzy numeru pin i dodaj go do mapy. Widać [dodać do mapy numer pin](./map-add-pin.md) instrukcje.

Trzeci bloku kodu tworzy mają być wyświetlane w menu podręcznym. Zawartość menu podręczne jest elementu HTML. 

Tworzy czwarty blok kodu [obiekt menu podręczne](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) za pośrednictwem `new atlas.Popup()`. Menu podręczne właściwości, takie jak zawartość i pozycji są częścią [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popupoptions?view=azure-iot-typescript-latest). PopupOptions można zdefiniować w Konstruktorze menu podręczne lub za pośrednictwem [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) funkcja klasy menu podręczne.

Korzysta z ostatniego bloku kodu [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener) funkcja klasy mapy do nasłuchiwania zdarzeń etykietka wskaźnika myszy na numery PIN i używa [Otwórz](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open) funkcja klasy menu podręczne, aby otworzyć menu podręcznego, jeśli wystąpi zdarzenie.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 

* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Menu podręczne](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Otwórz](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Zamknij](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)
    
Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 
* [Dodawanie kształtów](./map-add-shape.md)
* [Dodaj niestandardowy kod HTML](./map-add-custom-html.md)
