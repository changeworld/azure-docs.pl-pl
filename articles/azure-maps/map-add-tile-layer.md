---
title: Dodawanie warstwy kafelków do Azure Maps | Microsoft Docs
description: Jak dodać warstwę kafelków do Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3f047ec1aced55038384cbe29bd3a4b8a948dce9
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976455"
---
# <a name="add-a-tile-layer-to-a-map"></a>Dodawanie warstwy kafelków do mapy

W tym artykule pokazano, jak można nałożyć warstwę kafelków na mapie. Warstwy kafelków umożliwiają nakładanie obrazów na kafelkach mapy podstawowej Azure Maps. Więcej informacji o Azure Maps systemie rozmieszczania można znaleźć w dokumentacji [poziomów powiększenia i siatki kafelków](zoom-levels-and-tile-grid.md) .

Warstwa kafelków jest ładowana na kafelkach z serwera. Te obrazy mogą być wstępnie renderowane i przechowywane jak każdy inny obraz na serwerze przy użyciu konwencji nazewnictwa, która jest rozpoznawana przez warstwę kafelków, lub usługi dynamicznej, która generuje obrazy na bieżąco. Istnieją trzy różne konwencje nazewnictwa usługi kafelków obsługiwane przez Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) klasy: 

* X, Y, z notacją powiększenia w oparciu o poziom powiększenia, x to kolumna, a Y to pozycja w wierszu kafelka w siatce kafelków.
* Quadkey-kombinacja x, y, Powiększ informacje w postaci pojedynczej wartości ciągu, która jest unikatowym identyfikatorem dla kafelka.
* Współrzędne pola ograniczenia obwiedni mogą służyć do określania obrazu w formacie `{west},{south},{east},{north}` , który jest często używany przez [usługi mapowania sieci Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) to doskonały sposób wizualizacji dużych zestawów danych na mapie. Nie tylko można wygenerować warstwy kafelków z obrazu, ale dane wektorowe mogą być również renderowane jako warstwa kafelków. Przez renderowanie danych wektorowych jako warstwy kafelków, formant mapy musi ładować tylko kafelki, które mogą być znacznie mniejsze w rozmiarze pliku niż dane wektorowe, które reprezentują. Ta technika jest używana przez wiele osób, które muszą renderować miliony wierszy danych na mapie.

Adres URL kafelka przesłany do warstwy kafelków musi być adresem URL protokołu HTTP/HTTPS do zasobu TileJSON lub szablonem adresu URL kafelka, który używa następujących parametrów: 

* `{x}`-X pozycja kafelka. Również wymagają `{y}` i `{z}`.
* `{y}`-Y pozycja kafelka. Również wymagają `{x}` i `{z}`.
* `{z}`— Poziom powiększenia kafelka. Również wymagają `{x}` i `{y}`.
* `{quadkey}`-Kafelek quadkey identyfikator oparty na konwencji nazewnictwa systemu kafelków mapy Bing.
* `{bbox-epsg-3857}`-Ciąg pola granicznego z formatem `{west},{south},{east},{north}` w systemie referencyjnym przestrzennym EPSG 3857.
* `{subdomain}`— Symbol zastępczy, w którym zostaną dodane wartości poddomeny, jeśli zostały określone.

## <a name="add-a-tile-layer"></a>Dodawanie warstwy kafelków

 Ten przykład pokazuje, jak utworzyć warstwę kafelków, która wskazuje zestaw kafelków korzystających z systemu dzielenia x, y. Źródłem tej warstwy kafelków jest nałożenie radaru pogody z [Iowa środowiska Mesonet Iowa University](https://mesonet.agron.iastate.edu/ogc/). Podczas przeglądania danych radaru najlepiej, aby użytkownicy mogli jasno widzieć etykiety miast podczas nawigowania po mapie. można to zrobić, wstawiając warstwę kafelka poniżej `labels` warstwy.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Poniżej znajduje się kompletny przykładowy kod wykonywany z powyższymi funkcjami.

<br/>

<iframe height='500' scrolling='no' title='Warstwa kafelków używająca X, Y i Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz warstwę kafelków pióra <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>przy użyciu X, Y i z z</a> Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Dostosowywanie warstwy kafelków

Klasa kafelków ma wiele opcji stylów. Oto narzędzie do wypróbowania.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy kafelków' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Opcje warstwy kafelków</a> pióra według Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Dodaj warstwę obrazu](./map-add-image-layer.md)
