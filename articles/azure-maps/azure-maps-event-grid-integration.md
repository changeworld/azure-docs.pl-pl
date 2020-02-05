---
title: Reagowanie na zdarzenia mapy za pomocą Event Grid | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak reagować na Microsoft Azure mapowanie zdarzeń przy użyciu Event Grid.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a89983a9ae45f21deb7a823de049373b4ff9b935
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989063"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagowanie na zdarzenia Azure Maps przy użyciu Event Grid 

Azure Maps integruje się z Azure Event Grid, dzięki czemu użytkownicy mogą wysyłać powiadomienia o zdarzeniach do innych usług i wyzwalać procesy podrzędne. Celem tego artykułu jest ułatwienie konfigurowania aplikacji firmowych do nasłuchiwania zdarzeń Azure Maps. Ta usługa umożliwia reagowanie na krytyczne zdarzenia w niezawodny, skalowalny i bezpieczny sposób. Na przykład użytkownicy mogą utworzyć aplikację w celu zaktualizowania bazy danych, utworzenia biletu i dostarczenia powiadomienia e-mail za każdym razem, gdy urządzenie przejdzie do geofencingu.

Azure Event Grid to w pełni zarządzana usługa routingu zdarzeń, która używa modelu publikowania/subskrybowania. Event Grid ma wbudowaną obsługę usług platformy Azure, takich jak [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) i [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview). Może ona dostarczać alerty zdarzeń do usług innych niż Azure przy użyciu elementów webhook. Aby zapoznać się z pełną listą programów obsługi zdarzeń obsługiwanych przez Event Grid, zobacz [wprowadzenie do Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).


![Azure Event Grid model funkcjonalny](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Typy zdarzeń Azure Maps

Funkcja Event Grid używa [subskrypcji zdarzeń](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Konto Azure Maps emituje następujące typy zdarzeń: 

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Wywoływane, gdy otrzymane współrzędne zostały przeniesione z zewnątrz danego elementu geołożonego do wewnątrz |
| Microsoft.Maps.GeofenceExited | Wywoływane, gdy odebrane współrzędne przeniesiono z wewnątrz danego elementu geołożonego do zewnątrz |
| Microsoft.Maps.GeofenceResult | Wywoływane za każdym razem, gdy zapytanie geofencingu zwraca wynik, niezależnie od stanu |

## <a name="event-schema"></a>Schemat zdarzeń

W poniższym przykładzie przedstawiono schemat dla GeofenceResult:

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

## <a name="tips-for-consuming-events"></a>Porady dotyczące używania zdarzeń

Aplikacje, które obsługują zdarzenia geofencingu Azure Maps powinny spełniać kilka zalecanych praktyk:

* Można skonfigurować wiele subskrypcji, aby kierować zdarzenia do tego samego programu obsługi zdarzeń. Ważne jest, aby nie założyć, że zdarzenia pochodzą z określonego źródła. Zawsze sprawdzaj temat wiadomości, aby upewnić się, że pochodzi od oczekiwanego źródła.
* Komunikaty mogą być przychodzące z kolejności lub po opóźnieniu. Użyj pola `X-Correlation-id` w nagłówku odpowiedzi, aby zrozumieć, czy informacje o obiektach są aktualne.
* Gdy interfejs API get i POST jest wywoływany z parametrem Mode ustawionym na `EnterAndExit`, zdarzenie Enter lub Exit jest generowane dla każdej geometrii w geoogrodzeniu, dla którego stan zmienił się od poprzedniego wywołania interfejsu API z geoogrodzeniem.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sposobach kontrolowania operacji w lokacji konstrukcja przy użyciu geofencingu, zobacz:

> [!div class="nextstepaction"] 
> [Konfigurowanie geoogrodzenia przy użyciu Azure Maps](tutorial-geofence.md)
