---
title: Zrozumienie niestandardowe punkty końcowe usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — przy użyciu zapytań routingu do kierowania komunikatów z urządzenia do chmury do niestandardowych punktów końcowych.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e5e92c40cef15e99431dc9652820c71e87935f67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61244348"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Użyj trasy wiadomość i niestandardowe punkty końcowe dla komunikatów z urządzenia do chmury

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Usługa IoT Hub [Routing komunikatów](iot-hub-devguide-routing-query-syntax.md) umożliwia kierowanie komunikatów z urządzenia do chmury do punktów końcowych przeznaczonych dla usługi. Routing udostępnia również możliwość wykonywanie zapytania do filtrowania danych przed przesłaniem go do punktów końcowych. Każda kwerenda routingu, które można skonfigurować ma następujące właściwości:

| Właściwość      | Opis |
| ------------- | ----------- |
| **Nazwa**      | Unikatowa nazwa identyfikująca zapytania. |
| **Element źródłowy**    | Pochodzenie strumień danych, aby zostać podjęte. Na przykład danych telemetrycznych z urządzenia. |
| **Warunek** | Wyrażenie zapytania dla zapytania routingu, które jest uruchamiane właściwości aplikacji wiadomości, właściwości systemu, treści wiadomości, tagów bliźniaczych reprezentacji urządzeń i właściwości bliźniaczych reprezentacji urządzeń w celu ustalenia, czy jest dopasowanie dla punktu końcowego. Aby uzyskać więcej informacji na temat tworzenia zapytania znajduje się w [komunikat o składni zapytań routingu](iot-hub-devguide-routing-query-syntax.md) |
| **Punkt końcowy**  | Nazwa punktu końcowego, gdzie Centrum IoT Hub wysyła komunikaty, które pasujących do zapytania. Firma Microsoft zaleca, wybierz punkt końcowy w tym samym regionie, co Centrum IoT hub. |

Pojedynczy komunikat może odpowiadać warunku w wielu zapytań routingu, w których przypadku usługi IoT Hub dostarcza wiadomość do endpoint skojarzone z każdym zapytaniem dopasowane. Centrum IoT automatycznie deduplicates dostarczanie komunikatów, więc jeśli komunikat jest zgodny z wielu zapytań, które mają tego samego miejsca docelowego, są tylko zapisywane jeden raz do tego miejsca docelowego.

## <a name="endpoints-and-routing"></a>Punkty końcowe i routingu

Centrum IoT hub jest powiązana wartość domyślna [wbudowany punkt końcowy](iot-hub-devguide-messages-read-builtin.md). Możesz utworzyć niestandardowe punkty końcowe do wyznaczania tras, łącząc innych usług w Twojej subskrypcji do Centrum. Usługa IoT Hub obsługuje obecnie kontenery usługi Azure Storage, usługa Event Hubs, kolejki usługi Service Bus i tematów usługi Service Bus jako niestandardowe punkty końcowe.

Gdy używasz routingu, jak i niestandardowe punkty końcowe, wiadomości tylko są dostarczane do wbudowany punkt końcowy, jeśli nie są zgodne dowolnego zapytania. Aby dostarcza komunikaty do wbudowanego punktu końcowego również do niestandardowego punktu końcowego, należy dodać trasę, która wysyła komunikaty do wbudowanej **zdarzenia** punktu końcowego.

> [!NOTE]
> * Usługa IoT Hub obsługuje tylko zapisywanie danych w kontenerach usługi Azure Storage jako obiekty BLOB.
> * Kolejki usługi Service Bus i tematów za pomocą **sesje** lub **wykrywania duplikatów** włączone nie są obsługiwane jako niestandardowe punkty końcowe.

Aby uzyskać więcej informacji na temat tworzenia niestandardowych punktów końcowych w usłudze IoT Hub, zobacz [punktów końcowych usługi IoT Hub](iot-hub-devguide-endpoints.md).

Aby uzyskać więcej informacji na temat odczytywanie ich z niego niestandardowe punkty końcowe zobacz:

* Odczytywanie z [kontenery usługi Azure Storage](../storage/blobs/storage-blobs-introduction.md).

* Odczytywanie z [usługi Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* Odczytywanie z [kolejek usługi Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Odczytywanie z [tematów usługi Service Bus](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat punktów końcowych usługi IoT Hub, zobacz [punktów końcowych usługi IoT Hub](iot-hub-devguide-endpoints.md).

* Aby uzyskać więcej informacji na temat język zapytań, służą do definiowania zapytań routingu, zobacz [składni zapytań routingu komunikatów](iot-hub-devguide-routing-query-syntax.md).

* [Komunikaty urządzenia do chmury usługi IoT Hub procesu przy użyciu tras](tutorial-routing.md) samouczek pokazuje, jak używać zapytań routingu i niestandardowe punkty końcowe.