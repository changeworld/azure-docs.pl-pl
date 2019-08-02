---
title: Dodaj warstwę symboli do Azure Maps | Microsoft Docs
description: Jak dodać symbole do mapy JavaScript
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ba5d5d3aaa6a83dbcc5e5072872bca0fcd22bbf9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638724"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Dodawanie warstwy symboli do mapy

W tym artykule pokazano, jak można renderować dane punktu ze źródła danych jako warstwę symboli na mapie. Warstwy symboli są renderowane przy użyciu WebGL i obsługują znacznie większe zestawy punktów niż znaczniki HTML, ale nie obsługują tradycyjnych stylów CSS i HTML do ustawiania stylów.  

> [!TIP]
> Warstwy symboli domyślnie będą renderować współrzędne wszystkich geometrie w źródle danych. Aby ograniczyć warstwę w taki sposób, że renderuje tylko funkcje geometrii punktów `filter` , ustawia właściwość warstwy na `['==', ['geometry-type'], 'Point']` lub `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Jeśli chcesz również uwzględnić funkcje MultiPoint.

## <a name="add-a-symbol-layer"></a>Dodawanie warstwy symboli

<iframe height='500' scrolling='no' title='Przełącz lokalizację numeru PIN' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Sprawdź <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>lokalizację numeru PIN przełączania</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Pierwszy blok kodu powyżej konstruuje obiekt mapy. Aby uzyskać instrukcje, zobacz [Tworzenie mapy](./map-create.md) .

W drugim bloku kodu obiekt źródła danych jest tworzony przy użyciu klasy [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) . Element [Feature] zawierający geometrię [punktu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) jest opakowany przez klasę [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) , aby ułatwić jego aktualizowanie, a następnie utworzenie i dodanie do źródła danych.

Trzeci blok kodu tworzy [odbiornik zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) i aktualizuje współrzędne punktu po kliknięciu myszą za pomocą metody setwspółrzędnej klasy kształtu [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) .

[Warstwa symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) używa tekstu lub ikon do renderowania danych opartych na punkcie [w postaci symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) na mapie.  Źródło danych, odbiornik zdarzeń kliknięcia i warstwa symbol są tworzone i dodawane do mapy w ramach `ready` funkcji detektora [zdarzeń](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) , aby upewnić się, że punkt jest wyświetlany po załadowaniu mapy i gotowości do uzyskania dostępu.

> [!TIP]
> Domyślnie w przypadku wydajności warstwy symboli optymalizują renderowanie symboli, ukrywając symbole, które nakładają się na siebie. Podczas powiększania ukrytych symboli stają się widoczne. Aby wyłączyć tę funkcję i renderować wszystkie symbole przez cały czas, ustaw `allowOverlap` Właściwość `iconOptions` opcji na `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Dodaj niestandardową ikonę do warstwy symboli

Warstwy symboli są renderowane przy użyciu WebGL. Ponieważ wszystkie zasoby, takie jak obrazy ikon, muszą zostać załadowane do kontekstu WebGL. Ten przykład pokazuje, jak dodać niestandardową ikonę do zasobów mapy, a następnie użyć jej do renderowania danych punktu z symbolem niestandardowym na mapie. `textField` Właściwość warstwy symboli wymaga określenia wyrażenia. W tym przypadku chcemy renderować Właściwość temperatury, ale ponieważ jest to liczba, należy ją przekonwertować na ciąg. Ponadto chcemy dołączyć do niego "°F". Do tego celu można użyć wyrażenia; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ikona obrazu niestandardowego symbolu' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>ikonę niestandardowego obrazu symbolu</a> pióra przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-symbol-layer"></a>Dostosowywanie warstwy symboli 

Warstwa symboli ma dostępne wiele opcji stylów. Oto narzędzie do testowania różnych opcji stylów.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy symboli' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Opcje warstwy symboli</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodawanie okna podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie kształtu](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodaj marki HTML](map-add-bubble-layer.md)
