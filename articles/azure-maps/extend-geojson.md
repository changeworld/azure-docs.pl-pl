---
title: Rozszerzony geometrie GEOJSON w Azure Maps | Microsoft Docs
description: Dowiedz się, jak zwiększyć geometrie GEOJSON w Azure Maps
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 52325248d21a5d5112c9a7f9497c3e03fdf102a4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881983"
---
# <a name="extended-geojson-geometries"></a>Rozszerzony geometrie GEOJSON

Azure Maps zawiera listę zaawansowanych interfejsów API do przeszukiwania wewnątrz i wzdłużnych funkcji geograficznych.
Te interfejsy API służą do standaryzacji [specyfikacji GEOJSON][1] dla reprezentowania funkcji geograficznych (na przykład granic stanu, tras).  

[Specyfikacja GEOJSON][1] obsługuje tylko następujące geometrie:

* GeometryCollection
* LineString
* MultiLineString
* Usług
* MultiPolygon
* Moment
* Tworząc

Niektóre Azure Maps interfejsy API (na przykład: [Wyszukiwanie wewnątrz geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) Zaakceptuj geometrie, takie jak "Circle", które nie są częścią [specyfikacji GEOJSON][1].

Ten artykuł zawiera szczegółowy opis sposobu, w jaki Azure Maps rozszerza [specyfikację GEOJSON][1] , aby reprezentować niektóre geometrie.

## <a name="circle"></a>Okrąg

Geometria nie jest obsługiwana przez specyfikację GEOJSON. [][1] `Circle` Używamy `GeoJSON Point Feature` obiektu do reprezentowania okręgu.

Geometria reprezentowana `GeoJSON Feature` przy użyciu obiektu __musi__ zawierać następujące elementy: `Circle`

- Do środka

    Środek okręgu jest reprezentowany przy użyciu `GeoJSON Point` obiektu.

- RADIUS

    Okrąg `radius` jest przedstawiany przy użyciu `GeoJSON Feature`właściwości. Wartość promienia jest w _metrach_ i musi być typu `double`.

- SubType

    Geometria koła musi również zawierać właściwość `subType`. Ta właściwość musi być częścią `GeoJSON Feature`właściwości, a jej wartością powinna być _koło_

#### <a name="example"></a>Przykład

Poniżej przedstawiono sposób reprezentowania okręgu w środku (Szerokość geograficzna: 47,639754, Długość geograficzna:-122,126986) z promień równą 100 mierników, `GeoJSON Feature` przy użyciu obiektu:

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

Geometria nie jest obsługiwana przez specyfikację GEOJSON. [][1] `Rectangle` Używamy `GeoJSON Polygon Feature` obiektu do reprezentowania prostokąta. Rozszerzenie prostokąta jest używane głównie przez moduł narzędzi do rysowania zestawu SDK sieci Web.

Geometria reprezentowana `GeoJSON Polygon Feature` przy użyciu obiektu __musi__ zawierać następujące elementy: `Rectangle`

- Rogi

    Rogi prostokąta są reprezentowane za pomocą współrzędnych `GeoJSON Polygon` obiektu. Powinna istnieć pięć współrzędnych, jeden dla każdego rogu i piąta Współrzędna, która jest taka sama jak wartość 1, aby zamknąć pierścień wielokąta. Współrzędne te zostaną zamierzone i obrócone odpowiednio przez dewelopera.

- SubType

    Geometria prostokąta musi również zawierać `subType` właściwość. Ta właściwość musi być częścią `GeoJSON Feature`właściwości, a jej wartością powinna być _prostokąt_

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
## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o danych GEOJSON w Azure Maps:

> [!div class="nextstepaction"]
> [Format geonotacji GEOJSON](geofence-geojson.md)

Zapoznaj się ze słownikiem typowych warunków technicznych związanych z aplikacjami do analizy Azure Maps i lokalizacji:

> [!div class="nextstepaction"]
> [Azure Maps słownik](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
