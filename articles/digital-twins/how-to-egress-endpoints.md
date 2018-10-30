---
title: Ruch wychodzący i punktów końcowych w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Wytyczne dotyczące sposobu tworzenia punktów końcowych za pomocą Twins cyfrowych platformy Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c09ee84cda5f0a9747d3ee1f8f1b37d1323f2cc2
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212254"
---
# <a name="egress-and-endpoints"></a>Ruch wychodzący i punktów końcowych

Twins cyfrowych platformy Azure obsługuje pojęcie _punktów końcowych_ gdzie każdy punkt końcowy reprezentuje brokera komunikatów/zdarzeń, w ramach subskrypcji platformy Azure użytkownika. Zdarzenia i komunikaty, które mogą być wysyłane do **Centrum zdarzeń**, **usługi Event Grid**, i **tematów usługi Service Bus**.

Zdarzenia, które zostaną wysłane do punktów końcowych, zgodnie z preferencjami routingu wstępnie zdefiniowane: użytkownik może określić, który punkt końcowy powinien zostać wyświetlony dowolne z następujących zdarzeń: **TopologyOperation**, **UdfCustom**, **SensorChange**, **SpaceChange**, lub **DeviceMessage**.

Aby uzyskać podstawową wiedzę na temat zdarzenia, routing i typy zdarzeń, zobacz [Routing zdarzeń i komunikatów](concepts-events-routing.md).

## <a name="event-types-description"></a>Opis typów zdarzeń

Poniżej przedstawiono formaty zdarzeń dla każdego typu zdarzenia:

- **TopologyOperation**

  Stosuje się do zmian grafu. *Podmiotu* właściwość określa typ obiektu, których to dotyczy. Typy obiektów, które mogą wyzwalać to zdarzenie: **urządzenia**, **DeviceBlobMetadata**, **DeviceExtendedProperty**, **ExtendedPropertyKey**, **Typ ExtendedType**, **magazynu kluczy**, **raportu**, **definicji RoleDefinition**, **czujnik**, **SensorBlobMetadata**, **SensorExtendedProperty**, **miejsca**, **SpaceBlobMetadata**,  **SpaceExtendedProperty**, **SpaceResource**, **SpaceRoleAssignment**, **systemu**, **użytkownika**, **UserBlobMetadata**, **UserExtendedProperty**.

  Przykład:

  ```JSON
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "subject": "ExtendedPropertyKey",
    "data": {
      "SpacesToNotify": [
        "3a16d146-ca39-49ee-b803-17a18a12ba36"
      ],
      "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
      "AccessType": "Create"
    },
    "eventType": "TopologyOperation",
    "eventTime": "2018-04-17T17:41:54.9400177Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nazwa atrybutu niestandardowego | Zamień |
    | --- | --- |
    | *yourTopicName* | Nazwa dostosowanego tematu |

- **UdfCustom**

  Zdarzenia wysłanego przez funkcję zdefiniowaną przez użytkownika (UDF). 
  
  > [!IMPORTANT]
  > To zdarzenie ma być jawnie wysyłane z systemu plików UDF, sam.

  Przykład:

  ```JSON
  {
    "id": "568fd394-380b-46fa-925a-ebb96f658cce",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "ResourceType": "Space",
      "Payload": "\"Room is not available or air quality is poor\"",
      "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
    },
    "eventType": "UdfCustom",
    "eventTime": "2018-10-02T06:50:15.198Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nazwa atrybutu niestandardowego | Zamień |
    | --- | --- |
    | *yourTopicName* | Nazwa dostosowanego tematu |

- **SensorChange**

  Aktualizacja stanu czujnika na podstawie danych telemetrycznych zmian.

  Przykład:

  ```JSON
  {
    "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "subject": "SensorChange",
    "data": {
      "Type": "Classic",
      "DataType": "Motion",
      "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
      "Value": "False",
      "PreviousValue": "True",
      "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
      "MessageType": "sensor",
      "Properties": {
        "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
        "ms-activity-id": "ct22YwXEGJ5u.605.0"
      }
    },
    "eventType": "SensorChange",
    "eventTime": "2018-04-17T17:46:18.5452993Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nazwa atrybutu niestandardowego | Zamień |
    | --- | --- |
    | *yourTopicName* | Nazwa dostosowanego tematu |

- **SpaceChange**

  Aktualizacja stanu miejsca na podstawie danych telemetrycznych zmian.

  Przykład:

  ```JSON
  {
    "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
    "subject": "SpaceChange",
    "data": {
      "Type": null,
      "DataType": "AvailableAndFresh",
      "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "Value": "Room is not available or air quality is poor",
      "PreviousValue": null,
      "RawData": null,
      "transactionId": null,
      "EventTimestamp": null,
      "MessageType": null,
      "Properties": null,
      "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
    },
    "eventType": "SpaceChange",
    "eventTime": "2018-10-02T06:50:20.128Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nazwa atrybutu niestandardowego | Zamień |
    | --- | --- |
    | *yourTopicName* | Nazwa dostosowanego tematu |

- **DeviceMessage**

  Umożliwia określenie **EventHub** połączenia, do którego nieprzetworzone dane telemetryczne zdarzeń można kierować również z bliźniaczych reprezentacji cyfrowych platformy Azure.

> [!NOTE]
> - **DeviceMessage** jest możliwych do łączenia tylko w przypadku **EventHub**; nie można połączyć **DeviceMessage** za pomocą dowolnego typu zdarzenia.
> - Można określić tylko jeden punkt końcowy połączenia typu **EventHub** lub **DeviceMessage**.

## <a name="configuring-endpoints"></a>Konfigurowanie punktów końcowych

Punkt końcowy zarządzania jest wykonywane za pośrednictwem punktów końcowych interfejsu API. Poniżej przedstawiono kilka przykładów dotyczących sposobu konfigurowania różnych obsługiwanych punktów końcowych. Należy zwrócić szczególną uwagę na tablicy typów zdarzeń, ponieważ definiuje on routingu dla punktu końcowego:

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Kierowanie do **usługi Service Bus** typy zdarzeń: **SensorChange**, **SpaceChange**, **TopologyOperation**

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourTopicName"
  }
  ```

    | Nazwa atrybutu niestandardowego | Zamień |
    | --- | --- |
    | *yourNamespace* | Przestrzeń nazw punktu końcowego usługi |
    | *yourPrimaryKey* | Podstawowe parametry połączenia używane do uwierzytelniania |
    | *yourSecondaryKey* | Pomocnicze parametry połączenia używane do uwierzytelniania |
    | *yourTopicName* | Nazwa dostosowanego tematu |

- Kierowanie do **usługi Event Grid** typy zdarzeń: **SensorChange**, **SpaceChange**, **TopologyOperation**

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "yourPrimaryKey",
    "secondaryConnectionString": "yourSecondaryKey",
    "path": "yourTopicName.westus-1.eventgrid.azure.net"
  }
  ```

    | Nazwa atrybutu niestandardowego | Zamień |
    | --- | --- |
    | *yourPrimaryKey* | Podstawowe parametry połączenia używane do uwierzytelniania|
    | *yourSecondaryKey* | Pomocnicze parametry połączenia używane do uwierzytelniania |
    | *yourTopicName* | Nazwa dostosowanego tematu |

- Kierowanie do **Centrum zdarzeń** typy zdarzeń: **SensorChange**, **SpaceChange**, **TopologyOperation**

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourEventHubName"
  }
  ```

    | Nazwa atrybutu niestandardowego | Zamień |
    | --- | --- |
    | *yourNamespace* | Przestrzeń nazw punktu końcowego usługi |
    | *yourPrimaryKey* | Podstawowe parametry połączenia używane do uwierzytelniania |
    | *yourSecondaryKey* | Pomocnicze parametry połączenia używane do uwierzytelniania |
    | *yourEventHubName* | Nazwa Twojej **Centrum zdarzeń** |

- Kierowanie do **Centrum zdarzeń** typ zdarzenia: **DeviceMessage**. Włączenie `EntityPath` w **connectionString** jest obowiązkowe.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey;EntityPath=yourEventHubName",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey;EntityPath=yourEventHubName",
    "path": "yourEventHubName"
  }
  ```

    | Nazwa atrybutu niestandardowego | Zamień |
    | --- | --- |
    | *yourNamespace* | Przestrzeń nazw punktu końcowego usługi |
    | *yourPrimaryKey* | Podstawowe parametry połączenia używane do uwierzytelniania |
    | *yourSecondaryKey* | Pomocnicze parametry połączenia używane do uwierzytelniania |
    | *yourEventHubName* | Nazwa Twojej **Centrum zdarzeń** |

> [!NOTE]
> Po utworzeniu nowego punktu końcowego może upłynąć do 5 do 10 minut, aby uruchomić odbieranie zdarzeń w punkcie końcowym.

## <a name="primary-and-secondary-connection-keys"></a>Klucze podstawowe i pomocnicze połączenia

Gdy klucz podstawowy połączenia staje się nieautoryzowane, system automatycznie próbuje klucza pomocniczego połączenia. Który zawiera kopię zapasową i umożliwia możliwości do poprawnego działania uwierzytelniania i aktualizowanie klucza podstawowego, za pośrednictwem punktów końcowych interfejsu API.

W przypadku nieautoryzowanych klucze podstawowe i pomocnicze połączenie systemu staną się maksymalnie 30-minutowy czas oczekiwania wykładniczego wycofywania. Zdarzenia zostaną usunięte w każdej czas oczekiwania wyzwalane wycofywania.

Zawsze wtedy, gdy system jest w stan oczekiwania wycofywania, aktualizowanie kluczy połączenia za pośrednictwem interfejsu API punktów końcowych może potrwać do 30 minut, aby zastosować zmiany.

## <a name="unreachable-endpoints"></a>Punkty końcowe jest nieosiągalny

Gdy punkt końcowy stanie się niedostępny, system staną się maksymalnie 30-minutowy czas oczekiwania wykładniczego wycofywania. Zdarzenia zostaną usunięte w każdej czas oczekiwania wyzwalane wycofywania.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak używać Azure cyfrowego Twins Swagger, przeczytaj [Azure cyfrowego Twins Swagger](how-to-use-swagger.md).

Aby dowiedzieć się więcej na temat routingu zdarzeń i komunikatów w reprezentacji urządzeń cyfrowych platformy Azure, przeczytaj [Routing zdarzeń i komunikatów](concepts-events-routing.md).
