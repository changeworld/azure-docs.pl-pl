---
title: Ustawianie opcji rysowania w Azure Maps | Microsoft Docs
description: Jak ustawić dane opcji rysowania przy użyciu zestawu Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309775"
---
# <a name="set-drawing-options"></a>Ustawianie opcji rysowania

W tym artykule przedstawiono sposób zmiany środowiska użytkownika przez różne opcje [Menedżera rysunku](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-) . Możesz określić opcje dla Menedżera rysowania podczas tworzenia jego wystąpienia lub alternatywnie użyć funkcji **rysowaniamanager. SetOptions ()** , aby ustawić opcje.


## <a name="set-drawing-mode"></a>Ustaw tryb rysowania

Poniższy kod tworzy wystąpienie Menedżera rysowania i ustawia opcję **trybu** rysowania. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Poniższy kod stanowi kompletny przykład sposobu ustawiania trybu rysowania Menedżera rysowania. Kliknij mapę, aby rozpocząć rysowanie wielokąta.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rysowanie wielokątu" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz pióro <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>narysuj Wielokąt</a> o Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="set-interaction-type"></a>Ustaw typ interakcji

Poniższy kod ustawia typ interakcji, z którą Menedżer rysowania powinien przestrzegać. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

Poniżej znajduje się przykładowy kod implementujący funkcje, które umożliwiają swobodne rysowanie na mapie, trzymając jednocześnie lewy przycisk myszy i przeciągając go. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rysowanie swobodne" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>rysunek bezpłatny</a> pióro według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w witrynie <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="customizing-drawing-options"></a>Dostosowywanie opcji rysowania

Poprzednie przykłady pokazują, jak dostosować opcje rysowania podczas tworzenia wystąpienia Menedżera rysowania. Możesz również ustawić opcje Menedżera rysowania przy użyciu funkcji **rysowaniamanager. SetOptions ()** . Poniżej znajduje się narzędzie do testowania dostosowywania wszystkich opcji Menedżera rysowania przy użyciu funkcji SetOptions.

<br/>

<iframe height="685" title="Dostosuj Menedżera rysowania" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Zobacz pióro <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>Pobierz dane kształtu</a> według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [Zmapować](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Menedżer rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rysowanie paska narzędzi](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
