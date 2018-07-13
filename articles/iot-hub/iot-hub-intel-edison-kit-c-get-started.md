---
title: Urządzenia Intel Edison w chmurze (C) — urządzenia Intel Edison połączyć z usługą Azure IoT Hub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i połączyć z urządzeniem Intel Edison do usługi Azure IoT Hub dla urządzenia Intel Edison w celu wysyłania danych do platformy w chmurze w ramach tego samouczka.
author: rangv
manager: ''
keywords: Usługa Azure iot intel edison, intel edison Centrum iot, intel edison wysyłania danych do chmury firmy intel edison w chmurze
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 2efea8054320323df0e0eb603a20a5773d03cad8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38676626"
---
# <a name="connect-intel-edison-to-azure-iot-hub-c"></a>Łączenie urządzenia Intel Edison do IoT Hub (C) platformy Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku rozpoczniesz od podstawy pracy z urządzeniem Intel Edison uczenia. Następnie dowiesz się, jak bezproblemowe łączenie urządzeń do chmury przy użyciu [usługi Azure IoT Hub](iot-hub-what-is-iot-hub.md).

Nie masz jeszcze zestawu? Rozpocznij [tutaj](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Co należy zrobić

* Konfigurowanie urządzenia Intel Edison i i Grove modułów.
* Tworzenie Centrum IoT.
* Zarejestruj urządzenie Edison w usłudze IoT hub.
* Uruchom przykładową aplikację na Edison do wysyłania danych czujnika do usługi IoT hub.

Połącz urządzenia Intel Edison do usługi IoT hub, którą tworzysz. Następnie uruchom przykładową aplikację na Edison, aby zbierać dane temperatury i wilgotności z czujnika temperatury Grove. Na koniec wysyłania danych czujnika do usługi IoT hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć Centrum Azure IoT hub i uzyskać nowe parametry połączenia urządzenia.
* Jak połączyć Edison przy użyciu czujnika temperatury Grove.
* Jak zbierać dane z czujników po uruchomieniu aplikacji przykładowej na Edison.
* Jak wysyłać danych czujnika do usługi IoT hub.

## <a name="what-you-need"></a>Co jest potrzebne

![Co jest potrzebne](media/iot-hub-intel-edison-kit-c-get-started/0_kit.png)

* Tablica Intel Edison
* Karty rozszerzeń Arduino
* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [Utwórz bezpłatne konto wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w ciągu kilku minut.
* Komputer Mac lub komputerze z systemem Windows lub Linux.
* Połączenie internetowe.
* Do kabla USB typu B Micro
* Zasilacz prąd (DC). Zasilacza powinien sklasyfikowania w następujący sposób:
  - 7-15V KONTROLERA DOMENY
  - Co najmniej 1500mA
  - Numer pin center/wewnętrzny powinien być bieguna dodatni zasilania

Opcjonalne są następujące elementy:

* Grove podstawowy tarczy w wersji 2
* Grove - czujnik temperatury
* Kabel Grove
* Paski rozdzielacza lub śruby objęte opakowania, w tym dwóch śruby aby przyspieszyć modułu do karty rozszerzeń oraz wykonania czterech zestawów śruby i tworzywa odstępników.

> [!NOTE] 
Te elementy są opcjonalne, ponieważ obsługa próbki kodu symulowane dane czujników.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Konfigurowanie urządzenia Intel Edison

### <a name="assemble-your-board"></a>Złóż tablicy

Ta sekcja zawiera kroki, aby dołączyć modułu Intel® Edison do tablicy rozszerzenia.

1. Umieść modułu Intel® Edison w ramach kontur biały na tablicy rozszerzenia, wyrównywania luki w module za pomocą śruby na tablicy rozszerzenia.

2. W module tuż poniżej wyrażenie naciśnięty klawisz `What will you make?` dopóki nie poczujesz się bardzo proste.

   ![Złóż tablicy 2](media/iot-hub-intel-edison-kit-c-get-started/1_assemble_board2.jpg)

3. Dwa nuts szesnastkowych (uwzględniony w pakiecie) umożliwia zabezpieczanie modułu do tablicy rozszerzenia.

   ![Złóż tablicy 3](media/iot-hub-intel-edison-kit-c-get-started/2_assemble_board3.jpg)

4. Wstaw śruby w jednym otworów narożników na tablicy rozszerzenia. Skręcenie i podwyższenie poziomu jednego białe odstępników tworzywa na śrubę.

   ![Złóż tablicy 4](media/iot-hub-intel-edison-kit-c-get-started/3_assemble_board4.jpg)

5. Należy powtórzyć dla innych odstępników trzy rogu.

   ![Złóż tablicy 5](media/iot-hub-intel-edison-kit-c-get-started/4_assemble_board5.jpg)

Teraz jest składany tablicy.

   ![zmontowanych tablicy](media/iot-hub-intel-edison-kit-c-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Łączenie tarczy Base Grove i czujnik temperatury

1. Umieść tarczy Base Grove do tablicy. Upewnij się, że wszystkie kody PIN ściśle są podłączone do tablicy.
   
   ![Grove podstawowy Shield](media/iot-hub-intel-edison-kit-c-get-started/6_grove_base_sheild.jpg)

2. Łączenie czujników temperatury Grove na tarczy Base Grove przy użyciu kabla Grove **A0** portu.

   ![Nawiązać połączenie z czujnika temperatury](media/iot-hub-intel-edison-kit-c-get-started/7_temperature_sensor.jpg)
   
   ![Połączenie Edison i czujnik](media/iot-hub-intel-edison-kit-c-get-started/16_edion_sensor.png)

Teraz Twoja czujnik jest gotowy.

### <a name="power-up-edison"></a>Wzmocnij Edison

1. Podłącz zasilania.

   ![Podłącz zasilacz](media/iot-hub-intel-edison-kit-c-get-started/8_plug_power.jpg)

2. Zielona LED (oznaczony DS1 na tablicy rozszerzenia Arduino *) należy uruchomić i pozostają świeci.

3. Odczekaj minutę dla tablicy ukończyć rozruch.

   > [!NOTE]
   > Jeśli nie masz źródło zasilania kontrolera domeny, może nadal obsługiwać tablicy za pośrednictwem portu USB. Zobacz `Connect Edison to your computer` sekcji, aby uzyskać szczegółowe informacje. Najważniejsza tablicy w ten sposób może spowodować nieprzewidywalne zachowanie z tablicy, szczególnie w przypadku, gdy za pomocą sieci Wi-Fi lub prowadzenia motors.

### <a name="connect-edison-to-your-computer"></a>Podłącz Edison do komputera

1. Przełącz mikroprzełącznika kierunku dwóch wczesnych portów USB w dół tak, aby Edison jest w trybie urządzenia. Różnice między trybu urządzenia ani hosta, odwołać [tutaj](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Przełącz mikroprzełącznika w dół](media/iot-hub-intel-edison-kit-c-get-started/9_toggle_down_microswitch.jpg)

2. Podłącz wczesnych kabla USB do górnej wczesnych portu USB.

   ![Najważniejsze wczesnych port USB](media/iot-hub-intel-edison-kit-c-get-started/10_top_usbport.jpg)

3. Podłącz drugi koniec kabla USB do komputera.

   ![Komputer USB](media/iot-hub-intel-edison-kit-c-get-started/11_computer_usb.jpg)

4. Będzie wiadomo, że tablicy jest w pełni zainicjowany po komputer instaluje nowy dysk (podobnie do wstawiania karta SD do komputera).

## <a name="download-and-run-the-configuration-tool"></a>Pobierz i uruchom narzędzie konfiguracji
Pobierz najnowsze narzędzia konfiguracji z [ten link](https://software.intel.com/en-us/iot/hardware/edison/downloads) obszarze `Installers` nagłówka. Wykonywanie narzędzia i postępuj zgodnie z jej na ekranie instrukcjami, klikając przycisk Dalej, w razie potrzeby

### <a name="flash-firmware"></a>Oprogramowanie układowe Flash
1. Na `Set up options` kliknij `Flash Firmware`.
2. Wybierz obraz, który ma programu flash na tablicy, wykonując jedną z następujących czynności:
   - Aby pobrać i flash tablicy przy użyciu najnowszego obrazu oprogramowania układowego pochodzących od firmy Intel, wybierz `Download the latest image version xxxx`.
   - Aby flash tablicy za pomocą obrazu zapisywane już na komputerze, wybierz pozycję `Select the local image`. Przejdź do, a następnie wybierz obraz, który chcesz flash do tablicy.
3. Narzędzie Instalator będzie podejmować próby flash tablicy. Migające cały proces może potrwać do 10 minut.

### <a name="set-password"></a>Ustaw hasło
1. Na `Set up options` kliknij `Enable Security`.
2. Możesz ustawić niestandardową nazwę tablicy Intel® Edison. Jest to opcjonalne.
3. Wpisz hasło do tablicy, a następnie kliknij przycisk `Set password`.
4. Oznacz dół hasła, który jest używany później.

### <a name="connect-wi-fi"></a>Połączenia sieci Wi-Fi
1. Na `Set up options` kliknij `Connect Wi-Fi`. Poczekaj maksymalnie jedną minutę, co komputer skanuje pod kątem dostępnych sieci Wi-Fi.
2. Z `Detected Networks` listy rozwijanej wybierz sieci.
3. Z `Security` listy rozwijanej wybierz typ zabezpieczeń sieci.
4. Podaj swoje informacje logowania i hasło, a następnie kliknij przycisk `Configure Wi-Fi`.
5. Oznacz adres IP, który jest używany w dalszej części.

> [!NOTE]
> Upewnij się, że Edison jest podłączony do sieci z komputera. Komputer łączy się z Edison przy użyciu adresu IP.

   ![Nawiązać połączenie z czujnika temperatury](media/iot-hub-intel-edison-kit-c-get-started/12_configuration_tool.png)

Gratulacje! Pomyślnie skonfigurowano Edison.

## <a name="run-a-sample-application-on-intel-edison"></a>Uruchamianie przykładowej aplikacji na urządzenia Intel Edison

### <a name="prepare-the-azure-iot-device-sdk"></a>Przygotowywanie platformy Azure zestaw SDK urządzeń IoT

1. Aby nawiązać połączenie z urządzeniem Intel Edison, użyj jednej z następujących klientów SSH z komputera hosta. Adres IP jest za pomocą narzędzia konfiguracji i hasło zostało ustawione w tym narzędziu.
    - [PuTTY](http://www.putty.org/) for Windows.
    - Wbudowany klient SSH w systemie Ubuntu lub macOS (uruchamianie `ssh root@"the IP address"`).

2. Sklonuj przykładową aplikację klienta do Twojego urządzenia. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-edison-client-app.git
   ```

3. Następnie przejdź do folderu repozytorium, aby uruchomić następujące polecenie, aby skompilować zestaw SDK Azure IoT

   ```bash
   cd iot-hub-c-intel-edison-client-app
   sed -i -e 's/\r$//' buildSDK.sh
   chmod 755 buildSDK.sh
   ./buildSDK.sh
   ```

### <a name="configure-the-sample-application"></a>Konfigurowanie przykładowej aplikacji

1. Otwórz plik konfiguracji, uruchamiając następujące polecenia:

   ```bash
   nano config.h
   ```

   ![Plik konfiguracji](media/iot-hub-intel-edison-kit-c-get-started/13_configure_file.png)

   Istnieją dwa makra w tym pliku mogą configurate. Pierwsza z nich jest `INTERVAL`, która definiuje odstęp czasu między dwa komunikaty, które wysyłają do chmury. Drugi `SIMULATED_DATA`, który jest wartość logiczna wskazująca, czy używać danych z czujników symulowanych lub nie.

   Jeśli użytkownik **nie ma czujnika**ustaw `SIMULATED_DATA` wartość `1` się do aplikacji przykładowej, tworzenia i używania danych z symulowanych czujników.

2. Zapisz i zamknij, naciskając klawisz formantu O > Wprowadź > Control X.

### <a name="build-and-run-the-sample-application"></a>Kompilowanie i uruchamianie przykładowej aplikacji

1. Tworzenie przykładowej aplikacji, uruchamiając następujące polecenie:

   ```bash
   cmake . && make
   ```
   ![Dane wyjściowe kompilacji](media/iot-hub-intel-edison-kit-c-get-started/14_build_output.png)

1. Uruchamianie przykładowej aplikacji, uruchamiając następujące polecenie:

   ```bash
   sudo ./app '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Upewnij się, możesz kopiowania i wklejania parametry połączenia urządzenia w apostrofy.

Powinny być widoczne następujące dane wyjściowe, dane czujników i komunikaty, które są wysyłane do usługi IoT hub.

![Dane wyjściowe — dane czujników wysyłanych z urządzenia Intel Edison do Centrum IoT hub](media/iot-hub-intel-edison-kit-c-get-started/15_message_sent.png)

## <a name="next-steps"></a>Kolejne kroki

Po uruchomieniu aplikacji przykładowej, zbieranie danych z czujników i wysyłać je do Centrum IoT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
