---
title: Format danych GeoJSON wirtualnego ogrodzenia w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o GeoJSON wirtualnego ogrodzenia format danych w usługi Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 6f76eea365311cd6c10dd39054187bf3a07bd595
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568995"
---
# <a name="geofencing-geojson-data"></a>Geofencing GeoJSON danych

Usługi Azure Maps [UZYSKAĆ wirtualnego ogrodzenia](https://docs.microsoft.com/rest/api/maps/spatial/getgeofencepreview) i [WPIS w wirtualnym ogrodzeniu](https://docs.microsoft.com/rest/api/maps/spatial/postgeofencepreview) interfejsy API umożliwiają pobieranie odległości współrzędnych względem podanego wirtualnego ogrodzenia lub zbiór ogrodzenia. Ten artykuł szczegółowo opisuje sposób przygotowania danych wirtualnego ogrodzenia, którego można UZYSKAĆ mapy platformy Azure i interfejsu API wpisu.

Dane dla wirtualnego ogrodzenia lub zestawu wirtualne ogrodzenia są reprezentowane przez `Feature` obiektu i `FeatureCollection` obiektu `GeoJSON` formatu, który jest zdefiniowany w [rfc7946](https://tools.ietf.org/html/rfc7946). Oprócz to:

* Typ obiektu GeoJSON może być `Feature` obiektu lub `FeatureCollection` obiektu.
* Typ obiektu geometrii może być `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`, i `GeometryCollection`.
* Wszystkie właściwości funkcji powinna zawierać `geometryId`, który umożliwia identyfikowanie wirtualnym ogrodzeniu.
* Funkcja z `Point`, `MultiPoint`, `LineString`, `MultiLineString` musi zawierać `radius` we właściwościach. `radius` wartość jest mierzona w metrach, `radius` wartość z zakresu od 1 do 10000.
* Funkcja z `polygon` i `multipolygon` typu Geometria nie ma właściwości usługi radius.
* `validityTime` Opcjonalna właściwość, która umożliwia użytkownikowi ustawiono czas wygaśnięcia i ważności przedziale czasu dla danych w wirtualnym ogrodzeniu. Jeśli nie zostanie określony, dane nigdy nie wygasa i zawsze jest prawidłowy.
* `expiredTime` Jest data i godzina wygaśnięcia geofencing danych. Jeśli wartość `userTime` w żądaniu jest nowszej niż ta dostarczona w odpowiedniej wirtualnego ogrodzenia danych jest uznawany za wygasły danych i nie obsługują zapytań. Na którym geometryId o tym wirtualnym ogrodzeniu dane zostaną uwzględnione w `expiredGeofenceGeometryId` tablicy w wirtualnym ogrodzeniu odpowiedzi.
* `validityPeriod` Znajduje się lista ważności okresu czasu w wirtualnym ogrodzeniu. Jeśli wartość `userTime` w wypada żądania poza okresem ważności, odpowiadające im dane wirtualnego ogrodzenia jest uznawany za jako nieprawidłowy i nie będzie można wyświetlić. GeometryId tych danych w wirtualnym ogrodzeniu znajduje się w `invalidPeriodGeofenceGeometryId` tablicy w wirtualnym ogrodzeniu odpowiedzi. W poniższej tabeli przedstawiono właściwości elementu validityPeriod.

| Name (Nazwa) | Type | Wymagane  | Opis |
| :------------ |:------------: |:---------------:| :-----|
| startTime | DateTime  | true | Data i godzina ważności przedziale czasu rozpoczęcia. |
| endTime   | DateTime  | true |  Data i godzina zakończenia ważności przedziale czasu. |
| recurrenceType | string | false |   Typ cyklu okresu. Wartość może być `Daily`, `Weekly`, `Monthly`, lub `Yearly`. Wartość domyślna to `Daily`.|
| businessDayOnly | Boolean | false |  Wskazuje, czy dane jest prawidłowy tylko w dni robocze. Wartość domyślna to `false`.|


* Wszystkie wartości współrzędnych są reprezentowane jako [szerokość_geograficzna, długość_geograficzna] zdefiniowany w `WGS84`.
* Dla każdej funkcji, która zawiera `MultiPoint`, `MultiLineString`, `MultiPolygon` , lub `GeometryCollection`, właściwości są stosowane do wszystkich elementów. Na przykład: Wszystkie punkty w `MultiPoint` użyje tego samego serwera radius w celu utworzenia wielu wirtualnego ogrodzenia okrąg.
* W scenariuszu okrąg punktów geometrii okrąg mogą być reprezentowane z przy użyciu `Point` geometrii obiekt z właściwościami opracowane w [geometrii rozszerzanie GeoJSON](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Poniżej przedstawiono przykładowe treści żądania dla wirtualnego ogrodzenia, reprezentowane jako okrąg geometrii wirtualnego ogrodzenia, w `GeoJSON` przy użyciu punktu centralnego i usługi radius. Prawidłowy okres danych wirtualnego ogrodzenia rozpoczyna się od 2018-10-22, 9: 00 do 17: 00, powtarzany codziennie, z wyjątkiem weekendy. `expiredTime` Wskazuje, te dane w wirtualnym ogrodzeniu będą uznawane za wygasłe, jeśli `userTime` w żądaniu jest późniejsza niż `2019-01-01`.  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
