---
title: Ruch wychodzący i punktów końcowych w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące sposobu tworzenia punktów końcowych za pomocą Twins cyfrowych platformy Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c94d29f16c011a9ff9951d064d7496d3a87f70ef
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636309"
---
# <a name="egress-and-endpoints"></a>Ruch wychodzący i punktów końcowych

Twins cyfrowych platformy Azure obsługuje pojęcie **punktów końcowych**. Każdy punkt końcowy reprezentuje brokera komunikatów lub zdarzeń w subskrypcji platformy Azure użytkownika. Zdarzenia i komunikaty mogą być wysyłane do tematów usługi Azure Event Hubs, Azure Event Grid i Azure Service Bus.

Zdarzenia są wysyłane do punktów końcowych, zgodnie z preferencjami routingu wstępnie zdefiniowane. Użytkownik może określić, który punkt końcowy powinien zostać wyświetlony dowolne z następujących zdarzeń: 

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Aby uzyskać podstawową wiedzę na temat zdarzenia, routing i typy zdarzeń, zobacz [Routing zdarzeń i komunikatów](concepts-events-routing.md).

## <a name="event-types-description"></a>Opis typów zdarzeń

W poniższych sekcjach opisano formatu zdarzeń dla każdego z typów zdarzeń.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** stosuje się do zmian grafu. **Podmiotu** właściwość określa typ obiektu, których to dotyczy. Następujące typy obiektów mogą wywołać zdarzenie to: 

- Urządzenie
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- Typ ExtendedType
- Magazyn kluczy
- Raport
- Roledefinition:
- Czujnik
- SensorBlobMetadata
- SensorExtendedProperty
- Przestrzeń kosmiczna
- SpaceBlobMetadata
- SpaceExtendedProperty
- SpaceResource
- SpaceRoleAssignment
- System
- Użytkownik
- UserBlobMetadata
- UserExtendedProperty

#### <a name="example"></a>Przykład

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

### <a name="udfcustom"></a>UdfCustom

**UdfCustom** jest zdarzenia wysłanego przez funkcję zdefiniowaną przez użytkownika (UDF). 
  
> [!IMPORTANT]  
> Tego zdarzenia muszą być jawnie wysyłane z systemu plików UDF, sam.

#### <a name="example"></a>Przykład

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

### <a name="sensorchange"></a>SensorChange

**SensorChange** to aktualizacja stanu czujnika na podstawie danych telemetrycznych zmian.

#### <a name="example"></a>Przykład

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

### <a name="spacechange"></a>SpaceChange

**SpaceChange** to aktualizacja stanu miejsca na podstawie danych telemetrycznych zmian.

#### <a name="example"></a>Przykład

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
  "topic": "/subscriptions/YOUR_TOPIC_NAME"
}
```

| Wartość | Zamień na |
| --- | --- |
| YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

### <a name="devicemessage"></a>DeviceMessage

Za pomocą **DeviceMessage**, można określić **EventHub** połączenia, do którego nieprzetworzone dane telemetryczne zdarzeń można kierować również z bliźniaczych reprezentacji cyfrowych platformy Azure.

> [!NOTE]
> - **DeviceMessage** jest możliwych do łączenia tylko w przypadku **EventHub**. Nie można łączyć **DeviceMessage** za pomocą dowolnego typu zdarzenia.
> - Można określić tylko jeden punkt końcowy połączenia typu **EventHub** lub **DeviceMessage**.

## <a name="configure-endpoints"></a>Konfigurowanie punktów końcowych

Punkt końcowy zarządzania jest wykonywane za pośrednictwem punktów końcowych interfejsu API. W poniższych przykładach pokazano sposób konfigurowania różnych obsługiwanych punktów końcowych. Należy zwrócić szczególną uwagę na tablicy typów zdarzeń, ponieważ definiuje on routingu dla punktu końcowego:

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Trasa do typów zdarzeń usługi Service Bus **SensorChange**, **SpaceChange**, i **TopologyOperation**:

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME"
  }
  ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_NAMESPACE | Przestrzeń nazw punktu końcowego usługi |
    | YOUR_PRIMARY_KEY | Podstawowe parametry połączenia używane do uwierzytelniania |
    | YOUR_SECONDARY_KEY | Pomocnicze parametry połączenia używane do uwierzytelniania |
    | YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

- Trasa do typów zdarzeń usługi Event Grid **SensorChange**, **SpaceChange**, i **TopologyOperation**:

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "YOUR_SECONDARY_KEY",
    "path": "YOUR_TOPIC_NAME.westus-1.eventgrid.azure.net"
  }
  ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_PRIMARY_KEY | Podstawowe parametry połączenia używane do uwierzytelniania|
    | YOUR_SECONDARY_KEY | Pomocnicze parametry połączenia używane do uwierzytelniania |
    | YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

- Trasa do typów zdarzeń usługi Event Hubs **SensorChange**, **SpaceChange**, i **TopologyOperation**:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_NAMESPACE | Przestrzeń nazw punktu końcowego usługi |
    | YOUR_PRIMARY_KEY | Podstawowe parametry połączenia używane do uwierzytelniania |
    | YOUR_SECONDARY_KEY | Pomocnicze parametry połączenia używane do uwierzytelniania |
    | YOUR_EVENT_HUB_NAME | Nazwa Centrum zdarzeń |

- Trasa do typów zdarzeń usługi Event Hubs **DeviceMessage**. Włączenie `EntityPath` w **connectionString** jest wymagane:

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_PRIMARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "secondaryConnectionString": "Endpoint=sb://YOUR_NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SECONDARY_KEY;EntityPath=YOUR_EVENT_HUB_NAME",
    "path": "YOUR_EVENT_HUB_NAME"
  }
  ```

    | Wartość | Zamień na |
    | --- | --- |
    | YOUR_NAMESPACE | Przestrzeń nazw punktu końcowego usługi |
    | YOUR_PRIMARY_KEY | Podstawowe parametry połączenia używane do uwierzytelniania |
    | YOUR_SECONDARY_KEY | Pomocnicze parametry połączenia używane do uwierzytelniania |
    | YOUR_EVENT_HUB_NAME | Nazwa Centrum zdarzeń |

> [!NOTE]  
> Po utworzeniu nowego punktu końcowego może upłynąć do 5 do 10 minut, aby uruchomić odbieranie zdarzeń w punkcie końcowym.

## <a name="primary-and-secondary-connection-keys"></a>Klucze podstawowe i pomocnicze połączenia

Gdy klucz podstawowy połączenia staje się nieautoryzowane, system automatycznie próbuje klucza pomocniczego połączenia. Który zawiera kopię zapasową i umożliwia możliwości do poprawnego działania uwierzytelniania i aktualizowanie klucza podstawowego, za pośrednictwem punktów końcowych interfejsu API.

W przypadku nieautoryzowanych klucze podstawowe i pomocnicze połączenie system przechodzi do 30-minutowy czas oczekiwania wykładniczego wycofywania. Zdarzenia są odrzucane na każdy czas oczekiwania wyzwalane wycofywania.

Zawsze wtedy, gdy system jest w stan oczekiwania wycofywania, aktualizowanie kluczy połączenia za pośrednictwem interfejsu API punktów końcowych może potrwać do 30 minut, aby zastosować zmiany.

## <a name="unreachable-endpoints"></a>Punkty końcowe jest nieosiągalny

Gdy punkt końcowy stanie się niedostępny, system przechodzi do 30-minutowy czas oczekiwania wykładniczego wycofywania. Zdarzenia są odrzucane na każdy czas oczekiwania wyzwalane wycofywania.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, [sposób używania Azure cyfrowego Twins Swagger](how-to-use-swagger.md).

- Dowiedz się więcej o [routingu zdarzenia i komunikaty](concepts-events-routing.md) w reprezentacji urządzeń cyfrowych platformy Azure.
