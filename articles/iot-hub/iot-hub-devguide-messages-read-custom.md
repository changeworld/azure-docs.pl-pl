---
title: Zrozumienie niestandardowe punkty końcowe usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — przy użyciu zapytań routingu do kierowania komunikatów z urządzenia do chmury do niestandardowych punktów końcowych.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: af0b819c6c60835089c174a1f9f7c3a6215e362c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956965"
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

Centrum IoT hub jest powiązana wartość domyślna [wbudowany punkt końcowy][lnk-built-in]. Możesz utworzyć niestandardowe punkty końcowe do wyznaczania tras, łącząc innych usług w Twojej subskrypcji do Centrum. Usługa IoT Hub obsługuje obecnie kontenery usługi Azure Storage, usługa Event Hubs, kolejki usługi Service Bus i tematów usługi Service Bus jako niestandardowe punkty końcowe.

Gdy używasz routingu, jak i niestandardowe punkty końcowe, wiadomości tylko są dostarczane do wbudowany punkt końcowy, jeśli nie są zgodne dowolnego zapytania. Aby dostarcza komunikaty do wbudowanego punktu końcowego również do niestandardowego punktu końcowego, należy dodać trasę, która wysyła komunikaty do **zdarzenia** punktu końcowego.

> [!NOTE]
> Usługa IoT Hub obsługuje tylko zapisywanie danych w kontenerach usługi Azure Storage jako obiekty BLOB.

> [!WARNING]
> Kolejki usługi Service Bus i tematów za pomocą **sesje** lub **wykrywania duplikatów** włączone nie są obsługiwane jako niestandardowe punkty końcowe.

Aby uzyskać więcej informacji na temat tworzenia niestandardowych punktów końcowych w usłudze IoT Hub, zobacz [punktów końcowych usługi IoT Hub][lnk-devguide-endpoints].

Aby uzyskać więcej informacji na temat odczytywanie ich z niego niestandardowe punkty końcowe zobacz:

* Odczytywanie z [kontenery usługi Azure Storage][lnk-getstarted-storage].
* Odczytywanie z [usługi Event Hubs][lnk-getstarted-eh].
* Odczytywanie z [kolejek usługi Service Bus][lnk-getstarted-queue].
* Odczytywanie z [tematów usługi Service Bus][lnk-getstarted-topic].

### <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat punktów końcowych usługi IoT Hub, zobacz [punktów końcowych usługi IoT Hub][lnk-devguide-endpoints].
* Aby uzyskać więcej informacji na temat język zapytań, służą do definiowania zapytań routingu, zobacz [składni zapytań routingu komunikatów](iot-hub-devguide-routing-query-syntax.md).
* [Komunikaty urządzenia do chmury usługi IoT Hub procesu przy użyciu tras] [ lnk-d2c-tutorial] samouczek pokazuje, jak używać zapytań routingu i niestandardowe punkty końcowe.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
