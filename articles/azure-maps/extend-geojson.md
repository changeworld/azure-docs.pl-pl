---
title: Rozszerzanie mają GeoJSON geometrię w społeczności Maps Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozszerzyć mają GeoJSON geometrię w społeczności Maps Azure
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 2cc0e29615ad4fc19040055d847435a9dffa9c95
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655087"
---
# <a name="extending-geojson-geometries"></a>Rozszerzanie mają GeoJSON geometrię

Mapy Azure zawiera listę zaawansowanych interfejsów API do wyszukiwania wewnątrz/wzdłuż funkcje geograficznych.
Ustandaryzować te interfejsy API na [GeoJSON spec] [ 1] reprezentujący funkcje geograficzne (na przykład: stan granice, trasy).  

[GeoJSON spec] [ 1] obsługuje tylko następujące mają geometrię:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* punkt
* wielokąta

Niektórych interfejsów API map Azure (na przykład: [wyszukiwania wewnątrz geometrii](https://docs.microsoft.com/en-us/rest/api/maps/search/postsearchinsidegeometry)) Zaakceptuj mają geometrię, takich jak "Okrąg", które nie są częścią [GeoJSON spec][1].

Ten artykuł zawiera szczegółowy opis na sposób mapowania Azure rozszerza [GeoJSON spec] [ 1] do reprezentowania niektórych mają geometrię.

### <a name="circle"></a>koło

`Circle` Geometrii nie jest obsługiwana przez [GeoJSON spec][1]. Używamy `GeoJSON Feature` obiektu do reprezentowania okręgu.

A `Circle` geometrii reprezentowanej przy użyciu `GeoJSON Feature` obiektu __musi__ zawierają następujące:

1. Do środka
   >Środka okręgu odpowiada za pomocą `GeoJSON Point` typu.

2. RADIUS
   >Koło `radius` odpowiada za pomocą `GeoJSON Feature`jego właściwości. Wartość promienia jest _liczników_ i musi być typu `double`.

3. Podtyp
   >Geometria koła musi zawierać `subType` właściwości. Ta właściwość musi być częścią `GeoJSON Feature`jego właściwości i jej wartość powinna być _okręgu_


#### <a name="example"></a>Przykład

Oto jak będzie reprezentować koło skupia się na (szerokości geograficznej: 47.639754, długość:-122.126986) z protokołem radius równy 100 liczników, przy użyciu `GeoJSON Feature` obiektu:

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

[1]: https://tools.ietf.org/html/rfc7946
