---
title: ESP8266 do chmury — łączenie Feather HUZZAH ESP8266 dla usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i połączyć urządzenie Adafruit Feather HUZZAH ESP8266 do usługi Azure IoT Hub dla niego do wysyłania danych do platformy w chmurze w ramach tego samouczka.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 00d0e3a749c4af8faacfed5f725ce16e0492c399
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409082"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Zestaw Adafruit Feather HUZZAH ESP8266 nawiązać połączenie z usługi Azure IoT Hub w chmurze

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Połączenie między DHT22 Feather HUZZAH ESP8266 i Centrum IoT Hub](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>Co należy zrobić

Zestaw Adafruit Feather HUZZAH ESP8266 nawiązać połączenia usługi IoT hub, którą tworzysz. Następnie uruchom przykładową aplikację na ESP8266, aby zbierać dane temperatury i wilgotności z czujnika DHT22. Na koniec wysyłania danych czujnika do usługi IoT hub.

> [!NOTE]
> Jeśli używasz innych tablic ESP8266 można nadal wykonaj następujące kroki, aby połączyć go z Centrum IoT hub. W zależności od tablicy ESP8266, używasz, może być konieczne ponowne skonfigurowanie `LED_PIN`. Na przykład, jeśli używasz ESP8266 z Thinker sztucznej Inteligencji, można zmienić go z `0` do `2`. Nie masz jeszcze zestawu? Pobrać je z [witryny sieci Web Azure](https://azure.com/iotstarterkits).

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć Centrum IoT hub i rejestrowanie urządzenia dla Feather HUZZAH ESP8266
* Jak połączyć Feather HUZZAH ESP8266 z czujnikiem i komputera
* Jak zbierać dane z czujników po uruchomieniu aplikacji przykładowej na Feather HUZZAH ESP8266
* Jak wysyłać danych czujnika do usługi IoT hub

## <a name="what-you-need"></a>Co jest potrzebne

![Elementy wymagane dla tego samouczka](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Aby ukończyć tę operację, potrzebne są następujące elementy w zestawie Starter Feather HUZZAH ESP8266:

* Tablica Feather HUZZAH ESP8266
* Micro USB do kabla USB typu

Wymagane są również następujące elementy dla swojego środowiska programowania:

* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [Utwórz bezpłatne konto wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w ciągu kilku minut.
* Mac lub komputerze z systemem Windows i Ubuntu.
* Sieci bezprzewodowej dla Feather HUZZAH ESP8266 nawiązać połączenie.
* Połączenie internetowe, aby pobrać to narzędzie konfiguracji.
* [Rozszerzenie programu Visual Studio Code dla Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> Wersja środowisku IDE Arduino, używane przez rozszerzenia programu Visual Studio Code dla Arduino musi być w wersji 1.6.8 lub nowszej. Wcześniejsze wersje nie działają z biblioteką AzureIoT.

Następujące elementy są opcjonalne, w przypadku, gdy nie ma czujnika. Istnieje również możliwość korzystania z danych z symulowanych czujników.

* Czujnik temperatury i wilgotności Adafruit DHT22
* Breadboard
* Przewodów jumper M/M

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Łączenie Feather HUZZAH ESP8266 z czujnikiem i komputera

W tej sekcji możesz nawiązać czujników tablicy. Następnie możesz podłączyć urządzenia do komputera w celu dalszego użycia.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Czujnik temperatury i wilgotności DHT22 nawiązać połączenie z Feather HUZZAH ESP8266

Użyj przewodów breadboard i jumper, aby nawiązać połączenie w następujący sposób. Jeśli nie masz czujnik, Pomiń tę sekcję, ponieważ zamiast tego użyj danych z symulowanych czujników.

![Odwołanie połączenia](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

Czujnik numery PIN można użyć następujących połączeń:

| Uruchom (czujnik)           | Końcowy (tablica)            | Kolor kabel   |
| -----------------------  | ---------------------- | ------------  |
| VDD (Pin 31F)            | 3V (Pin 58H)           | Czerwony kabel     |
| DANE (Pin 32F)           | Interfejs GPIO 2 (Pin 46A)       | Niebieski kabel    |
| GND (Pin 34F)            | GND (PIn 56I)          | Czarnego przewodu   |

Aby uzyskać więcej informacji, zobacz [Instalatora czujnika Adafruit DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) i [Adafruit Feather HUZZAH Esp8266 w wyprowadzenia styków](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).

Teraz Twoja Feather Huzzah ESP8266 powinny być połączone z czujnikiem pracy.

![Skupionej DHT22 Feather Huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Podłącz Feather HUZZAH ESP8266 do komputera

Jak pokazano dalej, należy użyć USB Micro celu kabla USB typu połączyć Feather HUZZAH ESP8266 do komputera.

![Podłącz Feather Huzzah do komputera](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Dodawanie uprawnień portu szeregowego (tylko Ubuntu)

Jeśli używasz systemu Ubuntu, upewnij się, że masz uprawnienia do działania na portu USB w Feather HUZZAH ESP8266. Aby dodać uprawnienia do portu szeregowego, wykonaj następujące kroki:

1. Uruchom następujące polecenia w terminalu:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Zostanie wyświetlony jeden z następujących danych wyjściowych:

   * crw-rw---xxxxxxxx uucp głównego 1
   * crw-rw---1 główny xxxxxxxx inicjowania połączeń

   W danych wyjściowych, zwróć uwagę, że `uucp` lub `dialout` to nazwa właściciela grupy portu USB.

2. Dodaj użytkownika do grupy, uruchamiając następujące polecenie:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` jest to nazwa właściciela grupy, uzyskane w poprzednim kroku. `<username>` to nazwa użytkownika systemu Ubuntu.

3. Wyloguj się z systemem Ubuntu, a następnie zaloguj się ponownie do zmiana była wyświetlana.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Zbieranie danych z czujników i wysyłać je do Centrum IoT hub

W tej sekcji, wdrażanie i uruchamianie aplikacji przykładowej na Feather HUZZAH ESP8266. Przykładowa aplikacja miga dioda LED na Feather HUZZAH ESP8266 i wysyła dane dotyczące temperatury i wilgotności, zebranych z czujników DHT22 do usługi IoT hub.

### <a name="get-the-sample-application-from-github"></a>Pobieranie przykładowej aplikacji z usługi GitHub

Przykładowa aplikacja jest hostowana w usłudze GitHub. Sklonuj repozytorium przykładowej, zawierający przykładową aplikację z usługi GitHub. Aby sklonować przykładowe repozytorium, wykonaj następujące kroki:

1. Otwórz wiersz polecenia lub okno terminalu.

2. Przejdź do folderu, w której chcesz przykładowej aplikacji, które mają być przechowywane.

3. Uruchom następujące polecenie:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   Następnie należy zainstalować pakiet Feather HUZZAH ESP8266 w programie Visual Studio Code.

4. Otwórz folder, w którym znajduje się przykładowa aplikacja.

5. Otwórz plik app.ino w folderze aplikacji w Visual Studio Code.

   ![Otwórz przykładową aplikację w programie Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. W programie Visual Studio Code wprowadź `F1`.

7. Typ **Arduino** i wybierz **Arduino: Menedżer tablicy**.

8. W **Menedżera tablicy Arduino** kliknij pozycję **dodatkowych adresów URL**.

   ![Menedżer tablicy Arduino kodu programu VS](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. W **ustawienia użytkownika** okna, skopiuj i wklej następujący kod na końcu pliku

   ```json
   "arduino.additionalUrls": "https://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```

   ![Skonfiguruj adres URL pakietu Arduino w programie VS Code](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. Zapisz plik i Zamknij **ustawienia użytkownika** kartę.

11. Kliknij przycisk **odświeżania pakietu indeksów**. Po ukończeniu odświeżania, wyszukaj **esp8266**.

12. Kliknij przycisk **zainstalować** przycisk esp8266.

    Menedżer tablice wskazuje, czy ESP8266 z wersją 2.2.0 lub nowszy jest zainstalowany.

    ![Pakiet esp8266 jest zainstalowany](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. Wprowadź `F1`, a następnie wpisz **Arduino** i wybierz **Arduino: Tablicy Config**.

14. Kliknij pole **wybrane tablicy:** i typ **esp8266**, a następnie wybierz **Adafruit HUZZAH ESP8266 (esp8266)** .

    ![Wybierz esp8266 tablicy](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Instalowanie wymaganych bibliotek

1. W programie Visual Studio Code wprowadź `F1`, a następnie wpisz **Arduino** i wybierz **Arduino: Menedżer biblioteki**.

2. Wyszukaj następujące biblioteki nazwy jedno po drugim. Dla każdej biblioteki, który można znaleźć, kliknij przycisk **zainstalować**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nie masz rzeczywistych czujników DHT22?

Przykładowa aplikacja można symulować dane temperatury i wilgotności, w przypadku, gdy nie masz rzeczywistych czujników DHT22. Aby skonfigurować przykładowej aplikacji na korzystanie z symulowanych danych, wykonaj następujące kroki:

1. Otwórz `config.h` w pliku `app` folderu.

2. Znajdź następujący wiersz kodu, a następnie zmień wartość z `false` do `true`:

   ```c
   define SIMULATED_DATA true
   ```

   ![Konfigurowanie przykładowej aplikacji na korzystanie z symulowanych danych](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. Zapisz plik.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Wdrażanie przykładowej aplikacji w celu Feather HUZZAH ESP8266

1. W programie Visual Studio Code kliknij  **\<portu szeregowego wybierz >** stanu paska, a następnie kliknij port szeregowy dla Feather HUZZAH ESP8266.

2. Wprowadź `F1`, a następnie wpisz **Arduino** i wybierz **Arduino: Przekaż** do tworzenia i wdrażania przykładowej aplikacji w celu Feather HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Wprowadź swoje poświadczenia

Po pomyślnym ukończeniu przekazywania, wykonaj następujące kroki, aby wprowadzić poświadczenia:

1. Kliknij pozycję Otwórz środowisku IDE Arduino **narzędzia** > **Serial monitora**.

2. W oknie Monitora serial Zwróć uwagę, dwie listy rozwijanej w prawym dolnym rogu.

3. Wybierz **nie rsza** na liście rozwijanej po lewej stronie.

4. Wybierz **115200 transmisji** prawo rozwijanej listy.

5. W polu wejściowym, który znajduje się w górnej części okna monitora szeregowe, wprowadź następujące informacje, jeśli zostanie wyświetlony monit, podaj je, a następnie kliknij przycisk **wysyłania**.

   * Wi-Fi SSID
   * Hasło sieci Wi-Fi
   * Parametry połączenia urządzenia

> [!Note]
> Informacje poświadczeń są przechowywane w pamięci EEPROM z Feather HUZZAH ESP8266. Jeśli klikniesz przycisk Resetuj na tablicy Feather HUZZAH ESP8266, przykładowej aplikacji pyta, jeśli chcesz wymazać dane. Wprowadź `Y` posiadanie informacji wymazane. Zostanie wyświetlony monit o podanie informacji po raz drugi.

### <a name="verify-the-sample-application-is-running-successfully"></a>Upewnij się, że przykładowa aplikacja działa prawidłowo

Jeśli zobaczysz następujące dane wyjściowe z okna monitora szeregowe i migająca dioda LED na Feather HUZZAH ESP8266 Przykładowa aplikacja działa poprawnie.

![Końcowe dane wyjściowe w środowisku IDE Arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Przeczytaj komunikaty odbierane przez Centrum

Jednym ze sposobów monitorowania komunikaty odbierane przez Centrum IoT hub na urządzeniu jest konieczne użycie narzędzi IoT platformy Azure dla programu Visual Studio Code. Aby dowiedzieć się więcej, zobacz [Użyj narzędzia IoT platformy Azure dla programu Visual Studio Code do wysyłania i odbierania komunikatów między urządzeniem i Centrum IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Więcej sposobów na przetwarzanie danych przesyłanych przez urządzenia przejdź do następnej sekcji.

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie połączona Feather HUZZAH ESP8266 z Centrum IoT i wysyłane dane przechwycone czujnika do usługi IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]