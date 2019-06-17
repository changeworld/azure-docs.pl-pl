---
title: Dodaj warstwę kafelków do usługi Azure Maps | Dokumentacja firmy Microsoft
description: Jak dodać Kafelek warstwę do mapy Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3a773c24993d229f20df698113ff7535fea634ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769230"
---
# <a name="add-a-tile-layer-to-a-map"></a>Dodaj warstwę kafelków do mapy

W tym artykule pokazano, jak można nakładki warstwy kafelków na mapie. Warstwy kafelków umożliwiają nakładania obrazy na podstawie kafelków map podstawowa usługi Azure Maps. Więcej informacji na temat usługi Azure Maps fragmentacji systemu można znaleźć w [poziomy powiększenia i siatka kafelków](zoom-levels-and-tile-grid.md) dokumentacji.

Obciążenia warstwy kafelków na kafelkach z serwera. Obrazy te można wstępnie renderowana i przechowywane, takich jak dowolnego innego obrazu na serwerze przy użyciu konwencji nazewnictwa, która rozumie warstwę kafelków lub dynamicznej usługa, która generuje obrazy na bieżąco. Istnieją trzy różne Kafelek konwencji nazewnictwa usługi obsługiwane przez usługi Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) klasy; 

* X, Y notacji powiększenia — zależnie od poziomu powiększenia i x jest kolumną y jest pozycja wiersz fragmentu siatkę kafelków.
* Notacja Quadkey - kombinacja x, y, informacje powiększenia do wartości pojedynczy ciąg, który jest unikatowy identyfikator dla danego kafelka.
* Pole ograniczenia — współrzędne pola ograniczenia może służyć do określenia obrazu w formacie `{west},{south},{east},{north}` która jest powszechnie używana przez [Services mapowanie sieci Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> A [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) jest doskonałym sposobem na wizualizowanie dużych zestawów danych na mapie. Nie można wygenerować warstwę kafelków z obrazu tylko dane wektorowe może również być renderowana jako warstwa kafelków za. Przez Renderowanie danych wektorowych jako warstwa kafelków, kontrolki mapy tylko musi załadować Kafelki, które mogą być znacznie mniejszy rozmiar pliku niż dane wektorowe, które reprezentują. Ta technika jest używany przez wiele, którzy potrzebują do renderowania milionów wierszy danych na mapie.

Adres URL kafelka przekazany do warstwy kafelków muszą być adres URL protokołu http/https do zasobu TileJSON lub szablon adres URL kafelka, który wykorzystuje następujące parametry: 

* `{x}` -X położenia kafelka. Musi również `{y}` i `{z}`.
* `{y}` -Y położenia kafelka. Musi również `{x}` i `{z}`.
* `{z}` -Poziom powiększenia kafelka. Musi również `{x}` i `{y}`.
* `{quadkey}` -Kafelka identyfikatorem quadkey oparte na konwencji nazewnictwa system kafelków map Bing.
* `{bbox-epsg-3857}` -Otaczający pola ciągu w formacie `{west},{south},{east},{north}` EPSG 3857 przestrzenne odwołanie do systemu.
* `{subdomain}` — Symbol zastępczy, gdzie ma zostać dodana wartości podrzędnej, jeśli określony.

## <a name="add-a-tile-layer"></a>Dodawanie warstwy kafelków

 Ten przykład pokazuje, jak utworzyć warstwę Kafelek wskazujący zestaw kafelków, korzystających z x, y, powiększenia fragmentacji systemu. Źródłem tej warstwy kafelków jest w nakładce radarowy o pogodzie z [Iowa środowiska Mesonet z Iowa State University](https://mesonet.agron.iastate.edu/ogc/).

<br/>

<iframe height='500' scrolling='no' title='Kafelek warstwy przy użyciu X, Y i Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>warstwę kafelków przy użyciu X, Y i Z</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

W powyższym kodzie pierwszy blok kodu tworzy obiekt mapy. Możesz zobaczyć [Utwórz mapę](./map-create.md) instrukcje.

W drugim bloku kodu [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) jest tworzony przez przekazanie sformatowanym adresem URL usługi kafelka, rozmiar fragmentu i nieprzezroczystość być półprzezroczysty. Ponadto, dodając warstwę kafelków do mapy, jest ona dodawana poniżej `labels` warstwy tak, aby etykiety nadal są wyraźnie widoczne.

## <a name="customize-a-tile-layer"></a>Dostosowywanie warstwę kafelków

Warstwa kafelków tylko ma wiele opcji stylów. W tym miejscu to narzędzie ich wypróbowanie.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy kafelków' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz pióra <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>opcje warstwy kafelków</a> przez usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>funkcji codepen można</a>.
</iframe>

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat klasy i metody używane w tym artykule:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Zobacz następujące artykuły, aby uzyskać więcej przykładów kodu dodać do map:

> [!div class="nextstepaction"]
> [Dodaj warstwę obrazu](./map-add-image-layer.md)
