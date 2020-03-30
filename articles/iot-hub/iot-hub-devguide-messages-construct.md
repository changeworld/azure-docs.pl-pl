---
title: Opis formatu wiadomości usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — opisuje format i oczekiwaną zawartość komunikatów Usługi IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: 28537ac2389fbb1ca43ca4014515564bddeba4ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69872482"
---
# <a name="create-and-read-iot-hub-messages"></a>Tworzenie i odczytywanie komunikatów usługi IoT Hub

Aby zapewnić bezproblemową współdziałanie między protokołami, Usługa IoT Hub definiuje wspólny format wiadomości dla wszystkich protokołów przeznaczonych dla urządzeń. Ten format wiadomości jest używany zarówno [do routingu między urządzeniami a chmurą,](iot-hub-devguide-messages-d2c.md) jak i dla komunikatów [z chmury do urządzenia.](iot-hub-devguide-messages-c2d.md) 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Usługa IoT Hub implementuje wiadomości między urządzeniami w chmurze przy użyciu wzorca przesyłania strumieniowego wiadomości. Komunikaty usługi IoT Hub z urządzenia do chmury są bardziej podobne do *zdarzeń* [usługi Event Hubs](/azure/event-hubs/) niż *komunikatów* [usługi Service Bus,](/azure/service-bus-messaging/) ponieważ istnieje duża liczba zdarzeń przechodzących przez usługę, które mogą być odczytywane przez wielu czytelników.

Komunikat Usługi IoT Hub składa się z:

* Z góry określony zestaw *właściwości systemu,* jak podano poniżej.

* Zestaw *właściwości aplikacji*. Słownik właściwości ciągu, które aplikacja może zdefiniować i uzyskać dostęp, bez konieczności deserializacji treści wiadomości. Centrum IoT nigdy nie modyfikuje tych właściwości.

* Nieprzezroczysty obiekt binarny.

Nazwy i wartości właściwości mogą zawierać tylko znaki alfanumeryczne ASCII, a także ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` podczas wysyłania wiadomości z urządzenia do chmury przy użyciu protokołu HTTPS lub wysyłania komunikatów z chmury do urządzenia.

Obsługa wiadomości między urządzeniami w chmurze za pomocą usługi IoT Hub ma następujące cechy:

* Komunikaty między urządzeniami do chmury są trwałe i zachowywane w domyślnym punkcie końcowym **wiadomości/zdarzeń** centrum IoT przez maksymalnie siedem dni.

* Komunikaty między urządzeniami do chmury mogą mieć co najwyżej 256 KB i mogą być grupowane w partiach w celu optymalizacji wysyłanych. Partie mogą mieć co najwyżej 256 KB.

* Centrum IoT hub nie zezwala na partycjonowanie dowolnego. Komunikaty między urządzeniami w chmurze są podzielone na partycje na podstawie ich **urządzenia źródłowegoId**.

* Jak wyjaśniono w [kontroli dostępu do Usługi IoT Hub,](iot-hub-devguide-security.md)Centrum IoT umożliwia uwierzytelnianie na urządzeniu i kontrolę dostępu.

* Można stemplować wiadomości z informacjami, które przechodzi do właściwości aplikacji. Aby uzyskać więcej informacji, zobacz [wzbogacenia wiadomości](iot-hub-message-enrichments-overview.md).

Aby uzyskać więcej informacji na temat kodowania i dekodowania wiadomości wysyłanych przy użyciu różnych protokołów, zobacz [Zestawy SDK IoT platformy Azure](iot-hub-devguide-sdks.md).

## <a name="system-properties-of-d2c-iot-hub-messages"></a>Właściwości systemu komunikatów centrum IoT usługi **D2C**

| Właściwość | Opis  |Settable użytkownika?|Słowo kluczowe dla </br>kwerenda routingu|
| --- | --- | --- | --- |
| identyfikator wiadomości |Identyfikator umożliwiający ustawienie użytkownika wiadomości używanej dla wzorców żądanie-odpowiedź. Format: Ciąg rozróżniany wielkości liter (do 128 znaków) 7-bitowych znaków `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`alfanumerycznej ASCII + .  | Tak | Messageid |
| iothub-enqueuedtime |Data i godzina odebranie przez centrum IoT Hub [komunikatu z urządzenia do chmury.](iot-hub-devguide-d2c-guidance.md) | Nie | enqueuedCzas |
| user-id |Identyfikator używany do określania pochodzenia wiadomości. Gdy wiadomości są generowane przez Centrum IoT, jest ustawiona na `{iot hub name}`. | Tak | userId |
| iothub-connection-device-id |Identyfikator ustawiony przez Centrum IoT na komunikatach z urządzenia do chmury. Zawiera **identyfikator deviceid** urządzenia, które wysłało wiadomość. | Nie | łączeDeviceId |
| iothub-connection-module-id |Identyfikator ustawiony przez Centrum IoT na komunikatach z urządzenia do chmury. Zawiera **identyfikator modułu** urządzenia, które wysłało wiadomość. | Nie | Mieszk łączeModuleId |
| iothub-connection-auth-generation-id |Identyfikator ustawiony przez Centrum IoT na komunikatach z urządzenia do chmury. Zawiera **connectionDeviceGenerationId** (zgodnie z [właściwościami tożsamości urządzenia)](iot-hub-devguide-identity-registry.md#device-identity-properties)urządzenia, które wysłało wiadomość. | Nie |connectionDeviceGenerationId |
| iothub-connection-auth-method |Metoda uwierzytelniania ustawiona przez Centrum IoT na komunikatach z urządzenia do chmury. Ta właściwość zawiera informacje o metodzie uwierzytelniania używanej do uwierzytelniania urządzenia wysyłającego wiadomość.| Nie | połączenieAuthMetoda |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>Właściwości systemu komunikatów centrum IoT usługi **C2D**

| Właściwość | Opis  |Settable użytkownika?|
| --- | --- | --- |
| identyfikator wiadomości |Identyfikator umożliwiający ustawienie użytkownika wiadomości używanej dla wzorców żądanie-odpowiedź. Format: Ciąg rozróżniany wielkości liter (do 128 znaków) 7-bitowych znaków `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`alfanumerycznej ASCII + .  |Tak|
| numer sekwencyjny |Numer (unikatowy na kolejkę urządzenia) przypisany przez centrum IoT Hub do każdego komunikatu chmury do urządzenia. |Nie|
| na |Miejsce docelowe określone w [komunikatach z chmury do urządzenia.](iot-hub-devguide-c2d-guidance.md) |Nie|
| bezwzględny czas wygaśnięcia |Data i godzina wygaśnięcia wiadomości. |Nie|   |
| correlation-id |Właściwość ciągu w komunikacie odpowiedzi, który zazwyczaj zawiera MessageId żądania, w wzorcach żądanie odpowiedź. |Tak|
| user-id |Identyfikator używany do określania pochodzenia wiadomości. Gdy wiadomości są generowane przez Centrum IoT, jest ustawiona na `{iot hub name}`. |Tak|
| iothub-ack |Generator wiadomości zwrotnych. Ta właściwość jest używana w komunikatach chmury do urządzenia, aby zażądać Usługi IoT Hub do generowania wiadomości zwrotnych w wyniku użycia wiadomości przez urządzenie. Możliwe wartości: **brak** (domyślnie): nie jest generowany komunikat zwrotny, **pozytywny:** otrzymaj wiadomość zwrotną, jeśli wiadomość została ukończona, **negatywna:** otrzymaj wiadomość zwrotną, jeśli wiadomość wygasła (lub osiągnięto maksymalną liczbę dostarczenia) bez ukończenia przez urządzenie lub **pełna**: dodatnia i negatywna. |Tak|

## <a name="message-size"></a>Rozmiar komunikatu

Usługa IoT Hub mierzy rozmiar wiadomości w sposób niezależny od protokołu, biorąc pod uwagę tylko rzeczywisty ładunek. Rozmiar w bajtach jest obliczany jako suma następujących wartości:

* Rozmiar ciała w bajtach.
* Rozmiar w bajtach wszystkich wartości właściwości systemu wiadomości.
* Rozmiar w bajtach wszystkich nazw i wartości właściwości użytkownika.

Nazwy i wartości właściwości są ograniczone do znaków ASCII, więc długość ciągów jest równa rozmiarowi w bajtach.

## <a name="anti-spoofing-properties"></a>Właściwości zapobiegające fałszowaniu

Aby uniknąć fałszowania urządzeń w komunikatach typu device-to-cloud, Centrum IoT stempluje wszystkie wiadomości następującymi właściwościami:

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

Pierwsze dwa zawierają **deviceId** i **generationId** urządzenia źródłowego, zgodnie z [właściwości tożsamości urządzenia](iot-hub-devguide-identity-registry.md#device-identity-properties).

Właściwość **iothub-connection-auth-method** zawiera obiekt szeregowy JSON z następującymi właściwościami:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o limitach rozmiaru wiadomości w Centrum IoT, zobacz [Przydziały i ograniczanie przepustowości usługi IoT Hub](iot-hub-devguide-quotas-throttling.md).

* Aby dowiedzieć się, jak tworzyć i odczytywać komunikaty Usługi IoT Hub w różnych językach programowania, zobacz [Przewodniky Szybki start](quickstart-send-telemetry-node.md).
