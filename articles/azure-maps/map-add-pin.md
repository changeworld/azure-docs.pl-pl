---
title: Dodawanie warstwy symbolu do mapy | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak dostosować symbol za pomocą warstwy Symbol i dodać symbole na mapie przy użyciu zestawu SDK w sieci Web usług Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209702"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Dodawanie warstwy symboli do mapy

Połącz symbol ze źródłem danych i użyj go do renderowania ikony lub tekstu w danym punkcie. 

Warstwy symboli są renderowane przy użyciu sieci WebGL. Użyj warstwy symbolu, aby renderować duże zbiory punktów na mapie. W porównaniu ze znacznikiem HTML warstwa symbolu renderuje dużą liczbę danych punktowych na mapie, z lepszą wydajnością. Jednak warstwa symboli nie obsługuje tradycyjnych elementów CSS i HTML do stylizacji.  

> [!TIP]
> Warstwy symboli domyślnie renderują współrzędne wszystkich geometrii w źródle danych. Aby ograniczyć warstwę w taki sposób, że `filter` renderuje tylko `['==', ['geometry-type'], 'Point']` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` obiekty geometrii punktowej, ustawia właściwość warstwy lub jeśli chcesz, można również dołączyć obiekty MultiPoint.

Menedżer sprite'a map ładuje niestandardowe obrazy używane przez warstwę symboli. Obsługuje następujące formaty obrazów:

- JPEG
- PNG
- Svg
- BMP
- GIF (bez animacji)

## <a name="add-a-symbol-layer"></a>Dodawanie warstwy symboli

Aby można było dodać warstwę symboli do mapy, należy wykonać kilka kroków. Najpierw utwórz źródło danych i dodaj je do mapy. Tworzenie warstwy symboli. Następnie przekaż w źródle danych do warstwy symbolu, aby pobrać dane ze źródła danych. Na koniec dodaj dane do źródła danych, aby było coś do renderowania. 

Poniższy kod pokazuje, co należy dodać do mapy po załadowaniu. W tym przykładzie renderuje pojedynczy punkt na mapie przy użyciu warstwy symboli. 

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

- Geometria punktu GeoJSON — ten obiekt zawiera tylko współrzędną punktu i nic więcej. Klasa `atlas.data.Point` pomocnika może służyć do łatwego tworzenia tych obiektów.
- Geometria GeoJSON MultiPoint — ten obiekt zawiera współrzędne wielu punktów i nic więcej. Klasa `atlas.data.MultiPoint` pomocnika może służyć do łatwego tworzenia tych obiektów.
- Funkcja GeoJSON — ten obiekt składa się z dowolnej geometrii GeoJSON i zestawu właściwości zawierających metadane skojarzone z geometrią. Klasa `atlas.data.Feature` pomocnika może służyć do łatwego tworzenia tych obiektów.
- `atlas.Shape`jest podobna do funkcji GeoJSON. Oba składają się z geometrii GeoJSON i zestawu właściwości, które zawierają metadane skojarzone z geometrią. Jeśli obiekt GeoJSON zostanie dodany do źródła danych, można go łatwo renderować w warstwie. Jeśli jednak właściwość współrzędnych tego obiektu GeoJSON zostanie zaktualizowana, źródło danych i mapa nie ulegnie zmianie. To dlatego, że nie ma mechanizmu w obiekcie JSON, aby wyzwolić aktualizację. Klasa shape udostępnia funkcje aktualizacji danych, które zawiera. Po wprowadzeniu zmiany źródło danych i mapa są automatycznie powiadamiane i aktualizowane. 

Poniższy przykład kodu tworzy geometrię GeoJSON Point `atlas.Shape` i przekazuje ją do klasy, aby ułatwić aktualizację. Środek mapy jest początkowo używany do renderowania symbolu. Zdarzenie kliknięcie jest dodawane do mapy w taki sposób, że podczas odpalania współrzędne myszy są używane z funkcją kształtów. `setCoordinates` Współrzędne myszy są rejestrowane w momencie zdarzenia kliknięcia. Następnie aktualizuje `setCoordinates` lokalizację symbolu na mapie.

<br/>

<iframe height='500' scrolling='no' title='Przełączanie lokalizacji pinu' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>lokalizację pinu przełącznika pióra</a> przez usługę Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Domyślnie warstwy symboli optymalizują renderowanie symboli, ukrywając nakładające się symbole. Podczas powiększania ukryte symbole stają się widoczne. Aby wyłączyć tę funkcję i cały czas renderować wszystkie symbole, ustaw `allowOverlap` właściwość opcji na `iconOptions` `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Dodawanie niestandardowej ikony do warstwy symboli

Warstwy symboli są renderowane przy użyciu sieci WebGL. W związku z tym wszystkie zasoby, takie jak obrazy ikon, muszą zostać załadowane do kontekstu WebGL. W tym przykładzie pokazano, jak dodać ikonę niestandardową do zasobów mapy. Ta ikona jest następnie używana do renderowania danych punktów z niestandardowym symbolem na mapie. Właściwość `textField` warstwy symbolu wymaga określenia wyrażenia. W takim przypadku chcemy renderować właściwość temperatury. Ponieważ temperatura jest liczbą, musi zostać przekonwertowana na ciąg. Dodatkowo chcemy dołączyć do niego "°F". Wyrażenie może służyć do tego łączenia; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Niestandardowa ikona obrazu symbolu' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz ikonę <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>niestandardowego obrazu symbolu</a> pióra według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Zestaw SDK sieci Web usługi Azure Maps udostępnia kilka konfigurowalnych szablonów obrazów, których można używać z warstwą symboli. Aby uzyskać więcej informacji, zobacz [dokument Jak używać szablonów obrazów.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-symbol-layer"></a>Dostosowywanie warstwy symboli 

Warstwa symbolu ma wiele opcji stylizacji dostępnych. Oto narzędzie do testowania tych różnych opcji stylizacji.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy symboli' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/PxVXje/'>opcje warstwy symboli</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pióra według usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

> [!TIP]
> Jeśli chcesz renderować tylko tekst z warstwą symboli, `image` możesz ukryć ikonę, ustawiając właściwość opcji ikony na `'none'`.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Warstwa symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Opcje warstw symboli](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Ikonyopcje](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Opcje tekstu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Tworzenie źródła danych](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie menu podręcznego](map-add-popup.md)

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Jak używać szablonów obrazów](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie twórców HTML](map-add-bubble-layer.md)
