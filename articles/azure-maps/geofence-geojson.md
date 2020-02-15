---
title: Format danych GEOJSON dla geofencingu | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak przygotować dane z geoogrodzenia, które mogą być używane w Microsoft Azure Maps Pobierz i umieść interfejs API z geoogrodzeniem.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7d1c9a1587771a020f5c9f89e2497a25eb1bba70
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210025"
---
# <a name="geofencing-geojson-data"></a>Geogeofencingu dane GEOJSON

Azure Maps [Get subogrodzenia](/rest/api/maps/spatial/getgeofence) i [przedogrodzeni](/rest/api/maps/spatial/postgeofence) interfejsy API umożliwiają pobranie bliskości współrzędnej względem podanego geoogrodzenia lub zestawu ograniczników. W tym artykule szczegółowo opisano sposób przygotowania danych geoogrodzenia, które mogą być używane w Azure Maps GET i POST API.

Dane dla geofencingu lub zestawu geoogrodzenia są reprezentowane przez `Feature` obiektu i `FeatureCollection` obiektu w formacie `GeoJSON`, który jest zdefiniowany w [rfc7946](https://tools.ietf.org/html/rfc7946). Oprócz tego:

* Typ obiektu GEOJSON może być obiektem `Feature` lub obiektem `FeatureCollection`.
* Typem obiektu geometrii może być `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`i `GeometryCollection`.
* Wszystkie właściwości funkcji powinny zawierać `geometryId`, który jest używany do identyfikowania geoogrodzenia.
* Funkcja z `Point`, `MultiPoint`, `LineString``MultiLineString` musi zawierać `radius` we właściwościach. wartość `radius` jest mierzona w licznikach, `radius` wartość z zakresu od 1 do 10000.
* Funkcja z typem geometrii `polygon` i `multipolygon` nie ma właściwości RADIUS.
* `validityTime` to opcjonalna właściwość, która umożliwia użytkownikowi ustawianie czasu wygaśnięcia i okresu ważności danych geoogrodzenia. Jeśli nie zostanie określony, dane nigdy nie wygasną i są zawsze poprawne.
* `expiredTime` to data i godzina wygaśnięcia danych dotyczących geofencingu. Jeśli wartość `userTime` w żądaniu jest późniejsza niż ta wartość, odpowiednie dane geoogrodzenia są uznawane za wygasłe i nie są wysyłane do zapytania. Z tego momentu geometryId danych geołożonych zostanie uwzględniony w tablicy `expiredGeofenceGeometryId` w obrębie odpowiedzi geoogrodzeniowej.
* `validityPeriod` jest listą okresów ważności geoogrodzenia. Jeśli wartość `userTime` w żądaniu przypada poza okresem ważności, odpowiednie dane geoogrodzenia są traktowane jako nieprawidłowe i nie będą wysyłane do zapytania. GeometryId tych danych geograficznych jest dołączany do tablicy `invalidPeriodGeofenceGeometryId` w obrębie odpowiedzi geoogrodzenia. W poniższej tabeli przedstawiono właściwości elementu validityPeriod.

| Name (Nazwa) | Typ | Wymagane  | Opis |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Data/godzina  | {1&gt;true&lt;1} | Data i godzina rozpoczęcia okresu ważności. |
| endTime   | Data/godzina  | {1&gt;true&lt;1} |  Data i godzina zakończenia okresu ważności. |
| recurrenceType | ciąg | {1&gt;false&lt;1} |   Typ cyklu okresu. Wartość może być `Daily`, `Weekly`, `Monthly`lub `Yearly`. Wartość domyślna to `Daily`.|
| businessDayOnly | Wartość logiczna | {1&gt;false&lt;1} |  Wskazuje, czy dane są prawidłowe tylko w dniach roboczych. Wartość domyślna to `false`.|


* Wszystkie wartości współrzędnych są reprezentowane jako [Długość geograficzna, Szerokość geograficzna] zdefiniowane w `WGS84`.
* Dla każdej funkcji, która zawiera `MultiPoint`, `MultiLineString`, `MultiPolygon` lub `GeometryCollection`, właściwości są stosowane do wszystkich elementów. na przykład: wszystkie punkty w `MultiPoint` będą używały tego samego promienia do utworzenia wieloogrodzenia z wieloma okręgami.
* W scenariuszu z kółkiem, geometria okręgu może być reprezentowana przy użyciu `Point` obiektu geometrii z właściwościami opracowanymi w ramach [rozszerzania GEOJSON geometrie](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Poniżej znajduje się Przykładowa treść żądania dla geoogrodzenia reprezentowana jako geometria geoogrodzenia okręgu w `GeoJSON` przy użyciu punktu centralnego i promienia. Prawidłowy okres danych geoogrodzenia zaczyna się od 2018-10-22, 9:00 do 17:00, powtarzany każdego dnia z wyjątkiem weekendu. `expiredTime` wskazuje, że te dane geoogrodzenia będą uznawane za wygasłe, jeśli `userTime` w żądaniu jest późniejsza niż `2019-01-01`.  

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
