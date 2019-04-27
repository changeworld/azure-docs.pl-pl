---
title: Omówienie obsługi komunikatów usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — urządzenia do chmury i obsługa komunikatów za pomocą usługi IoT Hub chmury do urządzenia. Zawiera informacje na temat formatów wiadomości i protokołów komunikacyjnych obsługiwanych.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626251"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Wysyłanie komunikatów z urządzenia do chmury i z chmury do urządzeń z usługą IoT Hub

Usługa IoT Hub umożliwia dwukierunkowej komunikacji z urządzeniami. Użycie usługi IoT Hub komunikatów do komunikowania się z urządzeniami przez wysyłanie komunikatów z urządzeń do rozwiązania ponownie zakończyć i wysyłania poleceń z IoT rozwiązania zaplecza do urządzeń z systemem. Dowiedz się więcej o [format komunikatów usługi IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Wysyłanie komunikatów z urządzenia do chmury do Centrum IoT Hub

Usługa IoT Hub ma wbudowanej usługi punktu końcowego, który może służyć przez usługi zaplecza do odczytywania komunikatów telemetrycznych z urządzeń. Ten punkt końcowy jest zgodny z [usługi Event Hubs](https://docs.microsoft.com/azure/event-hubs/) można użyć standardowego IoT Hub SDKs do [odczytywać ten wbudowany punkt końcowy](iot-hub-devguide-messages-read-builtin.md).

Usługa IoT Hub obsługuje również [niestandardowe punkty końcowe](iot-hub-devguide-endpoints.md#custom-endpoints) , można zdefiniować przez użytkowników do wysyłania danych telemetrycznych z urządzeń i zdarzeń do usług platformy Azure przy użyciu [routing komunikatów](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Wysyłanie komunikatów z chmury do urządzeń z usługi IoT Hub

Możesz wysłać [chmury do urządzenia](iot-hub-devguide-messages-c2d.md) wiadomości z rozwiązania zaplecza do urządzeń.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Właściwości podstawowe funkcje obsługi komunikatów usługi IoT Hub są niezawodności i trwałości wiadomości. Właściwości te umożliwiają odporność na sporadycznie po stronie urządzenia i załadować skoki w przypadku przetwarzania po stronie chmury. IoT Hub zaimplementowano *co najmniej raz* dostarczania gwarantuje komunikatów urządzenia do chmury i z chmury do urządzeń.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Wybierania właściwego typu komunikatów w Centrum IoT Hub

Na użytek komunikatów z urządzenia do chmury do wysyłania czas serii telemetrię i alerty z aplikacji urządzenia i komunikaty z chmury do urządzenia jednokierunkowe powiadomienia do aplikacji urządzenia.

* Zapoznaj się [wskazówki dotyczące komunikacji urządzenia do chmury](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) wybrać komunikatów z urządzenia do chmury, zgłaszanych właściwości lub przekazywanie pliku.

* Zapoznaj się [wskazówki dotyczące komunikacji chmury do urządzenia](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) dokonać wyboru między komunikaty z chmury do urządzenia, żądanych właściwości i metod bezpośrednich.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o usłudze IoT Hub [routing komunikatów](iot-hub-devguide-messages-d2c.md).

* Dowiedz się więcej o usłudze IoT Hub [komunikatów z chmury do urządzenia](iot-hub-devguide-messages-c2d.md).