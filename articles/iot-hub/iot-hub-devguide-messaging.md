---
title: Zrozumienie Centrum IoT Azure messaging | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — urządzenia do chmury i wiadomości z Centrum IoT chmury do urządzenia. Zawiera informacje na temat formaty wiadomości i protokołów komunikacyjnych obsługiwanych.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 451a8226bbc52727dad562a4be352e352925bd0b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632710"
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>Urządzenia do chmury i wiadomości z Centrum IoT chmury do urządzenia

Użyj Centrum IoT wiadomości do komunikowania się z urządzeniami przez:

* Wysyłanie [urządzenia do chmury] [ lnk-d2c] zaplecza wiadomości z urządzeń do rozwiązania.
* Wysyłanie [chmury do urządzenia] [ lnk-c2d] wiadomości z rozwiązania zaplecza na urządzeniach.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Właściwości podstawowe Centrum IoT z obsługą wiadomości są niezawodność i trwałość wiadomości. Te właściwości Włącz odporności na niestabilne połączenie po stronie urządzenia i załadować wzrostów w przypadku przetwarzania po stronie chmury. Centrum IoT implementuje *co najmniej raz* gwarantuje dostarczania dla wiadomości zarówno urządzenia do chmury, jak i chmury do urządzenia.

Aby obejrzeć wprowadzenie do funkcji Centrum IoT, zobacz [Omówienie usługi Azure IoT Hub][lnk-iot-hub-overview].

## <a name="when-to-use-iot-hub-messaging"></a>Kiedy należy używać Centrum IoT obsługi wiadomości

Jednokierunkowe powiadomień do aplikacji urządzenia, należy użyć urządzenia do chmury wiadomości do wysyłania telemetrii serie czasu i alertów z aplikacją urządzenia i wiadomości chmury do urządzenia.

* Zapoznaj się [wskazówki komunikację urządzenia do chmury] [ lnk-d2c-guidance] if wątpliwe między przy użyciu wiadomości urządzenia do chmury, zgłoszone właściwości lub przekazywania pliku.
* Zapoznaj się [wskazówki dotyczące komunikacji chmury do urządzenia] [ lnk-c2d-guidance] if wątpliwe między przy użyciu wiadomości chmury do urządzenia, odpowiednie właściwości lub metody bezpośredniego.

## <a name="next-steps"></a>Kolejne kroki

* Więcej informacji na temat Centrum IoT [urządzenia do chmury do obsługi komunikatów][lnk-d2c].
* Więcej informacji na temat Centrum IoT [wiadomości chmury do urządzenia][lnk-c2d].

[lnk-azure-iot]: ../iot-fundamentals/index.yml
[lnk-iot-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md