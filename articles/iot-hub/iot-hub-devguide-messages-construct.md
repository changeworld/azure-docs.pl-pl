---
title: Omówienie formatu komunikatów usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — w tym formacie i oczekiwanej zawartości komunikatami usługi IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: edea20343c2a261902c082dbc5c96b78db6b470d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973224"
---
# <a name="create-and-read-iot-hub-messages"></a>Tworzenie i odczytywanie komunikatów usługi IoT Hub

Aby umożliwić bezproblemowe współdziałanie różnych protokołów, usługi IoT Hub definiuje typowego formatu komunikatów dla wszystkich protokołów przeznaczonych dla urządzeń. Ten format wiadomości jest używany zarówno [routingu urządzenia do chmury](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) i [chmury do urządzenia](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-c2d) wiadomości. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub zaimplementowano urządzenia do chmury komunikatów przy użyciu wzorca obsługi wiadomości przesyłanych strumieniowo. Przypominają komunikatów przesyłanych z chmury do urządzenia usługi IoT Hub [usługi Event Hubs](https://docs.microsoft.com/azure/event-hubs/) *zdarzenia* niż [usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/) *wiadomości* , istnieje duża przekazywanie za pośrednictwem usługi, która może zostać odczytany przez czytniki wielu zdarzeń.

Komunikat usługi IoT Hub składa się z:
* Predefiniowany zestaw *właściwości systemu* wymienione poniżej.
* Zbiór *właściwości aplikacji*. Słownik właściwości ciągów, które można zdefiniować aplikację i dostępu, bez konieczności wykonywania deserializacji treści komunikatu. Usługa IoT Hub nigdy nie modyfikuje te właściwości.
* Nieprzezroczysty dane binarne ciała.

Nazwy i wartości właściwości mogą zawierać tylko znaki alfanumeryczne ASCII, a także ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` podczas wysyłania komunikatów z urządzenia do chmury przy użyciu protokołu HTTPS protokołu lub wysyłać komunikaty z chmury do urządzenia.

Obsługa komunikatów za pomocą usługi IoT Hub urządzenia do chmury ma następującą charakterystykę:

* Komunikaty z urządzenia do chmury są trwałe i zachowane w domyślnej usługi IoT hub **komunikaty/zdarzenia** punktu końcowego dla maksymalnie siedem dni.
* Komunikaty z urządzenia do chmury może być co najwyżej 256 KB i mogą być grupowane w partiach, aby zoptymalizować wysyła. Partie może mieć co najwyżej 256 KB.
* Usługi IoT Hub nie zezwala na dowolne partycjonowania. Komunikaty z urządzenia do chmury są podzielone na partycje oparte na ich pochodzące **deviceId**.
* Jak wyjaśniono w [kontrolować dostęp do usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security) sekcji usługi IoT Hub umożliwia na urządzenia uwierzytelniania i kontroli dostępu.

Aby uzyskać więcej informacji na temat kodowania i dekodowania wiadomości wysłane przy użyciu różnych protokołów, zobacz [Azure IoT SDKs](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Poniższa lista zawiera zbiór właściwości systemu w komunikatach usługi IoT Hub.

| Właściwość | Opis | Czy użytkownika można ustawić? |
| --- | --- | --- |
| MessageId |Identyfikator użytkownika można ustawić dla komunikatu używanego dla wzorców "żądanie-odpowiedź". Format: Ciąg uwzględniający wielkość liter (maksymalnie 128 znaków) znaków alfanumerycznych ASCII 7-bitowego + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Yes |
| Numer sekwencyjny |Liczba (unikatowe na urządzeniu kolejkę) przypisany przez usługę IoT Hub do każdego komunikatu chmury do urządzenia. | Brak komunikatów C2D; tak, w przeciwnym razie. |
| Do |Miejsce docelowe, określone w komunikatach [chmury do urządzenia] [lnk-c2d]. | Brak komunikatów C2D; tak, w przeciwnym razie. |
| ExpiryTimeUtc |Data i godzina wygaśnięcia komunikatu. | Yes |
| EnqueuedTime |Data i godzina odebrania komunikatu [chmury do urządzenia] [lnk-c2d] przez usługę IoT Hub. | Brak komunikatów C2D; tak, w przeciwnym razie. |
| CorrelationId |Właściwość ciągu w komunikacie odpowiedzi, który zwykle zawiera identyfikator komunikatu żądania we wzorcach "żądanie-odpowiedź". | Yes |
| UserId |Identyfikator używany do określenia pochodzenia wiadomości. Gdy komunikaty są generowane przez usługę IoT Hub, jest równa `{iot hub name}`. | Nie |
| Potwierdzenia |Generator komunikat o opinię. Ta właściwość jest używana w komunikatów z chmury do urządzeń do usługi IoT Hub do generowania komunikatów zwrotnych w wyniku użycia komunikatu żądania przez urządzenie. Możliwe wartości: **Brak** (ustawienie domyślne): Brak komunikatu opinii jest generowany, **dodatnią**: Jeśli wiadomość została ukończona, wyświetlony komunikat opinii **ujemna**: odbierania komunikat opinii wygasł (lub została osiągnięta maksymalna liczba prób dostarczenia) bez kończone przez to urządzenie lub **pełne**: pozytywne i negatywne. Aby uzyskać więcej informacji, zobacz [komunikat opinia] [lnk opinia]. | Yes |
| ConnectionDeviceId |Identyfikator jest ustawiony przez usługę IoT Hub na komunikaty z urządzenia do chmury. Zawiera on **deviceId** urządzenia wysyłającego wiadomość. | Brak komunikatów D2C; tak, w przeciwnym razie. |
| ConnectionDeviceGenerationId |Identyfikator jest ustawiony przez usługę IoT Hub na komunikaty z urządzenia do chmury. Zawiera on **generationId** (zgodnie z [urządzenia properties][lnk-device-properties]) tożsamości urządzenia, która wysłała komunikat. | Brak komunikatów D2C; tak, w przeciwnym razie. |
| ConnectionAuthMethod |Metoda uwierzytelniania, ustawić przez usługę IoT Hub dla komunikatów z urządzenia do chmury. Ta właściwość zawiera informacje o metodę uwierzytelniania stosowaną w celu uwierzytelnienia urządzenia wysyłania wiadomości. Aby uzyskać więcej informacji, zobacz [urządzenie z chmurą, ochrona przed fałszowaniem] [lnk-antispoofing]. | Brak komunikatów D2C; tak, w przeciwnym razie. |
| CreationTimeUtc | Data i godzina utworzenia komunikatu na urządzeniu. Urządzenie musi jawnie ustaw tę wartość. | Yes |

## <a name="message-size"></a>Rozmiar komunikatu

Usługi IoT Hub mierzy rozmiar wiadomości w sposób niezależny od protokołu, biorąc pod uwagę rzeczywiste obciążenie. Rozmiar w bajtach jest obliczany jako suma z następujących czynności:

* Rozmiar treści w bajtach.
* Rozmiar w bajtach wartości właściwości systemu komunikatu.
* Rozmiar w bajtach wszystkie użytkownika nazwy i wartości właściwości.

Nazwy i wartości właściwości są ograniczone do znaków ASCII, więc długość ciągów jest równy rozmiar w bajtach.

## <a name="anti-spoofing-properties"></a>Ochrona przed fałszowaniem właściwości

Aby uniknąć urządzenia fałszowanie w komunikatów z urządzenia do chmury, usługi IoT Hub sygnatury wszystkie komunikaty z następującymi właściwościami:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Zawiera dwa pierwsze **deviceId** i **generationId** urządzenia źródłowego zgodnie [właściwości tożsamości urządzenia](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#device-identity-properties).

**ConnectionAuthMethod** właściwość zawiera obiekt serializacji JSON z następującymi właściwościami:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać informacji na temat limitów rozmiaru wiadomości w usłudze IoT Hub, zobacz [usługi IoT Hub przydziałów i dławienia](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-quotas-throttling).
* Aby dowiedzieć się, jak tworzenie i odczytywanie wiadomości w różnych językach programowania usługi IoT Hub, zobacz [przewodników Szybki Start](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-node).