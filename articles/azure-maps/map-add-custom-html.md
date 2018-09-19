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
ms.openlocfilehash: e5cfbc7ddc10edf9b21afce73e3b7f8795fcdac9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121961"
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

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do map zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Wyświetlanie wyników wyszukiwania](./map-search-location.md)

> [!div class="nextstepaction"]
> [Uzyskiwanie informacji na podstawie współrzędnych](./map-get-information-from-coordinate.md)