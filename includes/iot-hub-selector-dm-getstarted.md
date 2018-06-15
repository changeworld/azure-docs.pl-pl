> [!div class="op_single_selector"]
> * [Urządzenie: Node.js, usługa: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [Urządzenie: C# usługi: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Urządzeń: Usługa języka Java: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Urządzeń: Usługa języka Python: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Zaplecza aplikacji można użyć Centrum IoT Azure w nim elementów podstawowych, takich jak [dwie urządzenia] [ lnk-devtwin] i [bezpośrednie metody][lnk-c2dmethod], aby zdalnie uruchomić i monitorować urządzenia Akcje zarządzania na urządzeniach. Ten samouczek pokazuje, jak aplikacji zaplecza i aplikacji urządzenia mogą współdziałać ze sobą do inicjowania i monitorowania ponowne uruchomienie urządzenia zdalnego przy użyciu Centrum IoT.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Metoda bezpośrednia umożliwia zainicjować akcje zarządzania urządzeniami (takie jak ponowne uruchomienie komputera, resetowanie do ustawień fabrycznych i aktualizacji oprogramowania układowego) z zaplecza aplikacji w chmurze. Urządzenie jest odpowiedzialny za:

* Obsługa żądanie metody wysyłane z Centrum IoT.
* Inicjowanie działanie urządzenia na urządzeniu.
* Dostarczanie aktualizacji stanu za pośrednictwem *zgłosił właściwości* z Centrum IoT.

Wewnętrznym aplikacji w chmurze służy do uruchamiania zapytań dwie urządzenia do raport dotyczący postępu operacji zarządzania urządzeniami.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
