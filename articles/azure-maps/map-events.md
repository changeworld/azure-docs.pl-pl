---
title: Obsługa zdarzeń myszy przy użyciu Azure Maps | Microsoft Docs
description: Jak utworzyć interaktywną mapę JavaScript ze zdarzeniami mapy
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: da6b183155de0fbc370751254a6842343d280874
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638969"
---
# <a name="interact-with-the-map---mouse-events"></a>Korzystanie z zdarzeń dotyczących mapy — myszy

W tym artykule pokazano, jak używać właściwości [zdarzenia](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [klasy mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do wyróżniania zdarzeń na mapie i na różnych warstwach mapy. Pokazano w nim także, jak używać właściwości zdarzenia klasy mapy do wyróżniania zdarzeń podczas korzystania z znacznika HTML.

## <a name="interact-with-the-map"></a>Korzystanie z mapy

<iframe height='600' scrolling='no' title='Korzystanie z mapy — zdarzenia myszy' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem, korzystając <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>z zdarzeń dotyczących mapy — wskaźnik myszy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

Odtwórz przy użyciu powyższej mapy i zobacz odpowiednie zdarzenia myszy wyróżnione po prawej stronie. Możesz kliknąć **kartę js** , aby wyświetlić i edytować kod JavaScript. Możesz również kliknąć przycisk **Edytuj na CodePen** i edytować kod w CodePen.

## <a name="interact-with-map-layers"></a>Korzystanie z warstw mapy

<iframe height='600' scrolling='no' title='Korzystanie z zdarzeń mapy — zdarzenia warstwy' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem, korzystając <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>z zdarzeń mapy — zdarzenia warstwy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

Powyższy kod wyróżnia nazwę zdarzeń, które są wyzwalane podczas korzystania z warstwy symboli. Warstwa symbol, bąbelki, linia i Wielokąt obsługują ten sam zestaw zdarzeń. Warstwa kafelków nie obsługuje żadnego z tych zdarzeń.

## <a name="interact-with-html-marker"></a>Korzystanie z znacznika HTML

<iframe height='500' scrolling='no' title='Korzystanie z zdarzeń mapy — znaczniki HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz, jak <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>korzystać z pióra z zdarzeniami znacznika mapy kodu HTML</a> według<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () w <a href='https://codepen.io'>CodePen</a>.
</iframe>

Powyższy kod dodaje zdarzenia mapy JavaScript do znacznika HTML. Wyróżnia także nazwę zdarzeń, które są wyzwalane podczas korzystania z znacznika HTML.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby zapoznać się z pełnymi przykładami kodu:

> [!div class="nextstepaction"]
> [Korzystanie z modułu Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Przykładowa strona kodowa](https://aka.ms/AzureMapsSamples)
