---
title: ESP8266 do chmury - nawiązać Centrum IoT Azure piór HUZZAH ESP8266 | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i Adafruit piór HUZZAH ESP8266 nawiązać połączenia z Centrum IoT Azure go do przesyłania danych do platformy w chmurze platformy Azure, w tym samouczku.
author: rangv
manager: nasing
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 3431cc729550c0dd6eae8f332e2f8996cde9b02f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631482"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Adafruit piór HUZZAH ESP8266 nawiązać połączenia z Centrum IoT Azure w chmurze

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Połączenie między DHT22 ESP8266 HUZZAH piór i Centrum IoT](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>Co zrobić


Adafruit piór HUZZAH ESP8266 nawiązać połączenia z Centrum IoT utworzony. Następnie uruchom przykładową aplikację na ESP8266 do zbierania danych temperatury i wilgotności z czujnika DHT22. Ponadto użytkownik wysyła dane czujników do Centrum IoT.

> [!NOTE]
> Jeśli używasz innych tablice ESP8266 można nadal wykonaj następujące kroki, aby połączyć go z Centrum IoT. W zależności od tablicy ESP8266 używasz, może zaistnieć konieczność zmiany konfiguracji `LED_PIN`. Na przykład, jeśli używasz ESP8266 z AI Thinker, można zmienić go z `0` do `2`. Nie masz jeszcze zestawu? Pobierz go z [witryny sieci Web Azure](http://azure.com/iotstarterkits).




## <a name="what-you-learn"></a>Omawiane zagadnienia

* Tworzenie Centrum IoT i zarejestrować urządzenie ESP8266 HUZZAH piór
* Jak połączyć piór HUZZAH ESP8266 z czujnika i komputera
* Jak zbierać dane czujników, uruchamiając przykładową aplikację na ESP8266 HUZZAH piór
* Sposób wysłania danych czujnika do Centrum IoT

## <a name="what-you-need"></a>Co jest potrzebne

![elementy potrzebne do samouczka](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Aby wykonać tę operację, potrzebne następujące części z Twojej piór HUZZAH ESP8266 Starter Kit:

* Tablica ESP8266 HUZZAH piór
* Dysk USB Micro do kabla USB A typu

Należy również następujące czynności dla swojego środowiska programowania:

* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [utworzyć bezpłatne konto próbne Azure](https://azure.microsoft.com/free/) za kilka minut.
* Mac lub komputera z systemem Windows lub Ubuntu.
* Sieci bezprzewodowej dla ESP8266 HUZZAH piór do nawiązania połączenia.
* Połączenia internetowego na pobieranie narzędzia do konfiguracji.
* [Rozszerzenia programu Visual Studio Code Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> Wersja Arduino IDE używane przez rozszerzenia programu Visual Studio Code dla Arduino musi być wersji 1.6.8 lub nowszej. Starszych wersjach nie działają z biblioteki AzureIoT.

Następujące elementy są opcjonalne, w przypadku, gdy nie masz czujnika. Masz również opcję użycia danych czujnika symulowane.

* Czujnik temperatury i wilgotności Adafruit DHT22
* Breadboard
* Przewodów zworek M/M


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Połącz piór HUZZAH ESP8266 z czujnika i komputera
W tej sekcji czujników nawiązać tablicy. Następnie możesz podłączyć urządzenia do tego komputera do dalszego użytku.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Czujnik temperatury i wilgotności DHT22 nawiązać połączenia z ESP8266 HUZZAH piór

Użyj przewodów breadboard i zworek do nawiązania połączenia w następujący sposób. Jeśli nie masz czujnika, Pomiń tę sekcję, ponieważ można użyć danych czujnika symulowane.

![Odwołanie do połączenia](media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)


Czujnik numery PIN można użyć następujących połączeń:


| Start (czujnik)           | Końcowy (tablica)           | Kolor kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 31F)            | 3V (przypiąć 58H)           | Czerwony kabel     |
| DANE (Pin 32F)           | Interfejs GPIO 2 (Pin 46A)       | Niebieski kabel    |
| GND (Pin 34F)            | GND (PIn 56I)          | Czarne kabel   |

Aby uzyskać więcej informacji, zobacz [Instalatora czujnik Adafruit DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) i [wyprowadzenia Adafruit piór HUZZAH Esp8266 styków](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).



Teraz ESP8266 Huzzah Twojego piór powinny być połączone z czujnika pracy.

![Uzyskuj DHT22 Huzzah piór](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Łączenie się z komputerem ESP8266 HUZZAH piór

Jak pokazano w następnym, użyj USB Micro do kabla USB A typu, aby połączyć piór HUZZAH ESP8266 do komputera.

![Łączenie się z komputerem Huzzah piór](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Dodaj uprawnienia portu szeregowego (tylko Ubuntu)


Jeśli używasz Ubuntu, upewnij się, że masz uprawnienia do działania na portu USB w piór HUZZAH ESP8266. Aby dodać uprawnienia portu szeregowego, wykonaj następujące kroki:


1. W terminalu, uruchom następujące polecenia:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Pojawia się jeden z następujących danych wyjściowych:

   * crw-rw---xxxxxxxx uucp głównego 1
   * crw-rw---xxxxxxxx inicjowania połączeń głównego 1

   W danych wyjściowych, zwróć uwagę, że `uucp` lub `dialout` jest nazwa właściciela grupy portu USB.

1. Dodaj użytkownika do grupy, uruchamiając następujące polecenie:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` jest to nazwa właściciela grupy uzyskanego w poprzednim kroku. `<username>` to nazwa użytkownika Ubuntu.

1. Wylogować się z Ubuntu, a następnie zaloguj ponownie zmiany są wyświetlane.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Zbieraj dane czujników i wysyłania go do Centrum IoT

W tej sekcji Wdróż i uruchom przykładową aplikację na ESP8266 HUZZAH piór. Przykładowa aplikacja miganie LED na ESP8266 HUZZAH piór i wysyła temperatury i wilgotności dane zbierane z czujnika DHT22 do Centrum IoT.

### <a name="get-the-sample-application-from-github"></a>Pobierz przykładową aplikację z usługi GitHub

Przykładowa aplikacja znajduje się w witrynie GitHub. Klonowanie repozytorium przykładowej zawierający przykładową aplikację z usługi GitHub. Klonowanie repozytorium przykładowej, wykonaj następujące kroki:

1. Otwórz wiersz polecenia lub okno terminalu.
1. Przejdź do folderu, w którym ma przykładowej aplikacji, które mają być przechowywane.
1. Uruchom następujące polecenie:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Zainstaluj pakiet dla piór HUZZAH ESP8266 w programie Visual Studio Code:

1. Otwórz folder, w którym przechowywana jest przykładowej aplikacji.
1. Otwórz plik app.ino w folderze aplikacji w programie Visual Studio Code.

   ![Otwórz w programie Visual Studio Code przykładowej aplikacji](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

1. W programie Visual Studio Code wprowadź `F1`.
1. Typ **Arduino** i wybierz **Arduino: Menedżer tablicy**.
1. W **Menedżera tablicy Arduino** , kliknij pozycję **dodatkowych adresów URL**.

   ![Menedżer tablicy Arduino kodu VS](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

1. W **ustawienia użytkownika** okna, skopiuj i wklej poniższy na końcu pliku

   `"arduino.additionalUrls": "http://arduino.esp8266.com/stable/package_esp8266com_index.json"`

   ![Skonfiguruj adres URL pakietu Arduino w kodzie VS](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

1. Zapisz plik i Zamknij **ustawienia użytkownika** kartę.
1. Kliknij przycisk **Odśwież indeksów pakietu**. Po zakończeniu odświeżania, wyszukaj **esp8266**.
1. Kliknij przycisk **zainstalować** przycisk esp8266.

   Tablice Menedżera wskazuje, że ESP8266 przy użyciu wersji 2.2.0 lub nowszej jest zainstalowane.

   ![Pakiet esp8266 jest zainstalowany](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

1. Wprowadź `F1`, wpisz **Arduino** i wybierz **Arduino: Config tablicy**.
1. Kliknij pole **wybrane tablicy:** i typ **esp8266**, a następnie wybierz pozycję **Adafruit HUZZAH ESP8266 (esp8266)**.

   ![Wybierz esp8266 tablicy](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Zainstaluj wymagane biblioteki

1. W programie Visual Studio Code wprowadź `F1`, wpisz **Arduino** i wybierz **Arduino: Library Manager**.
1. Wyszukiwanie w bibliotece następujące nazwy jeden po drugim. Dla każdej biblioteki można znaleźć, kliknij przycisk **zainstalować**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nie masz rzeczywistych czujnik DHT22?

Przykładową aplikację można symulować danych temperatury i wilgotności, w przypadku, gdy nie ma rzeczywistego czujnik DHT22. Aby skonfigurować przykładowej aplikacji za pomocą symulowane danych, wykonaj następujące kroki:

1. Otwórz `config.h` w pliku `app` folderu.
1. Znajdź następujący wiersz kodu i zmień wartość `false` do `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Skonfiguruj przykładową aplikację za pomocą symulowane danych](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

1. Zapisz plik.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Wdrażanie przykładowej aplikacji do ESP8266 HUZZAH piór

1. W programie Visual Studio Code kliknij **<Select Serial Port>** stan pasek, a następnie kliknij przycisk portu szeregowego ESP8266 HUZZAH piór.
1. Wprowadź `F1`, wpisz **Arduino** i wybierz **Arduino: Przekaż** do tworzenia i wdrażania przykładowej aplikacji do ESP8266 HUZZAH piór.

### <a name="enter-your-credentials"></a>Wprowadź swoje poświadczenia

Po pomyślnym ukończeniu przekazywania, wykonaj następujące kroki, aby wprowadzić poświadczenia:

1. Otwórz Arduino IDE, kliknij pozycję **narzędzia** > **Serial Monitor**.
1. W oknie Monitora serial zauważyć dwie listy rozwijanej w prawym dolnym rogu.
1. Wybierz **nie zakończenia wiersza** po lewej stronie listy rozwijanej.
1. Wybierz **transmisji 115200** na liście po prawej listy rozwijanej.
1. W odpowiednim polu znajduje się w górnej części okna monitora szeregowych, wprowadź następujące informacje, jeśli zostanie wyświetlona prośba o Podaj je, a następnie kliknij przycisk **wysyłania**.
   * Wi-Fi SSID
   * Hasło sieci Wi-Fi
   * Ciąg połączenia urządzenia

> [!Note]
> Informacje poświadczeń są przechowywane w pamięci EEPROM ESP8266 HUZZAH piór. Po kliknięciu przycisku resetowania na tablicy ESP8266 HUZZAH piór przykładowej aplikacji zapyta, czy chcesz wymazać informacje. Wprowadź `Y` informacji wymazane. Zostanie wyświetlona prośba o podanie informacji po raz drugi.

### <a name="verify-the-sample-application-is-running-successfully"></a>Sprawdź, czy Przykładowa aplikacja działa prawidłowo

Jeśli widzisz następujące dane wyjściowe z okna monitora szeregowe i migający LED na ESP8266 HUZZAH piór Przykładowa aplikacja działa prawidłowo.

![Ostateczne dane wyjściowe w Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie połączony ESP8266 HUZZAH piór Centrum IoT i wysyłane dane czujników przechwyconych do Centrum IoT. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

