---
title: Omówienie obsługi komunikatów chmura urządzenie usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Ten przewodnik dla deweloperów w tym artykule omówiono sposób korzystania z chmury do urządzenia, obsługa komunikatów za pomocą usługi IoT hub. Zawiera informacje o cyklu życia komunikatu i opcje konfiguracji.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 0fc1b65a4ba1c8a3d76b48206d6a4703035e05bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055333"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Wysyłanie komunikatów z chmury do urządzeń z usługi IoT hub

Wysyłać jednokierunkowe powiadomienia aplikacji urządzenia z zapleczem rozwiązania, wysyłanie komunikatów z chmury do urządzeń z usługi IoT hub na urządzenie. Aby uzyskać omówienie inne opcje chmury do urządzenia obsługiwane przez usługę Azure IoT Hub, zobacz [wskazówki dotyczące komunikacji z chmury do urządzenia](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Wysyłanie komunikatów z chmury do urządzeń za pośrednictwem punktu końcowego usługi skierowaną */komunikatów/devicebound*. Urządzenie odbiera komunikaty za pośrednictwem punktu końcowego specyficznych dla urządzenia */devices/ {deviceId} / komunikatów/devicebound*.

Pod kątem każdego komunikatu chmura urządzenie, na jednym urządzeniu, ustawia Centrum IoT hub **do** właściwości */devices/ {deviceId} / komunikatów/devicebound*.

Każda kolejka urządzenia przechowuje maksymalnie 50 komunikatów z chmury do urządzeń. Próbował wysłać więcej wiadomości do tego samego urządzenia powoduje wystąpienie błędu.

## <a name="the-cloud-to-device-message-life-cycle"></a>Cykl życia komunikatów z chmury do urządzenia

Aby zagwarantować dostarczanie komunikatów co najmniej jednokrotne, Centrum IoT hub utrzymuje komunikatów z chmury do urządzeń w kolejkach na urządzenie. Centrum IoT usunąć komunikaty z kolejki, urządzenia musi jawnie potwierdzić *uzupełniania*. Ta metoda gwarantuje odporność na łączność i awarii urządzenia.

Wykres stanu cyklu życia jest wyświetlana na poniższym diagramie:

![Cykl życia komunikatów z chmury do urządzenia](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Gdy usługi IoT hub wysyła wiadomość do urządzenia, Usługa ustawia stan komunikatu *umieszczonych w kolejce*. Kiedy urządzenie chce *otrzymywać* komunikat o błędzie, usługi IoT hub *blokad* wiadomości, ustawiając stan *niewidoczne*. Ten stan umożliwia inne wątki na urządzeniu, aby zacząć otrzymywać inne komunikaty. Po zakończeniu przetwarzania komunikatów wątku urządzenia powiadomi użytkownika w Centrum IoT hub za *Kończenie* wiadomości. Usługa IoT hub ustawia stan do *Ukończono*.

Urządzenie może również:

* *Odrzuć* wiadomości, co powoduje, że Centrum IoT, ustaw ją na *Dead lettered* stanu. Urządzenia łączące się za pośrednictwem protokołu komunikatów usługi kolejkowania MQTT (Telemetry Transport) nie można odrzucić komunikatów z chmury do urządzeń.

* *Porzuć* wiadomości, co powoduje, że usługi IoT hub można umieścić komunikatu w kolejce, ze stanem równa *umieszczonych w kolejce*. Urządzenia łączące się za pośrednictwem protokołu MQTT nie można porzucić komunikatów z chmury do urządzeń.

Wątek może się nie powieść przetwarzania wiadomości bez powiadamiania usługi IoT hub. W tym przypadku wiadomości automatyczne przejście z *niewidoczne* powrót do stanu *umieszczonych w kolejce* stanu po *widoczność* limitu czasu (lub *blokady* limitu czasu). Wartość domyślna tego limitu czasu jest jedna minuta.

**Maksymalna liczba prób dostarczenia** właściwość w Centrum IoT hub określa maksymalną liczbę razy komunikat może przejść między *umieszczonych w kolejce* i *niewidoczne* stanów. Po tej liczbie przejścia usługi IoT hub, ustawia stan wiadomości do *Dead lettered*. Podobnie, usługa IoT hub ustawia stan wiadomości do *Dead lettered* po jego czas wygaśnięcia. Aby uzyskać więcej informacji, zobacz [czas wygaśnięcia](#message-expiration-time-to-live).

[o sposobie wysyłania komunikatów z chmury do urządzeń z usługą IoT Hub](iot-hub-csharp-csharp-c2d.md) artykule pokazano, jak wysyłać komunikaty z chmury do urządzenia w chmurze i odbierać je na urządzeniu.

Urządzenie zazwyczaj kończy wiadomość chmury do urządzeń podczas utraty komunikat nie ma wpływu na logice aplikacji. Na przykład może być, gdy urządzenie ma trwały lokalnie zawartości wiadomości lub pomyślnie wykonano operację. Komunikat może również zawierać informacje przejściowych, w której utraty nie ma wpływu na funkcjonalność aplikacji. Czasami, w przypadku długotrwałych zadań, możesz:

* Zakończ wiadomość chmury do urządzenia, po urządzenia zawiera utrwalone w opisie zadania w magazynie lokalnym.

* Powiadom zaplecze rozwiązania z jednego lub więcej komunikatów z urządzenia do chmury na różnych etapach postęp zadania.

## <a name="message-expiration-time-to-live"></a>Wygaśnięcie komunikatu (czas wygaśnięcia)

Wszystkie komunikaty z chmury do urządzeń ma czas wygaśnięcia. Tym razem zostanie ustawiony przy użyciu jednej z następujących czynności:

* **ExpiryTimeUtc** właściwości usługi
* Centrum IoT, przy użyciu domyślnej *czas wygaśnięcia* określona jako właściwość Centrum IoT

Zobacz [opcje konfiguracji chmury do urządzenia](#cloud-to-device-configuration-options).

Typowy sposób, aby móc korzystać z wygaśnięcie komunikatu i unikać wysyłania wiadomości do odłączone urządzenia jest ustawienie krótki *czas wygaśnięcia* wartości. To podejście osiąga ten sam wynik jako zachowanie stanu połączenia urządzenia, ale jest bardziej wydajne. Żądając potwierdzeń komunikatów usługi IoT hub powiadamia użytkownika, które urządzenia są:

* Może odbierać wiadomości.
* Nie są w trybie online lub zakończyć się niepowodzeniem.

## <a name="message-feedback"></a>Komunikat o opinię

Podczas wysyłania komunikatu chmura urządzenie usługi mogą żądać dostarczanie wiadomości swoją opinię na temat stanu końcowego tego komunikatu. Możesz to zrobić, ustawiając **iothub potwierdzenia** właściwości aplikacji w komunikacie chmury do urządzenia, który jest wysyłany do jednego z następujących czterech wartości:

| Wartość właściwości ACK. | Zachowanie |
| ------------ | -------- |
| Brak     | Usługa IoT hub nie generuje komunikat opinii (zachowanie domyślne). |
| pozytywna | Jeśli osiągnie komunikatu chmura urządzenie *Ukończono* stanu usługi IoT hub generuje komunikat opinii. |
| Ujemna | Jeśli osiągnie komunikatu chmura urządzenie *Dead lettered* stanu usługi IoT hub generuje komunikat opinii. |
| pełne     | Usługa IoT hub generuje komunikat opinii w obu przypadkach. |

Jeśli **potwierdzenia** wartość *pełne*i nie pojawi się komunikat o opinię, oznacza to, wygaśnięcie komunikatu opinii. Usługa nie wiedzieć, co się stało z oryginalnej wiadomości. W praktyce usługi należy upewnić się, że opinia może przetworzyć przed jej wygaśnięciem. Czas wygaśnięcia maksymalna dwa dni, a co pozostawia czas na usługę działa ponownie po wystąpieniu błędu.

Jak wyjaśniono w [punktów końcowych](iot-hub-devguide-endpoints.md), IoT hub dostarczy opinii za pośrednictwem punktu końcowego usługi skierowaną */messages/servicebound/feedback*, jako komunikaty. Semantyka dla otrzymująca opinie są takie same jak dla komunikatów z chmury do urządzeń. Jeśli to możliwe, opinii wiadomości jest partii w pojedynczym komunikacie o następującym formacie:

| Właściwość     | Opis |
| ------------ | ----------- |
| EnqueuedTime | Sygnatura czasowa, która wskazuje, kiedy odebrano komunikat opinii przez Centrum |
| UserId       | `{iot hub name}` |
| Typ zawartości  | `application/vnd.microsoft.iothub.feedback.json` |

Treść jest serializacji JSON tablicą rekordów, każdy z następującymi właściwościami:

| Właściwość           | Opis |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Sygnatura czasowa, która wskazuje, kiedy się stało wynik komunikatu (na przykład Centrum odebrał komunikat o opinie lub wygasła w oryginalnej wiadomości) |
| OriginalMessageId  | *MessageId* komunikatu chmura urządzenie dotyczy tych informacji opinii |
| StatusCode         | Wymagany ciąg używany w komunikatów zwrotnych, które są generowane przez usługę IoT hub: <br/> *Powodzenie* <br/> *Wygasła* <br/> *DeliveryCountExceeded* <br/> *Odrzucone* <br/> *Wyczyszczone* |
| Opis        | Wartości dla ciągów *StatusCode* |
| DeviceId           | *DeviceId* urządzenia docelowego komunikatu chmura urządzenie, do którego odnosi się ten element opinii |
| DeviceGenerationId | *DeviceGenerationId* urządzenia docelowego komunikatu chmura urządzenie, do którego odnosi się ten element opinii |

Dla komunikatu chmury do urządzenia do skorelowania swoją opinię przy użyciu oryginalnego komunikatu, należy określić usługę *MessageId*.

Treść komunikatu opinii pokazano w poniższym kodzie:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Opcje konfiguracji chmury do urządzenia

Każde Centrum IoT hub udostępnia następujące opcje konfiguracji dla komunikatów z chmury do urządzenia:

| Właściwość                  | Opis | Zakres i domyślne |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Domyślny czas wygaśnięcia dla komunikatów z chmury do urządzeń | Interwał ISO_8601 maksymalnie dwa dni (minimalna 1 minuta); Wartość domyślna: 1 godzina |
| maxDeliveryCount          | Maksymalna liczba dostarczonych komunikatów kolejek chmury do urządzenia na urządzenie | 1 do 100; Wartość domyślna: 10 |
| feedback.ttlAsIso8601     | Przechowywanie komunikatów zwrotnych powiązane z usługi | Interwał ISO_8601 maksymalnie dwa dni (minimalna 1 minuta); Wartość domyślna: 1 godzina |
| feedback.maxDeliveryCount | Maksymalna liczba dostarczonych komunikatów kolejki opinii | 1 do 100; Wartość domyślna: 100 |

Aby uzyskać więcej informacji na temat ustawiania tych opcji konfiguracji, zobacz [centra IoT tworzenie](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące zestawów SDK, które służą do odbierania komunikatów z chmury do urządzeń, zobacz [Azure IoT SDKs](iot-hub-devguide-sdks.md).

Aby wypróbować odbieranie komunikatów z chmury do urządzeń, zobacz [wysyłania z chmury do urządzenia](iot-hub-csharp-csharp-c2d.md) samouczka.
