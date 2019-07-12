---
title: Schemat zdarzeń w usłudze Azure Event Grid Azure SignalR
description: Opisuje właściwości, które są dostarczane dla zdarzeń SignalR platformy Azure za pomocą usługi Azure Event Grid
services: event-grid
author: chenyl
ms.service: event-grid
ms.topic: reference
ms.date: 06/11/2019
ms.author: chenyl
ms.openlocfilehash: 3b072ff2b680ad6d144c7441190ab2df9870f5d0
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789074"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Schemat zdarzeń Azure Event Grid usługi SignalR

Ten artykuł zawiera właściwości i schematu dla zdarzeń usługi SignalR. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md).


## <a name="available-event-types"></a>Zdarzenie dostępne typy

Usługa SignalR emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | Wywoływane, gdy połączone połączenia klienta. |
| Microsoft.SignalRService.ClientConnectionDisconnected | Wywoływane, gdy Rozłączono połączenie klienta. |

## <a name="example-event"></a>Przykład zdarzenia

Poniższy przykład przedstawia schematu klienta zdarzenie połączone połączenia: 

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

Schemat zdarzenia rozłączeniu połączenia klienta jest podobne: 

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

Zdarzenie zawiera następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| topic | ciąg | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |
| subject | ciąg | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| eventType | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| data | object | Dane zdarzenia usługi SignalR. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | ciąg | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| timestamp | ciąg | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| hubName | ciąg | Koncentrator, z której należy połączenie klienta. |
| connectionId | ciąg | Unikatowy identyfikator połączenia klienta. |
| userId | ciąg | Identyfikator użytkownika zdefiniowane w oświadczenie. |
| Komunikat o błędzie | ciąg | Błąd, który powoduje, że połączenie jest odłączony. |

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
