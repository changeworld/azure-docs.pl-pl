---
ms.openlocfilehash: 72ccad94301e053d8103ca949d41202e58d9f5bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780524"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). Usługa IoT Hub utrzymuje bliźniaczą reprezentację urządzenia dla każdego urządzenia, który nawiązuje z nim połączenie.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Używają bliźniaczych reprezentacji urządzeń do:

* Store metadanych urządzenia z zapleczem rozwiązania.
* Raport bieżące informacje o stanie, takie jak dostępne możliwości i warunki (na przykład łączności metodę) z aplikacji urządzenia.
* Synchronizuj stan długotrwałe przepływy pracy (takich jak aktualizacje oprogramowania układowego i konfiguracji), między aplikacją urządzenia i aplikacji zaplecza.
* Zapytanie z metadanych urządzeń, konfiguracji lub stanu.

Bliźniacze reprezentacje urządzeń są przeznaczone dla synchronizacji i wysyłanie zapytań dotyczących konfiguracji urządzenia i warunki. Więcej informacji o tym, kiedy należy używać bliźniaczych reprezentacji urządzeń można znaleźć w [opis bliźniaczych reprezentacji urządzeń](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Bliźniacze reprezentacje urządzeń są przechowywane w usłudze IoT hub i zawierać:

* *tagi*, metadane urządzenia dostępne tylko dla zaplecza rozwiązania;
* *żądane właściwości*, obiekty JSON, które można modyfikować przez rozwiązanie zapleczem a obserwowalnymi przez aplikację urządzenia; oraz
* *zgłaszane właściwości*, obiekty JSON argumentami modyfikowalnymi przez aplikację urządzenia i odczytywane w zapleczu rozwiązania. Tagów i właściwości nie mogą zawierać tablic, ale obiekty mogą być zagnieżdżone.

![Funkcję pokazywanie obrazów bliźniaczej reprezentacji urządzenia](./media/iot-hub-selector-twin-get-started/twin.png)

Ponadto zaplecze rozwiązania może zapytania bliźniaczych reprezentacji urządzeń na podstawie wszystkich powyższych danych.
Zapoznaj się [opis bliźniaczych reprezentacji urządzeń](../articles/iot-hub/iot-hub-devguide-device-twins.md) Aby uzyskać więcej informacji dotyczących bliźniaczych reprezentacji urządzeń i [język zapytań usługi IoT Hub](../articles/iot-hub/iot-hub-devguide-query-language.md) odwołanie do wykonywania zapytań.


Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji zaplecza, która dodaje *tagi* bliźniaczej reprezentacji urządzenia i aplikacji symulowanego urządzenia, który zgłasza kanału łączności jako *zgłaszane właściwości* w bliźniaczej reprezentacji urządzenia.
* Wyślij zapytanie do urządzeń z poziomu aplikacji zaplecza przy użyciu filtrów na temat tagów i właściwości utworzone wcześniej.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md