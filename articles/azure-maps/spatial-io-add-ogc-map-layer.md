---
title: Dodawanie warstwy mapy Open Geospatial Consortium (OGC) | Mapy platformy Microsoft Azure
description: Dowiedz się, jak nakładać warstwę mapy OGC na mapę i jak korzystać z różnych opcji w klasie OgcMapLayer.
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334297"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Dodawanie warstwy mapy z Konsorcjum Otwartego Geoprzestrzennego (OGC)

Klasa `atlas.layer.OgcMapLayer` może nakładać na mapę obrazów usług map internetowych (WMS) i obrazów WMTS (Web Map Tile Services). WMS to standardowy protokół opracowany przez OGC do obsługi georeferencyjnych obrazów map przez Internet. Georeferencje obrazu to procesy kojarzenia obrazu z lokalizacją geograficzną. WMTS jest również standardowym protokołem opracowanym przez OGC. Jest przeznaczony do obsługi wstępnie renderowanych i georeferencyjnych kafelków map.

W poniższych sekcjach opisano funkcje usługi map `OgcMapLayer` internetowych, które są obsługiwane przez klasę.

**Usługa map internetowych (WMS)**

- Obsługiwane wersje: `1.0.0` `1.1.0`, `1.1.1`, , i`1.3.0`
- Usługa musi obsługiwać system `EPSG:3857` projekcji lub obsługiwać ponowneprojections.
- GetFeatureInfo wymaga usługi `EPSG:4326` do obsługi lub obsługi reprojections. 
- Obsługiwane operacje:

    | | |
    | :-- | :-- |
    | GetCapabilities (Zdolności do uzyskania) | Pobiera metadane dotyczące usługi z obsługiwanymi możliwościami |
    | Mapa GetMap | Pobiera obraz mapy dla określonego regionu |
    | GetFeatureInfo | Pobiera `feature_info`, który zawiera podstawowe dane dotyczące funkcji |

**Usługa kafelków map internetowych (WMTS)**

- Obsługiwane wersje:`1.0.0`
- Płytki muszą być kwadratowe, takie, że `TileWidth == TileHeight`.
- Crs obsługiwane: `EPSG:3857` lub`GoogleMapsCompatible` 
- Identyfikator TileMatrix musi być wartością całkowitą odpowiadającą poziomowi powiększenia na mapie. Na mapie azure poziom powiększenia jest wartością między `"0"` i `"22"`. Tak, `"0"` jest obsługiwany, `"00"` ale nie jest obsługiwany.
- Obsługiwane operacje:

    | | |
    | :-- | :-- |
    | GetCapabilities (Zdolności do uzyskania) | Pobiera obsługiwane operacje i funkcje |
    | GetTile (właśc. | Pobiera obrazy dla określonego kafelka |

## <a name="overlay-an-ogc-map-layer"></a>Nakładanie warstwy mapy OGC

`url` Może to być podstawowy adres URL usługi lub pełny adres URL z zapytaniem o uzyskanie możliwości usługi. W zależności od podanych szczegółów klient WFS może wypróbować kilka standardowych formatów adresów URL, aby określić, jak początkowo uzyskać dostęp do usługi.

Poniższy kod pokazuje, jak nałożyć warstwę mapy OGC na mapę.

<br/>

<iframe height='700' scrolling='no' title='Przykład warstwy mapy OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>przykład warstwy mapy pióra OGC</a> według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opcje warstwy mapy OGC

Poniższy przykład pokazuje różne opcje warstwy mapy OGC. Możesz kliknąć przycisk pióra kodu w prawym górnym rogu, aby edytować pióro kodu.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy mapy OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>opcje warstwy mapy Pióra OGC</a> według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Eksplorator usługi map internetowych OGC

Następujące narzędzie nakłada na warstwy obrazy z usług map internetowych (WMS) i usług kafelek map internetowych (WMTS). Można wybrać, które warstwy w usłudze są renderowane na mapie. Dla tych warstw można również wyświetlić skojarzone legendy.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='Eksplorator usługi map internetowych OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>Eksplorator usługi map internetowych Pióra OGC</a> według usługi Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na funkcji <a href='https://codepen.io'>CodePen</a>.
</iframe>

Można również określić ustawienia mapy, aby korzystać z usługi proxy. Usługa serwera proxy umożliwia ładowanie zasobów hostowanych w domenach, które nie mają włączonego mechanizmu CORS.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o klasach i metodach użytych w tym artykule:

> [!div class="nextstepaction"]
> [Warstwa OgcMap](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions (OgcMapLayerOptions)](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Zobacz następujące artykuły, które zawierają przykłady kodu, które można dodać do map:

> [!div class="nextstepaction"]
> [Łączenie się z usługą WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Wykorzystanie podstawowych operacji](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)
