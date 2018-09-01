---
title: Dodawanie niestandardowego kodu html w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać niestandardowy kod html do mapy Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5060839be244f55700b40735e598964a0b7b6709
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382049"
---
# <a name="add-custom-html-to-the-map"></a>Dodawanie niestandardowego kodu HTML do mapy

W tym artykule przedstawiono sposób dodawania niestandardowego kodu HTML, takich jak plik obrazu do mapy. 

## <a name="understand-the-code"></a>Zrozumienie kodu

<iframe height='466' scrolling='no' title='Dodawanie niestandardowego kodu html do mapy - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Dodawanie niestandardowego kodu html do mapy - png</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

Drugi blok kodu tworzy HTML element na podstawie obrazu.

Ostatni blok kodu używa [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addhtml) funkcji klasy mapy, aby dodać obraz do określonej pozycji mapy.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule: 
* [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addhtml)
    
Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły: 
* [Wyświetlanie wyników wyszukiwania](./map-search-location.md)
* [Uzyskiwanie informacji na podstawie współrzędnych](./map-get-information-from-coordinate.md)

