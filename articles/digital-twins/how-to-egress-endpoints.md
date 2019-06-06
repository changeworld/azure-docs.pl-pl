---
title: Ruch wychodzący i punktów końcowych w reprezentacji urządzeń cyfrowych platformy Azure | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące sposobu tworzenia punktów końcowych za pomocą Twins cyfrowych platformy Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/31/2018
ms.author: alinast
ms.openlocfilehash: e93811a56f934a95dde45633c4fb64312b3696df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924809"
---
# <a name="egress-and-endpoints"></a>Ruch wychodzący i punktów końcowych

Azure Twins cyfrowego *punktów końcowych* reprezentują brokera komunikatów lub zdarzeń w ramach subskrypcji platformy Azure przez użytkownika. Zdarzenia i komunikaty mogą być wysyłane do tematów usługi Azure Event Hubs, Azure Event Grid i Azure Service Bus.

Zdarzenia są kierowane do punktów końcowych, zgodnie z preferencjami routingu wstępnie zdefiniowane. Określ użytkowników, które *typy zdarzeń* każdy punkt końcowy, może zostać wyświetlony.

Aby dowiedzieć się więcej na temat zdarzenia, routing i typy zdarzeń dotyczą [Routing zdarzeń i komunikatów w reprezentacji urządzeń cyfrowych platformy Azure](./concepts-events-routing.md).

## <a name="events"></a>Zdarzenia

Zdarzenia są wysyłane przez IoT obiektów (takich jak urządzenia i czujniki) do przetworzenia przez Azure brokerów komunikat i zdarzeń. Zdarzenia są definiowane przez następujące [odwołanie do schematu zdarzeń usługi Azure Event Grid](../event-grid/event-schema.md).

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

| Atrybut | Type | Opis |
| --- | --- | --- |
| id | string | Unikatowy identyfikator zdarzenia. |
| subject | string | Ścieżka zdefiniowana przez wydawcę na temat zdarzenia. |
| data | obiekt | Dane zdarzenia specyficzne dla dostawcy zasobów. |
| eventType | string | Jeden z typów zdarzeń zarejestrowane dla tego źródła zdarzeń. |
| eventTime | string | Czas, którego zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| dataVersion | string | Wersja schematu dla obiektu danych. Wydawca Określa wersję schematu. |
| metadataVersion | string | Wersja schematu dla metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Usługa Event Grid udostępnia tę wartość. |
| subject | string | Zasobów Pełna ścieżka do źródła zdarzeń. To pole nie jest zapisywalna. Usługa Event Grid udostępnia tę wartość. |

Aby uzyskać więcej informacji na temat schematów zdarzeń usługi Event Grid:

- Przegląd [odwołanie do schematu zdarzeń usługi Azure Event Grid](../event-grid/event-schema.md).
- Odczyt [odwołanie do usługi Azure EventGrid Node.js SDK EventGridEvent](https://docs.microsoft.com/javascript/api/azure-eventgrid/eventgridevent?view=azure-node-latest).

## <a name="event-types"></a>Typy zdarzeń

Typy zdarzeń klasyfikacji charakteru zdarzenia i są ustawiane w **Typzdarzenia** pola. Zdarzenie dostępne typy są określone przez następującej listy:

- TopologyOperation
- UdfCustom
- SensorChange
- SpaceChange
- DeviceMessage

Formatu zdarzeń dla każdego typu zdarzenia są opisane w następujących podsekcjach.

### <a name="topologyoperation"></a>TopologyOperation

**TopologyOperation** stosuje się do zmian grafu. **Podmiotu** właściwość określa typ obiektu, których to dotyczy. Następujące typy obiektów mogą wywołać zdarzenie to:

- Urządzenie
- DeviceBlobMetadata
- DeviceExtendedProperty
- ExtendedPropertyKey
- Typ ExtendedType
- Magazyn kluczy
- Raport
- RoleDefinition
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

Punkt końcowy zarządzania jest wykonywane za pośrednictwem punktów końcowych interfejsu API.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Poniższe przykłady pokazują, jak skonfigurować punkty końcowe obsługiwane.

>[!IMPORTANT]
> Należy zwrócić uwagę dokładnej **eventTypes** atrybutu. Definiuje zdarzenia, które typy są obsługiwane przez punkt końcowy i określić routingu.

Żądanie HTTP POST uwierzytelnionego względem

```plaintext
YOUR_MANAGEMENT_API_URL/endpoints
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
