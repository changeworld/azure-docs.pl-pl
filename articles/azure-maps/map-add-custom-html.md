---
title: Dodaj niestandardowy kod html w społeczności Maps Azure | Dokumentacja firmy Microsoft
description: Jak dodać niestandardowe html do mapy kodu Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3ee1f394536061cecaa5d775650149b30098560e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600225"
---
# <a name="add-custom-html-to-the-map"></a>Dodaj niestandardowy kod HTML do mapy

W tym artykule przedstawiono sposób dodawania niestandardowych HTML, takich jak plik obrazu do mapy. 

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='466' scrolling='no' title='Dodaj niestandardowy kod html do mapy — png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Dodaj niestandardowy kod html do mapy — png</a> mapach Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Widać [Tworzenie mapy](./map-create.md) instrukcje.

W drugim bloku kodu tworzy element kodu HTML z obrazu.

Korzysta z ostatniego bloku kodu [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml) funkcji klasy mapy, aby dodać obraz do określonej pozycji mapy.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 
* [mapy](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml)
    
Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 
* [Pokaż wyniki wyszukiwania](./map-search-location.md)
* [Uzyskaj informacje z współrzędnych](./map-get-information-from-coordinate.md)

