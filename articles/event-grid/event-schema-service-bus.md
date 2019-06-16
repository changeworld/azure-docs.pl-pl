---
title: Schemat zdarzeń w usłudze Azure Event Grid usługi Service Bus
description: Opisuje właściwości, które są dostarczane dla zdarzeń usługi Service Bus za pomocą usługi Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: babanisa
ms.openlocfilehash: f44d2c1c5be6ac895b6f5ea9feca29c0f8ed09f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60561765"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Schemat zdarzeń Azure Event Grid dla usługi Service Bus

Ten artykuł zawiera właściwości i schematu dla zdarzeń usługi Service Bus. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [schematu zdarzeń usługi Azure Event Grid](event-schema.md).

Aby uzyskać listę przykładowych skryptów i samouczków, zobacz [źródła zdarzeń usługi Service Bus](event-sources.md#service-bus).

## <a name="available-event-types"></a>Zdarzenie dostępne typy

Usługa Service Bus emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Wywoływane, gdy istnieją aktywne wiadomości w kolejce lub subskrypcji i nie ma nasłuchujących odbiorników. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Wywoływane, gdy istnieją aktywne komunikaty w kolejce utraconych wiadomości i nie aktywne odbiorniki. |

## <a name="example-event"></a>Przykład zdarzenia

Poniższy przykład przedstawia schematu active komunikatów za pomocą żadne zdarzenie odbiorniki:

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

Schemat zdarzenia kolejki utraconych wiadomości jest podobne:

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

Zdarzenie zawiera następujące dane najwyższego poziomu:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| topic | string | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |
| subject | string | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| eventType | string | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | string | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | string | Unikatowy identyfikator zdarzenia. |
| data | obiekt | Dane zdarzenia magazynu obiektów blob. |
| dataVersion | string | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | string | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| namespaceName | string | Przestrzeń nazw usługi Service Bus zasób istnieje w. |
| requestUri | string | Identyfikator URI do określonej kolejki lub subskrypcji wysyłających zdarzenia. |
| entityType | string | Typ jednostki usługi Service Bus wysyłających zdarzenia (kolejki lub subskrypcji). |
| queueName | string | Kolejka z aktywne komunikaty, jeśli subskrypcja do kolejki. Wartość null, jeśli za pomocą tematów / subskrypcji. |
| topicName | string | Temat subskrypcji usługi Service Bus przy użyciu aktywne komunikaty należą do. Wartość null, jeśli za pomocą kolejki. |
| subscriptionName | string | Subskrypcja usługi Service Bus przy użyciu aktywne wiadomości. Wartość null, jeśli za pomocą kolejki. |

## <a name="next-steps"></a>Kolejne kroki

* Wprowadzenie do usługi Azure Event Grid, zobacz [co to jest usługa Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
* Aby uzyskać więcej informacji na temat korzystania z usługi Azure Event Grid przy użyciu usługi Service Bus, zobacz [Service Bus i Event Grid Omówienie integracji](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Spróbuj [odbierania zdarzeń usługi Service Bus przy użyciu Functions lub Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
