---
title: Omówienie formatu komunikatów usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — w tym formacie i oczekiwanej zawartości komunikatami usługi IoT Hub.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 7c08848698f07d64bbbff429682c18525659f7bf
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286521"
---
# <a name="create-and-read-iot-hub-messages"></a>Tworzenie i odczytywanie komunikatów usługi IoT Hub

Aby umożliwić bezproblemowe współdziałanie różnych protokołów, usługi IoT Hub definiuje typowego formatu komunikatów dla wszystkich protokołów przeznaczonych dla urządzeń. Ten format wiadomości jest używany zarówno [urządzenia do chmury] [ lnk-d2c] i [chmury do urządzenia] [ lnk-c2d] wiadomości. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

[Komunikatów usługi IoT Hub] [ lnk-messaging] obejmuje:

* Predefiniowany zestaw *właściwości systemu* wymienione poniżej.
* Zbiór *właściwości aplikacji*. Słownik właściwości ciągów, które można zdefiniować aplikację i dostępu, bez konieczności wykonywania deserializacji treści komunikatu. Usługa IoT Hub nigdy nie modyfikuje te właściwości.
* Nieprzezroczysty dane binarne ciała.

Nazwy i wartości właściwości mogą zawierać tylko znaki alfanumeryczne ASCII, oraz ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` po użytkownik:  

* Wysyłanie komunikatów z urządzenia do chmury przy użyciu protokołu HTTPS.
* Wysyłanie komunikatów z chmury do urządzeń.

Aby uzyskać więcej informacji na temat kodowania i dekodowania wiadomości wysłane przy użyciu różnych protokołów, zobacz [Azure IoT SDKs][lnk-sdks].

Poniższa lista zawiera zbiór właściwości systemu w komunikatach usługi IoT Hub.

| Właściwość | Opis | Czy użytkownika można ustawić? |
| --- | --- | --- |
| MessageId |Identyfikator użytkownika można ustawić dla komunikatu używanego dla wzorców "żądanie-odpowiedź". Format: Ciąg uwzględniający wielkość liter (maksymalnie 128 znaków) znaków alfanumerycznych ASCII 7-bitowego + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Yes |
| Numer sekwencyjny |Liczba (unikatowe na urządzeniu kolejkę) przypisany przez usługę IoT Hub do każdego komunikatu chmury do urządzenia. | Brak komunikatów C2D; tak, w przeciwnym razie. |
| Do |Lokalizacji docelowej, określone w [chmury do urządzenia] [ lnk-c2d] wiadomości. | Brak komunikatów C2D; tak, w przeciwnym razie. |
| ExpiryTimeUtc |Data i godzina wygaśnięcia komunikatu. | Yes |
| EnqueuedTime |Data i godzina [chmury do urządzenia] [ lnk-c2d] wiadomość została odebrana przez usługę IoT Hub. | Brak komunikatów C2D; tak, w przeciwnym razie. |
| CorrelationId |Właściwość ciągu w komunikacie odpowiedzi, który zwykle zawiera identyfikator komunikatu żądania we wzorcach "żądanie-odpowiedź". | Yes |
| UserId |Identyfikator używany do określenia pochodzenia wiadomości. Gdy komunikaty są generowane przez usługę IoT Hub, jest równa `{iot hub name}`. | Nie |
| Potwierdzenia |Generator komunikat o opinię. Ta właściwość jest używana w komunikatów z chmury do urządzeń do usługi IoT Hub do generowania komunikatów zwrotnych w wyniku użycia komunikatu żądania przez urządzenie. Możliwe wartości: **Brak** (ustawienie domyślne): Brak komunikatu opinii jest generowany, **dodatnią**: Jeśli wiadomość została ukończona, wyświetlony komunikat opinii **ujemna**: odbierania komunikat opinii wygasł (lub została osiągnięta maksymalna liczba prób dostarczenia) bez kończone przez to urządzenie lub **pełne**: pozytywne i negatywne. Aby uzyskać więcej informacji, zobacz [komunikatu opinii][lnk-feedback]. | Yes |
| ConnectionDeviceId |Identyfikator jest ustawiony przez usługę IoT Hub na komunikaty z urządzenia do chmury. Zawiera on **deviceId** urządzenia wysyłającego wiadomość. | Brak komunikatów D2C; tak, w przeciwnym razie. |
| ConnectionDeviceGenerationId |Identyfikator jest ustawiony przez usługę IoT Hub na komunikaty z urządzenia do chmury. Zawiera on **generationId** (zgodnie [właściwości tożsamości urządzenia][lnk-device-properties]) urządzenia, która wysłała komunikat. | Brak komunikatów D2C; tak, w przeciwnym razie. |
| ConnectionAuthMethod |Metoda uwierzytelniania, ustawić przez usługę IoT Hub dla komunikatów z urządzenia do chmury. Ta właściwość zawiera informacje o metodę uwierzytelniania stosowaną w celu uwierzytelnienia urządzenia wysyłania wiadomości. Aby uzyskać więcej informacji, zobacz [urządzenia do chmury, ochrony przed fałszowaniem][lnk-antispoofing]. | Brak komunikatów D2C; tak, w przeciwnym razie. |
| CreationTimeUtc | Data i godzina utworzenia komunikatu na urządzeniu. Urządzenie musi jawnie ustaw tę wartość. | Yes |

## <a name="message-size"></a>Rozmiar komunikatu

Usługi IoT Hub mierzy rozmiar wiadomości w sposób niezależny od protokołu, biorąc pod uwagę rzeczywiste obciążenie. Rozmiar w bajtach jest obliczany jako suma z następujących czynności:

* Rozmiar treści w bajtach.
* Rozmiar w bajtach wartości właściwości systemu komunikatu.
* Rozmiar w bajtach wszystkie użytkownika nazwy i wartości właściwości.

Nazwy i wartości właściwości są ograniczone do znaków ASCII, więc długość ciągów jest równy rozmiar w bajtach.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacji na temat limitów rozmiaru wiadomości w usłudze IoT Hub, zobacz [usługi IoT Hub przydziałów i dławienia][lnk-quotas].

Aby dowiedzieć się, jak tworzenie i odczytywanie wiadomości w różnych językach programowania usługi IoT Hub, zobacz [przewodników Szybki Start][lnk-get-started].

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
