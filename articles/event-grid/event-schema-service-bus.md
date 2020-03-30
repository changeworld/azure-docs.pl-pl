---
title: Schemat zdarzenia usługi Azure Event Service Bus
description: W tym artykule opisano właściwości, które są dostarczane dla zdarzeń usługi Service Bus z usługą Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: babanisa
ms.openlocfilehash: f44d2c1c5be6ac895b6f5ea9feca29c0f8ed09f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561765"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Schemat zdarzenia usługi Azure Event Grid dla usługi Service Bus

Ten artykuł zawiera właściwości i schemat zdarzeń usługi Service Bus.Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md).

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [Źródło zdarzenia usługi Service Bus](event-sources.md#service-bus).

## <a name="available-event-types"></a>Dostępne typy zdarzeń

Usługa Service Bus emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesDostępneDołącza Nielisteners | Wywoływane, gdy istnieją aktywne wiadomości w kolejce lub subskrypcji i nie odbiorników nasłuchuje. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Wywoływane, gdy istnieją aktywne wiadomości w kolejce utraconych list i nie aktywnych odbiorników. |

## <a name="example-event"></a>Przykładowe zdarzenie

W poniższym przykładzie przedstawiono schemat aktywnych komunikatów bez zdarzenia odbiorników:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Schemat zdarzenia kolejki utraconych wiadomości jest podobny:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
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
| dane | obiekt | Dane zdarzenia magazynu obiektów blob. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Namespacename | ciąg | Obszar nazw usługi Service Bus, w istnieje. |
| Requesturi | ciąg | Identyfikator URI do określonej kolejki lub subskrypcji emitujących zdarzenie. |
| Entitytype | ciąg | Typ jednostki usługi Service Bus emitującej zdarzenia (kolejka lub subskrypcja). |
| Queuename | ciąg | Kolejka z aktywnymi wiadomościami, jeśli subskrybuje kolejkę. Wartość null w przypadku używania tematów / subskrypcji. |
| topicName | ciąg | Temat, do którego należy subskrypcja usługi Service Bus z aktywnymi wiadomościami. Wartość null, jeśli używasz kolejki. |
| subscriptionName | ciąg | Subskrypcja usługi Service Bus z aktywnymi wiadomościami. Wartość null, jeśli używasz kolejki. |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
* Aby uzyskać szczegółowe informacje na temat korzystania z usługi Azure Event Grid z usługą Service Bus, zobacz [omówienie integracji usługi Service Bus to Event Grid.](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md)
* Spróbuj [odbierać zdarzenia usługi Service Bus za pomocą funkcji lub aplikacji logiki](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
