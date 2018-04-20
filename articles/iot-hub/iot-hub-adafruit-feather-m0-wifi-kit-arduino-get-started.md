---
title: 'M0 do chmury: M0 piór sieci Wi-Fi nawiązać połączenia z Centrum IoT Azure | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować i sieci Wi-Fi Adafruit piór M0 nawiązać połączenia z Centrum IoT Azure w celu wysyłania danych do platformy w chmurze Azure, w tym samouczku.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: ''
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 2a6a65a3c4a69a49788ce9799ceed53d53edcd77
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Połączenia sieci Wi-Fi Adafruit piór M0 z Centrum IoT Azure w chmurze
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Połączenie między BME280, M0 piór sieci Wi-Fi i Centrum IoT](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

W tym samouczku Rozpocznij od podstawy pracy z tablicy Arduino learning. Następnie Dowiedz się jak bezproblemowo połączyć z urządzenia do chmury przy użyciu [Centrum IoT Azure](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>Co zrobić

Wi-Fi Adafruit piór M0 nawiązać połączenia z Centrum IoT utworzony. Następnie uruchom przykładową aplikację na M0 sieci Wi-Fi, aby zbierać dane temperatury i wilgotności z BME280. Ponadto użytkownik wysyła dane czujników do Centrum IoT.


## <a name="what-you-learn"></a>Omawiane zagadnienia

* Tworzenie Centrum IoT i zarejestrować urządzenie M0 piór sieci Wi-Fi
* Jak połączyć M0 piór sieci Wi-Fi z czujnika i komputera
* Jak zbierać dane czujników, uruchamiając przykładową aplikację na M0 piór sieci Wi-Fi
* Sposób wysłania danych czujnika do Centrum IoT

## <a name="what-you-need"></a>Co jest potrzebne

![elementy potrzebne do samouczka](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

Aby wykonać tę operację, potrzebne następujące części z Twojej startowy piór M0 sieci Wi-Fi:

* Tablica M0 piór sieci Wi-Fi
* Dysk USB Micro do kabla USB A typu

Należy również następujące czynności dla swojego środowiska programowania:

* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [utworzyć bezpłatne konto próbne Azure](https://azure.microsoft.com/free/) za kilka minut.
* Mac lub komputera z systemem Windows lub Ubuntu.
* Sieć bezprzewodową piór M0 Wi-Fi do nawiązania połączenia.
* Połączenie internetowe, aby pobrać to narzędzie konfiguracji.
* [Arduino IDE](https://www.arduino.cc/en/main/software) wersji 1.6.8 lub nowszej. Starszych wersjach nie działają z biblioteki Azure IoT Hub.

Jeśli nie masz czujnika, następujące elementy są opcjonalne. Masz również opcję użycia danych czujnika symulowanego:

* Czujnik temperatury i wilgotności BME280
* Breadboard
* Przewodów zworek M/M

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Połączenia Wi-Fi piór M0 z czujnika i komputera
W tej sekcji czujników nawiązać tablicy. Następnie możesz podłączyć urządzenia do tego komputera do dalszego użytku.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Czujnik temperatury i wilgotności DHT22 nawiązać połączenie WiFi M0 piór

Użyj przewodów breadboard i zworek do nawiązania połączenia. Jeśli nie masz czujnika, Pomiń tę sekcję, ponieważ można użyć danych czujnika symulowane.

![Odwołanie do połączenia](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


Czujnik numery PIN można użyć następujących połączeń:


| Start (czujnik)           | Końcowy (tablica)            | Kolor kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27A)            | 3V (3A kodu Pin)            | Czerwony kabel     |
| GND (Pin 29A)            | GND (6A kodu Pin)           | Czarne kabel   |
| SCK (Pin 30A)            | SCK (Pin 12A)          | Żółty kabel  |
| SDO (Pin 31A)            | MI (Pin 14A)           | Białe kabel   |
| SDI (Pin 32A)            | M0 (Pin 13A)           | Niebieski kabel    |
| CS (Pin 33A)             | Interfejs GPIO 5 (Pin 15J)       | Kabel pomarańczowy  |

Aby uzyskać więcej informacji, zobacz [Adafruit BME280 wilgotności + barometryczne wykorzystania podgrupach czujnik temperatury](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) i [wyprowadzenia styków sieci Wi-Fi Adafruit piór M0](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Teraz sieci Wi-Fi M0 piór powinny być połączone z czujnika pracy.

![Uzyskuj DHT22 Huzzah piór](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Łączenie się z komputerem M0 piór sieci Wi-Fi

Umożliwia USB Micro do kabla USB A typu połączenia Wi-Fi piór M0 do komputera, jak pokazano:

![Łączenie się z komputerem Huzzah piór](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Dodaj uprawnienia portu szeregowego (tylko Ubuntu)

Jeśli używasz Ubuntu, upewnij się, że masz uprawnienia do działania na portu USB w piór M0 WiFi. Aby dodać uprawnienia portu szeregowego, wykonaj następujące kroki:


1. W terminalu uruchom następujące polecenia:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Pojawia się jeden z następujących danych wyjściowych:

   * crw-rw---xxxxxxxx uucp głównego 1
   * crw-rw---xxxxxxxx inicjowania połączeń głównego 1

   W danych wyjściowych, zwróć uwagę, że `uucp` lub `dialout` jest nazwa właściciela grupy portu USB.

2. Aby dodać użytkownika do grupy, uruchom następujące polecenie:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   W poprzednim kroku, uzyskać nazwę właściciela grupy `<group-owner-name>`. Nazwa użytkownika Ubuntu jest `<username>`.

3. Zmiany są wyświetlane, wyloguj się Ubuntu i zaloguj ponownie.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Zbieraj dane czujników i wysyłania go do Centrum IoT

W tej sekcji Wdróż i uruchom przykładową aplikację na M0 piór sieci Wi-Fi. Przykładowa aplikacja nawiązuje migania LED M0 piór sieci Wi-Fi. Wysyła dane temperatury i wilgotności zebrane z czujnika BME280 do Centrum IoT.

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>Pobierz przykładową aplikację z usługi GitHub i przygotować Arduino IDE

Przykładowa aplikacja znajduje się w witrynie GitHub. Klonowanie repozytorium przykładowej zawierający przykładową aplikację z usługi GitHub. Klonowanie repozytorium przykładowej, wykonaj następujące kroki:

1. Otwórz wiersz polecenia lub okno terminalu.

2. Przejdź do folderu, w którym ma przykładowej aplikacji, które mają być przechowywane.
3. Uruchom następujące polecenie:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>Zainstaluj pakiet dla piór M0 Wi-Fi w Arduino IDE

1. Otwórz folder, w którym przechowywana jest przykładowej aplikacji.

2. Otwórz plik app.ino w folderze aplikacji w środowisku IDE Arduino.

   ![Otwórz aplikację przykładową Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. Kliknij przycisk **pliku** > **preferencje** (system Windows/Linux) lub **Arduino** > **preferencje** (Mac) i skopiuj i wklej poniższe łącze do **dodatkowych adresów URL Menedżera tablice** opcji w preferencjach Arduino IDE.
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. Kliknij przycisk **narzędzia** > **tablicy** > **Menedżera tablice**, a następnie zainstaluj `Arduino SAMD Boards` wersji `1.6.2` lub nowszej. 

1. W tym samym oknie zainstalować `Adafruit SAMD Boards` pakietu można dodać definicji pliku tablicy.

   ![Pakiet esp8266 jest zainstalowany](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. Kliknij przycisk **narzędzia** > **tablicy** > **Adafruit M0 sieci Wi-Fi**.

5. Zainstaluj sterowniki (tylko dla systemu Windows). Po podłączeniu M0 piór sieci Wi-Fi, może być konieczne zainstalowanie sterownika. Kliknij przycisk [łącze na stronie sieci Web](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) pobrać instalatora sterownika. Wykonaj kroki, aby zainstalować sterowniki, które mają.

### <a name="install-necessary-libraries"></a>Zainstaluj wymagane biblioteki

1. W środowisku IDE Arduino, kliknij przycisk **szkicu** > **obejmują biblioteki** > **zarządzanie bibliotekami**.

2. Wyszukiwanie w bibliotece następujące nazwy jeden po drugim. Dla każdej biblioteki można znaleźć, kliknij przycisk **zainstalować**:

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. Ręcznie zainstaluj `Adafruit_WINC1500`. Przejdź do [tej witryny sieci Web](https://github.com/adafruit/Adafruit_WINC1500) i kliknij przycisk **klonowania lub pobierania** > **Pobierz ZIP**. Następnie środowiskiem IDE Arduino, przejdź do **szkicu** > **obejmują biblioteki** > **dodać .zip biblioteki** i Dodaj plik zip.

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>Użyj przykładowej aplikacji, jeśli nie masz rzeczywistych czujnik BME280

Jeśli nie masz rzeczywistych czujnik BME280 przykładową aplikację można symulować temperatury i wilgotności danych. Aby skonfigurować przykładowej aplikacji za pomocą symulowane danych, wykonaj następujące kroki:

1. Otwórz `config.h` w pliku `app` folderu.

2. Znajdź następujący wiersz kodu i zmień wartość `false` do `true`:

   ```c
   define SIMULATED_DATA true
   ```
   ![Skonfiguruj przykładową aplikację za pomocą symulowane danych](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. Zapisz plik z `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Wdrażanie przykładowej aplikacji do M0 piór sieci Wi-Fi

1. W środowisku IDE Arduino, kliknij przycisk **narzędzie** > **portu**, a następnie kliknij przycisk portu szeregowego M0 piór sieci Wi-Fi.

2. Kliknij przycisk **szkicu** > **przekazać** do tworzenia i wdrażania przykładowej aplikacji do M0 piór sieci Wi-Fi.

### <a name="enter-your-credentials"></a>Wprowadź swoje poświadczenia

Po pomyślnym ukończeniu przekazywania, wykonaj następujące kroki, aby wprowadzić poświadczenia:

1. W środowisku IDE Arduino, kliknij przycisk **narzędzia** > **Serial Monitor**.

2. W prawym dolnym rogu okna monitora szeregowej wybierz **nie zakończenia wiersza** na liście rozwijanej po lewej stronie.
3. Wybierz **transmisji 115200** na liście rozwijanej po prawej stronie.
4. W odpowiednim polu u góry, wprowadź następujące informacje, jeśli użytkownik jest proszony o podaj go, a następnie kliknij przycisk **wysyłania**:

   * Wi-Fi SSID
   * Hasło sieci Wi-Fi
   * Ciąg połączenia urządzenia

> [!Note]
> Informacje poświadczeń są przechowywane w pamięci EEPROM WiFi M0 piór. Jeśli klikniesz przycisk reset na tablicy M0 piór sieci Wi-Fi, przykładowej aplikacji zapyta, czy chcesz wymazać informacje. Wprowadź `Y` do usuwania informacji. Konieczne jest podanie informacji po raz drugi.

### <a name="verify-that-the-sample-application-is-running-successfully"></a>Sprawdź, czy Przykładowa aplikacja działa prawidłowo

Jeśli widzisz następujące dane wyjściowe z okna monitora szeregowe i migający LED na M0 piór sieci Wi-Fi Przykładowa aplikacja działa prawidłowo:

![Ostateczne dane wyjściowe w Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie połączony M0 piór sieci Wi-Fi z Centrum IoT i wysyłane dane czujników przechwyconych do Centrum IoT. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

