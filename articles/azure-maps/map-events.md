---
title: Obsługa zdarzeń myszy przy użyciu Azure Maps | Microsoft Docs
description: Jak utworzyć interaktywną mapę sieci Web SDK przy użyciu zdarzeń mapy
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 37a3fc3178fe5caeacedfd355a6065ee189a5890
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976542"
---
# <a name="interact-with-the-map---mouse-events"></a>Korzystanie z zdarzeń dotyczących mapy — myszy

W tym artykule pokazano, jak używać właściwości [zdarzenia klasy mapy](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) do wyróżniania zdarzeń na mapie i na różnych warstwach mapy. Pokazano w nim także, jak używać właściwości zdarzenia klasy mapy do wyróżniania zdarzeń podczas korzystania z znacznika HTML.

## <a name="interact-with-the-map"></a>Korzystanie z mapy

Odtwórz przy użyciu mapy poniżej i zobacz odpowiednie zdarzenia myszy wyróżnione po prawej stronie. Możesz kliknąć **kartę js** , aby wyświetlić i edytować kod JavaScript. Możesz również kliknąć przycisk **Edytuj na CodePen** i edytować kod w CodePen.

<br/>

<iframe height='600' scrolling='no' title='Korzystanie z mapy — zdarzenia myszy' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem, korzystając <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>z zdarzeń dotyczących mapy — wskaźnik myszy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Korzystanie z warstw mapy

Poniższy kod wyróżnia nazwę zdarzeń, które są wyzwalane podczas korzystania z warstwy symboli. Warstwa symbol, bąbelki, linia i Wielokąt obsługują ten sam zestaw zdarzeń. Warstwy mapy cieplnej i kafelki nie obsługują żadnego z tych zdarzeń.

<br/>

<iframe height='600' scrolling='no' title='Korzystanie z zdarzeń mapy — zdarzenia warstwy' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z piórem, korzystając <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>z zdarzeń mapy — zdarzenia warstwy</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Korzystanie z znacznika HTML

Poniższy kod dodaje zdarzenia mapy JavaScript do znacznika HTML. Wyróżnia także nazwę zdarzeń, które są wyzwalane podczas korzystania z znacznika HTML.

<br/>

<iframe height='500' scrolling='no' title='Korzystanie z zdarzeń mapy — znaczniki HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz, jak <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>korzystać z pióra z zdarzeniami znacznika mapy kodu HTML</a> według<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby zapoznać się z pełnymi przykładami kodu:

> [!div class="nextstepaction"]
> [Korzystanie z modułu Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Przykłady kodu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
