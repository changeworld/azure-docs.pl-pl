---
title: M0 w chmurze — łączenie Feather M0 WiFi do usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i połączyć Adafruit Feather M0 WiFi do usługi Azure IoT Hub, wysyłać dane do platformy w chmurze w ramach tego samouczka.
author: rangv
manager: nasing
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 46763cbc53f70e25810a9849b71429a315a6fb7d
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185367"
---
# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Zestaw Adafruit Feather M0 WiFi nawiązać połączenie z usługi Azure IoT Hub w chmurze
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Połączenie między BME280, Feather M0 WiFi i Centrum IoT Hub](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

W tym samouczku rozpoczniesz od nauki podstawy pracy z urządzeniem Arduino tablicy. Następnie dowiesz się, jak bezproblemowe łączenie urządzeń do chmury przy użyciu [usługi Azure IoT Hub](about-iot-hub.md).

## <a name="what-you-do"></a>Co należy zrobić

Zestaw Adafruit Feather M0 WiFi nawiązać połączenia usługi IoT hub, którą tworzysz. Następnie uruchom przykładową aplikację na M0 WiFi, aby zbierać dane temperatury i wilgotności z BME280. Na koniec wysyłania danych czujnika do usługi IoT hub.


## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć Centrum IoT hub i rejestrowanie urządzenia dla Feather M0 WiFi
* Jak połączyć Feather M0 WiFi z czujnikiem i komputera
* Jak zbierać dane z czujników po uruchomieniu aplikacji przykładowej na Feather M0 WiFi
* Jak wysyłać danych czujnika do usługi IoT hub

## <a name="what-you-need"></a>Co jest potrzebne

![Elementy wymagane dla tego samouczka](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

Aby wykonać tę operację, potrzebne są następujące elementy z zestawu SDK Starter Feather M0 sieci Wi-Fi:

* Tablica Feather M0 WiFi
* Micro USB do kabla USB typu

Wymagane są również następujące elementy dla swojego środowiska programowania:

* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [Utwórz bezpłatne konto wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w ciągu kilku minut.
* Mac lub komputerze z systemem Windows i Ubuntu.
* Sieci bezprzewodowej Feather M0 WiFi nawiązać połączenie.
* Połączenie internetowe, aby pobrać to narzędzie konfiguracji.
* [Środowisku IDE Arduino](https://www.arduino.cc/en/main/software) wersji 1.6.8 lub nowszej. Wcześniejsze wersje nie działają z biblioteki usługi Azure IoT Hub.

Jeśli nie masz czujnika następujące elementy są opcjonalne. Istnieje również możliwość korzystania z danych z symulowanych czujników:

* Czujnik temperatury i wilgotności BME280
* Breadboard
* Przewodów jumper M/M

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Łączenie Feather M0 WiFi z czujnikiem i komputera
W tej sekcji możesz nawiązać czujników tablicy. Następnie możesz podłączyć urządzenia do komputera w celu dalszego użycia.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Czujnik temperatury i wilgotności DHT22 nawiązać połączenie z Feather M0 WiFi

Użyj przewodów breadboard i jumper do nawiązania połączenia. Jeśli nie masz czujnik, Pomiń tę sekcję, ponieważ zamiast tego użyj danych z symulowanych czujników.

![Odwołanie połączenia](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


Czujnik numery PIN można użyć następujących połączeń:


| Start (czujnik)           | Końcowy (tablica)            | Kolor kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27A)            | 3V (3A numeru Pin)            | Czerwony kabel     |
| GND (Pin 29A)            | GND (6A numeru Pin)           | Czarnego przewodu   |
| SCK (Pin 30A)            | SCK (Pin 12A)          | Żółty kabel  |
| SDO (Pin 31A)            | MI (Pin 14A)           | Kabel biały   |
| SDI (Pin 32A)            | M0 (Pin 13A)           | Niebieski kabel    |
| CS (Pin 33A)             | Interfejs GPIO 5 (Pin 15J)       | Kabel pomarańczowy  |

Aby uzyskać więcej informacji, zobacz [Adafruit BME280 wilgotności i ciśnienie atmosferyczne podgrupach czujnika temperatury](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) i [wyprowadzenia styków Adafruit Feather M0 WiFi](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Teraz Twoja Feather M0 WiFi powinny być połączone z czujnikiem pracy.

![Skupionej DHT22 Feather Huzzah](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Podłącz Feather M0 WiFi do komputera

Umożliwia USB Micro celu kabla USB typu Feather M0 WiFi nawiązać połączenie z komputera, jak pokazano:

![Podłącz Feather Huzzah do komputera](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Dodawanie uprawnień portu szeregowego (tylko Ubuntu)

Jeśli używasz systemu Ubuntu, upewnij się, że masz uprawnienia do działania na portu USB w Feather M0 WiFi. Aby dodać uprawnienia do portu szeregowego, wykonaj następujące kroki:


1. W terminalu uruchom następujące polecenia:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Zostanie wyświetlony jeden z następujących danych wyjściowych:

   * crw-rw---xxxxxxxx uucp głównego 1
   * crw-rw---1 główny xxxxxxxx inicjowania połączeń

   W danych wyjściowych, zwróć uwagę, że `uucp` lub `dialout` to nazwa właściciela grupy portu USB.

2. Aby dodać użytkownika do grupy, uruchom następujące polecenie:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   W poprzednim kroku uzyskany Nazwa właściciela grupy `<group-owner-name>`. Nazwa użytkownika systemu Ubuntu to `<username>`.

3. Aby zmiana zaczęła występować Wyloguj się z systemem Ubuntu, a następnie zaloguj się do niej ponownie.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Zbieranie danych z czujników i wysyłać je do Centrum IoT hub

W tej sekcji, wdrażanie i uruchamianie aplikacji przykładowej na Feather M0 WiFi. Przykładowa aplikacja sprawia, że LED blink na Feather M0 WiFi. Wysyła dane temperatury i wilgotności zebrane z czujników BME280 do usługi IoT hub.

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>Pobieranie przykładowej aplikacji z usługi GitHub i przygotuj środowisku IDE Arduino

Przykładowa aplikacja jest hostowana w usłudze GitHub. Sklonuj repozytorium przykładowej, zawierający przykładową aplikację z usługi GitHub. Aby sklonować przykładowe repozytorium, wykonaj następujące kroki:

1. Otwórz wiersz polecenia lub okno terminalu.

2. Przejdź do folderu, w której chcesz przykładowej aplikacji, które mają być przechowywane.
3. Uruchom następujące polecenie:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>Zainstaluj pakiet dla Feather M0 WiFi w środowisku IDE Arduino

1. Otwórz folder, w którym znajduje się przykładowa aplikacja.

2. Otwórz plik app.ino w folderze aplikacji w środowisku IDE Arduino.

   ![Otwórz przykładową aplikację w środowisku IDE Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. Kliknij przycisk **pliku** > **preferencje** (Windows/Linux) lub **Arduino** > **preferencje** (Mac) i skopiuj i Wklej poniższy link do **dodatkowych adresów URL Menedżera tablice** opcji w środowisku IDE Arduino preferencjach.
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. Kliknij przycisk **narzędzia** > **tablicy** > **Menedżera tablice**, a następnie zainstaluj `Arduino SAMD Boards` wersji `1.6.2` lub nowszej. 

1. W tym samym oknie Zainstaluj `Adafruit SAMD Boards` pakietu można dodać definicji pliku tablicy.

   ![Pakiet esp8266 jest zainstalowany](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. Kliknij przycisk **narzędzia** > **tablicy** > **Adafruit M0 WiFi**.

5. Zainstaluj sterowniki (dla tylko Windows). Po podłączeniu Feather M0 WiFi może być konieczne zainstalowanie sterownika. Kliknij przycisk [link pobierania na stronie internetowej](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) do pobierania Instalatora sterowników. Wykonaj kroki, aby zainstalować sterowniki, które mają.

### <a name="install-necessary-libraries"></a>Instalowanie wymaganych bibliotek

1. W środowisku IDE Arduino, kliknij przycisk **szkicu** > **obejmują biblioteki** > **zarządzanie bibliotekami**.

2. Wyszukaj następujące biblioteki nazwy jedno po drugim. Dla każdej biblioteki, który można znaleźć, kliknij przycisk **zainstalować**:

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. Ręcznie zainstaluj `Adafruit_WINC1500`. Przejdź do [tej witryny sieci Web](https://github.com/adafruit/Adafruit_WINC1500) i kliknij przycisk **klonowania lub pobierania** > **Pobierz ZIP**. Następnie w środowisku IDE Arduino, przejdź do obszaru **szkicu** > **obejmują biblioteki** > **Dodaj zip biblioteki** i Dodaj plik zip.

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>Jeśli nie masz rzeczywistych czujników BME280 za pomocą aplikacji przykładowej

Jeśli nie masz rzeczywistych czujników BME280 przykładową aplikację można symulować dane temperatury i wilgotności. Aby skonfigurować przykładowej aplikacji na korzystanie z symulowanych danych, wykonaj następujące kroki:

1. Otwórz `config.h` w pliku `app` folderu.

2. Znajdź następujący wiersz kodu, a następnie zmień wartość z `false` do `true`:

   ```c
   define SIMULATED_DATA true
   ```
   ![Konfigurowanie przykładowej aplikacji na korzystanie z symulowanych danych](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. Zapisz plik z `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Wdrażanie przykładowej aplikacji w celu Feather M0 WiFi

1. W środowisku IDE Arduino, kliknij przycisk **narzędzie** > **portu**, a następnie kliknij przycisk portu szeregowego Feather M0 WiFi.

2. Kliknij przycisk **szkicu** > **przekazywanie** do tworzenia i wdrażania przykładowej aplikacji w celu Feather M0 WiFi.

### <a name="enter-your-credentials"></a>Wprowadź swoje poświadczenia

Po pomyślnym ukończeniu przekazywania, wykonaj następujące kroki, aby wprowadzić poświadczenia:

1. W środowisku IDE Arduino, kliknij przycisk **narzędzia** > **Serial Monitor**.

2. W prawym dolnym rogu okna monitora szeregowej wybierz **nie rsza** na liście rozwijanej po lewej stronie.
3. Wybierz **115200 transmisji** na liście rozwijanej po prawej stronie.
4. W polu wejściowym u góry, wprowadź następujące informacje, jeśli zostanie wyświetlony monit, podaj je, a następnie kliknij przycisk **wysyłania**:

   * Wi-Fi SSID
   * Hasło sieci Wi-Fi
   * Parametry połączenia urządzenia

> [!Note]
> Informacje poświadczeń są przechowywane w pamięci EEPROM z Feather M0 WiFi. Jeśli klikniesz przycisk Resetuj na tablicy Feather M0 WiFi, przykładowej aplikacji pyta, jeśli chcesz wymazać dane. Wprowadź `Y` wymazać dane. Pojawi się prośba o podanie informacji po raz drugi.

### <a name="verify-that-the-sample-application-is-running-successfully"></a>Sprawdź, czy Przykładowa aplikacja jest uruchomiona pomyślnie

Jeśli zobaczysz następujące dane wyjściowe z okna monitora szeregowe i migająca dioda LED na Feather M0 WiFi Przykładowa aplikacja jest uruchomiona pomyślnie:

![Końcowe dane wyjściowe w środowisku IDE Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie połączona Feather M0 WiFi z Centrum IoT i wysyłane dane przechwycone czujnika do usługi IoT hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

