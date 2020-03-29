---
title: Schemat zdarzenia usługi Azure Event Azure SignalR
description: W tym artykule opisano właściwości, które są dostarczane dla zdarzeń usługi Azure SignalR z usługą Azure Event Grid
services: event-grid
author: chenyl
ms.service: event-grid
ms.topic: reference
ms.date: 06/11/2019
ms.author: chenyl
ms.openlocfilehash: 3b072ff2b680ad6d144c7441190ab2df9870f5d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67789074"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Schemat zdarzenia usługi SignalR usługi Usługi SignalR usługi usługi Azure Event Grid

Ten artykuł zawiera właściwości i schemat zdarzeń usługi SignalR.Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md).


## <a name="available-event-types"></a>Dostępne typy zdarzeń

SignalR Service emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Połączenie z usługą Microsoft.SignalRService.ClientConnection | Wywoływane, gdy połączenie klienta jest połączone. |
| Microsoft.SignalRService.ClientConnectionZrozłączenie | Wywoływane po rozłączeniu połączenia klienta. |

## <a name="example-event"></a>Przykładowe zdarzenie

Poniższy przykład przedstawia schemat zdarzenia połączonego połączenia klienta: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat zdarzenia rozłączone połączenie klienta jest podobny: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
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
| dane | obiekt | SignalR Service dane zdarzeń. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| sygnatura czasowa | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| nazwa centrum | ciąg | Koncentrator, do którego należy połączenie klienta. |
| Connectionid | ciąg | Unikatowy identyfikator połączenia klienta. |
| userId | ciąg | Identyfikator użytkownika zdefiniowany w oświadczeń. |
| Errormessage | ciąg | Błąd, który powoduje rozłączenie połączenia. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
