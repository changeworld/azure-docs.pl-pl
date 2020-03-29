---
title: Opis obsługi wiadomości usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — wiadomości między urządzeniami a chmurą i z chmury do urządzenia za pomocą usługi IoT Hub. Zawiera informacje o formatach wiadomości i obsługiwanych protokołach komunikacyjnych.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60626251"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Wysyłanie komunikatów między urządzeniami do chmury i chmury do urządzenia za pomocą usługi IoT Hub

Centrum IoT umożliwia dwukierunkową komunikację z urządzeniami. Komunikacja z urządzeniami za pomocą usługi IoT Hub umożliwia komunikację z urządzeniami, wysyłając wiadomości z urządzeń do zaplecza rozwiązań i wysyłając polecenia z rozwiązań IoT zaplecza do urządzeń. Dowiedz się więcej o [formacie wiadomości Centrum IoT](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Wysyłanie komunikatów z urządzenia do chmury do centrum IoT Hub

Usługa IoT Hub ma wbudowany punkt końcowy usługi, który może być używany przez usługi zaplecza do odczytywania komunikatów telemetrycznych z urządzeń. Ten punkt końcowy jest zgodny z [centrum zdarzeń](https://docs.microsoft.com/azure/event-hubs/) i można użyć standardowych pakietów SDK usługi IoT Hub do [odczytu z tego wbudowanego punktu końcowego.](iot-hub-devguide-messages-read-builtin.md)

Usługa IoT Hub obsługuje również [niestandardowe punkty końcowe,](iot-hub-devguide-endpoints.md#custom-endpoints) które mogą być definiowane przez użytkowników w celu wysyłania danych i zdarzeń telemetrycznych urządzenia do usług platformy Azure przy użyciu [routingu wiadomości.](iot-hub-devguide-messages-d2c.md)

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Wysyłanie komunikatów z chmury do urządzenia z Usługi IoT Hub

Możesz wysyłać [komunikaty z chmury do urządzenia](iot-hub-devguide-messages-c2d.md) z zaplecza rozwiązania do urządzeń.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Podstawowe właściwości funkcji obsługi wiadomości usługi IoT Hub to niezawodność i trwałość wiadomości. Te właściwości umożliwiają odporność na sporadyczne łączności po stronie urządzenia i załadować skoki w przetwarzaniu zdarzeń po stronie chmury. Usługa IoT Hub implementuje *co najmniej raz* gwarancje dostarczania zarówno dla komunikacji między urządzeniami do chmury, jak i z chmury do urządzenia.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Wybieranie odpowiedniego typu wiadomości usługi IoT Hub

Użyj komunikatów z urządzenia do chmury do wysyłania danych telemetrycznych szeregów czasowych i alertów z aplikacji urządzenia oraz komunikatów z chmury do urządzenia dla jednokierunkowych powiadomień do aplikacji urządzenia.

* Zapoznaj się ze [wskazówkami dotyczącymi komunikacji między urządzeniami a chmurą,](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) aby wybrać między komunikatami między urządzeniami a chmurami, zgłoszonymi właściwościami lub przekazywaniem plików.

* Zapoznaj się ze [wskazówkami dotyczącymi komunikacji z chmury do urządzenia,](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) aby wybrać między komunikatami z chmury do urządzenia, żądanymi właściwościami lub metodami bezpośrednimi.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [routingu wiadomości](iot-hub-devguide-messages-d2c.md)w centrum IoT .

* Dowiedz się więcej o [wiadomościach z chmury do urządzenia](iot-hub-devguide-messages-c2d.md)usługi IoT Hub.