---
title: Schemat zdarzenia usługi Azure Maps w usłudze Azure
description: Zawiera opis właściwości i schematu dla zdarzeń usługi Azure Maps za pomocą usługi Azure Event Grid
services: event-grid
author: femila
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: femila
ms.openlocfilehash: 9acef524521e8fac6ce6f8f61e5ff3fbbb81d18d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486363"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Schemat zdarzeń usługi Azure Event Grid dla usługi Azure Maps

Ten artykuł zawiera właściwości i schemat zdarzeń usługi Azure Maps. Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Dostępne typy zdarzeń

Konto usługi Azure Maps emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceWprowadzone | Podniesione, gdy otrzymane współrzędne zostały przeniesione z zewnątrz danego obszaru geograficznego do |
| Microsoft.Maps.GeofenceNieoczesło | Wywoływane, gdy współrzędne zostały przeniesione z danej geofence na zewnątrz |
| Microsoft.Maps.GeofenceResult | Wywoływana za każdym razem, gdy kwerenda geofencingowa zwraca wynik, niezależnie od stanu |

## <a name="event-examples"></a>Przykłady zdarzeń

W poniższym przykładzie przedstawiono schemat zdarzenia **GeofenceEntered**

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

W poniższym przykładzie pokazano schemat **geofenceResult** 

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

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzeń geofencingu. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| kategoria api | ciąg | kategorii interfejsu API zdarzenia. |
| nazwa api | ciąg | Nazwa interfejsu API zdarzenia. |
| issues | obiekt | Wyświetla listę problemów napotkanych podczas przetwarzania. Jeśli zostaną zwrócone jakiekolwiek problemy, nie będzie żadnych geometrii zwrócone z odpowiedzi. |
| responseCode | numer | Kod odpowiedzi HTTP |
| Geometrii | obiekt | Wyświetla listę geometrii ogrodzenia, które zawierają położenie współrzędnych lub nakładają się na wyszukiwanieBuffer wokół pozycji. |

Obiekt błędu jest zwracany, gdy wystąpi błąd w interfejsie API map. Obiekt błędu ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| error | ErrorDetails |Ten obiekt jest zwracany, gdy wystąpi błąd w interfejsie API mapy  |

Obiekt ErrorDetails jest zwracany, gdy wystąpi błąd w interfejsie API map. Szczegóły lub obiekt ErrorDetails ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| kod | ciąg | Kod stanu HTTP. |
| message | ciąg | Jeśli jest dostępny, czytelny dla człowieka opis błędu. |
| innererror | InnerError ( InnerError ) | Jeśli jest dostępny, obiekt zawierający informacje specyficzne dla usługi o błędzie. |

InnerError jest obiektem zawierającym informacje specyficzne dla usługi o błędzie. InnerError obiekt ma następujące właściwości: 

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| kod | ciąg | Komunikat o błędzie. |

Obiekt geometrii, wyświetla identyfikatory geometrii geofences, które wygasły w stosunku do czasu użytkownika w żądaniu. Obiekt geometrii ma elementy geometrii o następujących właściwościach: 

| Właściwość | Typ | Opis |
|:-------- |:---- |:----------- |
| Deviceid | ciąg | Identyfikator urządzenia. |
| odległość | ciąg | <p>Odległość od współrzędnej do najbliższej granicy geofence. Dodatni oznacza, że współrzędna znajduje się poza geofence. Jeśli współrzędna znajduje się poza geograficznym, ale więcej niż wartość searchBuffer od najbliższej granicy geograficznej, a następnie wartość jest 999. Ujemny oznacza, że współrzędna znajduje się wewnątrz geofence. Jeśli współrzędna znajduje się wewnątrz wielokąta, ale więcej niż wartość searchBuffer od najbliższej granicy geofencingu, wartość wynosi -999. Wartość 999 oznacza, że istnieje duża pewność, że współrzędna znajduje się poza geofence. Wartość -999 oznacza, że istnieje duża pewność, że współrzędna znajduje się dobrze w granicach geograficznej.<p> |
| geometria |ciąg | Unikatowy identyfikator identyfikuje geometrię geofence. |
| nearestlat (najbliższylat) | numer | Szerokość geograficzna najbliższego punktu geometrii. |
| nearestlon (najbliższylon) | numer | Długość geograficzna najbliższego punktu geometrii. |
| Udid | ciąg | Unikatowy identyfikator zwrócony z usługi przesyłania użytkownika podczas przesyłania geofence. Nie zostaną uwzględnione w interfejsie API wpisu geofencingu. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | ciąg[] | Wyświetla listę identyfikatora geometrii geograficznego, który wygasł w stosunku do czasu użytkownika w żądaniu. |
| Geometrii | geometrie[] |Wyświetla listę geometrii ogrodzenia, które zawierają położenie współrzędnych lub nakładają się na wyszukiwanieBuffer wokół pozycji. |
| invalidPeriodGeofenceGeometryId | ciąg[]  | Wyświetla listę identyfikatora geometrii geograficznego, który jest w nieprawidłowym okresie w stosunku do czasu użytkownika w żądaniu. |
| isEventPublikowane | wartość logiczna | Wartość true, jeśli co najmniej jedno zdarzenie jest publikowane do subskrybenta zdarzeń usługi Azure Maps, false, jeśli żadne zdarzenie nie jest publikowane do subskrybenta zdarzeń usługi Azure Maps. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).