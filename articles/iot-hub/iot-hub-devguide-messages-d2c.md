---
title: Omówienie obsługi komunikatów urządzenie chmura usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dla deweloperów — jak korzystać z urządzenia do chmury, obsługa komunikatów za pomocą usługi IoT Hub. Zawiera informacje na temat wysyłania danych telemetrii i innych telemtry i przy użyciu routingu w celu dostarczania komunikatów.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: be87b00f27f0d0b25cd77a0634ab1c653a85e5ac
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126446"
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Wysyłanie komunikatów z urządzenia do chmury do Centrum IoT Hub

Wysyłać szeregów czasowych telemetrię i alerty z urządzeń w zapleczu rozwiązania, wysyłanie komunikatów z urządzenia do chmury z urządzenia do usługi IoT hub. Aby uzyskać omówienie inne opcje urządzenia do chmury, obsługiwane przez usługę IoT Hub, zobacz [wskazówki dotyczące komunikacji z urządzenia do chmury][lnk-d2c-guidance].

Wysyłanie komunikatów z urządzenia do chmury za pośrednictwem punktu końcowego dostępnego z urządzenia (**/devices/ {deviceId} / komunikaty/zdarzenia**). Reguły routingu, a następnie kierować wiadomości do jednego z punktów końcowych przeznaczonych dla usługi w usłudze IoT hub. Reguły routingu Użyj nagłówki i treść komunikatów z urządzenia do chmury, aby ustalić, gdzie można je rozesłać. Domyślnie komunikaty są kierowane do wbudowany punkt końcowy przeznaczonych dla usługi (**komunikaty/zdarzenia**), która jest zgodna z [usługi Event Hubs][lnk-event-hubs]. W związku z tym, można użyć standardowego [integracji usługi Event Hubs i zestawy SDK] [ lnk-compatible-endpoint] do odbierania komunikatów z urządzenia do chmury w w zapleczu rozwiązania.

IoT Hub zaimplementowano urządzenia do chmury komunikatów przy użyciu wzorca obsługi wiadomości przesyłanych strumieniowo. Przypominają komunikatów przesyłanych z chmury do urządzenia usługi IoT Hub [usługi Event Hubs] [ lnk-event-hubs] *zdarzenia* niż [usługi Service Bus] [ lnk-servicebus] *wiadomości* , istnieje duża liczba zdarzeń przekazywanych przez usługę, który może zostać odczytany przez czytniki wielu.

Obsługa komunikatów za pomocą usługi IoT Hub urządzenia do chmury ma następującą charakterystykę:

* Komunikaty z urządzenia do chmury są trwałe i zachowane w domyślnej usługi IoT hub **komunikaty/zdarzenia** punktu końcowego dla maksymalnie siedem dni.
* Komunikaty z urządzenia do chmury może być co najwyżej 256 KB i mogą być grupowane w partiach, aby zoptymalizować wysyła. Partie może mieć co najwyżej 256 KB.
* Jak wyjaśniono w [kontrolować dostęp do usługi IoT Hub] [ lnk-devguide-security] sekcji usługi IoT Hub umożliwia na urządzenia uwierzytelniania i kontroli dostępu.
* Usługa IoT Hub umożliwia tworzenie maksymalnie 10 niestandardowych punktów końcowych. Komunikaty są dostarczane do punktów końcowych, oparte na trasach skonfigurowane w usłudze IoT hub. Aby uzyskać więcej informacji, zobacz [reguły routingu](iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).
* Usługa IoT Hub udostępnia milionów równocześnie połączonych urządzeń (zobacz [przydziałów i dławienia][lnk-quotas]).
* Usługi IoT Hub nie zezwala na dowolne partycjonowania. Komunikaty z urządzenia do chmury są podzielone na partycje oparte na ich pochodzące **deviceId**.

Aby uzyskać więcej informacji na temat różnic między centrum IoT Hub i Event Hubs, zobacz [porównanie usługi Azure IoT Hub i Azure Event Hubs][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Wysyłaj ruch nietelemetrycznych

Często oprócz telemetrii, urządzenia wysyłają komunikaty i żądania, które wymagają wykonywania oddzielnych i obsługa w zapleczu rozwiązania. Na przykład alertów krytycznych, musi wyzwalać określonej akcji w zapleczu. Można napisać [reguły routingu] [ lnk-devguide-custom] do wysłania tego rodzaju wiadomości do punktu końcowego przeznaczone do przetwarzania, ich na podstawie nagłówka wiadomości lub wartością w treści komunikatu.

Aby uzyskać więcej informacji na temat najlepszym sposobem przetwarzania tego rodzaju wiadomości zobacz [samouczek: jak przetwarzać komunikaty z urządzenia do chmury usługi IoT Hub] [ lnk-d2c-tutorial] samouczka.

## <a name="route-device-to-cloud-messages"></a>Kierowanie komunikatów z urządzenia do chmury

Masz dwie opcje służącą do rozesłania wiadomości z urządzenia do chmury do aplikacji zaplecza:

* Użyj wbudowanych [punktu końcowego zgodnego z Centrum zdarzeń] [ lnk-compatible-endpoint] aby umożliwić aplikacji zaplecza, aby odczytywać komunikaty urządzenie chmura odbierane przez Centrum. Aby dowiedzieć się więcej na temat wbudowany punkt końcowy zgodny z Centrum zdarzeń, zobacz [odczytywanie komunikatów z urządzenia do chmury z wbudowanego punktu końcowego][lnk-devguide-builtin].
* Użyj reguł routingu do wysyłania komunikatów do niestandardowych punktów końcowych w usłudze IoT hub. Niestandardowe punkty końcowe umożliwiają aplikacji zaplecza do odczytywania komunikatów z urządzenia do chmury przy użyciu usługi Event Hubs, kolejki usługi Service Bus lub tematów usługi Service Bus. Aby dowiedzieć się więcej na temat routingu i niestandardowych punktów końcowych, zobacz [użyć niestandardowych punktów końcowych i reguł routingu dla komunikatów z urządzenia do chmury][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Ochrona przed fałszowaniem właściwości

Aby uniknąć urządzenia fałszowanie w komunikatów z urządzenia do chmury, usługi IoT Hub sygnatury wszystkie komunikaty z następującymi właściwościami:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Zawiera dwa pierwsze **deviceId** i **generationId** urządzenia źródłowego zgodnie [właściwości tożsamości urządzenia][lnk-device-properties].

**ConnectionAuthMethod** właściwość zawiera obiekt serializacji JSON z następującymi właściwościami:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące zestawów SDK, służy do wysyłania komunikatów urządzenie chmura, zobacz [Azure IoT SDKs][lnk-sdks].

[Przewodników Szybki Start] [ lnk-get-started] dowiesz się, jak wysyłać komunikaty urządzenie chmura z symulowanych urządzeń. Aby uzyskać więcej informacji, zobacz [komunikaty urządzenia do chmury usługi IoT Hub procesu przy użyciu tras] [ lnk-d2c-tutorial] samouczka.

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: quickstart-send-telemetry-node.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: tutorial-routing.md
