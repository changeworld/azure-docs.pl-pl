---
title: Wyjście i punkty końcowe — azure digital twins | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć i wyjmować punkty końcowe zdarzeń w usłudze Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 3803802a3d81655091d8be543ae9cb17221a98d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511573"
---
# <a name="egress-and-endpoints-in-azure-digital-twins"></a>Ruch przychodzący i punkty końcowe w programie Azure Digital Twins

*Punkty końcowe* usługi Azure Digital Twins reprezentują wiadomość lub brokera zdarzeń w ramach subskrypcji platformy Azure użytkownika. Zdarzenia i wiadomości mogą być wysyłane do witryny Azure Event Hubs, usługi Azure Event Grid i azure service bus tematy.

Zdarzenia są kierowane do punktów końcowych zgodnie ze wstępnie zdefiniowanymi preferencjami routingu. Użytkownicy *określają,* które typy zdarzeń mogą odbierać każdy punkt końcowy.

Aby dowiedzieć się więcej o zdarzeniach, routingu i typach zdarzeń, zobacz [Zdarzenia i wiadomości routingu w usłudze Azure Digital Twins](./concepts-events-routing.md).

## <a name="events"></a>Zdarzenia

Zdarzenia są wysyłane przez obiekty IoT (takie jak urządzenia i czujniki) do przetwarzania przez brokerów wiadomości i zdarzeń platformy Azure. Zdarzenia są definiowane przez następujące [odwołanie do schematu zdarzeń usługi Azure Event Grid](../event-grid/event-schema.md).

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

| Atrybut | Typ | Opis |
| --- | --- | --- |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| dane | obiekt | Dane zdarzeń specyficzne dla dostawcy zasobów. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |

Aby uzyskać więcej informacji na temat schematu zdarzenia Event Grid:

- Przejrzyj [odwołanie do schematu zdarzeń usługi Azure Event Grid](../event-grid/event-schema.md).
- Przeczytaj [odwołanie do zdarzenia EventGrid Node.js SDK.](https://docs.microsoft.com/javascript/api/@azure/eventgrid/eventgridevent?view=azure-node-latest)

## <a name="event-types"></a>Typy zdarzeń

Typy zdarzeń klasyfikują charakter zdarzenia i są ustawiane w polu **eventType.** Dostępne typy zdarzeń są podane przez następującą listę:

- TopologiaOperacja
- UdfCustom (Polski)
- Zmiany czujników
- SpaceChange (Zmiany w przestrzeni)
- UrządzenieMessage

Formaty zdarzeń dla każdego typu zdarzenia są dalej opisane w następujących podsekcjach.

### <a name="topologyoperation"></a>TopologiaOperacja

**TopologiaOperacja** dotyczy zmian na wykresie. Właściwość **podmiotu** określa typ obiektu, którego dotyczy problem. Następujące typy obiektów mogą wyzwolić to zdarzenie:

- Urządzenie
- Identyfikatory DeviceBlobMetadata
- Właściwości o rozszerzonej wydajności urządzenia
- Klucz ExtendedPropertyKey
- Rozszerzony typ
- Keystore
- Raport
- Definicja roli
- Czujnik
- SensorBlobMetadata (Dane z czujników)
- SensorExtendedProperty
- Miejsce
- SpaceBlobMetadata (SpaceBlobMetadata)
- SpaceExtendedProperty
- Źródło spaceresource
- Znak KosmicznyRoleassignment
- System
- Użytkownik
- UserBlobMetadata (Dane użytkownika)
- Właściwości rozszerzonej użytkownika

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

### <a name="udfcustom"></a>UdfCustom (Polski)

**UdfCustom** jest zdarzeniem wysyłanym przez funkcję zdefiniowaną przez użytkownika (UDF).
  
> [!IMPORTANT]  
> To zdarzenie musi być jawnie wysyłane z samego UDF.

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

### <a name="sensorchange"></a>Zmiany czujników

**SensorChange** to aktualizacja stanu czujnika oparta na zmianach telemetrii.

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

### <a name="spacechange"></a>SpaceChange (Zmiany w przestrzeni)

**SpaceChange** jest aktualizacją do stanu przestrzeni na podstawie zmian telemetrycznych.

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

### <a name="devicemessage"></a>UrządzenieMessage

Za pomocą **DeviceMessage**, można określić połączenie **EventHub,** do którego nieprzetworzone zdarzenia telemetryczne mogą być kierowane również z usługi Azure Digital Twins.

> [!NOTE]
> - **DeviceMessage** można łączyć tylko z **EventHub**. Nie można połączyć **DeviceMessage** z żadnym z innych typów zdarzeń.
> - Można określić tylko jeden punkt końcowy kombinacji typu **EventHub** lub **DeviceMessage**.

## <a name="configure-endpoints"></a>Konfigurowanie punktów końcowych

Zarządzanie punktami końcowymi jest wykonywane za pośrednictwem interfejsu API punktów końcowych.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Poniższe przykłady pokazują, jak skonfigurować obsługiwane punkty końcowe.

>[!IMPORTANT]
> Należy zwrócić szczególną uwagę na **eventTypes** atrybut. Definiuje, które typy zdarzeń są obsługiwane przez punkt końcowy, a tym samym określić jego routingu.

Uwierzytelnione żądanie HTTP POST względem:

```URL
YOUR_MANAGEMENT_API_URL/endpoints
```

- Trasy do usługi Bus typy zdarzeń **SensorChange**, **SpaceChange**i **TopologyOperation:**

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
    | YOUR_NAMESPACE | Obszar nazw punktu końcowego |
    | YOUR_PRIMARY_KEY | Podstawowy ciąg połączenia używany do uwierzytelniania |
    | YOUR_SECONDARY_KEY | Pomocniczy ciąg połączenia używany do uwierzytelniania |
    | YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

- Trasy do siatki zdarzeń typy **zdarzeń SensorChange**, **SpaceChange**i **TopologyOperation:**

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
    | YOUR_PRIMARY_KEY | Podstawowy ciąg połączenia używany do uwierzytelniania|
    | YOUR_SECONDARY_KEY | Pomocniczy ciąg połączenia używany do uwierzytelniania |
    | YOUR_TOPIC_NAME | Nazwa dostosowanego tematu |

- Trasy do centrów zdarzeń typy zdarzeń **SensorChange**, **SpaceChange**i **TopologyOperation:**

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
    | YOUR_NAMESPACE | Obszar nazw punktu końcowego |
    | YOUR_PRIMARY_KEY | Podstawowy ciąg połączenia używany do uwierzytelniania |
    | YOUR_SECONDARY_KEY | Pomocniczy ciąg połączenia używany do uwierzytelniania |
    | YOUR_EVENT_HUB_NAME | Nazwa centrum zdarzeń |

- Trasa do centrum zdarzeń typu **DeviceMessage**. Włączenie `EntityPath` do **connectionString** jest obowiązkowe:

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
    | YOUR_NAMESPACE | Obszar nazw punktu końcowego |
    | YOUR_PRIMARY_KEY | Podstawowy ciąg połączenia używany do uwierzytelniania |
    | YOUR_SECONDARY_KEY | Pomocniczy ciąg połączenia używany do uwierzytelniania |
    | YOUR_EVENT_HUB_NAME | Nazwa centrum zdarzeń |

> [!NOTE]  
> Po utworzeniu nowego punktu końcowego może upłynąć do 5 do 10 minut, aby rozpocząć odbieranie zdarzeń w punkcie końcowym.

## <a name="primary-and-secondary-connection-keys"></a>Podstawowe i pomocnicze klucze połączeń

Gdy podstawowy klucz połączenia staje się nieautoryzowany, system automatycznie próbuje pomocniczy klucz połączenia. Zapewnia to kopię zapasową i umożliwia bezpiecznie uwierzytelnianie i aktualizowanie klucza podstawowego za pośrednictwem interfejsu API punktów końcowych.

Jeśli klucze połączenia podstawowego i pomocniczego są nieautoryzowane, system wprowadza wykładniczy czas oczekiwania wycofywania do 30 minut. Zdarzenia są odrzucane na każdy wyzwalany czas oczekiwania wycofywania.

Zawsze, gdy system jest w stanie oczekiwania wycofywania, aktualizowanie kluczy połączeń za pośrednictwem interfejsu API punktów końcowych może potrwać do 30 minut, aby wejść w życie.

## <a name="unreachable-endpoints"></a>Nieosiągalne punkty końcowe

Gdy punkt końcowy staje się nieosiągalny, system wchodzi wykładniczy czas oczekiwania wycofywania do 30 minut. Zdarzenia są odrzucane na każdy wyzwalany czas oczekiwania wycofywania.

## <a name="next-steps"></a>Następne kroki

- Dowiedz [się, jak korzystać z usługi Azure Digital Twins Swagger](how-to-use-swagger.md).

- Dowiedz się więcej o [routingu zdarzeń i wiadomości](concepts-events-routing.md) w usłudze Azure Digital Twins.
