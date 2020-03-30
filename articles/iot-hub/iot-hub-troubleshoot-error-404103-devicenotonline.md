---
title: Rozwiązywanie problemów z błędem usługi Azure IoT Hub 404103 DeviceNotOnline
description: Dowiedz się, jak naprawić błąd 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960817"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

W tym artykule opisano przyczyny i rozwiązania dla błędów **DeviceNotOnline 404103.**

## <a name="symptoms"></a>Objawy

Bezpośrednia metoda do urządzenia kończy się niepowodzeniem z błędem **404103 DeviceNotOnline,** nawet jeśli urządzenie jest w trybie online. 

## <a name="cause"></a>Przyczyna

Jeśli wiesz, że urządzenie jest w trybie online i nadal występuje błąd, prawdopodobnie, ponieważ bezpośrednie wywołanie zwrotne metody nie jest zarejestrowane na urządzeniu.

## <a name="solution"></a>Rozwiązanie

Aby prawidłowo skonfigurować urządzenie do bezpośrednich wywołań zwrotnych, zobacz [Obsługa metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).