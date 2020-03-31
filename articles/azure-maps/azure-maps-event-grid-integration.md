---
title: Reagowanie na zdarzenia na mapie za pomocą siatki zdarzeń | Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się, jak reagować na zdarzenia usługi Microsoft Azure Maps przy użyciu usługi Event Grid.
author: philmea
ms.author: philmea
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9c9483af191e5439af0c0b5e433187d6475c178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335711"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagowanie na zdarzenia usługi Azure Maps przy użyciu siatki zdarzeń 

Usługa Azure Maps integruje się z usługą Azure Event Grid, dzięki czemu użytkownicy mogą wysyłać powiadomienia o zdarzeniach do innych usług i wyzwalać procesy podrzędne. Celem tego artykułu jest pomoc w konfigurowaniu aplikacji biznesowych do nasłuchiwać zdarzeń usługi Azure Maps. Dzięki temu użytkownicy mogą reagować na krytyczne zdarzenia w niezawodny, skalowalny i bezpieczny sposób. Na przykład użytkownicy mogą tworzyć aplikację, aby zaktualizować bazę danych, utworzyć bilet i dostarczyć powiadomienie e-mail, za każdym razem, gdy urządzenie wejdzie geofence.

Usługa Azure Event Grid to w pełni zarządzana usługa routingu zdarzeń, która używa modelu publikowania i subskrybowania. Usługa Event Grid ma wbudowaną obsługę usług platformy Azure, takich jak [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) i Usługi Azure Logic [Apps.](https://docs.microsoft.com/azure/azure-functions/functions-overview) Może dostarczać alerty o zdarzeniach do usług innych niż Azure przy użyciu elementów webhook. Aby uzyskać pełną listę programów obsługi zdarzeń, które obsługuje usługa Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).


![Model funkcjonalny usługi Azure Event Grid](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Typy zdarzeń usługi Azure Maps

Siatka zdarzeń używa [subskrypcji zdarzeń](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) do kierowania wiadomości zdarzeń do subskrybentów. Konto usługi Azure Maps emituje następujące typy zdarzeń: 

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceWprowadzone | Podniesione po odebraniu współrzędne zostały przeniesione z zewnątrz danego obszaru geograficznego do |
| Microsoft.Maps.GeofenceNieoczesło | Wywoływane po przeniesieniu współrzędnych z danej geofence na zewnątrz |
| Microsoft.Maps.GeofenceResult | Wywoływana za każdym razem, gdy kwerenda geofencingowa zwraca wynik, niezależnie od stanu |

## <a name="event-schema"></a>Schemat zdarzeń

W poniższym przykładzie przedstawiono schemat geofenceResult:

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

## <a name="tips-for-consuming-events"></a>Wskazówki dotyczące spożywania zdarzeń

Aplikacje obsługujące zdarzenia geofence usługi Azure Maps powinny stosować się do kilku zalecanych rozwiązań:

* Skonfiguruj wiele subskrypcji, aby rozsyłać zdarzenia do tego samego programu obsługi zdarzeń. Ważne jest, aby nie zakładać, że zdarzenia pochodzą z określonego źródła. Zawsze sprawdź temat wiadomości, aby upewnić się, że wiadomość pochodzi z oczekiwanego źródła.
* Użyj `X-Correlation-id` pola w nagłówku odpowiedzi, aby dowiedzieć się, czy informacje o obiektach są aktualne. Wiadomości mogą być dostarczane poza kolejnością lub po opóźnieniu.
* Gdy żądanie GET lub POST w interfejsie API Geofence `EnterAndExit`jest wywoływane z parametrem mode ustawionym na , dla każdej geometrii w geczence, dla której stan uległ zmianie z poprzedniego wywołania interfejsu API Geofence, jest generowane zdarzenie Enter lub Exit.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak używać geofencingu do kontrolowania operacji na placu budowy, zobacz:

> [!div class="nextstepaction"] 
> [Konfigurowanie geofencingu przy użyciu usługi Azure Maps](tutorial-geofence.md)
