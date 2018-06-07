---
title: Centrum IoT Azure i siatki zdarzeń | Dokumentacja firmy Microsoft
description: Użyj siatki zdarzeń Azure, aby wyzwolić procesy oparte na akcje, które pojawiają się w Centrum IoT.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: f187aa81ca519f2597657f01c2d7a630740b5348
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634315"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions---preview"></a>Reagowanie na zdarzenia Centrum IoT przy użyciu siatki zdarzeń do akcje wyzwalacza - Preview

Centrum IoT Azure integruje się z siatki zdarzeń platformy Azure, dzięki czemu można wysłać powiadomienia o zdarzeniach w innych usługach i wyzwolić procesy podrzędne. Konfigurowanie aplikacji biznesowych do nasłuchiwania zdarzeń Centrum IoT, dzięki czemu można reagowania na zdarzenia krytyczne, w sposób niezawodny, skalowalną oraz bezpieczną. Na przykład utworzyć aplikację, aby wykonywać wiele akcji, takich jak aktualizacja bazy danych, tworzenie biletu i dostarczanie wiadomość e-mail z powiadomieniem za każdym razem, nowego urządzenia IoT jest zarejestrowany do Centrum IoT. 

[Azure siatki zdarzeń] [ lnk-eg-overview] to zdarzeń w pełni zarządzana usługa routingu, która używa publikowanie-subskrypcji modelu. Siatka zdarzeń ma wbudowaną obsługę usług Azure, takich jak [usługi Azure Functions](../azure-functions/functions-overview.md) i [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)i mogą dostarczać alerty zdarzeń do usług innych niż Azure za pomocą elementów webhook. Pełną listę programów obsługi zdarzeń, które obsługuje zdarzenia siatki, zobacz [wprowadzenie do usługi Azure Event siatki][lnk-eg-overview]. 

![Architektura siatki zdarzeń platformy Azure](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Dostępność regionalna

Integracja siatki zdarzeń jest dostępna dla centra IoT znajdujących się w regionach, gdzie siatki zdarzeń jest obsługiwana. Aby uzyskać najnowszą listę regionów, zobacz [wprowadzenie do usługi Azure Event siatki][lnk-eg-overview]. 

## <a name="event-types"></a>Typy zdarzeń

Centrum IoT publikuje następujące typy zdarzeń: 

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Gdy urządzenie jest zarejestrowane do Centrum IoT opublikowany. |
| Microsoft.Devices.DeviceDeleted | Opublikowana, gdy urządzenie zostanie usunięta z Centrum IoT. | 

Umożliwia skonfigurowanie zdarzeń do publikowania z każdym Centrum IoT w portalu Azure lub interfejsu wiersza polecenia Azure. Na przykład, spróbuj samouczka [wysyłanie powiadomień e-mail o zdarzeniach Centrum IoT Azure za pomocą aplikacji logiki](../event-grid/publish-iot-hub-events-to-logic-apps.md). 

## <a name="event-schema"></a>Schemat zdarzeń

Centrum IoT zdarzenia zawiera wszystkich informacji potrzebnych do reagowania na zmiany w cyklu życia z urządzeniami. Można zidentyfikować Centrum IoT zdarzenie przez sprawdzenie właściwości eventType rozpoczyna się od **Microsoft.Devices**. Aby uzyskać więcej informacji o sposobie używania zdarzeń siatki właściwości zdarzenia, zobacz [schematu zdarzeń siatki zdarzeń](../event-grid/event-schema.md).

### <a name="device-created-schema"></a>Schemat utworzonego urządzenia

W poniższym przykładzie przedstawiono schematu urządzenia utworzone zdarzenie: 

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
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Aby uzyskać szczegółowy opis każdej właściwości, zobacz [schematu zdarzeń Azure zdarzeń siatki dla Centrum IoT](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>Filtruj zdarzenia

Subskrypcja zdarzeń Centrum IoT można filtrować zdarzenia na podstawie zdarzeń nazwy typu i urządzenia. Filtry tematu w pracach siatki zdarzenia na podstawie **prefiks** i **sufiks** zgodny. Filtr korzysta `AND` operatora, aby zdarzenia z tematem, które odpowiadają prefiksu i sufiksu są dostarczane do subskrybenta. 

Podmiot IoT zdarzenia w formacie:

```json
devices/{deviceId}
```

### <a name="tips-for-consuming-events"></a>Porady dotyczące używania zdarzeń

Aplikacje obsługujące zdarzenia Centrum IoT należy stosować następujące sugerowane rozwiązania:

* Wiele subskrypcji można skonfigurować do trasy zdarzenia do tej procedury obsługi zdarzeń, dlatego ważne jest, aby nie założono, że zdarzenia pochodzą z określonego źródła. Zawsze sprawdzaj tematu wiadomości, aby upewnić się, że pochodzą z Centrum IoT, który będzie. 
* Nie wolno zakładać, że wszystkie zdarzenia, które otrzymujesz są oczekiwanych typów. Sprawdzać typ zdarzenia przed przetworzeniem wiadomości.
* Można nadejścia wiadomości, poza kolejnością lub z opóźnieniem. Użyj pola etag, aby zrozumieć, jeśli Twoje informacje o obiektach są aktualne.



## <a name="next-steps"></a>Kolejne kroki

* [Centrum IoT samouczkiem zdarzenia](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Dowiedz się więcej na temat zdarzeń siatki][lnk-eg-overview]
* [Porównania różnic między routingu zdarzenia Centrum IoT i komunikatów][lnk-eg-compare]

<!-- Links -->
[lnk-eg-overview]: ../event-grid/overview.md
[lnk-eg-compare]: iot-hub-event-grid-routing-comparison.md