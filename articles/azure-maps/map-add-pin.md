---
title: Dodawanie warstwy symboli do mapy | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak za pomocą warstwy symboli dostosowywać i dodawać symbole na mapie przy użyciu zestawu SDK sieci Web Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8c39c7b57167d65dfa639d41665f5d5b38110183
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933140"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Dodawanie warstwy symboli do mapy

Symbol połączony ze źródłem danych i używany do renderowania ikony i/lub tekstu w danym punkcie. Warstwy symboli są renderowane przy użyciu WebGL i są używane do renderowania dużych kolekcji punktów na mapie. W porównaniu do znacznika HTML, warstwa symboli renderuje dużą liczbę danych punktów na mapie, co zapewnia lepszą wydajność. Jednak warstwa symboli nie obsługuje tradycyjnych stylów CSS i HTML do ustawiania stylów.  

> [!TIP]
> Warstwy symboli domyślnie będą renderować współrzędne wszystkich geometrie w źródle danych. Aby ograniczyć warstwę, która umożliwia renderowanie tylko funkcji geometrii punktów, ustaw właściwość `filter` warstwy na `['==', ['geometry-type'], 'Point']` lub `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Jeśli chcesz, możesz również uwzględnić funkcje systemu MultiPoint.

Menedżer Sprite obrazu Maps ładuje obrazy niestandardowe używane przez warstwę symboli. Obsługuje następujące formaty obrazów:

- JPEG
- PNG
- FORMACIE
- BMP
- GIF (bez animacji)

## <a name="add-a-symbol-layer"></a>Dodawanie warstwy symboli

Aby można było dodać warstwę symboli do mapy, należy wykonać kilka kroków. Najpierw Utwórz źródło danych i Dodaj je do mapy. Warstwę symboli można następnie utworzyć i przesłać do źródła danych w celu pobrania danych ze źródła danych. Na koniec dane muszą zostać dodane do źródła danych, aby było możliwe renderowanie elementu. Poniższy kod przedstawia kod, który ma zostać dodany do mapy po załadowaniu. Kod renderuje pojedynczy punkt na mapie za pomocą warstwy symboli. 

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

- Geometria punktu GEOJSON — ten obiekt zawiera tylko współrzędną punktu i nic innego. Klasa pomocnika `atlas.data.Point` może być używana do łatwego tworzenia tych obiektów.
- Geometria GEOJSON MultiPoint — ten obiekt zawiera współrzędne wielu punktów i nic innego. Klasa pomocnika `atlas.data.MultiPoint` może być używana do łatwego tworzenia tych obiektów.
- Funkcja GEOJSON — ten obiekt składa się z geometrii GEOJSON oraz zestawu właściwości, które zawierają metadane skojarzone z geometrią. Klasa pomocnika `atlas.data.Feature` może być używana do łatwego tworzenia tych obiektów.
- Klasa `atlas.Shape` jest podobna do funkcji GEOJSON. Oba składają się z geometrii GEOJSON oraz zestawu właściwości, które zawierają metadane skojarzone z geometrią. Jeśli obiekt GEOJSON zostanie dodany do źródła danych, może być łatwo renderowany w warstwie. Jeśli jednak Właściwość koordynuje tego obiektu GEOJSON jest aktualizowana, źródło danych i mapa nie są zmieniane. Wynika to z faktu, że w obiekcie JSON nie ma mechanizmu wyzwalania aktualizacji. Klasa Shape zawiera funkcje służące do aktualizowania danych, które zawiera. Po dokonaniu zmiany źródło danych i mapa są automatycznie powiadamiane i aktualizowane. 

Poniższy przykład kodu tworzy geometrię punktu GEOJSON i przekazuje go do klasy `atlas.Shape`, aby ułatwić jego aktualizowanie. Środek mapy jest początkowo używany do renderowania symbolu. Zdarzenie kliknięcia jest dodawane do mapy, w taki sposób, że gdy wyzwalane, Współrzędne myszy są używane z funkcją Shapes `setCoordinates`. Współrzędne myszy są rejestrowane w momencie zdarzenia kliknięcia. Następnie `setCoordinates` aktualizuje lokalizację symbolu na mapie.

<br/>

<iframe height='500' scrolling='no' title='Przełącz lokalizację numeru PIN' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>lokalizację numeru PIN przełączania</a> piórem Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Domyślnie warstwy symboli optymalizują renderowanie symboli, ukrywając symbole, które nakładają się na siebie. W miarę powiększania, ukryte symbole stają się widoczne. Aby wyłączyć tę funkcję i renderować wszystkie symbole przez cały czas, ustaw właściwość `allowOverlap` opcji `iconOptions` na `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Dodaj niestandardową ikonę do warstwy symboli

Warstwy symboli są renderowane przy użyciu WebGL. Ponieważ wszystkie zasoby, takie jak obrazy ikon, muszą zostać załadowane do kontekstu WebGL. Ten przykład pokazuje, jak dodać niestandardową ikonę do zasobów mapy. Ta ikona służy następnie do renderowania danych punktu przy użyciu symbolu niestandardowego na mapie. Właściwość `textField` warstwy symboli wymaga określenia wyrażenia. W tym przypadku chcemy renderować Właściwość temperatury. Ponieważ temperatura jest liczbą, należy ją przekonwertować na ciąg. Ponadto chcemy dołączyć do niej "°F". Wyrażenie może służyć do tego łączenia; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ikona obrazu niestandardowego symbolu' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>ikonę niestandardowego obrazu symbolu</a> pióra przez Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Zestaw SDK sieci Web Azure Maps udostępnia kilka dostosowywalnych szablonów obrazów, których można używać z warstwą symboli. Aby uzyskać więcej informacji, zobacz dokument [jak korzystać z szablonów obrazów](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-symbol-layer"></a>Dostosowywanie warstwy symboli 

Warstwa symboli ma dostępne wiele opcji stylów. Oto narzędzie do testowania różnych opcji stylów.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy symboli' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Opcje warstwy symboli</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Aby renderować tylko tekst z warstwą symboli, można ukryć ikonę przez ustawienie właściwości `image` opcji ikon, aby `'none'`.

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
