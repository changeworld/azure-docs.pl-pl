---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69558737"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Aplikacje zaplecza można użyć platformy Azure IoT Hub prymitywów, takich jak [bliźniacze urządzenia][lnk-devtwin] i [metody bezpośrednie][lnk-c2dmethod], aby zdalnie uruchamiać i monitorować akcje zarządzania urządzeniami na urządzeniach. W tym samouczku pokazano, jak aplikacja zaplecza i aplikacja urządzenia mogą współpracować w celu zainicjowania i monitorowania ponownego uruchamiania urządzenia zdalnego przy użyciu usługi IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Użyj metody bezpośredniej, aby zainicjować akcje zarządzania urządzeniami (takie jak ponowne uruchomienie komputera, przywrócenie ustawień fabrycznych i aktualizacja oprogramowania układowego) z aplikacji zaplecza w chmurze. Urządzenie jest odpowiedzialne za:

* Obsługa żądania metody wysłanego z usługi IoT Hub.

* Inicjowanie odpowiedniej akcji specyficznej dla urządzenia na urządzeniu.

* Dostarczanie aktualizacji stanu za pośrednictwem *zgłoszonych właściwości* do Usługi IoT Hub.

Za pomocą aplikacji zaplecza w chmurze można uruchamiać zapytania bliźniaczej reprezentacji urządzeń, aby raportować postęp akcji zarządzania urządzeniami.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
