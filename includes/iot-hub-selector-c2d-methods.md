> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/quickstart-control-device-node.md)
> * [C#](../articles/iot-hub/quickstart-control-device-dotnet.md)
> * [Java](../articles/iot-hub/quickstart-control-device-java.md)
> * [Python](../articles/iot-hub/quickstart-control-device-python.md)

Azure IoT Hub to w pełni zarządzana usługa, która umożliwia niezawodne i bezpieczne komunikację dwukierunkową między milionami urządzeń i rozwiązania zaplecza. Poprzednich samouczków ([Rozpoczynanie pracy z usługą IoT Hub] i [wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub]) ilustrują podstawowe funkcje obsługi komunikatów usługi IoT Hub urządzenia do chmury i z chmury do urządzeń. Usługa IoT Hub daje również możliwość wywołania metod nietrwałe na urządzeniach z chmury. Metod bezpośrednich reprezentują interakcji "żądanie-odpowiedź", za pomocą urządzenia, które są podobne do wywołania protokołu HTTPS, w tym ich powodzenie lub niepowodzenie natychmiast (po określonej przez użytkownika limit czasu), w umożliwiające użytkownik wie, że stan połączenia. Artykuł [wywoływanie metody bezpośredniej o urządzeniu] [ lnk-devguide-methods] metod bezpośrednich bardziej szczegółowo opisano i zawiera wskazówki dotyczące kiedy należy używać metod bezpośrednich zamiast komunikatów z chmury do urządzeń lub żądane właściwości.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj witryny Azure portal do utworzenia Centrum IoT, a następnie tworzenie tożsamości urządzenia w usłudze IoT hub.
* Tworzenie aplikacji symulowanego urządzenia, która ma metody bezpośredniej, który można wywoływać za pomocą chmury.
* Tworzenie aplikacji konsoli, która wywołuje metody bezpośredniej w aplikacji symulowanego urządzenia za pośrednictwem usługi IoT hub.


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[Wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[Rozpoczynanie pracy z usługą IoT Hub]: ../articles/iot-hub/quickstart-send-telemetry-node.md