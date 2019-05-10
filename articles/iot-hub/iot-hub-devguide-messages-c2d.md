---
title: Omówienie obsługi komunikatów chmura urządzenie usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dla deweloperów — jak korzystać z chmury do urządzenia, obsługa komunikatów za pomocą usługi IoT Hub. Zawiera informacje na temat cykl życia komunikatów i opcje konfiguracji.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: b0c1b877a9468ce9c3b851bce62cb87c64c04260
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472739"
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Wysyłanie komunikatów z chmury do urządzeń z usługi IoT Hub

Wysyłać jednokierunkowe powiadomienia aplikacji urządzenia z zapleczem rozwiązania, wysyłanie komunikatów z chmury do urządzenia z usługi IoT hub na urządzenie. Aby uzyskać omówienie inne opcje chmury do urządzeń obsługiwanych przez usługę IoT Hub, zobacz [wskazówki dotyczące komunikacji z chmury do urządzenia](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Wysyłanie komunikatów z chmury do urządzeń za pośrednictwem punktu końcowego usługi skierowaną (**/komunikatów/devicebound**). Urządzenie odbiera komunikaty za pośrednictwem punktu końcowego specyficznych dla urządzenia (**/devices/ {deviceId} / komunikatów/devicebound**).

Pod kątem każdego komunikatu chmura urządzenie, na jednym urządzeniu, usługa IoT Hub ustawia **do** właściwości **/devices/ {deviceId} / komunikatów/devicebound**.

Każda kolejka urządzenia zawiera maksymalnie 50 wiadomości chmury do urządzenia. Próbuje wysłać więcej wiadomości do tego samego urządzenia powoduje wystąpienie błędu.

## <a name="the-cloud-to-device-message-lifecycle"></a>Cykl życia komunikatów chmura urządzenie

Aby zagwarantować dostarczanie komunikatów co najmniej jednokrotne, usługa IoT Hub utrzymuje komunikatów z chmury do urządzeń w kolejkach na urządzenie. Urządzenia musi jawnie potwierdzić *uzupełniania* usługi IoT Hub je usunąć z kolejki. Ta metoda gwarantuje odporność na łączność i awarii urządzenia.

Na poniższym diagramie przedstawiono wykres stanu cyklu życia dla komunikatu chmury do urządzenia w usłudze IoT Hub.

![Cykl życia komunikatów z chmury do urządzenia](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Gdy usługa IoT Hub wysyła wiadomość do urządzenia, Usługa ustawia stan komunikatu **umieszczonych w kolejce**. Kiedy urządzenie chce *otrzymywać* komunikat usługi IoT Hub *blokad* komunikat (ustawiając stan **niewidoczne**), co pozwala na urządzeniu, aby zacząć otrzymywać inne wątki inne komunikaty. Po zakończeniu przetwarzania komunikatów wątku urządzenia powiadamia o Centrum IoT Hub, *Kończenie* wiadomości. Usługa IoT Hub ustawia stan do **Ukończono**.

Urządzenia można również wybrać:

* *Odrzuć* wiadomości, co powoduje, że usługi IoT Hub ustawić go na **Dead lettered** stanu. Urządzenia łączące się za pośrednictwem protokołu MQTT, nie można odrzucić komunikatów z chmury do urządzeń.

* *Porzuć* wiadomości, co powoduje, że usługi IoT Hub można umieścić komunikatu w kolejce, ze stanem równa **umieszczonych w kolejce**. Urządzenia łączące się za pośrednictwem protokołu MQTT nie można porzucić komunikatów z chmury do urządzeń.

Wątek może się nie powieść przetwarzania wiadomości bez powiadamiania usługi IoT Hub. W tym przypadku wiadomości automatyczne przejście z **niewidoczne** powrót do stanu **umieszczonych w kolejce** stanu po *limitu czasu widoczności (lub blokady)*. Wartość domyślna tego limitu czasu jest jedna minuta.

**Maksymalna liczba prób dostarczenia** właściwość w Centrum IoT Hub określa maksymalną liczbę razy komunikat może przejść między **umieszczonych w kolejce** i **niewidoczne** stanów. Po tej liczbie przejścia usługi IoT Hub, ustawia stan wiadomości do **Dead lettered**. Podobnie, usługa IoT Hub ustawia stan wiadomości do **Dead lettered** po jego czas wygaśnięcia (zobacz [czas wygaśnięcia](#message-expiration-time-to-live)).

[o sposobie wysyłania komunikatów z chmury do urządzeń z usługą IoT Hub](iot-hub-csharp-csharp-c2d.md) pokazuje, jak wysyłać komunikaty z chmury do urządzenia w chmurze i odbierać je na urządzeniu.

Zazwyczaj urządzenia kończy wiadomość chmury do urządzenia, gdy utraty komunikat nie ma wpływu na logice aplikacji. Na przykład, kiedy urządzenie zawiera utrwalone lokalnie zawartości komunikatu lub został pomyślnie wykonać operacji. Komunikat może również zawierać informacje przejściowych, w której utraty nie mogło mieć wpływ na funkcjonalność aplikacji. Czasami, w przypadku długotrwałych zadań, możesz:

* Zakończ wiadomość chmury do urządzenia po przechowywanie opis zadania w magazynie lokalnym.

* Powiadom zaplecze rozwiązania z jednego lub więcej komunikatów z urządzenia do chmury na różnych etapach postęp zadania.

## <a name="message-expiration-time-to-live"></a>Wygaśnięcie komunikatu (czas wygaśnięcia)

Wszystkie komunikaty z chmury do urządzeń ma czas wygaśnięcia. Tym razem zostanie ustawiony przez jeden z:

* **ExpiryTimeUtc** właściwości w usłudze.
* Usługi IoT Hub przy użyciu domyślnego *czas wygaśnięcia* określony jako właściwość Centrum IoT Hub.

Zobacz [opcje konfiguracji chmury do urządzenia](#cloud-to-device-configuration-options).

Typowym sposobem zalet wygaśnięcie komunikatu i uniknąć wysyłanie komunikatów do odłączone urządzenia, jest ustalenie krótki czas wygaśnięcia wartości. To podejście osiąga ten sam wynik jako zachowaniu stanu połączenia urządzenia, będąc bardziej wydajne. Żądając potwierdzeń komunikatów usługi IoT Hub powiadamia użytkownika, które urządzenia są:

* Może odbierać wiadomości.
* Nie są w trybie online lub zakończyć się niepowodzeniem.

## <a name="message-feedback"></a>Komunikat o opinię

Podczas wysyłania komunikatu chmura urządzenie usługi mogą żądać dostarczanie wiadomości opinię na temat stanu końcowego tego komunikatu. Jest to realizowane przez ustawienie `iothub-ack` właściwości aplikacji w komunikacie C2D są wysyłane do jednej z następujących wartości:

| Wartość właściwości ACK. | Zachowanie |
| ------------ | -------- |
| **Brak**     | Usługa IoT Hub nie generuje komunikat opinii (zachowanie domyślne). |
| **Dodatnie** | Jeśli osiągnie komunikatu chmura urządzenie **Ukończono** stanu usługi IoT Hub generuje komunikat opinii. |
| **negative** | Jeśli osiągnie komunikatu chmura urządzenie **Dead lettered** stanu usługi IoT Hub generuje komunikat opinii. |
| **full**     | Usługa IoT Hub generuje komunikat opinii w obu przypadkach. |

Jeśli **potwierdzenia** jest **pełne**, a nie pojawi się komunikat o opinię, oznacza to, że opinia wygasł. Usługa nie wiedzieć, co się stało z oryginalnej wiadomości. W praktyce usługi należy upewnić się, że opinia może przetworzyć przed jej wygaśnięciem. Maksymalny czas wygaśnięcia jest dwa dni, a co pozostawia czas na usługę ponownie uruchomić po wystąpieniu błędu.

Jak wyjaśniono w [punktów końcowych](iot-hub-devguide-endpoints.md), IoT Hub dostarczy opinii za pośrednictwem punktu końcowego usługi skierowaną (**/messages/servicebound/feedback**) jako komunikaty. Semantyka dla otrzymująca opinie są takie same jak dla komunikatów z chmury do urządzeń. Jeśli to możliwe, opinii wiadomości jest partii w pojedynczym komunikacie o następującym formacie:

| Właściwość     | Opis |
| ------------ | ----------- |
| EnqueuedTime | Sygnatura czasowa wskazująca, kiedy odebrano komunikat opinii przez Centrum. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Treść jest serializacji JSON tablicą rekordów, każdy z następującymi właściwościami:

| Właściwość           | Opis |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Sygnatura czasowa wskazująca, kiedy się stało wynik komunikatu. Na przykład Centrum odebrał komunikat o opinie lub wygasła w oryginalnej wiadomości. |
| OriginalMessageId  | **Identyfikator komunikatu** komunikatu chmura urządzenie dotyczy tych informacji opinii. |
| StatusCode         | Wymagany ciąg. Używane w komunikatów zwrotnych generowane przez usługę IoT Hub. <br/> 'Success' <br/> "Wygasły" <br/> 'DeliveryCountExceeded' <br/> 'Rejected' <br/> "Usunięte" |
| Opis        | Wartości dla ciągów **StatusCode**. |
| DeviceId           | **DeviceId** urządzenia docelowego komunikatu chmura urządzenie, do którego odnosi się ten element opinii. |
| DeviceGenerationId | **DeviceGenerationId** urządzenia docelowego komunikatu chmura urządzenie, do którego odnosi się ten element opinii. |

Należy określić usługę **MessageId** dla komunikatu chmury do urządzenia mieć możliwość skorelowania swoją opinię przy użyciu oryginalnego komunikatu.

Poniższy przykład pokazuje treść komunikatu opinii.

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
| defaultTtlAsIso8601       | Domyślny czas wygaśnięcia dla komunikatów z chmury do urządzeń. | Interwał ISO_8601 maksymalnie 2D (minimalna 1 minuta). Domyślne: 1 godzinę. |
| maxDeliveryCount          | Maksymalna liczba dostarczonych komunikatów kolejek chmury do urządzenia na urządzenie. | 1 do 100. Domyślne: 10. |
| feedback.ttlAsIso8601     | Przechowywanie komunikatów zwrotnych powiązane z usługi. | Interwał ISO_8601 maksymalnie 2D (minimalna 1 minuta). Domyślne: 1 godzinę. |
| feedback.maxDeliveryCount |Maksymalna liczba dostarczonych komunikatów kolejki opinii. | 1 do 100. Domyślne: 100. |

Aby uzyskać więcej informacji na temat ustawiania tych opcji konfiguracji, zobacz [centra IoT tworzenie](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące zestawów SDK, można użyć do odbierania komunikatów z chmury do urządzeń, zobacz [Azure IoT SDKs](iot-hub-devguide-sdks.md).

Aby wypróbować odbieranie komunikatów z chmury do urządzeń, zobacz [wysyłania z chmury do urządzenia](iot-hub-csharp-csharp-c2d.md) samouczka.