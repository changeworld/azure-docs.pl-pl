---
title: Reagowanie na zdarzenia usługi Azure Maps za pomocą usługi Event Grid | Dokumentacja firmy Microsoft
description: Dowiedz się, jak reagować na zdarzenia usługi Azure Maps za pomocą usługi Event Grid.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a70011b934398ac4e7f74bb67013e93bb5e86e4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60799156"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagowanie na zdarzenia usługi Azure Maps za pomocą usługi Event Grid 

Tak, aby można było wysyłać powiadomienia o zdarzeniach do innych usług i procesy podrzędne wyzwalacza usługi Azure Maps integruje się z usługą Azure Event Grid. Celem tego artykułu jest pomocne w konfigurowaniu aplikacji biznesowych do nasłuchiwania zdarzeń usługi Azure Maps, dzięki czemu pozwala reagować na krytyczne zdarzenia w niezawodny, skalowalny i bezpieczny sposób. Na przykład utworzyć aplikację, która wykonuje wiele operacji, takich jak aktualizacja bazy danych, tworząc bilet i dostarczania wiadomość e-mail z powiadomieniem za każdym razem, gdy urządzenie wejdzie w wirtualnym ogrodzeniu.

Azure Event Grid to w pełni zarządzanej usługi routingu zdarzeń korzystającej Publikuj — Subskrybuj modelu. Usługa Event Grid ma wbudowaną obsługę usług Azure, takich jak [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) i [usługi Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview)i mogą dostarczać alerty zdarzeń do usługi spoza platformy Azure przy użyciu elementów webhook. Aby uzyskać pełną listę obsługi zdarzeń, które obsługuje usługi Event Grid, zobacz [wprowadzenie do usługi Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).


![Usługa Azure Event Grid funkcjonalności modelu.](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Typy zdarzeń usługi Azure Maps

Korzysta z usługi Event grid [subskrypcji zdarzeń](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) aby komunikaty o zdarzeniach trasy dla subskrybentów. Konto usługi Azure Maps emituje następujące typy zdarzeń: 

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Wywoływane, gdy współrzędne Odebrano zostały przeniesione z poza danego wirtualnego ogrodzenia można w ciągu |
| Microsoft.Maps.GeofenceExited | Wywoływane, gdy współrzędne Odebrano zostały przeniesione z w ramach danego wirtualnego ogrodzenia na zewnątrz |
| Microsoft.Maps.GeofenceResult | Wywoływane za każdym razem, gdy zapytanie geofencing zwraca wynik, bez względu na stan |

## <a name="event-schema"></a>Schemat zdarzeń

Poniższy schemat Pokaż przykład GeofenceResult

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

## <a name="tips-for-consuming-events"></a>Wskazówki dotyczące używania zdarzenia

Aplikacje, które obsługują usługi Azure Maps wirtualnego ogrodzenia zdarzenia, należy wykonać kilka zalecanych rozwiązań:

* Wiele subskrypcji można skonfigurować do kierowanie zdarzeń do tego samego programu obsługi zdarzeń. Warto nie przyjęto założenie, że zdarzenia pochodzą z określonego źródła. Zawsze sprawdzaj temat wiadomość, aby upewnić się, że pochodzi on z źródła, których oczekujesz.
* Komunikaty mogą pojawić się poza kolejnością, lub z opóźnieniem. Użyj `X-Correlation-id` pole w nagłówku odpowiedzi, aby dowiedzieć się, jeśli Twoje informacje o obiektach jest aktualny.
* Gdy Get i POST wirtualnego ogrodzenia API jest wywoływana z parametrem mode równa `EnterAndExit`, Enter lub przycisk Zakończ zdarzenie jest generowane dla każdego geometrii w wirtualnym ogrodzeniu, dla których stan zmienił się z poprzedniego wywołania interfejsu API w wirtualnym ogrodzeniu.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o sposobie używania wirtualnego grodzenia do operacji kontroli lokacji konstruowania, zobacz:

> [!div class="nextstepaction"] 
> [Konfigurowanie wirtualnego ogrodzenia przy użyciu usługi Azure Maps](tutorial-geofence.md)