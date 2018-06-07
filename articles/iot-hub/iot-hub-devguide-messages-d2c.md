---
title: Zrozumienie wiadomości urządzenia do chmury Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera - sposobu korzystania z urządzenia do chmury do obsługi komunikatów z Centrum IoT. Zawiera informacje na temat wysyłania danych zarówno dane telemetryczne, jak i z systemem innym niż telemtry i przy użyciu routingu w celu dostarczania komunikatów.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 6096d726d7a00a4ddf8047edeebb74ab3f151e51
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808261"
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Wysyłanie komunikatów urządzenia do chmury do Centrum IoT

Aby wysłać dane telemetryczne szeregów czasowych i alertów z urządzeń z zaplecza rozwiązania, wysyłać urządzenia do chmury z urządzenia do Centrum IoT. Omówienie opcji inne urządzenia do chmury, obsługiwanych przez Centrum IoT można znaleźć [wskazówki komunikację urządzenia do chmury][lnk-d2c-guidance].

Wysyłać urządzenia do chmury za pośrednictwem punktu końcowego uwzględniającym urządzenia (**/devices/ {deviceId} / wiadomości/zdarzenia**). Zasady routingu, a następnie kierować wiadomości do jednej połączonej usługi punkty końcowe Centrum IoT. Reguły routingu Użyj nagłówki i treści wiadomości urządzenia do chmury, aby ustalić, gdzie można je rozesłać. Domyślnie komunikaty są kierowane do wbudowanym punktem końcowym usługi połączonej (**wiadomości/zdarzenia**), która jest zgodna z [usługi Event Hubs][lnk-event-hubs]. W takim przypadku używane standardowe [integracji usługi Event Hubs i zestawy SDK] [ lnk-compatible-endpoint] do odbierania wiadomości urządzenia do chmury w Twojej zaplecza rozwiązania.

Centrum IoT implementuje wiadomości urządzenia do chmury przy użyciu przesyłania strumieniowego wzorzec przesyłania komunikatów. Przypominają komunikaty urządzenia do chmury Centrum IoT [usługi Event Hubs] [ lnk-event-hubs] *zdarzenia* niż [usługi Service Bus] [ lnk-servicebus] *wiadomości* w tym istnieje duża liczba zdarzeń przechodzącej przez usługę, która może zostać odczytany przez wielu czytników.

Wiadomości z Centrum IoT urządzenia do chmury ma następującą charakterystykę:

* Urządzenia do chmury wiadomości są trwałe i zachowanych w Centrum IoT domyślne **wiadomości/zdarzenia** punktu końcowego do siedmiu dni.
* Komunikaty urządzenia do chmury może mieć maksymalnie 256 KB i można grupować w partiach w celu zoptymalizowania wysyła. Partie może mieć maksymalnie 256 KB.
* Zgodnie z objaśnieniem w [kontrolować dostęp do Centrum IoT] [ lnk-devguide-security] sekcji Centrum IoT umożliwia na urządzenie uwierzytelniania i kontroli dostępu.
* Centrum IoT umożliwia tworzenie maksymalnie 10 niestandardowe punkty końcowe. Wiadomości są dostarczane do punktów końcowych oparte na trasach skonfigurowany w Centrum IoT. Aby uzyskać więcej informacji, zobacz [reguły routingu](iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).
* Centrum IoT umożliwia miliony równocześnie połączonych urządzeń (zobacz [przydziały i ograniczenia przepustowości][lnk-quotas]).
* Centrum IoT nie zezwala na dowolne partycjonowania. Urządzenia do chmury wiadomości są dzielone na podstawie ich źródłowych **deviceId**.

Aby uzyskać więcej informacji na temat różnic między centrum IoT i usługi Event Hubs, zobacz [porównania Azure IoT Hub i usługi Azure Event Hubs][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Wysyłaj ruch z systemem innym niż telemetrii

Często oprócz telemetrii, urządzenia wysyłają komunikaty i żądań, które wymagają oddzielnego wykonywania i obsługa w zaplecza rozwiązania. Na przykład alerty krytyczne musi wyzwalających określonej akcji w wewnętrznej. Można napisać [reguły routingu] [ lnk-devguide-custom] do wysyłania komunikatów tego typu punkt końcowy dedykowanego ich przetwarzanie na podstawie nagłówek wiadomości lub wartością w treści wiadomości.

Aby uzyskać więcej informacji na temat najlepszy sposób, aby przetworzyć tego rodzaju wiadomości, zobacz [samouczek: jak do przetwarzania komunikatów urządzenia do chmury Centrum IoT] [ lnk-d2c-tutorial] samouczka.

## <a name="route-device-to-cloud-messages"></a>Rozsyłanie wiadomości urządzenia do chmury

Masz dwie opcje do rozesłania wiadomości urządzenia do chmury do zaplecza aplikacji:

* Użyj wbudowanej [punktu końcowego Centrum zdarzeń zgodnych] [ lnk-compatible-endpoint] umożliwiające zaplecza aplikacjom odczytywanie wiadomości urządzenia do chmury odbierane przez koncentratora. Aby zapoznać się z wbudowanym punktem końcowym zgodnego Centrum zdarzeń, zobacz [odczytywać wiadomości urządzenia do chmury z wbudowanym punktem końcowym][lnk-devguide-builtin].
* Reguły routingu umożliwia wysyłanie komunikatów do niestandardowe punkty końcowe w Centrum IoT. Niestandardowe punkty końcowe Włącz aplikacji zaplecza do czytania wiadomości urządzenia do chmury przy użyciu usługi Event Hubs, kolejek usługi Service Bus lub tematów usługi Service Bus. Aby uzyskać informacje dotyczące routingu i niestandardowe punkty końcowe, zobacz [Użyj niestandardowe punkty końcowe i reguły routingu dla komunikatów urządzenia do chmury][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Ochrona przed fałszowaniem właściwości

Aby uniknąć urządzenia fałszowania w komunikatach urządzenia do chmury, Centrum IoT sygnatury wszystkie komunikaty z następującymi właściwościami:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Zawiera dwa pierwsze **deviceId** i **generationId** urządzenia źródłowego zgodnie [właściwości tożsamości urządzenia][lnk-device-properties].

**ConnectionAuthMethod** właściwość zawiera obiekt Zserializowany do postaci JSON, z następującymi właściwościami:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje o zestawy SDK służy do wysyłania wiadomości urządzenia do chmury, zobacz [Azure IoT SDK][lnk-sdks].

[Wprowadzenie] [ lnk-get-started] samouczki wyjaśniają, jak wysyłać urządzenia do chmury z zarówno symulowane, jak i fizycznych urządzeń. Aby uzyskać więcej szczegółów, zobacz [Centrum IoT procesu wiadomości urządzenia do chmury, za pomocą trasy] [ lnk-d2c-tutorial] samouczka.

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: iot-hub-get-started.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: tutorial-routing.md
