---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: eef073f5f4d1eb39fd5ccd8dafacd7074158fa37
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667843"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). IoT Hub utrzymuje sznurki urządzenia dla każdego urządzenia, które nawiązuje z nim połączenie.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Użyj bliźniaczych reprezentacji urządzenia, aby:

* Przechowuj metadane urządzenia z zaplecza rozwiązania.

* Zgłoś bieżące informacje o stanie, takie jak dostępne możliwości i warunki (na przykład używana metoda łączności) z aplikacji urządzenia.

* Zsynchronizuj stan długotrwałych przepływów pracy (takich jak oprogramowanie układowe i aktualizacje konfiguracji) między aplikacją urządzenia i aplikacją zaplecza.

* Wykonaj zapytanie dotyczące metadanych, konfiguracji lub stanu urządzenia.

Urządzenia bliźniaczych reprezentacji są przeznaczone do synchronizacji oraz do wykonywania zapytań dotyczących konfiguracji i warunków urządzeń. Więcej informacji o tym, kiedy używać bliźniaczych reprezentacji urządzeń, można znaleźć w temacie [Omówienie urządzenia bliźniaczych reprezentacji](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Urządzenia bliźniaczych reprezentacji są przechowywane w centrum IoT Hub i obejmują:

* *Tagi*, metadane urządzenia dostępne tylko dla zaplecza rozwiązania;

* *żądane właściwości*, obiekty JSON modyfikowane przez zaplecze rozwiązania i zauważalne przez aplikację urządzenia; lub

* *raportowane właściwości*, obiekty JSON modyfikowane przez aplikację urządzenia i odczytywane przez zaplecze rozwiązania. Tagi i właściwości nie mogą zawierać tablic, ale obiekty mogą być zagnieżdżane.

![Obraz przedstawiający sznurki urządzenia z możliwością](./media/iot-hub-selector-twin-get-started/twin.png)

Zaplecze rozwiązania może dodatkowo wykonywać zapytania dotyczące bliźniaczych reprezentacji urządzenia w oparciu o wszystkie powyższe dane.
Zapoznaj się z tematem [bliźniaczych reprezentacji urządzenia](../articles/iot-hub/iot-hub-devguide-device-twins.md) , aby uzyskać więcej informacji na temat bliźniaczych reprezentacji urządzeń oraz informacje dotyczące [języka zapytań IoT Hub](../articles/iot-hub/iot-hub-devguide-query-language.md) na potrzeby wykonywania zapytań.


Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację zaplecza, która dodaje *Tagi* do sznurka urządzenia, oraz aplikację symulowanego urządzenia, która zgłasza swój kanał łączności jako raportowaną *Właściwość* na sznurze urządzeń.

* Twórz zapytania dotyczące urządzeń z aplikacji zaplecza przy użyciu filtrów dla utworzonych wcześniej tagów i właściwości.
