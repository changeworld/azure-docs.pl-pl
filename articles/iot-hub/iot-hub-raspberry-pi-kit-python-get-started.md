---
title: Urządzenie raspberry Pi w chmurze (Python) - łączenie urządzenia Raspberry Pi dla usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i połączyć urządzenia Raspberry Pi do usługi Azure IoT Hub dla urządzenia Raspberry Pi w celu wysyłania danych do platformy w chmurze w ramach tego samouczka.
author: rangv
manager: ''
keywords: Usługa Azure iot urządzenia raspberry pi, usługi iot hub dla urządzenia raspberry pi, urządzenia raspberry pi wysyłania danych do chmury, urządzenia raspberry pi w chmurze
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 6e668394bcb647df901dfac72c552475e56f20bf
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161105"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-python"></a>Łączenie urządzenia Raspberry Pi do usługi Azure IoT Hub (Python)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku rozpoczniesz od podstawy pracy z urządzeniem Raspberry Pi, w którym działa Raspbian uczenia. Następnie dowiesz się, jak bezproblemowe łączenie urządzeń do chmury przy użyciu [usługi Azure IoT Hub](about-iot-hub.md). Aby uzyskać przykłady Windows 10 IoT Core, przejdź do [Centrum deweloperów Windows](http://www.windowsondevices.com/).

Nie masz jeszcze zestawu? Spróbuj [symulatora w trybie online urządzenia Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Lub Kup nowy zestaw [tutaj](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Co należy zrobić

* Tworzenie Centrum IoT.
* Zarejestruj urządzenie Pi w usłudze IoT hub.
* Konfiguracja urządzenia Raspberry Pi.
* Uruchom przykładową aplikację na Pi do wysyłania danych czujnika do usługi IoT hub.

Łączenie urządzenia Raspberry Pi do usługi IoT hub, którą tworzysz. Następnie uruchom przykładową aplikację na Pi, aby zbierać dane temperatury i wilgotności z czujnika BME280. Na koniec wysyłania danych czujnika do usługi IoT hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć Centrum Azure IoT hub i uzyskać nowe parametry połączenia urządzenia.
* Jak połączyć Pi z czujnikiem BME280.
* Jak zbierać dane z czujników po uruchomieniu aplikacji przykładowej na Pi.
* Jak wysyłać danych czujnika do usługi IoT hub.

## <a name="what-you-need"></a>Co jest potrzebne

![Co jest potrzebne](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Raspberry Pi 2 lub Raspberry Pi 3 tablicy.
* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [Utwórz bezpłatne konto wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w ciągu kilku minut.
* Monitor, USB klawiatury i myszy łączących się z Pi.
* Komputer Mac lub komputerze z systemem Windows lub Linux.
* Połączenie internetowe.
* 16 GB lub nowszej karcie microSD.
* USB-karty lub microSD na kartach SD Nagraj obraz systemu operacyjnego na karcie microSD.
* Podaj potęgą 2 i 5 wolt za pomocą kabla USB wczesnych metr 6.

Opcjonalne są następujące elementy:

* Zmontowanych Adafruit BME280 temperatury, wykorzystanie i wilgotności czujnika.
* Breadboard.
* Przewodów jumper 6 F/M.
* Rozproszona LED 10 mm.


> [!NOTE] 
Te elementy są opcjonalne, ponieważ obsługa próbki kodu symulowane dane czujników.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Konfigurowanie urządzenia Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Zainstaluj system operacyjny Raspbian pi

Przygotuj karcie microSD instalacji obrazu Raspbian.

1. Pobierz Raspbian.
   1. [Pobierz Raspbian Jessie za pomocą programu Desktop](https://www.raspberrypi.org/downloads/raspbian/) (plik zip).
   1. Wyodrębnij obraz Raspbian do folderu na komputerze.
1. Zainstaluj Raspbian karcie microSD.
   1. [Pobierz i zainstaluj narzędzie nagrywarka karty Etcher SD](https://etcher.io/).
   1. Uruchom Etcher, a następnie wybierz obraz Raspbian, który został wyodrębniony w kroku 1.
   1. Wybierz dysk karty microSD. Należy pamiętać, że Etcher może została już wybrana poprawnego dysku.
   1. Kliknij przycisk Flash, aby zainstalować Raspbian na karcie microSD.
   1. Karta microSD należy usunąć z komputera po zakończeniu instalacji. Jest bezpiecznie usunąć karcie microSD bezpośrednio, ponieważ Etcher automatycznie wysuwa lub odinstalowuje karcie microSD po jego ukończeniu.
   1. Wstaw karcie microSD do Pi.

### <a name="enable-ssh-and-i2c"></a>Włączanie protokołu SSH i I2C

1. Nawiązać połączenie z Pi monitora, klawiatury i myszy, uruchom Pi i następnie zaloguj za pomocą Raspbian `pi` jako nazwy użytkownika i `raspberry` jako hasło.
1. Kliknij ikonę Raspberry > **preferencje** > **Raspberry Pi konfiguracji**.

   ![W menu Preferencje Raspbian](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Na **interfejsów** kartę, należy ustawić **I2C** i **SSH** do **Włącz**, a następnie kliknij przycisk **OK**. Jeśli nie masz fizycznego czujniki i chcesz użyć danych z symulowanych czujników, ten krok jest opcjonalny.

   ![Włącz I2C i ustawieniami SSH na urządzeniu Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Aby włączyć protokół SSH i I2C, można znaleźć więcej dokumentów odwołania na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) i [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Łączenie z czujnika do Pi

Użyj przewodów breadboard i jumper nawiązać DIODĘ i BME280 Pi w następujący sposób. Jeśli nie masz czujnik [pominąć tę sekcję](#connect-pi-to-the-network).

![Połączenie urządzenia Raspberry Pi i czujnik](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

Czujnik BME280 może zbierać dane temperatury i wilgotności. I diody LED będzie blink w przypadku komunikacji między urządzeniem i chmurą. 

Czujnik numery PIN można użyć następujących połączeń:

| Uruchom (czujnik & LED)     | Końcowy (tablica)            | Kolor kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 5G)             | 3, 3V PWR (Pin 1)       | Kabel biały   |
| GND (Pin 7G)             | GND (Pin 6)            | Brązowy kabel   |
| SDI (Pin 10G)            | I2C1 SDA (Pin 3)       | Czerwony kabel     |
| SCK (Pin 8 K)             | I2C1 SCL (Pin 5)       | Kabel pomarańczowy  |
| VDD LED (Pin 18F)        | Interfejs GPIO 24 (Pin 18)       | Kabel biały   |
| GND LED (Pin 17F)        | GND (Pin 20)           | Czarnego przewodu   |

Kliknij, aby wyświetlić [Raspberry Pi 2 i 3 mapowania kodu Pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) użytkownikowi.

Po pomyślnym nawiązaniu BME280 urządzenia Raspberry Pi, należy go jak poniżej obrazu.

![Pi połączonych i BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Pi się z siecią

Włącz Pi przy użyciu micro kabla USB i zasilacz. Użyj kabla Ethernet do łączenia Pi sieci przewodowej lub postępuj zgodnie z [instrukcji z urządzeniem Raspberry Pi podstawę](https://www.raspberrypi.org/learning/software-guide/wifi/) do Pi nawiązać połączenie z sieci bezprzewodowej. Po Twojej Pi została pomyślnie podłączona do sieci, należy pamiętać o [adres IP Twojego Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Podłączone do sieci przewodowej](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Upewnij się, że Pi jest podłączony do sieci z komputera. Na przykład jeśli komputer jest połączony z siecią bezprzewodową, gdy Pi jest połączony z siecią przewodową, możesz nie zobaczyć adres IP w danych wyjściowych devdisco.

## <a name="run-a-sample-application-on-pi"></a>Uruchamianie przykładowej aplikacji na Pi

### <a name="install-the-prerequisite-packages"></a>Zainstaluj wstępnie wymagane pakiety

Aby nawiązać połączenie z urządzeniem Raspberry Pi, użyj jednej z następujących klientów SSH z komputera hosta.
   
   **Użytkownicy Windows**
   1. Pobierz i zainstaluj [PuTTY](http://www.putty.org/) for Windows. 
   1. Skopiuj adres IP w sekcji Pi do nazwy hosta (lub adres IP), a następnie wybierz typ połączenia SSH.
   
   
   **Mac i użytkownicy systemu Ubuntu**
   
   Użyj wbudowanego klienta SSH w systemie Ubuntu lub z systemem macOS. Może być konieczne uruchomienie `ssh pi@<ip address of pi>` połączyć Pi za pośrednictwem protokołu SSH.
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

   Istnieją 5 makra w tym pliku mogą configurate. Pierwsza z nich jest `MESSAGE_TIMESPAN`, która definiuje przedział czasu (w milisekundach) między dwa komunikaty, które wysyłają do chmury. Drugi `SIMULATED_DATA`, który jest wartość logiczna wskazująca, czy używać danych z czujników symulowanych lub nie. `I2C_ADDRESS` jest to adres I2C, która jest połączona z czujnika BME280. `GPIO_PIN_ADDRESS` jest to adres GPIO dla Twojego LED. Jest ostatni z nich `BLINK_TIMESPAN`, który zdefiniowany zakres czasu, po włączeniu usługi LED w milisekundach.

   Jeśli użytkownik **nie ma czujnika**ustaw `SIMULATED_DATA` wartość `True` się do aplikacji przykładowej, tworzenia i używania danych z symulowanych czujników.

1. Zapisz i zamknij, naciskając klawisz formantu O > Wprowadź > Control X.

### <a name="build-and-run-the-sample-application"></a>Kompilowanie i uruchamianie przykładowej aplikacji

1. Tworzenie przykładowej aplikacji przez uruchomienie następującego polecenia. Ponieważ zestawami SDK Azure IoT dla języka Python otoki na podstawie zestawu SDK C usługi Azure IoT urządzenia, należy skompilować bibliotek C, jeśli chcesz, lub musi być wygenerowany biblioteki języka Python z kodu źródłowego.

   ```bash
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```
   > [!NOTE] 
   Można również określić wersję, uruchamiając `sudo ./setup.sh [--python-version|-p] [2.7|3.4|3.5]`. Jeśli uruchamiasz skrypt bez parametru, skrypt automatycznie wykryje wersję języka python zainstalowane (kolejności wyszukiwania w wersji 2.7 -> 3.4 -> 3.5). Upewnij się, że Twoja wersja języka Python przechowuje spójny podczas kompilowania i uruchamiania. 
   
   > [!NOTE] 
   Na tworzeniu biblioteki klienta Python (iothub_client.so) na urządzeniach z systemem Linux, które mają mniej niż 1GB pamięci RAM, może zostać wyświetlony kompilacji gromadzą podczas kompilowania iothub_client_python.cpp, jak pokazano poniżej, o godzinie % 98 `[ 98%] Building CXX object python/src/CMakeFiles/iothub_client_python.dir/iothub_client_python.cpp.o`. Jeśli napotkasz ten problem, sprawdź zmniejszenie zużycia pamięci urządzenia przy użyciu `free -m command` w innym oknie terminalu, w tym samym czasie. Jeśli używasz za mało pamięci podczas kompilowania pliku iothub_client_python.cpp, może być konieczne tymczasowo zwiększyć obszar wymiany, aby uzyskać większą ilością dostępnej pamięci do pomyślnego tworzenia urządzenia klienta Biblioteka zestawu SDK języka Python.
   
1. Uruchamianie przykładowej aplikacji, uruchamiając następujące polecenie:

   ```bash
   python app.py '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Upewnij się, możesz kopiowania i wklejania parametry połączenia urządzenia w apostrofy. A jeśli używasz środowiska python 3, a następnie użyć polecenia `python3 app.py '<your Azure IoT hub device connection string>'`.


   Powinny być widoczne następujące dane wyjściowe, dane czujników i komunikaty, które są wysyłane do usługi IoT hub.

   ![Dane wyjściowe — dane czujników wysyłanych z urządzenia Raspberry Pi do Centrum IoT hub](media/iot-hub-raspberry-pi-kit-c-get-started/success.png
)

## <a name="next-steps"></a>Kolejne kroki

Po uruchomieniu aplikacji przykładowej, zbieranie danych z czujników i wysyłać je do Centrum IoT hub. Aby wyświetlić wiadomości wysłanych do usługi IoT hub lub wysyłania wiadomości do urządzenia Raspberry Pi urządzenia Raspberry Pi, zobacz [rozszerzenie użycia usługi Azure IoT Toolkit dla programu Visual Studio Code do wysyłania i odbierania komunikatów między urządzeniem i Centrum IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
