---
title: Dodaj warstwę symboli do Azure Maps | Microsoft Docs
description: Jak dodać symbole do Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 10f6a7ef92bfd6558ed93e7fb40df9e48e1b92f5
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976173"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Dodawanie warstwy symboli do mapy

Symbol można połączyć ze źródłem danych i służy do renderowania ikony i/lub tekstu w danym punkcie. Warstwy symboli są renderowane przy użyciu WebGL i mogą być używane do renderowania dużych kolekcji punktów na mapie. Ta warstwa może renderować wiele większej ilości danych na mapie, z dobrą wydajnością, niż to, co jest osiągalne przy użyciu znaczników HTML. Jednak warstwa symboli nie obsługuje tradycyjnych stylów CSS i HTML do ustawiania stylów.  

> [!TIP]
> Warstwy symboli domyślnie będą renderować współrzędne wszystkich geometrie w źródle danych. Aby ograniczyć warstwę w taki sposób, że renderuje tylko funkcje geometrii punktów `filter` , ustawia właściwość warstwy na `['==', ['geometry-type'], 'Point']` lub `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Jeśli chcesz również uwzględnić funkcje MultiPoint.

## <a name="add-a-symbol-layer"></a>Dodawanie warstwy symboli

Aby dodać warstwę symboli do mapy i renderować dane, najpierw należy utworzyć źródło danych i dodać je do mapy. Warstwę symboli można następnie utworzyć i przesłać do źródła danych w celu pobrania danych z programu. Na koniec dane muszą zostać dodane do źródła danych, aby było możliwe renderowanie. Poniższy kod przedstawia kod, który należy dodać do mapy po załadowaniu, aby renderować pojedynczy punkt na mapie przy użyciu warstwy symboli. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Istnieją cztery różne typy danych punktowych, które można dodać do mapy:

- Geometria punktu GEOJSON — ten obiekt zawiera tylko współrzędną punktu i nic innego. Klasy `atlas.data.Point` pomocnika można użyć do łatwego tworzenia tych obiektów.
- Geometria GEOJSON MultiPoint — ten obiekt zawiera współrzędne wielu punktów, ale nic innego. Klasy `atlas.data.MultiPoint` pomocnika można użyć do łatwego tworzenia tych obiektów.
- Funkcja GEOJSON — ten obiekt składa się z geometrii GEOJSON oraz zestawu właściwości, które zawierają metadane skojarzone z geometrią. Klasy `atlas.data.Feature` pomocnika można użyć do łatwego tworzenia tych obiektów.
- `atlas.Shape`Klasa jest podobna do funkcji GEOJSON, która składa się z geometrii GEOJSON oraz zestawu właściwości, które zawierają metadane skojarzone z geometrią. Jeśli obiekt GEOJSON zostanie dodany do źródła danych, można go łatwo renderować w warstwie, jednak jeśli właściwość współrzędnej tego obiektu GEOJSON zostanie zaktualizowana, źródło danych i mapa nie zmienią się, ponieważ nie ma żadnego mechanizmu w obiekcie JSON do wyzwolenia aktualizacji. Klasa Shape zawiera funkcje służące do aktualizowania danych, które zawiera, a po dokonaniu zmiany źródło danych i mapa są automatycznie powiadamiane i aktualizowane. 

Poniższy przykład kodu tworzy geometrię punktu GEOJSON i przekazuje go do `atlas.Shape` klasy, aby ułatwić jego aktualizowanie. Środek mapy jest początkowo używany do renderowania symbolu. Zdarzenie kliknięcia jest dodawane do mapy, w taki sposób, że gdy zostanie ona wyzwolona, współrzędne miejsca kliknięcia myszą są używane z funkcją `setCoordinates` Shapes, która aktualizuje lokalizację symbolu na mapie.

<br/>

<iframe height='500' scrolling='no' title='Przełącz lokalizację numeru PIN' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Sprawdź <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>lokalizację numeru PIN przełączania</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Domyślnie w przypadku wydajności warstwy symboli optymalizują renderowanie symboli, ukrywając symbole, które nakładają się na siebie. Podczas powiększania ukrytych symboli stają się widoczne. Aby wyłączyć tę funkcję i renderować wszystkie symbole przez cały czas, ustaw `allowOverlap` Właściwość `iconOptions` opcji na `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Dodaj niestandardową ikonę do warstwy symboli

Warstwy symboli są renderowane przy użyciu WebGL. Ponieważ wszystkie zasoby, takie jak obrazy ikon, muszą zostać załadowane do kontekstu WebGL. Ten przykład pokazuje, jak dodać niestandardową ikonę do zasobów mapy, a następnie użyć jej do renderowania danych punktu z symbolem niestandardowym na mapie. `textField` Właściwość warstwy symboli wymaga określenia wyrażenia. W tym przypadku chcemy renderować Właściwość temperatury, ale ponieważ jest to liczba, należy ją przekonwertować na ciąg. Ponadto chcemy dołączyć do niego "°F". Do tego celu można użyć wyrażenia; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ikona obrazu niestandardowego symbolu' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>ikonę niestandardowego obrazu symbolu</a> pióra przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Zestaw SDK sieci Web Azure Maps udostępnia kilka dostosowywalnych szablonów obrazów, których można używać z warstwą symboli. Więcej informacji można znaleźć w dokumencie [jak korzystać z szablonów obrazów](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-symbol-layer"></a>Dostosowywanie warstwy symboli 

Warstwa symboli ma dostępne wiele opcji stylów. Oto narzędzie do testowania różnych opcji stylów.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy symboli' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Opcje warstwy symboli</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Jeśli chcesz tylko renderować tekst za pomocą warstwy symboli, możesz ukryć ikonę przez ustawienie `image` właściwości opcji ikony na. `'none'`

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie okna podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Używanie wyrażeń stylów opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Jak używać szablonów obrazów](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę wielokątów](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodaj warstwę bąbelkową](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodaj marki HTML](map-add-bubble-layer.md)
