> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia (metadane, konfiguracje i warunki). Centrum IoT utrzymuje dwie urządzenia, dla każdego urządzenia, które nawiązuje z nim połączenie.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Użyj twins urządzenia do:

* Przechowywania metadanych urządzenie z Twojego zaplecza rozwiązania.
* Raport bieżące informacje o stanie, takie jak dostępne możliwości i warunki (na przykład łączności metodę) z aplikacją urządzenia.
* Synchronizuj stan długotrwałe przepływów pracy (takich jak aktualizacje oprogramowania układowego i konfiguracji), między aplikacją urządzenia i aplikacji zaplecza.
* Zapytanie z metadanych urządzeniami, konfiguracji lub stanu.

Twins urządzenia są przeznaczone do synchronizacji i do wykonywania zapytań w konfiguracji urządzeń i warunki. Więcej informacji o tym, kiedy należy używać twins urządzenia znajdują się w [zrozumieć urządzenia twins][lnk-twins].

Twins urządzeń są przechowywane w Centrum IoT i zawierają:

* *tagi*, metadane urządzenia jest dostępne tylko dla zaplecza rozwiązania;
* *żądany właściwości*, obiektów JSON można modyfikować przy użyciu rozwiązania wstecz zakończenia i według przez aplikację urządzenia, a
* *zgłoszone właściwości*, obiektów JSON można modyfikować za pomocą aplikacji urządzenia i czytelna dla zaplecza rozwiązania. Znaczniki i właściwości nie mogą zawierać tablic, ale może być zagnieżdżone obiekty.

![][img-twin]

Ponadto zaplecza rozwiązania można badać twins urządzenia na podstawie danych powyżej.
Zapoznaj się [zrozumieć twins urządzenia] [ lnk-twins] Aby uzyskać więcej informacji o urządzeniu twins oraz [język zapytań Centrum IoT] [ lnk-query] odwołania dla wykonywanie zapytania.


Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji zaplecza, który dodaje *tagi* dwie urządzeń i aplikacji symulowane urządzenie, która raportuje kanału łączności jako *podać właściwość* na dwie urządzenia.
* Wyślij zapytanie do urządzeń z zaplecza aplikacji przy użyciu filtrów na temat właściwości wcześniej utworzony i tagów.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md