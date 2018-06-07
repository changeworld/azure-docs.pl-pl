---
title: Pi malinowe do chmury (Node.js) - Połącz Pi malina z Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i Pi malina nawiązać połączenia z Centrum IoT Azure pi malina do wysyłania danych do platformy w chmurze platformy Azure, w tym samouczku.
author: rangv
manager: ''
keywords: pi Azure iot malinowe, Centrum iot malinowe pi, pi malinowe wysyłania danych do chmury, malinowe pi do chmury
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 034a864cd98bb383e97f1def8eff1c6f9842a554
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635750"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Pi malinowe nawiązać połączenia z Centrum IoT Azure (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku Rozpocznij od uczenia podstawy pracy z Pi malina, który działa Raspbian. Następnie Dowiedz się jak bezproblemowo połączyć z urządzenia do chmury przy użyciu [Centrum IoT Azure](iot-hub-what-is-iot-hub.md). Dla przykładów Windows 10 IoT Core, przejdź do [Centrum deweloperów systemu Windows](http://www.windowsondevices.com/).

Nie masz jeszcze zestawu? Spróbuj [symulatora online Pi malina](iot-hub-raspberry-pi-web-simulator-get-started.md). Lub kupowanie nowej kit [tutaj](https://azure.microsoft.com/develop/iot/starter-kits).


## <a name="what-you-do"></a>Co zrobić

* Tworzenie Centrum IoT.
* Zarejestruj urządzenie pi w Centrum IoT.
* Konfigurowanie malina Pi.
* Uruchom przykładową aplikację na Pi do wysyłania danych czujnika do Centrum IoT.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Sposób tworzenia Centrum Azure IoT i uzyskać nowy ciąg połączenia urządzenia.
* Jak nawiązać połączenia z czujnika BME280 Pi.
* Jak zbierać dane czujników, uruchamiając przykładową aplikację na Pi.
* Jak wysyłać dane czujników do Centrum IoT.

## <a name="what-you-need"></a>Co jest potrzebne

![Co jest potrzebne](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* Malina Pi 2 lub 3 Pi malina tablicy.
* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [utworzyć bezpłatne konto próbne Azure](https://azure.microsoft.com/free/) za kilka minut.
* Monitor, USB klawiatury i myszy, który łączy do Pi.
* Mac lub komputera z systemem Windows lub Linux.
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
Jeśli nie masz elementy opcjonalne, można użyć danych czujnika symulowane.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Konfigurowanie Pi malina

### <a name="install-the-raspbian-operating-system-for-pi"></a>Instalowanie systemu operacyjnego Raspbian pi

Przygotuj karty microSD instalacji obrazu Raspbian.

1. Pobierz Raspbian.
   1. [Pobierz Raspbian Stretch](http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/) (pliku .zip).

   > [!WARNING]
   > Użyj powyżej łącze aby pobrać `raspbian-2017-07-5` obraz zip. Najnowszą wersję obrazów Raspbian ma niektóre znane problemy z węzłem Pi połączeń, które mogą spowodować niepowodzenie w innej.
   1. Wyodrębnij obrazu Raspbian do folderu na komputerze.

1. Zainstaluj Raspbian karty microSD.
   1. [Pobierz i zainstaluj narzędzie palnika karty Etcher SD](https://etcher.io/).
   1. Uruchom Etcher i wybierz obraz Raspbian, który został wyodrębniony w kroku 1.
   1. Wybierz dysk karty microSD. Etcher już być może wybrano poprawnego dysku.
   1. Kliknij przycisk Flash instalowanie Raspbian karty microSD.
   1. Karta microSD należy usunąć z komputera po zakończeniu instalacji. Jest bezpiecznie usunąć karta microSD bezpośrednio, ponieważ Etcher automatycznie wysuwa lub odinstalowuje karty microSD po zakończeniu.
   1. Włóż kartę microSD do Pi.

### <a name="enable-ssh-and-i2c"></a>Włącz SSH i I2C

1. Pi nawiązać monitora, klawiatury i myszy. 
1. Start Pi, a następnie zaloguj Raspbian przy użyciu `pi` jako nazwy użytkownika i `raspberry` jako hasło.
1. Kliknij ikonę malinowe > **preferencje** > **malina Pi konfiguracji**.

   ![Menu Preferencje Raspbian](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

1. Na **interfejsów** ustaw **I2C** i **SSH** do **włączyć**, a następnie kliknij przycisk **OK**. Jeśli nie masz fizycznych czujników i chcesz użyć danych czujnika symulowane, ten krok jest opcjonalny.

   ![Włącz I2C i SSH na malinowe Pi](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
Aby włączyć SSH i I2C, można znaleźć więcej dokumentacji na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) i [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Połącz z czujnika do Pi

Użyj przewodów breadboard i zworek LED i nawiązywać BME280 Pi w następujący sposób. Jeśli nie masz czujnika, [pominąć tę sekcję](#connect-pi-to-the-network).

![Pi malina i czujnik połączenia](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

Czujnik BME280 może zbierać dane temperatury i wilgotności. LED miga, gdy urządzenie wysyła komunikat do chmury. 

Czujnik numery PIN można użyć następujących połączeń:

| Uruchom (czujnik & LED)     | Końcowy (tablica)            | Kolor kabel   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin: 5 GB/S)             | 3, 3V PWR (Pin 1)       | Białe kabel   |
| GND (Pin 7 GB/S)             | GND (Pin 6)            | Brązowy kabel   |
| SDI (Pin 10 GB/S)            | I2C1 SDA (Pin 3)       | Czerwony kabel     |
| SCK (Pin 8 GB/S)             | I2C1 SCL (Pin 5)       | Kabel pomarańczowy  |
| VDD LED (Pin 18F)        | Interfejs GPIO 24 (Pin 18)       | Białe kabel   |
| GND LED (Pin 17F)        | GND (Pin 20)           | Czarne kabel   |

Kliknij, aby wyświetlić [malina Pi 2 i 3 mapowania kodu pin](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) użytkownikowi.

Po pomyślnie nawiązano połączenie BME280 Twojego Pi malina, należy go jak poniżej obrazu.

![Pi połączonych i BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Połączenie z siecią Pi

Włącz Pi przy użyciu micro kabla USB i zasilania. Łączenie Pi sieci przewodowej lub wykonaj przy użyciu kabla Ethernet [instrukcji Foundation Pi malina](https://www.raspberrypi.org/learning/software-guide/wifi/) do nawiązania połączenia z siecią bezprzewodową Pi. Po Twoje Pi została pomyślnie podłączona do sieci, należy pamiętać o [adres IP Twojego Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Podłączony do sieci przewodowej](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Upewnij się, że Pi jest podłączony do sieci z komputera. Na przykład jeśli komputer jest połączony z siecią bezprzewodową, gdy Pi jest połączony z siecią przewodową, może nie być wyświetlana w danych wyjściowych devdisco adres IP.

## <a name="run-a-sample-application-on-pi"></a>Uruchom przykładową aplikację na Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Sklonuj przykładową aplikację i zainstalować wstępnie wymagane pakiety

1. Nawiązywanie połączenia z Pi malina jeden z następujących klientów SSH z komputera-hosta:
   
   **Użytkownicy systemu Windows**
   1. Pobierz i zainstaluj [PuTTY](http://www.putty.org/) dla systemu Windows. 
   1. Skopiuj adres IP z sekcji Pi na nazwę hosta (lub adres IP) i wybierz typ połączenia SSH.
   
   ![Programu puTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac i Ubuntu użytkowników**
   
   Ubuntu lub macOS, korzystając z wbudowanego klienta SSH. Może być konieczne uruchomienie `ssh pi@<ip address of pi>` nawiązać Pi za pośrednictwem protokołu SSH.
   > [!NOTE] 
   Domyślna nazwa użytkownika to `pi` , a hasło to `raspberry`.

1. Zainstaluj z Pi Node.js i NPM.
   
   Najpierw sprawdź wersję środowiska Node.js. 
   
   ```bash
   node -v
   ```

   Jeśli wersja jest starsza niż 4.x lub jeśli w Twojej Pi nie nie Node.js, zainstaluj najnowszą wersję.

   ```bash
   curl -sL http://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

1. Klonowanie przykładowej aplikacji.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

1. Zainstaluj wszystkie pakiety przykładowej. Instalacja obejmuje urządzenia Azure IoT SDK, czujnik BME280 biblioteki i biblioteki połączeń Pi.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   Może upłynąć kilka minut na zakończenie tego procesu instalacji, w zależności od połączenia sieciowego.

### <a name="configure-the-sample-application"></a>Konfigurowanie przykładowej aplikacji

1. Otwórz plik konfiguracji, uruchamiając następujące polecenia:

   ```bash
   nano config.json
   ```

   ![Plik konfiguracji](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   Istnieją dwa elementy w tym pliku, które można skonfigurować. Pierwsza z nich jest `interval`, która określa interwał (w milisekundach) między wiadomości wysyłane do chmury. Drugim jest `simulatedData`, który jest wartość logiczna, czy należy użyć danych czujnika symulowane lub nie.

   Jeśli użytkownik **nie ma czujnika**, ustaw `simulatedData` do wartości `true` dokonanie przykładowej aplikacji, tworzenia i używania danych czujnika symulowane.

1. Zapisz i zamknij wpisując formantu O > Enter > Control X.

### <a name="run-the-sample-application"></a>Uruchamianie przykładowej aplikacji

Uruchom przykładową aplikację, uruchamiając następujące polecenie:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Upewnij się, możesz kopiowania i wklejania ciąg połączenia urządzenia w pojedynczy cudzysłów.


Powinny zostać wyświetlone następujące dane wyjściowe, który zawiera dane czujników i komunikaty, które są wysyłane do Centrum IoT.

![Dane wyjściowe — dane czujników wysłanych z Pi malina Centrum IoT](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>Kolejne kroki

Uruchomiono przykładowej aplikacji do zbierania danych czujników i wysyłania go do Centrum IoT. Wyświetlanie komunikatów wysłanych do Centrum IoT z Pi malina lub wysyłać wiadomości do Twojej Pi malina za pomocą interfejsu wiersza polecenia, zobacz [Zarządzaj chmury urządzenia wiadomości z Centrum iothub explorer samouczek](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
