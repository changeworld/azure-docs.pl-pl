---
title: Rozszerzanie geometrii GeoJSON w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozszerzyć geometrii GeoJSON w usługi Azure Maps
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 319f9cba23d088553f361b6a0d648bbde94e0743
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968565"
---
# <a name="extending-geojson-geometries"></a>Rozszerzanie GeoJSON geometrii

Usługi Azure Maps zawiera listę zaawansowanych interfejsów API do wyszukiwania wewnątrz/wzdłuż funkcji geograficznego.
Standaryzuj te interfejsy API [Specyfikacja GeoJSON] [ 1] reprezentująca funkcji geograficznego (na przykład: stan granice, trasy).  

[Specyfikacja GeoJSON] [ 1] obsługuje tylko następujące geometrii:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Punkt
* Wielokąt

Niektórych interfejsów API usługi Azure Maps (na przykład: [wyszukiwania wewnątrz geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) Zaakceptuj geometrii, np. "Okrąg", które nie są częścią [Specyfikacja GeoJSON][1].

Ten artykuł zawiera szczegółowy opis sposobu rozszerza usługi Azure Maps [Specyfikacja GeoJSON] [ 1] do reprezentowania niektórych geometrii.

### <a name="circle"></a>Okrąg

`Circle` Geometrii nie jest obsługiwana przez [Specyfikacja GeoJSON][1]. Używamy `GeoJSON Feature` obiektu do reprezentowania okrąg.

A `Circle` geometrii reprezentowane za pomocą `GeoJSON Feature` obiektu __musi__ zawiera następujące czynności:

1. Do środka
   >Centrum koła jest reprezentowane za pomocą `GeoJSON Point` typu.

2. RADIUS
   >Koła `radius` jest reprezentowane za pomocą `GeoJSON Feature`jego właściwości. Wartość radius ma _liczniki_ i musi być typu `double`.

3. Podtyp
   >Geometria koła musi również zawierać `subType` właściwości. Ta właściwość musi być częścią `GeoJSON Feature`firmy właściwości i jej wartość powinna być _Circle_


#### <a name="example"></a>Przykład

Oto jak będzie reprezentować koła, a ich tematyka w (szerokość geograficzna: 47.639754, długość geograficzna:-122.126986) z protokołem radius równa 100 liczniki, za pomocą `GeoJSON Feature` obiektu:

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
