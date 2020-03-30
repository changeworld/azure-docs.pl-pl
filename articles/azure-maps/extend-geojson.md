---
title: Rozszerzone geometrie GeoJSON | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak usługi Microsoft Azure Maps rozszerza specyfikację GeoJSON do reprezentowania niektórych geometrii.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276402"
---
# <a name="extended-geojson-geometries"></a>Rozszerzone geometrie GeoJSON

Usługa Azure Maps udostępnia listę zaawansowanych interfejsów API do wyszukiwania wewnątrz i wzdłuż funkcji geograficznych. Te interfejsy API są zgodne ze standardową [specyfikacją GeoJSON][1] reprezentującą obiekty geograficzne.  

[Specyfikacja GeoJSON][1] obsługuje tylko następujące geometrie:

* Geometrycollection
* Linestring
* Multilinestring
* Multipoint
* Multipolygon
* Punkt
* Wielokąt

Niektóre interfejsy API usługi Azure Maps akceptują geometrie, które nie są częścią [specyfikacji GeoJSON.][1] Na przykład interfejs API [geometrii wyszukiwania wewnątrz](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) akceptuje circle i wielokąty.

Ten artykuł zawiera szczegółowe wyjaśnienie, w jaki sposób usługa Azure Maps rozszerza [specyfikację GeoJSON][1] do reprezentowania niektórych geometrii.

## <a name="circle"></a>Okrąg

Geometria `Circle` nie jest obsługiwana przez [specyfikację GeoJSON][1]. Używamy `GeoJSON Point Feature` obiektu do reprezentowania okręgu.

Geometria `Circle` reprezentowana przy `GeoJSON Feature` użyciu obiektu __musi__ zawierać następujące współrzędne i właściwości:

- Wyśrodkuj

    Środek okręgu jest reprezentowany za `GeoJSON Point` pomocą obiektu.

- Promień

    Okrąg `radius` jest reprezentowany przy użyciu `GeoJSON Feature`właściwości 's. Wartość promienia jest w _metrach_ i `double`musi być typu .

- SubType

    Geometria okręgu musi również `subType` zawierać właściwość. Ta właściwość musi być `GeoJSON Feature`częścią właściwości 's, a jej wartość powinna być _Circle_

#### <a name="example"></a>Przykład

Oto jak będziesz reprezentować okrąg za pomocą `GeoJSON Feature` obiektu. Wyśrodkowajmy okrąg na szerokości geograficznej: 47,639754 i długości geograficznej: -122.126986 i przypiszmy mu promień równy 100 metrów:

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

Geometria `Rectangle` nie jest obsługiwana przez [specyfikację GeoJSON][1]. Używamy `GeoJSON Polygon Feature` obiektu do reprezentowania prostokąta. Rozszerzenie prostokąta jest używane głównie przez moduł narzędzi do rysowania zestawu Web SDK.

Geometria `Rectangle` reprezentowana przy `GeoJSON Polygon Feature` użyciu obiektu __musi__ zawierać następujące współrzędne i właściwości:

- Narożniki

    Narożniki prostokąta są reprezentowane przy użyciu współrzędnych `GeoJSON Polygon` obiektu. Powinno być pięć współrzędnych, po jednym dla każdego narożnika. I piąta współrzędna, która jest taka sama jak pierwsza współrzędna, aby zamknąć pierścień wielokąta. Zakłada się, że te współrzędne wyrównać i że deweloper może obracać je zgodnie z oczekiwaniami.

- SubType

    Geometria prostokąta musi również `subType` zawierać właściwość. Ta właściwość musi być `GeoJSON Feature`częścią właściwości 's, a jej wartość powinna być _Rectangle_

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

Dowiedz się więcej o danych GeoJSON w usłudze Azure Maps:

> [!div class="nextstepaction"]
> [Format Geofence GeoJSON](geofence-geojson.md)

Przejrzyj słowniczek typowych terminów technicznych skojarzonych z usługą Azure Maps i aplikacjami analizy lokalizacji:

> [!div class="nextstepaction"]
> [Słownik usługi Azure Maps](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
