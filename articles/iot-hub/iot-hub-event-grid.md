---
title: Usługa Azure IoT Hub i Event Grid | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Event Grid, aby wyzwolić procesy oparte na akcje wykonywane w usłudze IoT Hub.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 73a9aebfd0c5338f63927860ce3f6c57b20428a4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754771"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagowanie na zdarzenia IoT Hub przy użyciu usługi Event Grid do wyzwalania akcji

Usługa Azure IoT Hub integruje się z usługi Azure Event Grid, dzięki czemu mogą wysyłać powiadomienia o zdarzeniach do innych usług i wyzwolić procesy podrzędne. Skonfiguruj swoje aplikacje biznesowe do nasłuchiwania zdarzeń usługi IoT Hub, dzięki czemu pozwala reagować na krytyczne zdarzenia w sposób niezawodny, skalowalny i bezpieczny. Na przykład utworzyć aplikację, która aktualizuje bazę danych, tworzy bilet pracę i dostarcza wiadomość e-mail z powiadomieniem za każdym razem, gdy nowych urządzeń IoT jest zarejestrowany do usługi IoT hub.

[Usługa Azure Event Grid](../event-grid/overview.md) jest w pełni zarządzanej usługi routingu zdarzeń korzystającej Publikuj — Subskrybuj modelu. Usługa Event Grid ma wbudowaną obsługę usług Azure, takich jak [usługi Azure Functions](../azure-functions/functions-overview.md) i [usługi Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)i mogą dostarczać alerty zdarzeń do usługi spoza platformy Azure przy użyciu elementów webhook. Aby uzyskać pełną listę obsługi zdarzeń, które obsługuje usługi Event Grid, zobacz [wprowadzenie do usługi Azure Event Grid](../event-grid/overview.md).

![Architektura usługi Azure Event Grid](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Dostępność regionalna

Integracja usługi Event Grid jest dostępna dla centrów IoT Hub znajdujących się w regionach, w którym są obsługiwane usługi Event Grid. Wszystkie zdarzenia urządzenia, z wyjątkiem zdarzeń telemetrii urządzenia są ogólnie dostępne. Zdarzenia telemetrii urządzenia jest w publicznej wersji zapoznawczej i jest dostępna we wszystkich regionach z wyjątkiem wschodnie stany USA, zachodnie stany USA, Europa Zachodnia, [Azure dla instytucji rządowych](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china), i [Azure (Niemcy)](https://azure.microsoft.com/global-infrastructure/germany/). Aby uzyskać najnowszą listę regionów, zobacz [wprowadzenie do usługi Azure Event Grid](../event-grid/overview.md).

## <a name="event-types"></a>Typy zdarzeń

Usługa IoT Hub publikuje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Opublikowane po zarejestrowaniu urządzenia do usługi IoT hub. |
| Microsoft.Devices.DeviceDeleted | Opublikowana, gdy urządzenie zostanie usunięty z usługi IoT hub. |
| Microsoft.Devices.DeviceConnected | Gdy urządzenie jest podłączone do usługi IoT hub opublikowany. |
| Microsoft.Devices.DeviceDisconnected | Gdy urządzenie jest odłączony od usługi IoT hub opublikowany. |
| Microsoft.Devices.DeviceTelemetry | W przypadku, gdy komunikaty telemetryczne urządzenia są wysyłane do usługi IoT hub |

Umożliwia skonfigurowanie zdarzeń do publikowania z każdej usługi IoT hub w portalu Azure lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać przykład, wypróbuj samouczek [wysyłanie wiadomości e-mail powiadomień o zdarzeniach usługi Azure IoT Hub przy użyciu aplikacji logiki](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Schemat zdarzeń

Zdarzenia usługi IoT Hub zawierają wszystkie informacje potrzebne do reagowania na zmiany w cyklu życia urządzenia. Można zidentyfikować zdarzenia usługi IoT Hub, sprawdzając właściwości Typ zdarzenia, który rozpoczyna się od **Microsoft.Devices**. Aby uzyskać więcej informacji o tym, jak korzystać z właściwości zdarzeń usługi Event Grid, zobacz [schematu zdarzeń usługi Event Grid](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Schemat podłączone urządzenia

Poniższy przykład przedstawia schematu zdarzeń do podłączonych urządzeń:

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="device-telemetry-schema"></a>Schemat danych Telemetrycznych urządzenia

Komunikaty telemetryczne urządzenia musi być w prawidłowym formacie JSON za pomocą contentType zestawu do formatu JSON i contentEncoding równa UTF-8 w komunikacie [właściwości systemu](iot-hub-devguide-routing-query-syntax.md#system-properties). Jeśli to nie jest ustawiona, usługi IoT Hub będzie zapisywać komunikaty w podstawowej formacie zakodowanym 64.

Możesz wzbogacić zdarzenia telemetrii urządzenia przed ich opublikowaniem do usługi Event Grid, wybierając opcję punkt końcowy jako usługi Event Grid. Aby uzyskać więcej informacji, zobacz [Przegląd wzbogacenia komunikatu](iot-hub-message-enrichments-overview.md).

Poniższy przykład przedstawia schematu zdarzeń telemetrii urządzenia:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Utworzono urządzenie schematu

Poniższy przykład przedstawia schematu utworzonego zdarzenia urządzenia:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Aby uzyskać szczegółowy opis każdej właściwości, zobacz [schematu zdarzeń usługi Azure Event Grid dla Centrum IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrowanie zdarzeń

Subskrypcje zdarzeń usługi IoT Hub można filtrować zdarzenia na podstawie typu zdarzenia, zawartość danych i podmiotu, który zawiera nazwę urządzenia.

Dzięki usłudze Event Grid [filtrowanie](../event-grid/event-filtering.md) zawartości typów, tematów i danych zdarzenia. Podczas tworzenia subskrypcji usługi Event Grid, można subskrybować wybranych zdarzeń IoT. Filtry tematu usługi Event Grid pracy oparte na **zaczyna się od** (prefiks) i **kończy się za pomocą** (sufiks) jest zgodna. Filtr korzysta `AND` operatora, dzięki czemu zdarzenia z tematem zgodne prefiks i sufiks są dostarczane do subskrybenta.

Temat zdarzenia IoT w formacie:

```json
devices/{deviceId}
```

Usługa Event Grid umożliwia również filtrowanie atrybutów każdego zdarzenia, w tym zawartości danych. Teraz można wybrać, jakie zdarzenia są dostarczane na podstawie zawartości komunikaty telemetryczne. Zobacz [zaawansowanego filtrowania](../event-grid/event-filtering.md#advanced-filtering) Aby wyświetlić przykłady.

W przypadku zdarzeń nietelemetrycznych DeviceConnected, DeviceDisconnected, DeviceCreated i DeviceDeleted Filtrowanie usługi Event Grid może służyć podczas tworzenia subskrypcji. Dla zdarzenia telemetrii, oprócz filtrowania w usłudze Event Grid użytkowników można również filtrować według bliźniaczych reprezentacji urządzeń, właściwości wiadomości i treść za pomocą kwerendy routingu komunikatów. Firma Microsoft tworzy domyślny [trasy](iot-hub-devguide-messages-d2c.md) w usłudze IoT Hub na podstawie subskrypcji usługi Event Grid do danych telemetrycznych z urządzenia. Ta trasa może obsługiwać wszystkie subskrypcje usługi Event Grid. Aby filtrować wiadomości przed wysłaniem danych telemetrycznych, możesz zaktualizować swoje [zapytań routingu](iot-hub-devguide-routing-query-syntax.md). Należy zauważyć, że to zapytanie routingu mogą dotyczyć treść komunikatu tylko wtedy, gdy treść JSON.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Ograniczenia dotyczące urządzenie, połączonych i odłączony zdarzenia

Do odbierania podłączonych urządzeń i zdarzenia urządzenia odłączony, należy otworzyć łącze D2C lub łącze C2D dla Twojego urządzenia. Jeśli urządzenie korzysta z protokołu MQTT, usługi IoT Hub zostanie zachowana C2D Otwórz link. Dla protokołu AMQP, możesz otworzyć C2D link przez wywołanie metody [wyświetlany API Async](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet).

D2C link jest otwarty, w przypadku wysyłania danych telemetrycznych. Migotanie jest połączenie z urządzeniem, co oznacza, że urządzenie łączy i odłącza często, firma Microsoft nie będzie wysyłać stan każdego pojedynczego połączenia, ale będą publikować stan połączenia, z których migawki co minutę. W przypadku awarii usługi IoT Hub opublikujemy odpowiednie stanu połączenia urządzenia jak najszybciej po zakończeniu awarii. Jeśli podczas tej niedostępności rozłączy się urządzenie, zdarzenia odłączonego urządzenia zostaną opublikowane w ciągu 10 minut.

## <a name="tips-for-consuming-events"></a>Wskazówki dotyczące używania zdarzenia

Aplikacje obsługujące zdarzeń usługi IoT Hub, należy wykonać następujące sugerowane rozwiązania:

* Wiele subskrypcji można skonfigurować do kierowanie zdarzeń do tego samego programu obsługi zdarzeń, więc nie wolno zakładać, że zdarzenia pochodzą z określonego źródła. Zawsze sprawdzaj temat wiadomość, aby upewnić się, że pochodzi on z usługi IoT hub, których oczekujesz.

* Nie wolno zakładać, że wszystkie zdarzenia, które otrzymujesz są typy, których można oczekiwać. Przed rozpoczęciem przetwarzania wiadomości, Zawsze sprawdzaj typu zdarzenia.

* Komunikaty mogą pojawić się poza kolejnością, lub z opóźnieniem. Aby dowiedzieć się, jeśli Twoje informacje o obiektach są aktualne utworzono urządzenie lub urządzenia, które usunięto zdarzeń, należy użyć pola element etag.

## <a name="next-steps"></a>Kolejne kroki

* [Wypróbuj samouczek zdarzeń usługi IoT Hub](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Dowiedz się, jak porządkować zdarzenia podłączania i odłączania urządzeń](iot-hub-how-to-order-connection-state-events.md)

* [Dowiedz się więcej na temat usługi Event Grid](../event-grid/overview.md)

* [Porównaj różnice między routingu zdarzeń usługi IoT Hub i komunikaty](iot-hub-event-grid-routing-comparison.md)
