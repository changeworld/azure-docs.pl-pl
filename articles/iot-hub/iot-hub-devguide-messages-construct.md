---
title: Omówienie formatu komunikatów usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — w tym artykule opisano format i oczekiwanej zawartości komunikatami usługi IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: e80fb136220330ddc53d513b22ebcfa19a35117b
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252799"
---
# <a name="create-and-read-iot-hub-messages"></a>Tworzenie i odczytywanie komunikatów usługi IoT Hub

Aby umożliwić bezproblemowe współdziałanie różnych protokołów, usługi IoT Hub definiuje typowego formatu komunikatów dla wszystkich protokołów przeznaczonych dla urządzeń. Ten format wiadomości jest używany zarówno [routingu urządzenia do chmury](iot-hub-devguide-messages-d2c.md) i [chmury do urządzenia](iot-hub-devguide-messages-c2d.md) wiadomości. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub zaimplementowano urządzenia do chmury komunikatów przy użyciu wzorca obsługi wiadomości przesyłanych strumieniowo. Przypominają komunikatów przesyłanych z chmury do urządzenia usługi IoT Hub [usługi Event Hubs](/azure/event-hubs/) *zdarzenia* niż [usługi Service Bus](/azure/service-bus-messaging/) *wiadomości* , istnieje duża przekazywanie za pośrednictwem usługi, która może zostać odczytany przez czytniki wielu zdarzeń.

Komunikat usługi IoT Hub składa się z:

* Predefiniowany zestaw *właściwości systemu* wymienione poniżej.

* Zbiór *właściwości aplikacji*. Słownik właściwości ciągów, które można zdefiniować aplikację i dostępu, bez konieczności wykonywania deserializacji treści komunikatu. Usługa IoT Hub nigdy nie modyfikuje te właściwości.

* Nieprzezroczysty dane binarne ciała.

Nazwy i wartości właściwości mogą zawierać tylko znaki alfanumeryczne ASCII, a także ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` podczas wysyłania komunikatów z urządzenia do chmury przy użyciu protokołu HTTPS protokołu lub wysyłać komunikaty z chmury do urządzenia.

Obsługa komunikatów za pomocą usługi IoT Hub urządzenia do chmury ma następującą charakterystykę:

* Komunikaty z urządzenia do chmury są trwałe i zachowane w domyślnej usługi IoT hub **komunikaty/zdarzenia** punktu końcowego dla maksymalnie siedem dni.

* Komunikaty z urządzenia do chmury może być co najwyżej 256 KB i mogą być grupowane w partiach, aby zoptymalizować wysyła. Partie może mieć co najwyżej 256 KB.

* Usługi IoT Hub nie zezwala na dowolne partycjonowania. Komunikaty z urządzenia do chmury są podzielone na partycje oparte na ich pochodzące **deviceId**.

* Jak wyjaśniono w [kontrolować dostęp do usługi IoT Hub](iot-hub-devguide-security.md), usługa IoT Hub udostępnia na urządzenia uwierzytelniania i kontroli dostępu.

* Za sygnatury komunikaty z informacjami, które przechodzi do właściwości aplikacji. Aby uzyskać więcej informacji, zobacz [komunikatu wzbogacenia](iot-hub-message-enrichments-overview.md).

Aby uzyskać więcej informacji na temat kodowania i dekodowania wiadomości wysłane przy użyciu różnych protokołów, zobacz [Azure IoT SDKs](iot-hub-devguide-sdks.md).

Poniższa lista zawiera zbiór właściwości systemu w komunikatach usługi IoT Hub.

| Właściwość | Opis | Czy użytkownika można ustawić? |
| --- | --- | --- |
| message-id |Identyfikator użytkownika można ustawić dla komunikatu używanego dla wzorców "żądanie-odpowiedź". Format: Ciąg uwzględniający wielkość liter (maksymalnie 128 znaków) znaków alfanumerycznych ASCII 7-bitowego + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Yes |
| numer sekwencyjny |Liczba (unikatowe na urządzeniu kolejkę) przypisany przez usługę IoT Hub do każdego komunikatu chmury do urządzenia. | Brak komunikatów C2D; tak, w przeciwnym razie. |
| na |Lokalizacji docelowej, określone w [chmury do urządzenia](iot-hub-devguide-c2d-guidance.md) wiadomości. | Brak komunikatów C2D; tak, w przeciwnym razie. |
| czas w przypadku wygaśnięcia bezwzględne |Data i godzina wygaśnięcia komunikatu. | Yes |
| iothub enqueuedtime |Data i godzina [chmury do urządzenia](iot-hub-devguide-c2d-guidance.md) wiadomość została odebrana przez usługę IoT Hub. | Brak komunikatów C2D; tak, w przeciwnym razie. |
| correlation-id |Właściwość ciągu w komunikacie odpowiedzi, który zwykle zawiera identyfikator komunikatu żądania we wzorcach "żądanie-odpowiedź". | Tak |
| user-id |Identyfikator używany do określenia pochodzenia wiadomości. Gdy komunikaty są generowane przez usługę IoT Hub, jest równa `{iot hub name}`. | Nie |
| potwierdzenia iothub |Generator komunikat o opinię. Ta właściwość jest używana w komunikatów z chmury do urządzeń do usługi IoT Hub do generowania komunikatów zwrotnych w wyniku użycia komunikatu żądania przez urządzenie. Możliwe wartości: **Brak** (ustawienie domyślne): Brak komunikatu opinii jest generowany, **dodatnią**: Jeśli wiadomość została ukończona, wyświetlony komunikat opinii **ujemna**: odbierania komunikat opinii wygasł (lub została osiągnięta maksymalna liczba prób dostarczenia) bez kończone przez to urządzenie lub **pełne**: pozytywne i negatywne. <!-- robinsh For more information, see [Message feedback][lnk-feedback].--> | Tak |
| iothub-connection-device-id |Identyfikator jest ustawiony przez usługę IoT Hub na komunikaty z urządzenia do chmury. Zawiera on **deviceId** urządzenia wysyłającego wiadomość. | Brak komunikatów D2C; tak, w przeciwnym razie. |
| iothub-connection-auth-generation-id |Identyfikator jest ustawiony przez usługę IoT Hub na komunikaty z urządzenia do chmury. Zawiera on **generationId** (zgodnie [właściwości tożsamości urządzenia](iot-hub-devguide-identity-registry.md#device-identity-properties)) urządzenia, która wysłała komunikat. | Brak komunikatów D2C; tak, w przeciwnym razie. |
| iothub-connection-auth-method |Metoda uwierzytelniania, ustawić przez usługę IoT Hub dla komunikatów z urządzenia do chmury. Ta właściwość zawiera informacje o metodę uwierzytelniania stosowaną w celu uwierzytelnienia urządzenia wysyłania wiadomości. <!-- ROBINSH For more information, see [Device to cloud anti-spoofing][lnk-antispoofing].--> | Brak komunikatów D2C; tak, w przeciwnym razie. |
| iothub-creation-time-utc | Data i godzina utworzenia komunikatu na urządzeniu. Urządzenie musi jawnie ustaw tę wartość. | Tak |

## <a name="message-size"></a>Rozmiar komunikatu

Usługi IoT Hub mierzy rozmiar wiadomości w sposób niezależny od protokołu, biorąc pod uwagę rzeczywiste obciążenie. Rozmiar w bajtach jest obliczany jako suma z następujących czynności:

* Rozmiar treści w bajtach.
* Rozmiar w bajtach wartości właściwości systemu komunikatu.
* Rozmiar w bajtach wszystkie użytkownika nazwy i wartości właściwości.

Nazwy i wartości właściwości są ograniczone do znaków ASCII, więc długość ciągów jest równy rozmiar w bajtach.

## <a name="anti-spoofing-properties"></a>Ochrona przed fałszowaniem właściwości

Aby uniknąć urządzenia fałszowanie w komunikatów z urządzenia do chmury, usługi IoT Hub sygnatury wszystkie komunikaty z następującymi właściwościami:

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **iothub-connection-auth-method**

Zawiera dwa pierwsze **deviceId** i **generationId** urządzenia źródłowego zgodnie [właściwości tożsamości urządzenia](iot-hub-devguide-identity-registry.md#device-identity-properties).

**Iothub połączenia — — metoda uwierzytelniania** właściwość zawiera obiekt serializacji JSON z następującymi właściwościami:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać informacji na temat limitów rozmiaru wiadomości w usłudze IoT Hub, zobacz [usługi IoT Hub przydziałów i dławienia](iot-hub-devguide-quotas-throttling.md).

* Aby dowiedzieć się, jak tworzenie i odczytywanie wiadomości w różnych językach programowania usługi IoT Hub, zobacz [przewodników Szybki Start](quickstart-send-telemetry-node.md).
