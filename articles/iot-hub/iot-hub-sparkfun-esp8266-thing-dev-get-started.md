---
title: ESP8266 do chmury — Połącz Sparkfun ESP8266 operacją deweloperów do Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i deweloperów operacją ESP8266 Sparkfun nawiązać połączenia z Centrum IoT Azure go do przesyłania danych do platformy w chmurze platformy Azure, w tym samouczku.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: ''
ms.assetid: 587fe292-9602-45b4-95ee-f39bba10e716
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: e6837d0312217d8e27e3639b8220f5016a2505a6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Sparkfun ESP8266 operacją deweloperów nawiązać połączenia z Centrum IoT Azure w chmurze

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![połączenie między DHT22, co deweloperów i Centrum IoT](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>Będzie wykonywać

Sparkfun ESP8266 operacją deweloperów nawiązać połączenia z Centrum IoT, które będą tworzone. Następnie uruchom przykładową aplikację na ESP8266 do zbierania danych temperatury i wilgotności z czujnika DHT22. Na koniec Wyślij dane czujników do Centrum IoT.

> [!NOTE]
> Jeśli używane są inne tablice ESP8266, można nadal wykonaj następujące kroki, aby połączyć go z Centrum IoT. W zależności od tablicy ESP8266 używasz, może zaistnieć konieczność zmiany konfiguracji `LED_PIN`. Na przykład, jeśli używasz ESP8266 z AI Thinker, możesz zmienić go z `0` do `2`. Zestaw nie ma jeszcze?: kliknij [tutaj](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>Co dowiesz się

* Tworzenie Centrum IoT i zarejestruj urządzenie na rzecz odchyleń
* Jak połączyć z operacją deweloperów z czujnika i komputera.
* Jak zbierać dane czujników, uruchamiając na rzecz odchyleń przykładowej aplikacji
* Jak wysyłać dane czujników do Centrum IoT.

## <a name="what-you-will-need"></a>Dane będą potrzebne

![elementy potrzebne do samouczka](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

Aby wykonać tę operację, potrzebne następujące elementy z operacją deweloperów Starter Kit:

* Tablica Sparkfun ESP8266 operacją deweloperów.
* USB Micro kabla USB A typu.

Należy również spełnić następujące warunki dla swojego środowiska programowania:

* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [utworzyć bezpłatne konto próbne Azure](https://azure.microsoft.com/free/) za kilka minut.
* Mac lub komputera z systemem Windows lub Ubuntu.
* Sieci bezprzewodowej dla deweloperów operacją ESP8266 Sparkfun do nawiązania połączenia.
* Połączenia internetowego na pobieranie narzędzia do konfiguracji.
* [Arduino IDE](https://www.arduino.cc/en/main/software) wersji 1.6.8 (lub nowsza), nie działa z biblioteki AzureIoT wcześniejszych wersji.

Następujące elementy są opcjonalne, w przypadku, gdy nie masz czujnika. Masz również opcję użycia danych czujnika symulowane.

* Adafruit DHT22 temperatury i wilgotności czujnika.
* Breadboard.
* M/M zworek przewodów.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Uzyskuj dostęp do deweloperów operacją ESP8266 czujnika i komputera

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Czujnik temperatury i wilgotności DHT22 nawiązać połączenia z ESP8266 operacją deweloperów

Użyj przewodów breadboard i zworek do nawiązania połączenia w następujący sposób. Jeśli nie masz czujnika, Pomiń tę sekcję, ponieważ można użyć danych czujnika symulowane.

![Odwołanie do połączenia](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

Dla czujnik numery PIN użyjemy następujących połączeń:

| Start (czujnik)           | Końcowy (tablica)           | Kolor kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27F)            | 3V (8A kodu Pin)           | Czerwony kabel     |
| DANE (Pin 28F)           | Interfejs GPIO 2 (9A kodu Pin)       | Białe kabel    |
| GND (Pin 30F)            | GND (7J kodu Pin)          | Czarne kabel   |


- Aby uzyskać więcej informacji, zobacz: [Instalatora czujnik DHT22](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) i [specyfikacji Sparkfun ESP8266 operacją deweloperów](https://www.sparkfun.com/products/13711)

Teraz deweloperów operacją ESP8266 Twojego Sparkfun powinny być połączone z czujnika pracy.

![Uzyskuj dht22 ESP8266 operacją deweloperów](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Łączenie się z komputerem Sparkfun ESP8266 operacją deweloperów

Użyj USB Micro do kabla USB A typu nawiązać deweloperów operacją ESP8266 Sparkfun komputer w następujący sposób.

![łączenie się z komputerem huzzah piór](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Dodaj uprawnienia portu szeregowego — tylko Ubuntu

Jeśli używasz Ubuntu, upewnij się, że zwykłego użytkownika ma uprawnienia do działania na USB portu z Sparkfun ESP8266 operacją odchyleń Dodawanie portu szeregowego uprawnień do zwykłego użytkownika, wykonaj następujące kroki:

1. W terminalu, uruchom następujące polecenia:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Pojawia się jeden z następujących danych wyjściowych:

   * crw-rw---xxxxxxxx uucp głównego 1
   * crw-rw---xxxxxxxx inicjowania połączeń głównego 1

   W danych wyjściowych, zwróć uwagę, `uucp` lub `dialout` oznacza to nazwa właściciela grupy portu USB.

1. Dodaj użytkownika do grupy, uruchamiając następujące polecenie:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` jest to nazwa właściciela grupy uzyskanego w poprzednim kroku. `<username>` to nazwa użytkownika Ubuntu.

1. Wyloguj się Ubuntu i logowania go ponownie, aby zmiany zaczęły obowiązywać.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Zbieraj dane czujników i wysyłania go do Centrum IoT

W tej sekcji Wdrażanie i uruchom przykładową aplikację na Sparkfun ESP8266 operacją odchyleń Przykładowa aplikacja miganie LED na Sparkfun ESP8266 operacją deweloperów i wysyła temperatury i wilgotności dane zbierane z czujnika DHT22 do Centrum IoT.

### <a name="get-the-sample-application-from-github"></a>Pobierz przykładową aplikację z usługi GitHub

Przykładowa aplikacja znajduje się w witrynie GitHub. Klonowanie repozytorium przykładowej zawierający przykładową aplikację z usługi GitHub. Klonowanie repozytorium przykładowej, wykonaj następujące kroki:

1. Otwórz wiersz polecenia lub okno terminalu.
1. Przejdź do folderu, w którym ma przykładowej aplikacji, które mają być przechowywane.
1. Uruchom następujące polecenie:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Dla deweloperów operacją ESP8266 Sparkfun w Arduino IDE, należy zainstalować pakiet:

1. Otwórz folder, w którym przechowywana jest przykładowej aplikacji.
1. Otwórz plik app.ino w folderze aplikacji w Arduino IDE.

   ![Otwórz aplikację przykładową arduino IDE](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. W środowisku IDE Arduino, kliknij przycisk **pliku** > **preferencje**.
1. W **preferencje** okna dialogowego kliknij ikonę obok **dodatkowych adresów URL Menedżera tablice** pola tekstowego.
1. W oknie podręcznym wprowadź następujący adres URL, a następnie kliknij przycisk **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![wskaż na adres url pakietu w arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. W **preferencji** okno dialogowe, kliknij przycisk **OK**.
1. Kliknij przycisk **narzędzia** > **tablicy** > **Menedżera tablice**, a następnie wyszukaj esp8266.
   Należy zainstalować ESP8266 przy użyciu wersji 2.2.0 lub nowszej.

   ![Pakiet esp8266 jest zainstalowany](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Kliknij przycisk **narzędzia** > **tablicy** > **Sparkfun ESP8266 operacją deweloperów**.

### <a name="install-necessary-libraries"></a>Zainstaluj wymagane biblioteki

1. W środowisku IDE Arduino, kliknij przycisk **szkicu** > **obejmują biblioteki** > **zarządzanie bibliotekami**.
1. Wyszukiwanie w bibliotece następujące nazwy jeden po drugim. Dla każdej biblioteki można znaleźć, kliknij przycisk **zainstalować**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Nie masz rzeczywistych czujnik DHT22?

Przykładową aplikację można symulować danych temperatury i wilgotności, w przypadku, gdy nie ma rzeczywistego czujnik DHT22. Aby włączyć przykładowej aplikacji za pomocą symulowane danych, wykonaj następujące kroki:

1. Otwórz `config.h` w pliku `app` folderu.
1. Znajdź następujący wiersz kodu i zmień wartość `false` do `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Skonfiguruj przykładową aplikację za pomocą symulowane danych](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Zapisz z `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Wdrażanie przykładowej aplikacji do deweloperów operacją ESP8266 Sparkfun

1. W środowisku IDE Arduino, kliknij przycisk **narzędzie** > **portu**, a następnie kliknij przycisk portu szeregowego na Sparkfun ESP8266 operacją odchyleń
1. Kliknij przycisk **szkicu** > **przekazać** do tworzenia i wdrażania przykładowej aplikacji do odchyleń operacją ESP8266 Sparkfun

> [!Note]
> Jeśli używasz macOS można prawdopodobnie zobacz następujące komunikaty podczas przekazywania. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Otwórz okno programu ternimal i Zakończ poniższych akcji, aby rozwiązać ten problem.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Wprowadź swoje poświadczenia

Po pomyślnym ukończeniu przekazywania, wykonaj kroki, aby wprowadzić swoje poświadczenia:

1. W środowisku IDE Arduino, kliknij przycisk **narzędzia** > **Serial Monitor**.
1. W oknie Monitora serial zauważyć dwie listy rozwijanej w prawym dolnym rogu.
1. Wybierz **nie zakończenia wiersza** po lewej stronie listy rozwijanej.
1. Wybierz **transmisji 115200** na liście po prawej listy rozwijanej.
1. W odpowiednim polu znajduje się w górnej części okna monitora szeregowych, wprowadź następujące informacje, jeśli zostanie wyświetlona prośba o Podaj je, a następnie kliknij przycisk **wysyłania**.
   * Wi-Fi SSID
   * Hasło sieci Wi-Fi
   * Ciąg połączenia urządzenia

> [!Note]
> Informacje poświadczeń są przechowywane w pamięci EEPROM ESP8266 odchyleń operacją Sparkfun Jeśli klikniesz przycisk reset na tablicy Sparkfun ESP8266 operacją deweloperów przykładowej aplikacji zapyta, czy chcesz wymazać informacje. Wprowadź `Y` mają informacji wymazane, a monit o podanie informacji ponownie.

### <a name="verify-the-sample-application-is-running-successfully"></a>Sprawdź, czy Przykładowa aplikacja działa prawidłowo

Jeśli widzisz następujące dane wyjściowe z okna monitora szeregowe i migający LED na deweloperów operacją ESP8266 Sparkfun Przykładowa aplikacja działa prawidłowo.

![ostateczne dane wyjściowe w arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie połączony deweloperów operacją ESP8266 Sparkfun Centrum IoT i wysyłane dane czujników przechwyconych do Centrum IoT. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
