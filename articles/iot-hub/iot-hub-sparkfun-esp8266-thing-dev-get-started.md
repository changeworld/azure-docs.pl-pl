---
title: ESP8266 do chmury — Połącz urządzenie Sparkfun ESP8266 Thing Dev dla usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i połączyć urządzenie Sparkfun ESP8266 Thing Dev do usługi Azure IoT Hub dla niego do wysyłania danych do platformy w chmurze w ramach tego samouczka.
author: rangv
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 75ff53d5be29af08bb8e9c1b41f61040e5710cf7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452519"
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Urządzenie Sparkfun ESP8266 Thing Dev nawiązać połączenie z usługi Azure IoT Hub w chmurze

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![połączenie między DHT22 Thing Dev i Centrum IoT Hub](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>Będziesz robić

Połącz urządzenie Sparkfun ESP8266 Thing Dev do usługi IoT hub, która zostanie utworzona. Następnie uruchomić ESP8266, aby zbierać dane temperatury i wilgotności z czujnika DHT22 przykładowej aplikacji. Na koniec wysyłania danych czujnika do usługi IoT hub.

> [!NOTE]
> Jeśli używasz innych tablic ESP8266, można nadal postępuj zgodnie z następujące kroki, aby połączyć go z Centrum IoT hub. W zależności od tablicy ESP8266, którego używasz, może być konieczne ponownie skonfigurować `LED_PIN`. Na przykład, jeśli używasz ESP8266 z Thinker sztucznej Inteligencji, możesz zmienić go z `0` do `2`. Nie masz jeszcze zestawu?: kliknij [tutaj](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>Zawiera informacje

* Jak utworzyć Centrum IoT hub i rejestrowanie urządzenia na rzecz Dev.
* Jak połączyć Thing Dev z czujnikiem i komputera.
* Jak zbierać dane z czujników, uruchamiając przykładowej aplikacji na rzecz Dev.
* Jak wysyłać danych czujnika do usługi IoT hub.

## <a name="what-you-will-need"></a>Będą potrzebne

![Elementy wymagane dla tego samouczka](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

Do ukończenia tej operacji, potrzebne są następujące elementy z Twojej rzecz początkowy zestaw deweloperski:

* Urządzenie Sparkfun ESP8266 Thing Dev tablicy.
* Micro połączenia USB w celu kabla USB typu.

Należy również spełnić następujące warunki dla swojego środowiska programowania:

* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [Utwórz bezpłatne konto wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w ciągu kilku minut.
* Mac lub komputerze z systemem Windows i Ubuntu.
* Sieci bezprzewodowej dla urządzenie Sparkfun ESP8266 Thing Dev nawiązać połączenie.
* Połączenie internetowe, aby pobrać to narzędzie konfiguracji.
* [Środowisku IDE Arduino](https://www.arduino.cc/en/main/software) wersji 1.6.8 (lub nowsza), wcześniejszych wersji nie będzie działać z biblioteką AzureIoT.

Następujące elementy są opcjonalne, w przypadku, gdy nie ma czujnika. Istnieje również możliwość korzystania z danych z symulowanych czujników.

* Zestaw Adafruit DHT22 temperatury i wilgotności czujnika.
* Breadboard.
* Przewodów jumper M/M.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Łączenie ESP8266 Thing Dev z czujnikiem i komputera

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Czujnik temperatury i wilgotności DHT22 nawiązać połączenie z ESP8266 Thing Dev

Użyj przewodów breadboard i jumper, aby nawiązać połączenie w następujący sposób. Jeśli nie masz czujnik, Pomiń tę sekcję, ponieważ zamiast tego użyj danych z symulowanych czujników.

![Odwołanie połączenia](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

Dla czujnika numery PIN użyjemy następujących połączeń:

| Uruchom (czujnik)           | Końcowy (tablica)           | Kolor kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27F)            | 3V (8A numeru Pin)           | Czerwony kabel     |
| DANE (Pin 28F)           | Interfejs GPIO 2 (9A numeru Pin)       | Kabel biały    |
| GND (Pin 30F)            | GND (7J numeru Pin)          | Czarnego przewodu   |


- Aby uzyskać więcej informacji, zobacz: [Instalatora czujnika DHT22](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) i [specyfikacji urządzenie Sparkfun ESP8266 Thing Dev](https://www.sparkfun.com/products/13711)

Teraz Twoje urządzenie Sparkfun ESP8266 Thing Dev powinny być połączone z czujnikiem pracy.

![skupionej dht22 ESP8266 Thing Dev](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Podłącz urządzenie Sparkfun ESP8266 Thing Dev do komputera

Za pomocą USB Micro celu kabla USB typu, aby połączyć urządzenie Sparkfun ESP8266 Thing Dev do komputera w następujący sposób.

![Podłącz feather huzzah do komputera](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Dodawanie uprawnień portu szeregowego — tylko w systemie Ubuntu

Jeśli używasz systemu Ubuntu, sprawdź, czy zwykłego użytkownika ma uprawnienia do działania na USB port z urządzenie Sparkfun ESP8266 rzecz Dev. Aby dodać uprawnienia portu szeregowego do zwykłego użytkownika, wykonaj następujące kroki:

1. Uruchom następujące polecenia w terminalu:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Zostanie wyświetlony jeden z następujących danych wyjściowych:

   * crw-rw---xxxxxxxx uucp głównego 1
   * crw-rw---1 główny xxxxxxxx inicjowania połączeń

   W danych wyjściowych, zwróć uwagę, `uucp` lub `dialout` oznacza to nazwa właściciela grupy portu USB.

1. Dodaj użytkownika do grupy, uruchamiając następujące polecenie:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` jest to nazwa właściciela grupy, uzyskane w poprzednim kroku. `<username>` to nazwa użytkownika systemu Ubuntu.

1. Wyloguj się Ubuntu, a następnie zaloguj się w niej ponownie, aby zmiany zaczęły obowiązywać.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Zbieranie danych z czujników i wysyłać je do Centrum IoT hub

W tej sekcji Wdrażanie i uruchamianie przykładowej aplikacji na urządzenie Sparkfun ESP8266 rzecz Dev. Przykładowa aplikacja miga dioda LED na urządzenie Sparkfun ESP8266 Thing Dev i wysyła dane dotyczące temperatury i wilgotności, zebranych z czujników DHT22 do usługi IoT hub.

### <a name="get-the-sample-application-from-github"></a>Pobieranie przykładowej aplikacji z usługi GitHub

Przykładowa aplikacja jest hostowana w usłudze GitHub. Sklonuj repozytorium przykładowej, zawierający przykładową aplikację z usługi GitHub. Aby sklonować przykładowe repozytorium, wykonaj następujące kroki:

1. Otwórz wiersz polecenia lub okno terminalu.
1. Przejdź do folderu, w której chcesz przykładowej aplikacji, które mają być przechowywane.
1. Uruchom następujące polecenie:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Zainstaluj pakiet dla urządzenie Sparkfun ESP8266 Thing Dev w środowisku IDE Arduino:

1. Otwórz folder, w którym znajduje się przykładowa aplikacja.
1. Otwórz plik app.ino w folderze aplikacji w środowisku IDE Arduino.

   ![Otwórz przykładową aplikację w środowisku ide arduino](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. W środowisku IDE Arduino, kliknij przycisk **pliku** > **preferencje**.
1. W **preferencje** okna dialogowego kliknij ikonę obok **dodatkowych adresów URL Menedżera tablice** pola tekstowego.
1. W oknie podręcznym wprowadź następujący adres URL, a następnie kliknij przycisk **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![wskaż pakiet adres url w środowisku ide arduino](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. W **preferencji** okno dialogowe, kliknij przycisk **OK**.
1. Kliknij przycisk **narzędzia** > **tablicy** > **Menedżera tablice**, a następnie wyszukaj esp8266.
   Powinien być zainstalowany ESP8266 z wersją 2.2.0 lub nowszej.

   ![Pakiet esp8266 jest zainstalowany](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Kliknij przycisk **narzędzia** > **tablicy** > **urządzenie Sparkfun ESP8266 Thing Dev**.

### <a name="install-necessary-libraries"></a>Instalowanie wymaganych bibliotek

1. W środowisku IDE Arduino, kliknij przycisk **szkicu** > **obejmują biblioteki** > **zarządzanie bibliotekami**.
1. Wyszukaj następujące biblioteki nazwy jedno po drugim. Dla każdej biblioteki, możesz znaleźć, kliknij **zainstalować**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nie masz rzeczywistych czujników DHT22?

Przykładowa aplikacja można symulować dane temperatury i wilgotności, w przypadku, gdy nie masz rzeczywistych czujników DHT22. Aby włączyć przykładowej aplikacji na korzystanie z symulowanych danych, wykonaj następujące kroki:

1. Otwórz `config.h` w pliku `app` folderu.
1. Znajdź następujący wiersz kodu, a następnie zmień wartość z `false` do `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Konfigurowanie przykładowej aplikacji na korzystanie z symulowanych danych](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Oszczędzaj dzięki `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Wdrażanie przykładowej aplikacji na urządzenie Sparkfun ESP8266 Thing Dev

1. W środowisku IDE Arduino, kliknij przycisk **narzędzie** > **portu**, a następnie kliknij przycisk portu szeregowego urządzenie Sparkfun ESP8266 rzecz Dev.
1. Kliknij przycisk **szkicu** > **przekazywanie** do tworzenia i wdrażania przykładowej aplikacji w celu urządzenie Sparkfun ESP8266 rzecz Dev.

> [!Note]
> Jeśli używasz systemu macOS, prawdopodobnie może pojawić się następujące komunikaty podczas przekazywania. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Otwórz okno ternimal i Zakończ poniższych akcji, aby rozwiązać ten problem.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Wprowadź swoje poświadczenia

Po pomyślnym ukończeniu przekazywania, postępuj zgodnie z instrukcjami, aby wprowadzić swoje poświadczenia:

1. W środowisku IDE Arduino, kliknij przycisk **narzędzia** > **Serial Monitor**.
1. W oknie Monitora serial Zwróć uwagę, dwie listy rozwijanej w prawym dolnym rogu.
1. Wybierz **nie rsza** na liście rozwijanej po lewej stronie.
1. Wybierz **115200 transmisji** prawo rozwijanej listy.
1. W polu wejściowym, który znajduje się w górnej części okna monitora szeregowe, wprowadź następujące informacje, jeśli zostanie wyświetlony monit, podaj je, a następnie kliknij przycisk **wysyłania**.
   * Wi-Fi SSID
   * Hasło sieci Wi-Fi
   * Parametry połączenia urządzenia

> [!Note]
> Informacje poświadczeń są przechowywane w pamięci EEPROM z urządzenie Sparkfun ESP8266 rzecz Dev. Jeśli klikniesz przycisk Resetuj na tablicy urządzenie Sparkfun ESP8266 Thing Dev, przykładowa aplikacja prosi o tym, jeśli chcesz wymazać dane. Wprowadź `Y` zapewnienie informacji wymazane i prośba o podanie tych informacji.

### <a name="verify-the-sample-application-is-running-successfully"></a>Upewnij się, że przykładowa aplikacja działa prawidłowo

Jeśli zobaczysz następujące dane wyjściowe z okna monitora szeregowe i migająca dioda LED na urządzenie Sparkfun ESP8266 Thing Dev Przykładowa aplikacja działa poprawnie.

![końcowe dane wyjściowe w środowisku ide arduino](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie podłączone urządzenie Sparkfun ESP8266 Thing Dev z Centrum IoT i wysyłane dane przechwycone czujnika do usługi IoT hub. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
