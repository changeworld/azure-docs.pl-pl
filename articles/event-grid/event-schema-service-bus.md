---
title: Azure schematu zdarzeń usługi Service Bus siatki zdarzeń
description: Opisuje właściwości, które są dostępne dla zdarzeń usługi Service Bus Azure zdarzeń siatki
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 02/21/2018
ms.author: babanisa
ms.openlocfilehash: 991679eeb0f7c98606133750b193a5895f39178f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Azure schematu zdarzeń siatki zdarzeń dla usługi Service Bus

Ten artykuł zawiera właściwości i schematu dla zdarzeń usługi Service Bus. Aby obejrzeć wprowadzenie do schematów zdarzeń, zobacz [schematu zdarzeń siatki zdarzeń Azure](event-schema.md).

## <a name="available-event-types"></a>Typy dostępnych zdarzeń

Usługa Service Bus emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Wywoływane, gdy ma aktywnych wiadomości w kolejce lub subskrypcji i odbiorców nasłuchiwania. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Wywoływane, gdy istnieją aktywne wiadomości w kolejki utraconych wiadomości i nie aktywne odbiorników. |

## <a name="example-event"></a>Przykład zdarzeń

W poniższym przykładzie przedstawiono schematu active wiadomości ze zdarzeniem nie odbiorników:

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

Schemat dla zdarzenia kolejki utraconych wiadomości jest podobne:

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
| Temat | ciąg | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalny. Zdarzenie siatki udostępnia tę wartość. |
| Temat | ciąg | Ścieżka zdefiniowana wydawcy podmiotem zdarzeń. |
| Typ zdarzenia | ciąg | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzenia. |
| eventTime | ciąg | Czas jest generowane zdarzenie oparte na czas UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia magazynu obiektów blob. |
| dataVersion | ciąg | Wersja schematu dla obiektu danych. Wydawca definiuje wersji schematu. |
| Element metadataVersion | ciąg | Wersja schematu dla metadanych zdarzenia. Zdarzenie siatki definiuje schemat właściwości najwyższego poziomu. Zdarzenie siatki udostępnia tę wartość. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| nameSpaceName | ciąg | Przestrzeń nazw magistrali usług zasób istnieje w. |
| requestUri | ciąg | Identyfikator URI do określonej kolejki lub wysyłających zdarzenia subskrypcji. |
| Dla obiektu | ciąg | Typ jednostki magistrali usług wysyłających zdarzenia (kolejki lub subskrypcji). |
| queueName | ciąg | Kolejka komunikatów active, jeśli subskrypcja do kolejki. Wartość null, jeśli przy użyciu tematy / subskrypcji. |
| TopicName | ciąg | Temat należy subskrypcji usługi Service Bus z active wiadomości. Wartość null, jeśli przy użyciu kolejki. |
| Nazwa subskrypcji | ciąg | Subskrypcja usługi Service Bus z active wiadomości. Wartość null, jeśli przy użyciu kolejki. |

## <a name="next-steps"></a>Kolejne kroki

* Aby obejrzeć wprowadzenie do usługi Azure Event siatki, zobacz [co to jest zdarzenie siatki?](overview.md)
* Aby uzyskać więcej informacji o tworzeniu subskrypcji platformy Azure zdarzeń siatki, zobacz [schematu subskrypcji zdarzeń siatki](subscription-creation-schema.md).
* Aby uzyskać szczegółowe informacje o wykorzystaniu siatki zdarzeń usługi Azure Service Bus, zobacz [usługi Service Bus do siatki zdarzeń omówienie integracji](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Spróbuj [odbieranie zdarzeń usługi Service Bus funkcji lub Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
