---
title: Format danych GeoJSON dla geofence | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak przygotować dane geofence, które mogą być używane w interfejsie API Microsoft Azure Maps GET i POST Geofence.
author: philmea
ms.author: philmea
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7b9860908dd3bdf3dcda727f350578a97b890cac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335617"
---
# <a name="geofencing-geojson-data"></a>Geofencing Dane GeoJSON

Interfejsy API usługi Azure Maps [GET Geofence](/rest/api/maps/spatial/getgeofence) i [POST Geofence](/rest/api/maps/spatial/postgeofence) umożliwiają pobieranie bliskości współrzędnych względem podanej ogrodzenia geograficznego lub zestawu ogrodzeń. W tym artykule opisano sposób przygotowania danych geofence, które mogą być używane w interfejsie API AZURE Maps GET i POST.

Dane dla geofence lub zestaw geofences `Feature` jest `FeatureCollection` reprezentowana `GeoJSON` przez Obiekt i obiekt w formacie, który jest zdefiniowany w [rfc7946](https://tools.ietf.org/html/rfc7946). Oprócz niego:

* Typ obiektu GeoJSON może `Feature` być `FeatureCollection` obiektem lub obiektem.
* Typ obiektu geometrii może `Point` `MultiPoint`być `LineString` `MultiLineString`, `Polygon` `MultiPolygon`, `GeometryCollection`, , , i .
* Wszystkie właściwości funkcji powinny `geometryId`zawierać program , który służy do identyfikacji geofence.
* Operacja `Point`z `MultiPoint` `LineString`, `MultiLineString` , `radius` musi zawierać we właściwościach. `radius`wartość jest mierzona `radius` w metrach, wartość waha się od 1 do 10000.
* Operacja `polygon` z `multipolygon` i typ geometrii nie ma właściwości promienia.
* `validityTime`jest opcjonalną właściwością, która umożliwia użytkownikowi ustawienie wygasłego czasu i okresu ważności dla danych geofence. Jeśli nie zostanie określony, dane nigdy nie wygasa i jest zawsze ważne.
* Jest `expiredTime` to data wygaśnięcia i godzina danych geofencingowych. Jeśli wartość `userTime` w żądaniu jest późniejsza niż ta wartość, odpowiednie dane geofence są traktowane jako dane wygasłe i nie są wyszukiwane. Na którym geometryId tych danych geofence zostaną `expiredGeofenceGeometryId` uwzględnione w tablicy w odpowiedzi geofence.
* Jest `validityPeriod` to lista okres ważności okresu ważności geofence. Jeśli wartość `userTime` w żądaniu nie mieści się w okresie ważności, odpowiednie dane geofence są uważane za nieprawidłowe i nie będą wyszukiwane. GeometryId tych danych geofence znajduje `invalidPeriodGeofenceGeometryId` się w tablicy w odpowiedzi geofence. W poniższej tabeli przedstawiono właściwości elementu okres ważności.

| Nazwa | Typ | Wymagany  | Opis |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime (data/godzina)  | true | Godzina rozpoczęcia okresu ważności. |
| endTime   | Datetime (data/godzina)  | true |  Godzina zakończenia okresu ważności. |
| typ cyklu | ciąg | false |   Typ cyklu okresu. Wartość może `Daily`być `Weekly` `Monthly`, `Yearly`, , lub . Wartością `Daily`domyślną jest .|
| businessDayOnly | Wartość logiczna | false |  Określ, czy dane są prawidłowe tylko w dni robocze. Wartością `false`domyślną jest .|


* Wszystkie wartości współrzędnych są reprezentowane jako `WGS84`[długość geograficzna, szerokość geograficzna] zdefiniowane w .
* Dla każdej operacji, `MultiPoint` `MultiLineString`która `MultiPolygon` zawiera `GeometryCollection`, , lub , właściwości są stosowane do wszystkich elementów. na przykład: Wszystkie `MultiPoint` punkty w użyje tego samego promienia, aby utworzyć geofence wielu okręgu.
* W scenariuszu okręgu punktowego geometria okręgu może `Point` być reprezentowana przy użyciu obiektu geometrii z właściwościami opracowanymi w rozszerzenia [geometrii GeoJSON](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Poniżej przedstawiono przykładową treść żądania dla geofence reprezentowaną `GeoJSON` jako geometria geofence okręgu przy użyciu punktu środkowego i promienia. Ważny okres danych geofence rozpoczyna się od 2018-10-22, 9AM do 5PM, powtarzane codziennie z wyjątkiem weekendu. `expiredTime`wskazuje, że te dane geofence zostaną `userTime` uznane za wygasłe, jeśli w żądaniu jest później niż `2019-01-01`.  

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
