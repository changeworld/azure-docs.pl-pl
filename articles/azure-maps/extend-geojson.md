---
title: Rozszerzony geometrie GEOJSON | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak mapy Microsoft Azure rozszerzają specyfikację GEOJSON do reprezentowania niektórych geometrie.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 75ad83411edfdfe7545e8f80df17fea56e317ee0
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911634"
---
# <a name="extended-geojson-geometries"></a>Rozszerzony geometrie GEOJSON

Azure Maps zawiera listę zaawansowanych interfejsów API do przeszukiwania wewnątrz i wzdłużnych funkcji geograficznych.
Te interfejsy API służą do standaryzacji [specyfikacji GEOJSON][1] dla reprezentowania funkcji geograficznych (na przykład granic stanu, tras).  

[Specyfikacja GEOJSON][1] obsługuje tylko następujące geometrie:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Punkt
* Tworząc

Niektóre Azure Maps interfejsy API (na przykład: [Search w geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) akceptują geometrie, takie jak "Circle", które nie są częścią [specyfikacji GEOJSON][1].

Ten artykuł zawiera szczegółowy opis sposobu, w jaki Azure Maps rozszerza [specyfikację GEOJSON][1] , aby reprezentować niektóre geometrie.

## <a name="circle"></a>Okrąg

Geometria `Circle` nie jest obsługiwana przez [specyfikację GEOJSON][1]. Używamy obiektu `GeoJSON Point Feature` do reprezentowania okręgu.

Geometria `Circle` reprezentowana przy użyciu obiektu `GeoJSON Feature` __musi__ zawierać następujące elementy:

- Center

    Środek okręgu jest reprezentowany przy użyciu obiektu `GeoJSON Point`.

- Promień

    `radius` okręgu jest reprezentowana przy użyciu właściwości `GeoJSON Feature`. Wartość promienia jest w _metrach_ i musi być typu `double`.

- SubType

    Geometria koła musi również zawierać właściwość `subType`. Ta właściwość musi być częścią właściwości `GeoJSON Feature`, a jej wartością powinna być _okrąg_

#### <a name="example"></a>Przykład

Poniżej przedstawiono sposób reprezentowania okręgu wyśrodkowanego na stronie (Latitude: 47,639754, Długość geograficzna: 122,126986) z promień równą 100 liczników przy użyciu obiektu `GeoJSON Feature`:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Prostokąt

Geometria `Rectangle` nie jest obsługiwana przez [specyfikację GEOJSON][1]. Używamy obiektu `GeoJSON Polygon Feature` do reprezentowania prostokąta. Rozszerzenie prostokąta jest używane głównie przez moduł narzędzi do rysowania zestawu SDK sieci Web.

Geometria `Rectangle` reprezentowana przy użyciu obiektu `GeoJSON Polygon Feature` __musi__ zawierać następujące elementy:

- Rogi

    Rogi prostokąta są reprezentowane za pomocą współrzędnych obiektu `GeoJSON Polygon`. Powinna istnieć pięć współrzędnych, jeden dla każdego rogu i piąta Współrzędna, która jest taka sama jak wartość 1, aby zamknąć pierścień wielokąta. Współrzędne te zostaną zamierzone i obrócone odpowiednio przez dewelopera.

- SubType

    Geometria prostokąta musi zawierać również właściwość `subType`. Ta właściwość musi być częścią właściwości `GeoJSON Feature`, a jej wartością powinna być _prostokąt_

### <a name="example"></a>Przykład

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o danych GEOJSON w Azure Maps:

> [!div class="nextstepaction"]
> [Format geonotacji GEOJSON](geofence-geojson.md)

Zapoznaj się ze słownikiem typowych warunków technicznych związanych z aplikacjami do analizy Azure Maps i lokalizacji:

> [!div class="nextstepaction"]
> [Azure Maps słownik](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
