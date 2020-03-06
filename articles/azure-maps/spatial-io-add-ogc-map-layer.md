---
title: Dodaj warstwę mapy Open Geospatial Consortium (OGC) | Mapy Microsoft Azure
description: Dowiedz się, jak nałożyć warstwę mapy OGC na mapę oraz jak używać różnych opcji w klasie OgcMapLayer.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c59376344d2df7e9e9c76e630a4462b26343d187
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371014"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Dodaj warstwę mapy z Open Geospatial Consortium (OGC)

Klasa `atlas.layer.OgcMapLayer` może nakładać obraz usług mapy sieci Web (WMS) i obrazów usług mapy sieci Web (WMTS) na mapie. WMS jest standardowym protokołem opracowanym przez OGC do obsługi obrazów mapy geograficznej za pośrednictwem Internetu. Odwołanie do obrazu jest procesami kojarzenia obrazu z lokalizacją geograficzną. WMTS jest również standardowym protokołem opracowanym przez OGC. Jest ona przeznaczona do obsługi wstępnie renderowanych i geoodwołaniowych kafelków mapy.

W poniższych sekcjach opisano funkcje usługi mapowania sieci Web, które są obsługiwane przez klasę `OgcMapLayer`.

**Usługa mapowania sieci Web (WMS)**

- Obsługiwane wersje: `1.0.0`, `1.1.0`, `1.1.1`i `1.3.0`
- Usługa musi obsługiwać system projekcji `EPSG:3857` lub usługa musi być w stanie obsłużyć reprojekcje.
- GetFeatureInfo wymaga, aby usługa obsługiwała `EPSG:4326` lub obsłużyć reprojekcje. 
- Obsługiwane operacje:

    | | |
    | :-- | :-- |
    | GetCapabilities | Pobiera metadane dotyczące usługi z obsługiwanymi możliwościami |
    | GetMap | Pobiera obraz mapy dla określonego regionu |
    | GetFeatureInfo | Pobiera `feature_info`, które zawierają dane podstawowe dotyczące funkcji |

**Usługa kafelków mapowania sieci Web (WMTS)**

- Obsługiwane wersje: `1.0.0`
- Kafelki muszą być kwadratowe, takie jak `TileWidth == TileHeight`.
- Obsługiwane przez KSR: `EPSG:3857` lub `GoogleMapsCompatible` 
- Identyfikator TileMatrix musi być wartością całkowitą, która odpowiada poziomowi powiększenia na mapie. Na mapie platformy Azure poziom powiększenia jest wartością między `"0"` i `"22"`. Dlatego `"0"` jest obsługiwana, ale `"00"` nie jest obsługiwana.
- Obsługiwane operacje:

    | | |
    | :-- | :-- |
    | GetCapabilities | Pobiera obsługiwane operacje i funkcje |
    | Getkafelk | Pobiera obraz dla określonego kafelka |

## <a name="overlay-an-ogc-map-layer"></a>Nałóż warstwę mapy OGC

`url` może być podstawowym adresem URL usługi lub pełnym adresem URL z zapytaniem dotyczącym uzyskiwania możliwości usługi. W zależności od podanych informacji klient WFS może wypróbować kilka standardowych formatów adresów URL, aby określić, jak początkowo uzyskać dostęp do usługi.

Poniższy kod przedstawia sposób nakładki warstwy mapy OGC na mapie.

<br/>

<iframe height='700' scrolling='no' title='Przykład warstwy mapy OGC' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>przykładem z warstwy mapy OGC</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) w <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-map-layer-options"></a>Opcje warstwy mapy OGC

Poniższy przykład ilustruje różne opcje warstwy mapy OGC. Aby edytować pióro kodu, możesz kliknąć przycisk pióra kodu w prawym górnym rogu.

<br/>

<iframe height='700' scrolling='no' title='Opcje warstwy mapy OGC' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zobacz <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>Opcje warstwy mapy OGC</a> pióra według Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>Eksplorator usługi mapy sieci Web OGC

Następujące Narzędzie nakłada obrazy z usług mapy sieci Web (WMS) i usługi kafelków mapy sieci Web (WMTS) jako warstwy. Możesz wybrać warstwy, które mają być renderowane na mapie. Możesz również wyświetlić skojarzone legendy dla tych warstw.

<br/>

<iframe height='700' scrolling='no' title='Eksplorator usługi mapy sieci Web OGC' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zapoznaj się z <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>Eksploratorem OGC usługi mapy sieci Web</a> programu piórem, Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

Możesz również określić ustawienia mapy, aby użyć usługi proxy. Usługa serwera proxy umożliwia ładowanie zasobów hostowanych w domenach, które nie mają włączonego mechanizmu CORs.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat klas i metod używanych w tym artykule:

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Zobacz następujące artykuły, które zawierają przykłady kodu, które można dodać do Twoich map:

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Korzystanie z podstawowych operacji](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Szczegóły obsługiwanego formatu danych](spatial-io-supported-data-format-details.md)
