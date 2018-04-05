> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-direct-methods.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-direct-methods.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-direct-methods.md)

Centrum IoT Azure jest w pełni zarządzaną usługę, która zapewnia niezawodne i bezpieczną komunikację dwukierunkową między milionów urządzeń i rozwiązanie zaplecza. Samouczki poprzedniego ([Rozpoczynanie pracy z Centrum IoT] i [wysyłać chmury do urządzenia z Centrum IoT]) ilustrują podstawowych funkcji obsługi komunikatów z Centrum IoT urządzenia do chmury i chmury do urządzenia. Centrum IoT daje również możliwość wywołania metod nietrwałe na urządzeniach z chmury. Bezpośrednie metody reprezentują żądanie odpowiedź interakcji z urządzeniem podobna do wywołania protokołu HTTPS w tym pomyślnie lub niepowodzeniem natychmiast (po limitu określonego przez użytkownika) użytkownikowi należy znać stan wywołania. [Wywoływanie metody bezpośrednio na urządzeniu] [ lnk-devguide-methods] opisano metody bezpośredniego bardziej szczegółowo i zawiera wskazówki, kiedy używać bezpośrednich metody zamiast wiadomości chmury do urządzenia lub odpowiednie właściwości.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj portalu Azure do tworzenia Centrum IoT i tworzenia tożsamości urządzenia w Centrum IoT.
* Tworzenie aplikacji symulowane urządzenie, który ma metoda bezpośrednia, który może być wywoływany przez chmurę.
* Tworzenie aplikacji konsoli, która wywołuje metodę bezpośrednio w aplikacji symulowane urządzenie za pomocą Centrum IoT.


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[wysyłać chmury do urządzenia z Centrum IoT]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[Rozpoczynanie pracy z Centrum IoT]: ../articles/iot-hub/iot-hub-node-node-getstarted.md