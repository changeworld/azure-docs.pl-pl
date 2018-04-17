---
title: Pi malinowe do chmury (C) - Połącz Pi malina z Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i Pi malina nawiązać połączenia z Centrum IoT Azure pi malina do wysyłania danych do platformy w chmurze platformy Azure, w tym samouczku.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: pi Azure iot malinowe, Centrum iot malinowe pi, pi malinowe wysyłania danych do chmury, malinowe pi do chmury
ms.assetid: 68c0e730-1dc8-4e26-ac6b-573b217b302d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ea4a7b4084faf702cf303036b414a9bc9e153840
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Nawiązać malinowe Pi Azure IoT Hub (C)

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

## <a name="setup-raspberry-pi"></a>Konfiguracja malinowe Pi

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

### <a name="enable-ssh-and-spi"></a>Włącz SSH i SPI

1. Pi nawiązać monitora, klawiatury i myszy, uruchom Pi, a następnie zaloguj Raspbian przy użyciu `pi` jako nazwy użytkownika i `raspberry` jako hasło.
1. Kliknij ikonę malinowe > **preferencje** > **malina Pi konfiguracji**.

   ![Menu Preferencje Raspbian](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. Na **interfejsów** ustaw **SPI** i **SSH** do **włączyć**, a następnie kliknij przycisk **OK**. Jeśli nie masz fizycznych czujników i chcesz użyć danych czujnika symulowane, ten krok jest opcjonalny.

   ![Włącz SPI i SSH na malinowe Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Aby włączyć SSH i SPI, można znaleźć więcej dokumentacji na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) i [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Połącz z czujnika do Pi

Użyj przewodów breadboard i zworek LED i nawiązywać BME280 Pi w następujący sposób. Jeśli nie masz czujnika, [pominąć tę sekcję](#connect-pi-to-the-network).

![Pi malina i czujnik połączenia](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

Czujnik BME280 może zbierać dane temperatury i wilgotności. I LED będzie blink w przypadku braku komunikacji między urządzeniem i chmurą. 

Czujnik numery PIN można użyć następujących połączeń:

| Uruchom (czujnik & LED)     | Końcowy (tablica)            | Kolor kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD LED (Pin: 5 GB/S)         | Interfejs GPIO 4 (Pin 7)         | Białe kabel   |
| GND LED (Pin 6 GB/S)         | GND (Pin 6)            | Czarne kabel   |
| VDD (Pin 18F)            | 3, 3V PWR (Pin 17)      | Białe kabel   |
| GND (Pin 20F)            | GND (Pin 20)           | Czarne kabel   |
| SCK (Pin 21F)            | SPI0 SCLK (Pin 23)     | Kabel pomarańczowy  |
| SDO (Pin 22F)            | SPI0 MISO (Pin 21)     | Żółty kabel  |
| SDI (Pin 23F)            | SPI0 MOSI (Pin 19)     | Zielony kabel   |
| CS (Pin 24F)             | SPI0 CS (Pin 24)       | Niebieski kabel    |

Kliknij, aby wyświetlić [malina Pi 2 i 3 mapowania kodu Pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) użytkownikowi.

Po pomyślnie nawiązano połączenie BME280 Twojego Pi malina, należy go jak poniżej obrazu.

![Pi połączonych i BME280](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Połączenie z siecią Pi

Włącz Pi przy użyciu micro kabla USB i zasilania. Łączenie Pi sieci przewodowej lub wykonaj przy użyciu kabla Ethernet [instrukcji Foundation Pi malina](https://www.raspberrypi.org/learning/software-guide/wifi/) do nawiązania połączenia z siecią bezprzewodową Pi. Po Twoje Pi została pomyślnie podłączona do sieci, należy pamiętać o [adres IP Twojego Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Podłączony do sieci przewodowej](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Uruchom przykładową aplikację na Pi

### <a name="login-to-your-raspberry-pi"></a>Zaloguj się do Twojego malinowe Pi

1. Aby nawiązać połączenie z Pi malina, użyj jednej z następujących klientów SSH z komputera hosta.
   
   **Użytkownicy systemu Windows**
   1. Pobierz i zainstaluj [PuTTY](http://www.putty.org/) dla systemu Windows. 
   1. Skopiuj adres IP z sekcji Pi na nazwę hosta (lub adres IP) i wybierz typ połączenia SSH.
   
   ![Programu puTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac i Ubuntu użytkowników**
   
   Ubuntu lub macOS, korzystając z wbudowanego klienta SSH. Może być konieczne uruchomienie `ssh pi@<ip address of pi>` nawiązać Pi za pośrednictwem protokołu SSH.
   > [!NOTE] 
   Domyślna nazwa użytkownika to `pi` , a hasło to `raspberry`.


### <a name="configure-the-sample-application"></a>Konfigurowanie przykładowej aplikacji

1. Klonowanie przykładowej aplikacji, uruchamiając następujące polecenie:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Uruchom skrypt instalacji:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Jeśli możesz **nie ma fizycznego BME280**, można użyć "--symulowane danych" jako parametr wiersza polecenia, aby symulować temperatury i wilgotności danych. `sudo ./setup.sh --simulated-data`

### <a name="build-and-run-the-sample-application"></a>Tworzenie i uruchamianie przykładowej aplikacji

1. Tworzenie przykładowej aplikacji, uruchamiając następujące polecenie:

   ```bash
   cmake . && make
   ```
   ![Dane wyjściowe kompilacji](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Uruchom przykładową aplikację, uruchamiając następujące polecenie:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Upewnij się, możesz kopiowania i wklejania ciąg połączenia urządzenia w pojedynczy cudzysłów.


Powinny zostać wyświetlone następujące dane wyjściowe, który zawiera dane czujników i komunikaty, które są wysyłane do Centrum IoT.

![Dane wyjściowe — dane czujników wysłanych z Pi malina Centrum IoT](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Kolejne kroki

Uruchomiono przykładowej aplikacji do zbierania danych czujników i wysyłania go do Centrum IoT. Wyświetlanie komunikatów wysłanych z Pi malina do IoT koncentratora lub wysłania wiadomości do Twojej Pi malina za pomocą interfejsu wiersza polecenia, zobacz [Zarządzaj chmury urządzenia wiadomości z Centrum iothub explorer samouczek](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
