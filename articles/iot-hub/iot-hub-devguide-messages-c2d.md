---
title: Zrozumienie wiadomości chmury do urządzenia Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera - sposobu korzystania z chmury do urządzenia wiadomości z Centrum IoT. Zawiera informacje na temat cykl życia komunikatów i opcji konfiguracji.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2018
ms.author: dobett
ms.openlocfilehash: 670cf45a48ca4b72576cedddd4678c0d569401cd
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Wysyłanie wiadomości chmury do urządzenia z Centrum IoT

Aby wysyłać powiadomienia jednokierunkowe aplikacji urządzenia z Twojej zaplecza rozwiązania, wysyłać chmury do urządzenia z Centrum IoT na urządzeniu. Omówienie innych opcji chmury do urządzeń obsługiwanych przez Centrum IoT można znaleźć [wskazówki dotyczące komunikacji chmury do urządzenia][lnk-c2d-guidance].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Wysyłanie wiadomości chmury do urządzenia za pośrednictwem punktu końcowego usługi połączonej (**/wiadomości/devicebound**). Urządzenie otrzyma wiadomości za pośrednictwem punktu końcowego specyficzne dla urządzenia (**/devices/ {deviceId} / wiadomości/devicebound**).

Aby skierować każdy komunikat chmury do urządzenia, na jednym urządzeniu, ustawia Centrum IoT **do** właściwości **/devices/ {deviceId} / wiadomości/devicebound**.

Każda kolejka urządzenia zawiera maksymalnie 50 wiadomości chmury do urządzenia. Próbuje wysłać komunikaty do tego samego urządzenia powoduje błąd.

## <a name="the-cloud-to-device-message-lifecycle"></a>Cykl życia komunikatów chmury do urządzenia

Aby zagwarantować dostarczanie komunikatów w najmniej jednokrotne, Centrum IoT utrzymuje chmury do urządzenia komunikatów w kolejkach na urządzenie. Urządzenia musi jawnie potwierdzić *zakończenia* dla Centrum IoT je usunąć z kolejki. Ta metoda gwarantuje odporność na błędy urządzenia i łączności.

Poniższy diagram przedstawia wykres stan cyklu życia komunikatu chmura urządzenie w Centrum IoT.

![Cykl życia komunikatów chmury do urządzenia][img-lifecycle]

Gdy usługa Centrum IoT wysyła wiadomość do urządzenia, Usługa ustawia stan wiadomości **umieszczonych w kolejce**. Gdy urządzenie będzie chciał *odbierania* komunikat Centrum IoT *blokad* wiadomości (przez ustawienie stanu **niewidoczne**), co pozwala na urządzeniu, aby rozpocząć odbieranie inne komunikaty inne wątki. Po zakończeniu przetwarzania komunikatów wątku urządzenia powiadomi Centrum IoT przez *Kończenie* wiadomości. Centrum IoT ustawia stan do **Ukończono**.

Urządzenia można także wybrać opcję:

* *Odrzuć* wiadomości, co powoduje, że Centrum IoT ustawić ją na **martwych lettered** stanu. Urządzeń łączących się za pośrednictwem protokołu MQTT nie można odrzucić wiadomości chmury do urządzenia.
* *Porzuć* wiadomości, co powoduje, że Centrum IoT umieścić wiadomości w kolejce, ze stanem ustawioną **umieszczonych w kolejce**. Urządzeń łączących się za pośrednictwem protokołu MQTT nie można porzucić wiadomości chmury do urządzenia.

Wątek może się nie powieść przetworzyć komunikatu bez powiadamiania Centrum IoT. W takim przypadku wiadomości automatycznie przejście od **niewidoczne** stanu do **umieszczonych w kolejce** stanu po *limitu czasu widoczności (lub blokowania)*. Wartość domyślna tego limitu czasu jest jedna minuta.

**Maksymalna liczba dostarczania** właściwość Centrum IoT określa maksymalną liczbę razy komunikat może przejść między **umieszczonych w kolejce** i **niewidoczne** stanów. Po liczby przejść, Centrum IoT ustawia stan wiadomości **martwych lettered**. Podobnie, ustawia stan wiadomości w Centrum IoT **martwych lettered** po godzinie wygaśnięcia (zobacz [czas wygaśnięcia][lnk-ttl]).

[Sposób wysyłania wiadomości chmury do urządzenia z Centrum IoT] [ lnk-c2d-tutorial] pokazano, jak wysyłać chmury do urządzenia z chmury i ich odbierania na urządzeniu.

Zazwyczaj urządzenia wypełnia komunikat chmury do urządzenia, gdy wiadomości nie wpływa na logiki aplikacji. Na przykład operacji wykonywane po urządzenia utrwalił komunikat zawartości lokalnie lub został pomyślnie. Komunikat może również zawierać informacje przejściowy, w których utraty nie może mieć wpływ na funkcjonalność aplikacji. Czasami dla długotrwałych zadań, można:

* Ukończ komunikatu chmura urządzenie po trwałym opis zadania w magazynie lokalnym.
* Powiadom zaplecza rozwiązania z jednego lub więcej komunikatów urządzenia do chmury na różnych etapach postęp zadania.

## <a name="message-expiration-time-to-live"></a>Wygaśnięcia wiadomości (czas wygaśnięcia)

Każdy komunikat chmury do urządzenia ma czasu wygaśnięcia. Ten czas jest ustawiony przez jeden z:

* **ExpiryTimeUtc** właściwości w usłudze.
* Centrum IoT przy użyciu domyślnego *czas wygaśnięcia* określony jako właściwość Centrum IoT.

Zobacz [opcje konfiguracji chmury do urządzenia][lnk-c2d-configuration].

Typowym sposobem wykorzystać wygaśnięcia wiadomości i uniknąć wysyłanie komunikatów do odłączonego urządzenia jest skonfigurowanie krótki czas wygaśnięcia wartości. Takie podejście osiąga ten sam rezultat jako obsługi stanu połączenia urządzenia, będąc bardziej wydajne. W przypadku żądania potwierdzeń wiadomości, Centrum IoT powiadamia użytkownika, które urządzenia są:

* Może odbierać wiadomości.
* Nie są w trybie online lub nie powiodło się.

## <a name="message-feedback"></a>Komunikat opinii

Podczas wysyłania komunikatu chmura urządzenie, usługa może wysłać żądanie dostarczania wiadomości informacji dotyczących stanu końcowego tej wiadomości.

| Właściwość ACK. | Zachowanie |
| ------------ | -------- |
| **Dodatnią** | Jeśli osiągnie komunikatu chmura urządzenie **Ukończono** stanu Centrum IoT generuje komunikat opinii. |
| **negative** | Jeśli osiągnie komunikatu chmura urządzenie **martwych lettered** stanu Centrum IoT generuje komunikat opinii. |
| **full**     | Centrum IoT generuje komunikat opinii w każdym przypadku. |

Jeśli **potwierdzenia** jest **pełne**i nie zostanie wyświetlony komunikat opinii, oznacza to, że opinia wygasł. Usługa nie może znać, co się stało z oryginalnej wiadomości. W praktyce usługi powinien upewnić, że opinii może przetworzyć przed jej wygaśnięciem. Czas wygaśnięcia maksymalną dwa dni, które pozostawia czas pobierania usługi działa ponownie, jeśli wystąpi błąd.

Zgodnie z objaśnieniem w [punkty końcowe][lnk-endpoints], Centrum IoT zapewnia informacje zwrotne przez punkt końcowy usługi połączonej (**/messages/servicebound/feedback**) jako wiadomości. Semantyka do odbierania opinii są takie same, jak w przypadku wiadomości chmury do urządzenia. Jeśli to możliwe, opinie komunikat jest przetwarzany wsadowo w pojedynczym komunikacie o następującym formacie:

| Właściwość     | Opis |
| ------------ | ----------- |
| EnqueuedTime | Sygnatura czasowa wskazująca otrzymania wiadomości opinii przez koncentratora. |
| Nazwa użytkownika       | `{iot hub name}` |
| Typ zawartości  | `application/vnd.microsoft.iothub.feedback.json` |

Treść jest serializacji JSON tablicą rekordów, każdy z następującymi właściwościami:

| Właściwość           | Opis |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Sygnatura czasowa wskazująca, kiedy wystąpiło wynik komunikatu. Na przykład koncentratora odebrał komunikat opinii lub wygasłe oryginalnej wiadomości. |
| OriginalMessageId  | **Identyfikator komunikatu** wiadomości chmury do urządzenia, do którego odnosi się ten opinii. |
| StatusCode         | Wymagany ciąg. Używane w opinii komunikaty generowane przez Centrum IoT. <br/> 'Success' <br/> "Wygasł" <br/> 'DeliveryCountExceeded' <br/> "Odrzucone" <br/> "Usunięte" |
| Opis        | Ciąg wartości **StatusCode**. |
| DeviceId           | **DeviceId** urządzenia docelowego komunikatu chmury do urządzenia, do którego odnosi się ten element opinii. |
| DeviceGenerationId | **DeviceGenerationId** urządzenia docelowego komunikatu chmury do urządzenia, do którego odnosi się ten element opinii. |

Usługa musi określać **MessageId** wiadomości chmury do urządzenia mógł skorelowania jego opinii z oryginalnej wiadomości.

W poniższym przykładzie przedstawiono treści wiadomości opinii.

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

Każdy Centrum IoT udostępnia następujące opcje konfiguracji dla obsługi wiadomości chmury do urządzenia:

| Właściwość                  | Opis | Zakres i domyślne |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Domyślny czas wygaśnięcia wiadomości chmury do urządzenia. | Interwał ISO_8601 maksymalnie 2D (minimalna 1 minuta). Wartość domyślna: 1 godzina. |
| maxDeliveryCount          | Liczba maksymalna dostarczania dla kolejek chmury do urządzenia na urządzeniu. | 1 do 100. Domyślny: 10. |
| feedback.ttlAsIso8601     | Przechowywanie komunikatów usługi powiązane z opinii. | Interwał ISO_8601 maksymalnie 2D (minimalna 1 minuta). Wartość domyślna: 1 godzina. |
| feedback.maxDeliveryCount |Liczba maksymalna dostarczania dla kolejki opinii. | 1 do 100. Domyślnie: 100. |

Aby uzyskać więcej informacji na temat ustawiania tych opcji konfiguracji, zobacz [centra IoT utworzyć][lnk-portal].

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje o zestawy SDK służy do odbierania wiadomości chmury do urządzenia, zobacz [Azure IoT SDK][lnk-sdks].

Aby wypróbować odbieranie komunikatów chmury do urządzenia, zobacz [wysyłania chmury do urządzenia] [ lnk-c2d-tutorial] samouczka.

[img-lifecycle]: ./media/iot-hub-devguide-messages-c2d/lifecycle.png

[lnk-portal]: iot-hub-create-through-portal.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-ttl]: #message-expiration-time-to-live
[lnk-c2d-configuration]: #cloud-to-device-configuration-options
[lnk-lifecycle]: #message-lifecycle
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
