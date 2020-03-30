---
title: Dodawanie warstwy kafelków do mapy | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak nakładać warstwę kafli na mapę przy użyciu zestawu SDK w sieci Web map platformy Microsoft Azure. Warstwy kafelków umożliwiają renderowanie obrazów na mapie.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988604"
---
# <a name="add-a-tile-layer-to-a-map"></a>Dodawanie warstwy kafelków do mapy

W tym artykule pokazano, jak nałożyć warstwę kafli na mapę. Warstwy kafelków umożliwiają nakładanie obrazów na kafelki map bazowych usługi Azure Maps. Aby uzyskać więcej informacji na temat systemu kafli usługi Azure Maps, zobacz [Poziomy powiększenia i siatka kafli](zoom-levels-and-tile-grid.md).

Warstwa kafli jest ładowana w kafelkach z serwera. Obrazy te mogą być wstępnie renderowane lub renderowane dynamicznie. Wstępnie renderowane obrazy są przechowywane jak każdy inny obraz na serwerze przy użyciu konwencji nazewnictwa, którą rozumie warstwa kafli. Dynamicznie renderowane obrazy używają usługi do ładowania obrazów w czasie zbliżonym do czasu rzeczywistego. Istnieją trzy różne konwencje nazewnictwa usługi kafelków obsługiwane przez klasę Azure Maps [TileLayer:](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 

* X, Y, Powiększenie notacji - X to kolumna, Y to położenie wiersza kafelka w siatce kafelków, a notacja Powiększenia jest wartością opartą na poziomie powiększenia.
* Notacja quadkey - Łączy informacje x, y i zoom do pojedynczej wartości ciągu. Ta wartość ciągu staje się unikatowym identyfikatorem pojedynczego kafelka.
* Obwiednia - Określ obraz w formacie `{west},{south},{east},{north}`współrzędnych obwiedni: . Ten format jest powszechnie używany przez [usługi mapowania sieci Web (WMS).](https://www.opengeospatial.org/standards/wms)

> [!TIP]
> A [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) to świetny sposób wizualizacji dużych zestawów danych na mapie. Nie tylko warstwa kafli może być generowana z obrazu, ale także dane wektorowe mogą być renderowane jako warstwa kafli. Renderowanie danych wektorowych jako warstwy kafelków musi tylko załadować kafelki o mniejszym rozmiarze pliku niż dane wektorowe, które reprezentują. Ta technika jest powszechnie używana do renderowania milionów wierszy danych na mapie.

Adres URL kafelka przekazany do warstwy kafelków musi być adresem URL http lub https do zasobu TileJSON lub szablonem adresu URL kafelka, który używa następujących parametrów: 

* `{x}`- X położenie płytki. Również `{y}` potrzebuje `{z}`i .
* `{y}`- Pozycja Y płytki. Również `{x}` potrzebuje `{z}`i .
* `{z}`- Poziom powiększenia płytki. Również `{x}` potrzebuje `{y}`i .
* `{quadkey}`- Identyfikator poczwórki kafelków oparty na konwencji nazewnictwa systemu kafli Bing Maps.
* `{bbox-epsg-3857}`- Ciąg obwiedni z `{west},{south},{east},{north}` formatem w systemie odniesienia przestrzennego EPSG 3857.
* `{subdomain}`- Symbol zastępczy dla wartości poddomeny, jeśli określono `subdomain` zostanie dodany.

## <a name="add-a-tile-layer"></a>Dodawanie warstwy kafelków

 W tym przykładzie pokazano, jak utworzyć warstwę kafli, która wskazuje zestaw kafelków. W tym przykładzie użyto systemu kafli x, y, zoom. Źródłem tej warstwy płytek jest nakładka radaru pogodowego z [Iowa Environmental Mesonet z Iowa State University.](https://mesonet.agron.iastate.edu/ogc/) Podczas przeglądania danych radarowych, najlepiej użytkownicy będą wyraźnie widzieć etykiety miast, gdy poruszają się po mapie. To zachowanie można zaimplementować, wstawiając warstwę kafli poniżej warstwy. `labels`

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Poniżej znajduje się kompletny przykładowy kod uruchomiony powyższej funkcji.

<br/>

<iframe height='500' scrolling='no' title='Warstwa kafelków przy użyciu X, Y i Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>warstwę kafelków pióra przy użyciu X, Y i Z</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Dostosowywanie warstwy kafli

Klasa warstwy kafli ma wiele opcji stylizacji. Oto narzędzie, aby je wypróbować.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy kafelków' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>opcje warstwy kafelków</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>pióra według usługi Azure Maps ( ) na <a href='https://codepen.io'>funkcji CodePen</a>.
</iframe>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Warstwa płytek](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions (TileLayerOptions)](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Aby uzyskać więcej przykładów kodu do dodania do map, zobacz następujące artykuły:

> [!div class="nextstepaction"]
> [Dodawanie warstwy obrazów](./map-add-image-layer.md)
