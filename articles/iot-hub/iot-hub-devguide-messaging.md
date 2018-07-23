---
title: Omówienie obsługi komunikatów usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — urządzenia do chmury i obsługa komunikatów za pomocą usługi IoT Hub chmury do urządzenia. Zawiera informacje na temat formatów wiadomości i protokołów komunikacyjnych obsługiwanych.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: b0667f820145f16c75a07ebe1849e20d2de36cc7
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185513"
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>Urządzenia do chmury i obsługa komunikatów za pomocą usługi IoT Hub chmury do urządzenia

Użyj usługi IoT Hub komunikatów do komunikowania się z urządzeniami przez:

* Wysyłanie [urządzenia do chmury] [ lnk-d2c] komunikatów z urządzeń do rozwiązania zaplecza.
* Wysyłanie [chmury do urządzenia] [ lnk-c2d] wiadomości z rozwiązania zaplecza do urządzeń.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Właściwości podstawowe funkcje obsługi komunikatów usługi IoT Hub są niezawodności i trwałości wiadomości. Właściwości te umożliwiają odporność na sporadycznie po stronie urządzenia i załadować skoki w przypadku przetwarzania po stronie chmury. IoT Hub zaimplementowano *co najmniej raz* dostarczania gwarantuje komunikatów urządzenia do chmury i z chmury do urządzeń.

Aby zapoznać się z możliwościami usługi IoT Hub, zobacz [Omówienie usługi Azure IoT Hub][lnk-iot-hub-overview].

## <a name="when-to-use-iot-hub-messaging"></a>Kiedy należy używać obsługi komunikatów w Centrum IoT Hub

Na użytek komunikatów z urządzenia do chmury do wysyłania czas serii telemetrię i alerty z aplikacji urządzenia i komunikaty z chmury do urządzenia jednokierunkowe powiadomienia do aplikacji urządzenia.

* Zapoznaj się [wskazówki dotyczące komunikacji urządzenia do chmury] [ lnk-d2c-guidance] if w stanie wątpliwości między przy użyciu komunikatów z urządzenia do chmury, zgłoszone właściwości aktualizuje lub przekazywanie pliku.
* Zapoznaj się [wskazówki dotyczące komunikacji chmury do urządzenia] [ lnk-c2d-guidance] if w stanie wątpliwości między za pomocą komunikatów przesyłanych z chmury do urządzenia, żądanych właściwości lub metody bezpośrednie.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o usłudze IoT Hub [komunikatów z urządzenia do chmury][lnk-d2c].
* Dowiedz się więcej o usłudze IoT Hub [komunikatów z chmury do urządzenia][lnk-c2d].

[lnk-azure-iot]: ../iot-fundamentals/index.yml
[lnk-iot-hub-overview]: about-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md