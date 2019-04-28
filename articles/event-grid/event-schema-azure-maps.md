---
title: Schemat zdarzeń w usłudze Azure Event Grid usługi Azure Maps
description: Opisuje właściwości i schematu podany dla zdarzeń usługi Azure Maps za pomocą usługi Azure Event Grid
services: event-grid
author: walsehgal
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: v-musehg
ms.openlocfilehash: 74a3674e632f8dc3f0755bc2ad48376708c7966f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861858"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Schemat zdarzeń Azure Event Grid dla usługi Azure Maps

Ten artykuł zawiera właściwości i schematu dla zdarzeń usługi Azure Maps. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Zdarzenie dostępne typy

Konto usługi Azure Maps emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Wywoływane, gdy współrzędne Odebrano zostały przeniesione z poza danego wirtualnego ogrodzenia można w ciągu |
| Microsoft.Maps.GeofenceExited | Wywoływane, gdy współrzędne Odebrano zostały przeniesione z w ramach danego wirtualnego ogrodzenia na zewnątrz |
| Microsoft.Maps.GeofenceResult | Wywoływane za każdym razem, gdy zapytanie geofencing zwraca wynik, bez względu na stan |

## <a name="event-examples"></a>Przykłady zdarzeń

W poniższym przykładzie przedstawiono schematu **GeofenceEntered** zdarzeń

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

Poniższy przykład Pokaż schemat **GeofenceResult** 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie zawiera następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | string | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |
| temat | string | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| eventType | string | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | string | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | string | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia wirtualnego Grodzenia. |
| dataVersion | string | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | string | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| apiCategory | string | Interfejs API kategorii zdarzenia. |
| apiName | string | Nazwa interfejsu API zdarzenia. |
| Problemy | obiekt | Wyświetla listę problemów napotkanych podczas przetwarzania. Jeśli zwracane są wszystkie problemy, będą nie geometrii zwrócony z odpowiedzią. |
| responseCode | numer | Kod odpowiedzi HTTP |
| Geometrii | obiekt | Wyświetla geometrii ogrodzenia, które zawierają współrzędnych pozycji lub nakładania się searchBuffer wokół pozycji. |

Obiekt błąd jest zwracany, gdy wystąpi błąd w interfejsie API map. Obiekt błędu ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| error | Szczegóły błędu |Ten obiekt jest zwracany, gdy wystąpi błąd w interfejsie API map  |

Obiekt ErrorDetails jest zwracany, gdy wystąpi błąd w interfejsie API map. Szczegóły błędu lub obiekt ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| kod | string | Kod stanu HTTP. |
| message | string | Jeśli to możliwe, ludzi, czytelny opis błędu. |
| innererror | InnerError | Jeśli to możliwe, obiekt zawierający informacje specyficzne dla usługi o błędzie. |

InnerError to obiekt zawierający informacje specyficzne dla usługi o błędzie. Obiekt InnerError ma następujące właściwości: 

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| kod | string | Komunikat o błędzie. |

Obiekt geometrii wymieniono geometrii identyfikatory wirtualne ogrodzenia, wygasłych względem czasu użytkownika w żądaniu. Obiekt geometrii ma geometrii elementy z następującymi właściwościami: 

| Właściwość | Typ | Opis |
|:-------- |:---- |:----------- |
| Identyfikator urządzenia | string | Identyfikator urządzenia. |
| odległość | string | <p>Odległość od współrzędnych do najbliższej granicy wirtualnym ogrodzeniu. Wynik dodatni oznacza, że współrzędnych znajduje się poza wirtualnym ogrodzeniu. Jeśli współrzędnych znajduje się poza wirtualnego ogrodzenia, ale większa niż wartość searchBuffer daleko od najbliższej granicy wirtualnego ogrodzenia, wartość jest 999. Ujemna oznacza, że współrzędnych jest w wirtualnym ogrodzeniu. Jeśli współrzędnych znajduje się wewnątrz wielokąta, ale większa niż wartość searchBuffer daleko od najbliższej granicy geofencing, wartość jest-999 = sprawdzanie. Wartość 999 oznacza, że istnieje pewnie współrzędnych znajduje się również poza wirtualnym ogrodzeniu. Wartość oznacza-999 = sprawdzanie, czy jest pewnie współrzędnych jest również w wirtualnym ogrodzeniu.<p> |
| geometryid |string | Unikatowy identyfikator identyfikuje geometrii wirtualnym ogrodzeniu. |
| nearestlat | numer | Szerokość najbliższy punkt geometrii. |
| nearestlon | numer | Długość geograficzna najbliższego punktu geometrii. |
| udId | string | Unikatowy identyfikator, zwrócone z usługi przekazywania użytkownika podczas przekazywania wirtualnego ogrodzenia. Nie będą uwzględniane w interfejsie API wpis wirtualnego grodzenia. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | ciąg] | Wyświetla identyfikator geometrii wirtualnego ogrodzenia, który wygasł względem czasu użytkownika w żądaniu. |
| Geometrii | [] geometrii |Wyświetla geometrii ogrodzenia, które zawierają współrzędnych pozycji lub nakładania się searchBuffer wokół pozycji. |
| invalidPeriodGeofenceGeometryId | ciąg]  | Wyświetla identyfikator geometrii wirtualnego ogrodzenia, który jest nieprawidłowy okres względem czasu użytkownika w żądaniu. |
| isEventPublished | wartość logiczna | Wartość true, jeśli co najmniej jednego zdarzenia są publikowane w subskrybent zdarzenia usługi Azure Maps, false, jeśli żadne zdarzenie jest publikowany subskrybent zdarzenia usługi Azure Maps. |

## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).