---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050392"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia, takie jak metadane, konfiguracje i warunki. Centrum IoT hub utrzymuje bliźniaczej reprezentacji urządzenia dla każdego urządzenia, które łączy się z nim.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Użyj bliźniąt urządzeń, aby:

* Przechowuj metadane urządzenia z zaplecza rozwiązania.

* Raport bieżących informacji o stanie, takich jak dostępne możliwości i warunki, na przykład użytej metody łączności, z aplikacji urządzenia.

* Synchronizuj stan długotrwałych przepływów pracy, takich jak aktualizacje oprogramowania układowego i konfiguracji, między aplikacją urządzenia a aplikacją zaplecza.

* Kwerenda metadanych urządzenia, konfiguracji lub stanu.

Bliźniacze reprezentacje urządzeń są przeznaczone do synchronizacji i wykonywania zapytań o konfiguracje i warunki urządzenia. Więcej informacji o tym, kiedy używać bliźniąt urządzeń można znaleźć w [opis bliźniacze urządzenia](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Bliźniacze dane bliźniacze urządzeń są przechowywane w centrum IoT hub i zawierają następujące elementy:

* **Tagi**. Metadane urządzenia dostępne tylko przez zaplecze rozwiązania.

* **Żądane właściwości**. Obiekty JSON modyfikowalne przez zaplecze rozwiązania i obserwowalne przez aplikację urządzenia.

* **Zgłoszone właściwości**. Obiekty JSON modyfikowalne przez aplikację urządzenia i czytelne przez zaplecze rozwiązania.

Znaczniki i właściwości nie mogą zawierać tablic, ale obiekty mogą być zagnieżdżone.

Na poniższej ilustracji przedstawiono organizację bliźniaczej reprezentacji urządzenia:

![Obraz bliźniaczej reprezentacji urządzenia z funkcją](./media/iot-hub-selector-twin-get-started/twin.png)

Ponadto zaplecza rozwiązania można zapytanie bliźniacze urządzenia na podstawie wszystkich powyższych danych.
Aby uzyskać więcej informacji na temat bliźniaczek urządzeń, zobacz [Opis bliźniacze urządzenia](../articles/iot-hub/iot-hub-devguide-device-twins.md). Aby uzyskać więcej informacji na temat wykonywania zapytań, zobacz [Język kwerend Centrum IoT](../articles/iot-hub/iot-hub-devguide-query-language.md).


Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację zaplecza, która dodaje tagi do bliźniaczej reprezentacji urządzenia i symulowaną aplikację urządzenia, która zgłasza swój kanał łączności jako zgłoszoną właściwość na bliźniaczej reprezentacji urządzenia.

* Wysyłaj zapytania do urządzeń z aplikacji zaplecza przy użyciu filtrów na wcześniej utworzonych tagach i właściwościach.
