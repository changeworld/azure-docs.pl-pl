---
title: Moduł narzędzi do rysowania | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak ustawić dane opcji rysowania przy użyciu zestawu Microsoft Azure Maps Web SDK
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334308"
---
# <a name="use-the-drawing-tools-module"></a>Korzystanie z modułu narzędzi do rysowania

Zestaw Azure Maps Web SDK udostępnia *moduł narzędzi do rysowania*. Ten moduł ułatwia rysowanie i edytowanie kształtów na mapie za pomocą urządzenia wejściowego, takiego jak mysz lub ekran dotykowy. Podstawową klasą tego modułu jest [menedżer rysunku](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). Menedżer rysunków zapewnia wszystkie możliwości potrzebne do rysowania i edytowania kształtów na mapie. Może być używany bezpośrednio i jest zintegrowany z niestandardowym interfejsem użytkownika paska narzędzi. Można również użyć wbudowanej klasy [paska narzędzi do rysowania.](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest) 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Ładowanie modułu narzędzi do rysowania na stronie sieci Web

1. Utwórz nowy plik HTML i [zaimplementuj mapę w zwykły sposób.](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)
2. Załaduj moduł narzędzi do rysowania usługi Azure Maps. Można go załadować na jeden z dwóch sposobów:
    - Użyj globalnie hostowane, usługi Azure Content Delivery Network wersji modułu usług Usługi Usługi Usługi Azure Maps. Dodaj odwołanie do arkusza stylów JavaScript i CSS w `<head>` elemencie pliku:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Możesz też załadować moduł narzędzi do rysowania dla kodu źródłowego zestawu SDK sieci Web usługi Azure Maps lokalnie przy użyciu pakietu npm [azure-maps-drawing-tools,](https://www.npmjs.com/package/azure-maps-drawing-tools) a następnie hostować go za pomocą aplikacji. Ten pakiet zawiera również definicje TypeScript. Użyj tego polecenia:
    
        > **npm zainstalować azure-maps-drawing-tools**
    
        Następnie dodaj odwołanie do arkusza stylów JavaScript `<head>` i CSS w elemencie pliku:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Użyj menedżera rysunku bezpośrednio

Po załadowaniu modułu narzędzi do rysowania w aplikacji można włączyć możliwości rysowania i edycji za pomocą [menedżera rysunków](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-). Można określić opcje menedżera rysunku podczas tworzenia wystąpienia `drawingManager.setOptions()` lub alternatywnie użyć funkcji.

### <a name="set-the-drawing-mode"></a>Ustawianie trybu rysowania

Poniższy kod tworzy wystąpienie menedżera rysunku i ustawia opcję **trybu** rysowania. 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

Poniższy kod jest kompletnym przykładem uruchamiania sposobu ustawiania trybu rysowania menedżera rysunku. Kliknij mapę, aby rozpocząć rysowanie wielokąta.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rysowanie wielokąta" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>pióro Rysowanie wielokąta</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Ustawianie typu interakcji

Menedżer rysunków obsługuje trzy różne sposoby interakcji z mapą w celu rysowania kształtów.

* `click`- Współrzędne są dodawane po kliknięciu myszy lub dotyku.
* `freehand `- Współrzędne są dodawane, gdy mysz lub dotyk jest przeciągany na mapie. 
* `hybrid`- Współrzędne są dodawane po kliknięciu lub przecięciu myszy lub dotyku.

Poniższy kod włącza tryb rysowania wielokąta i ustawia typ interakcji rysunku, do `freehand`których menedżer rysunku powinien przestrzegać . 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Ten przykładowy kod implementuje funkcjonalność rysowania wielokąta na mapie. Wystarczy przytrzymać lewy przycisk myszy i przeciągnąć go swobodnie.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rysowanie z wolnej ręki" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zobacz <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>rysunek pióra przez</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Dostosowywanie opcji rysowania

W poprzednich przykładach pokazano, jak dostosować opcje rysowania podczas tworzenia wystąpienia Menedżera rysunków. Opcje Menedżera rysunków można również `drawingManager.setOptions()` ustawić za pomocą tej funkcji. Poniżej znajduje się narzędzie do testowania dostosowywania wszystkich opcji menedżera rysunków za pomocą funkcji setOptions.

<br/>

<iframe height="685" title="Dostosowywanie menedżera rysunków" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Zobacz pióro Pobierz dane<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>kształtu</a> przez usługi Azure Maps ( ) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak korzystać z dodatkowych funkcji modułu narzędzi do rysowania:

> [!div class="nextstepaction"]
> [Dodawanie paska narzędzi rysowania](map-add-drawing-toolbar.md)

> [!div class="nextstepaction"]
> [Pobieranie danych kształtów](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagowanie na zdarzenia rysowania](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Typy interakcji i skróty klawiaturowe](drawing-tools-interactions-keyboard-shortcuts.md)

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Mapę](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Kierownik rysunku](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Pasek narzędzi rysowania](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
