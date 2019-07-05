---
title: Co to jest wydawca OPC — Azure | Dokumentacja firmy Microsoft
description: Omówienie wydawca OPC
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 99b3f6b4495ae5981322c90fce251b9b0a18bb5d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450912"
---
# <a name="what-is-opc-publisher"></a>Wydawca OPC co to jest?

Wydawca OPC są implementację referencyjną, który demonstruje sposób:

- Podłącz do istniejących serwerów OPC UA.
- Publikowanie danych telemetrycznych kodowany w formacie JSON z serwerów OPC UA w formacie OPC UA Pub/Sub, przy użyciu ładunek w formacie JSON, usługą Azure IoT Hub.

Można użyć dowolnego z protokołów transportowych, obsługiwanych przez zestaw SDK klienta usługi Azure IoT Hub: Protokół HTTPS, AMQP i protokołu MQTT.

Implementacja referencyjna obejmuje:

- OPC UA *klienta* do łączenia się z istniejących serwerów OPC UA, masz w sieci.
- OPC UA *serwera* na porcie 62222, używanej do zarządzania, co jest opublikowana, a także oferuje usługi IoT Hub bezpośrednie metody to samo.

Możesz pobrać [implementacja referencyjna wydawcy OPC](https://github.com/Azure/iot-edge-opc-publisher) z usługi GitHub.

Aplikacja jest implementowany przy użyciu technologii .NET Core i można uruchomić na dowolnej platformie, obsługiwana przez platformy .NET Core.

Wydawca OPC implementuje logikę ponawiania próby do nawiązywania połączeń z punktami końcowymi, które nie odpowiadają na określonej liczbie keep alive żądań. Na przykład, jeśli serwer OPC UA przestanie odpowiadać z powodu awarii zasilania.

Dla każdego distinct odstępu czasu publikowania do serwera OPC UA aplikacja tworzy oddzielną subskrypcję, nad którym są aktualizowane wszystkie węzły z tym odstępu czasu publikowania.

Wydawca OPC obsługuje partie danych wysyłanych do usługi IoT Hub, aby zmniejszyć obciążenie sieci. To przetwarzanie wsadowe wysyła pakiet do usługi IoT Hub, tylko wtedy, gdy osiągnie rozmiar skonfigurowanych pakietów.

Ta aplikacja używa stosu OPC Foundation OPC UA odwołanie jako pakiety NuGet. Zobacz [ https://opcfoundation.org/license/redistributables/1.3/ ](https://opcfoundation.org/license/redistributables/1.3/) do postanowień licencyjnych.

### <a name="next-steps"></a>Kolejne kroki

Teraz wiesz wydawca OPC są sugerowane następnym krokiem jest Dowiedz się, jak [skonfigurować wydawca OPC](howto-opc-publisher-configure.md).
