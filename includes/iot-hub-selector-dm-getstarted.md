---
ms.openlocfilehash: 19331f35ea2fa773325ec61e728677e37767ab54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346279"
---
> [!div class="op_single_selector"]
> * [Urządzenie: Usługa środowiska node.js: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Urządzenie: C# Service: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Urządzenie: Java Service: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Urządzenie: Python Service: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Serwer zaplecza w aplikacji można używać usługi Azure IoT Hub w nim elementów podstawowych, takich jak [bliźniaczej reprezentacji urządzenia] [ lnk-devtwin] i [metody bezpośrednie][lnk-c2dmethod], aby zdalnie uruchomić i monitorować urządzenia akcje z zakresu zarządzania na urządzeniach. Ten samouczek pokazuje, jak aplikacji zaplecza i aplikacji urządzenia mogą pracować razem inicjowania i monitorowania ponownego uruchomienia urządzenia zdalnego za pomocą usługi IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]
Użyj metody bezpośredniej do zainicjowania akcji zarządzania urządzeniami (na przykład ponowne uruchomienie komputera, resetowanie do ustawień fabrycznych i aktualizacja oprogramowania układowego) z poziomu aplikacji zaplecza w chmurze. Urządzenie jest odpowiedzialny za:

* Obsługa żądanie metody wysyłane z usługi IoT Hub.
* Inicjowanie odpowiednich czynności specyficznych dla urządzenia na urządzeniu.
* Dostarczanie aktualizacji stanu za pośrednictwem *zgłaszanych właściwości* do usługi IoT Hub.

Uruchamianie zapytań bliźniaczych reprezentacji urządzeń do raportowania postępu działań zarządzania urządzeniami, można użyć aplikacji zaplecza w chmurze.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
