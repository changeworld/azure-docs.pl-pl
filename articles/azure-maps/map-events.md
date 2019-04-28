---
title: Obsługa zdarzeń myszy przy użyciu usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak utworzyć mapę interaktywną Javascript za pomocą mapy zdarzeń
author: jingjing-z
ms.author: jinzh
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4fce8eae25942d098bb3f3277938bfaa3dafa00b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770452"
---
# <a name="interact-with-the-map---mouse-events"></a>Interakcja z mapą — zdarzenia myszy

W tym artykule dowiesz się, jak używać [map — klasa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [zdarzenia](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) właściwości w celu wyróżnienia zdarzeń na mapie i na różnych warstw mapy. On również pokazano, jak wyróżnienia zdarzeń podczas interakcji z znaczników HTML za pomocą właściwości zdarzeń klasy mapy.

## <a name="interact-with-the-map"></a>Interakcja z mapą

<iframe height='600' scrolling='no' title='Interakcja z mapą — zdarzenia myszy' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>interakcja z mapą — zdarzenia myszy</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Poeksperymentuj z powyższych mapy i zobacz pokrewnych zdarzeń myszy wyróżnione po prawej stronie. Możesz kliknąć **kartę JS** do wyświetlania i edytowania kodu JavaScript. Możesz również kliknąć **edytować na funkcji codepen można** znajdujący się i edytowanie kodu w funkcji codepen można.

## <a name="interact-with-map-layers"></a>Korzystać z warstwy mapy

<iframe height='600' scrolling='no' title='Interakcja z mapą — warstwa zdarzeń' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>interakcja z mapą — warstwa zdarzeń</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Powyższy kod wyróżnia nazwę zdarzenia, które Pobierz uruchamiane, podczas wchodzenia w interakcje z warstwą symboli. Symbol, bąbelkowy, wiersza i wielokąta warstwy wszystkich obsługują ten sam zestaw zdarzeń. Warstwa kafelków nie obsługuje dowolne z tych zdarzeń.

## <a name="interact-with-html-marker"></a>Interakcja z znacznik HTML

<iframe height='500' scrolling='no' title='Interakcja z mapą - zdarzeń znaczników HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interakcja z mapą - zdarzeń znaczników HTML</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

Powyższy kod dodaje zdarzenia mapy Javascript do znaczników HTML. To również wyodrębnić nazwę zdarzenia, które Pobierz uruchamiane, podczas wchodzenia w interakcje przy użyciu znaczników HTML.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [Mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać przykłady pełnego kodu:

> [!div class="nextstepaction"]
> [Za pomocą modułu usług mapy platformy Azure](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Strona przykładowy kod](https://aka.ms/AzureMapsSamples)
