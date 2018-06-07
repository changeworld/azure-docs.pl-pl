---
title: Pi malinowe do chmury (Python) - Połącz Pi malina z Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i Pi malina nawiązać połączenia z Centrum IoT Azure pi malina do wysyłania danych do platformy w chmurze platformy Azure, w tym samouczku.
author: rangv
manager: ''
keywords: pi Azure iot malinowe, Centrum iot malinowe pi, pi malinowe wysyłania danych do chmury, malinowe pi do chmury
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 15b917d811dff2d70b638ab82c808bdc7fa1012b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635155"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-python"></a>Pi malinowe nawiązać połączenia z Centrum IoT Azure (Python)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku Rozpocznij od uczenia podstawy pracy z Pi malina, który działa Raspbian. Następnie Dowiedz się jak bezproblemowo połączyć z urządzenia do chmury przy użyciu [Centrum IoT Azure](iot-hub-what-is-iot-hub.md). Dla przykładów Windows 10 IoT Core, przejdź do [Centrum deweloperów systemu Windows](http://www.windowsondevices.com/).

Nie masz jeszcze zestawu? Spróbuj [symulatora online Pi malina](iot-hub-raspberry-pi-web-simulator-get-started.md). Lub kupowanie nowej kit [tutaj](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Co zrobić

* Tworzenie Centrum IoT.
* Zarejestruj urządzenie pi w Centrum IoT.
* Instalator malinowe Pi.
* Uruchom przykładową aplikację na Pi do wysyłania danych czujnika do Centrum IoT.

Pi malina nawiązać połączenia z Centrum IoT utworzony. Następnie uruchom przykładową aplikację na Pi do zbierania danych temperatury i wilgotności z czujnika BME280. Ponadto użytkownik wysyła dane czujników do Centrum IoT.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Sposób tworzenia Centrum Azure IoT i uzyskać nowy ciąg połączenia urządzenia.
* Jak nawiązać połączenia z czujnika BME280 Pi.
* Jak zbierać dane czujników, uruchamiając przykładową aplikację na Pi.
* Jak wysyłać dane czujników do Centrum IoT.

## <a name="what-you-need"></a>Co jest potrzebne

![Co jest potrzebne](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Malina Pi 2 lub 3 Pi malina tablicy.
* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [utworzyć bezpłatne konto próbne Azure](https://azure.microsoft.com/free/) za kilka minut.
* Monitor, klawiatura USB i myszy łączący się Pi.
* Mac lub komputer z systemem Windows lub Linux.
* Połączenie internetowe.
* 16 GB lub powyżej karty microSD.
* USB-karty sieciowej lub microSD na kartach SD Nagraj obraz systemu operacyjnego na karcie microSD.
* 5 volt potęgą 2-amp dostarczyć stopy 6 micro kabel USB.

Opcjonalne są następujące elementy:

* Złożony Adafruit BME280 temperatury, wykorzystania i wilgotności czujnika.
* Breadboard.
* 6 F/M zworek przewodów.
* Rozproszona LED 10 mm.


> [!NOTE] 
Te elementy są opcjonalne, ponieważ dane czujników symulowane obsługi próbki kodu.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Konfigurowanie Pi malina

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalowanie systemu operacyjnego Raspbian pi

Przygotuj karty microSD instalacji obrazu Raspbian.

1. Pobierz Raspbian.
   1. [Pobierz Joasia Raspbian z pulpitem](https://www.raspberrypi.org/downloads/raspbian/) (pliku .zip).
   1. Wyodrębnij obrazu Raspbian do folderu na komputerze.
1. Zainstaluj Raspbian karty microSD.
   1. [Pobierz i zainstaluj narzędzie palnika karty Etcher SD](https://etcher.io/).
   1. Uruchom Etcher i wybierz obraz Raspbian, który został wyodrębniony w kroku 1.
   1. Wybierz dysk karty microSD. Należy pamiętać, że Etcher może już wybrane poprawnego dysku.
   1. Kliknij przycisk Flash instalowanie Raspbian karty microSD.
   1. Karta microSD należy usunąć z komputera po zakończeniu instalacji. Jest bezpiecznie usunąć karta microSD bezpośrednio, ponieważ Etcher automatycznie wysuwa lub odinstalowuje karty microSD po zakończeniu.
   1. Włóż kartę microSD do Pi.

### <a name="enable-ssh-and-i2c"></a>Włącz SSH i I2C

1. Pi nawiązać monitora, klawiatury i myszy, uruchom Pi, a następnie zaloguj Raspbian przy użyciu `pi` jako nazwy użytkownika i `raspberry` jako hasło.
1. Kliknij ikonę malinowe > **preferencje** > **malina Pi konfiguracji**.

   ![Menu Preferencje Raspbian](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Na **interfejsów** ustaw **I2C** i **SSH** do **włączyć**, a następnie kliknij przycisk **OK**. Jeśli nie masz fizycznych czujników i chcesz użyć danych czujnika symulowane, ten krok jest opcjonalny.

   ![Włącz I2C i SSH na malinowe Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Aby włączyć SSH i I2C, można znaleźć więcej dokumentacji na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) i [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Połącz z czujnika do Pi

Użyj przewodów breadboard i zworek LED i nawiązywać BME280 Pi w następujący sposób. Jeśli nie masz czujnika, [pominąć tę sekcję](#connect-pi-to-the-network).

![Pi malina i czujnik połączenia](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

Czujnik BME280 może zbierać dane temperatury i wilgotności. I LED będzie blink w przypadku braku komunikacji między urządzeniem i chmurą. 

Czujnik numery PIN można użyć następujących połączeń:

| Uruchom (czujnik & LED)     | Końcowy (tablica)            | Kolor kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin: 5 GB/S)             | 3, 3V PWR (Pin 1)       | Białe kabel   |
| GND (Pin 7 GB/S)             | GND (Pin 6)            | Brązowy kabel   |
| SDI (Pin 10 GB/S)            | I2C1 SDA (Pin 3)       | Czerwony kabel     |
| SCK (Pin 8 GB/S)             | I2C1 SCL (Pin 5)       | Kabel pomarańczowy  |
| VDD LED (Pin 18F)        | Interfejs GPIO 24 (Pin 18)       | Białe kabel   |
| GND LED (Pin 17F)        | GND (Pin 20)           | Czarne kabel   |

Kliknij, aby wyświetlić [malina Pi 2 i 3 mapowania kodu Pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) użytkownikowi.

Po pomyślnie nawiązano połączenie BME280 Twojego Pi malina, należy go jak poniżej obrazu.

![Pi połączonych i BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Połączenie z siecią Pi

Włącz Pi przy użyciu micro kabla USB i zasilania. Łączenie Pi sieci przewodowej lub wykonaj przy użyciu kabla Ethernet [instrukcji Foundation Pi malina](https://www.raspberrypi.org/learning/software-guide/wifi/) do nawiązania połączenia z siecią bezprzewodową Pi. Po Twoje Pi została pomyślnie podłączona do sieci, należy pamiętać o [adres IP Twojego Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Podłączony do sieci przewodowej](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Upewnij się, że Pi jest podłączony do sieci z komputera. Na przykład jeśli komputer jest połączony z siecią bezprzewodową, gdy Pi jest połączony z siecią przewodową, może nie być wyświetlana w danych wyjściowych devdisco adres IP.

## <a name="run-a-sample-application-on-pi"></a>Uruchom przykładową aplikację na Pi

### <a name="install-the-prerequisite-packages"></a>Zainstaluj wstępnie wymagane pakiety

Aby nawiązać połączenie z Pi malina, użyj jednej z następujących klientów SSH z komputera hosta.
   
   **Użytkownicy systemu Windows**
   1. Pobierz i zainstaluj [PuTTY](http://www.putty.org/) dla systemu Windows. 
   1. Skopiuj adres IP z sekcji Pi na nazwę hosta (lub adres IP) i wybierz typ połączenia SSH.
   
   
   **Mac i Ubuntu użytkowników**
   
   Ubuntu lub macOS, korzystając z wbudowanego klienta SSH. Może być konieczne uruchomienie `ssh pi@<ip address of pi>` nawiązać Pi za pośrednictwem protokołu SSH.
   > [!NOTE] 
   Domyślna nazwa użytkownika to `pi` , a hasło to `raspberry`.


### <a name="configure-the-sample-application"></a>Konfigurowanie przykładowej aplikacji

1. Klonowanie przykładowej aplikacji, uruchamiając następujące polecenie:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-python-raspberrypi-client-app.git
   ```
1. Otwórz plik konfiguracji, uruchamiając następujące polecenia:

   ```bash
   cd iot-hub-python-raspberrypi-client-app
   nano config.py
   ```

   Istnieje 5 makra w tym pliku mogą configurate. Pierwsza z nich jest `MESSAGE_TIMESPAN`, która określa przedział czasu (w milisekundach) między dwa komunikaty, które wysyłają do chmury. Drugi `SIMULATED_DATA`, która jest wartość logiczna, czy należy użyć danych czujnika symulowane lub nie. `I2C_ADDRESS` jest to adres I2C, które z czujnika BME280 jest połączony. `GPIO_PIN_ADDRESS` jest to adres interfejs GPIO dla Twojego LED. Jest ostatnim blokiem `BLINK_TIMESPAN`, który zdefiniowany zakres czasu, po włączeniu LED sieci w milisekundach.

   Jeśli użytkownik **nie ma czujnika**, ustaw `SIMULATED_DATA` do wartości `True` dokonanie przykładowej aplikacji, tworzenia i używania danych czujnika symulowane.

1. Zapisz i zamknij naciskając formantu O > Enter > Control X.

### <a name="build-and-run-the-sample-application"></a>Tworzenie i uruchamianie przykładowej aplikacji

1. Tworzenie przykładowej aplikacji, uruchamiając następujące polecenie. Ponieważ zestawy IoT Azure SDK dla języka Python otoki u góry zestawu SDK usługi Azure IoT urządzenia C, należy skompilować bibliotek C lub należy wygenerować biblioteki języka Python z kodu źródłowego.

   ```bash
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```
   > [!NOTE] 
   Można również określić, że wersja ma uruchamiając `sudo ./setup.sh [--python-version|-p] [2.7|3.4|3.5]`. Po uruchomieniu skryptu bez parametru skrypt będzie automatycznie wykrywał wersji języka Python zainstalowany (Sekwencja wyszukiwania 2.7 -> 3.4 -> 3.5). Upewnij się, że wersji języka Python śledzi spójne podczas tworzenia i uruchamiania. 
   
   > [!NOTE] 
   W bibliotece klienta języka Python (iothub_client.so) korzystając z urządzeniami z systemem Linux, które mają mniej niż 1GB pamięci RAM, mogą pojawić kompilacji gromadzą 98% podczas kompilowania iothub_client_python.cpp, jak pokazano poniżej `[ 98%] Building CXX object python/src/CMakeFiles/iothub_client_python.dir/iothub_client_python.cpp.o`. Jeśli napotkasz ten problem, sprawdź zużycia pamięci urządzeniami przy użyciu `free -m command` w innym oknie terminali w tym czasie. Jeśli używasz Brak pamięci podczas kompilowania pliku iothub_client_python.cpp, może być konieczne tymczasowo Zwiększ obszar wymiany, aby uzyskać więcej dostępnej pamięci, aby pomyślnie skompilować urządzenia klienckie Python Biblioteka zestawu SDK.
   
1. Uruchom przykładową aplikację, uruchamiając następujące polecenie:

   ```bash
   python app.py '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Upewnij się, możesz kopiowania i wklejania ciąg połączenia urządzenia w pojedynczy cudzysłów. A jeśli używasz języka python 3, a następnie użyć polecenia `python3 app.py '<your Azure IoT hub device connection string>'`.


   Powinny zostać wyświetlone następujące dane wyjściowe, który zawiera dane czujników i komunikaty, które są wysyłane do Centrum IoT.

   ![Dane wyjściowe — dane czujników wysłanych z Pi malina Centrum IoT](media/iot-hub-raspberry-pi-kit-c-get-started/success.png
)

## <a name="next-steps"></a>Kolejne kroki

Uruchomiono przykładowej aplikacji do zbierania danych czujników i wysyłania go do Centrum IoT. Wyświetlanie komunikatów wysłanych z Pi malina do IoT koncentratora lub wysłania wiadomości do Twojej Pi malina za pomocą interfejsu wiersza polecenia, zobacz [Zarządzaj chmury urządzenia wiadomości z Centrum iothub explorer samouczek](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
