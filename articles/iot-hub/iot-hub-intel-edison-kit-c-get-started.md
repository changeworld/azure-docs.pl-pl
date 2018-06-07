---
title: Edison Intel do chmury (C) - Połącz Edison Intel z Centrum IoT Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować i Intel Edison nawiązać połączenia z Centrum IoT Azure dla Edison Intel do wysyłania danych do platformy w chmurze platformy Azure, w tym samouczku.
author: rangv
manager: ''
keywords: Usługa Azure iot intel edison, intel edison Centrum iot, intel edison wysyłania danych do chmury, intel edison do chmury
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 2efea8054320323df0e0eb603a20a5773d03cad8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634883"
---
# <a name="connect-intel-edison-to-azure-iot-hub-c"></a>Nawiązać Intel Edison Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku Rozpocznij od uczenia podstawy pracy z Intel Edison. Następnie Dowiedz się jak bezproblemowo połączyć z urządzenia do chmury przy użyciu [Centrum IoT Azure](iot-hub-what-is-iot-hub.md).

Nie masz jeszcze zestawu? Uruchom [tutaj](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Co zrobić

* Konfiguracja Intel Edison i i Grove modułów.
* Tworzenie Centrum IoT.
* Zarejestruj urządzenie dla Edison w Centrum IoT.
* Uruchom przykładową aplikację na Edison do wysyłania danych czujnika do Centrum IoT.

Intel Edison nawiązać połączenia z Centrum IoT utworzony. Następnie uruchom przykładową aplikację na Edison do zbierania danych temperatury i wilgotności z czujnika temperatury Grove. Ponadto użytkownik wysyła dane czujników do Centrum IoT.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Sposób tworzenia Centrum Azure IoT i uzyskać nowy ciąg połączenia urządzenia.
* Jak nawiązać połączenia z czujnika temperatury Grove Edison.
* Jak zbierać dane czujników, uruchamiając na Edison przykładowej aplikacji.
* Jak wysyłać dane czujników do Centrum IoT.

## <a name="what-you-need"></a>Co jest potrzebne

![Co jest potrzebne](media/iot-hub-intel-edison-kit-c-get-started/0_kit.png)

* Tablica Intel Edison
* Karty rozszerzeń Arduino
* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [utworzyć bezpłatne konto próbne Azure](https://azure.microsoft.com/free/) za kilka minut.
* Mac lub komputer z systemem Windows lub Linux.
* Połączenie internetowe.
* B Micro do kabla USB A typu
* Zasilacz prądu (DC). Zasilacza powinny być sklasyfikowane w następujący sposób:
  - 7-15V KONTROLERA DOMENY
  - Co najmniej 1500mA
  - Numer pin center/wewnętrzny powinien być bieguna dodatnie zasilania

Opcjonalne są następujące elementy:

* Grove podstawowej tarczy V2
* Grove - czujnik temperatury
* Grove kabel
* Paski rozdzielacza lub śruby zawarte w pakiecie, łącznie z dwóch śruby aby przyspieszyć modułu do karty rozszerzeń i cztery zestawy śruby i tworzywa odstępników.

> [!NOTE] 
Te elementy są opcjonalne, ponieważ dane czujników symulowane obsługi próbki kodu.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Instalator Edison Intel

### <a name="assemble-your-board"></a>Złóż tablicy

Ta sekcja zawiera kroki, aby dołączyć modułu Intel® Edison do tablicy rozszerzenia.

1. Umieść modułu Intel® Edison w obrębie białe konspektu na tablicy rozszerzenia, wyrównywanie luk w module z śruby na tablicy rozszerzenia.

2. Naciśnij modułu poniżej słowa `What will you make?` dopóki uważasz, że bardzo proste.

   ![Złóż tablicy 2](media/iot-hub-intel-edison-kit-c-get-started/1_assemble_board2.jpg)

3. Użyj dwóch szesnastkowych poziomie NTS (uwzględniony w pakiecie) do zabezpieczania modułu do karty rozszerzeń.

   ![Złóż tablicy 3](media/iot-hub-intel-edison-kit-c-get-started/2_assemble_board3.jpg)

4. Wstaw śruby w jednym z luk narożników na tablicy rozszerzenia. Skręcenie i zwiększyć jedną białe plastikowe odstępników na śrubę.

   ![Złóż tablicy 4](media/iot-hub-intel-edison-kit-c-get-started/3_assemble_board4.jpg)

5. Powtórz dla innych odstępników trzy rogu.

   ![Złóż tablicy 5](media/iot-hub-intel-edison-kit-c-get-started/4_assemble_board5.jpg)

Teraz budowy tablicy.

   ![złożony tablicy](media/iot-hub-intel-edison-kit-c-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Połącz tarczy Base Grove i czujnik temperatury

1. Umieść tarczy Base Grove do tablicy. Upewnij się, że wszystkie numery PIN ściśle są podłączone do tablicy.
   
   ![Podstawowy tarczy Grove](media/iot-hub-intel-edison-kit-c-get-started/6_grove_base_sheild.jpg)

2. Czujnik temperatury Grove na tarczy Base Grove połączenia przy użyciu kabla Grove **A0** portu.

   ![Czujnik temperatury nawiązać połączenie](media/iot-hub-intel-edison-kit-c-get-started/7_temperature_sensor.jpg)
   
   ![Edison i czujnik połączenia](media/iot-hub-intel-edison-kit-c-get-started/16_edion_sensor.png)

Teraz z czujnika jest gotowa.

### <a name="power-up-edison"></a>Włącz Edison

1. Podłącz zasilania.

   ![Podłącz zasilacz](media/iot-hub-intel-edison-kit-c-get-started/8_plug_power.jpg)

2. Zielona LED (oznaczony DS1 na tablicy rozszerzenia Arduino *) powinien podświetlony i pozostać podświetlone.

3. Zaczekaj minutę dla tablicy ukończyć rozruch.

   > [!NOTE]
   > Jeśli nie masz zasilacz kontrolera domeny, można nadal zasilanie tablicy za pośrednictwem portu USB. Zobacz `Connect Edison to your computer` sekcji, aby uzyskać szczegółowe informacje. Włączanie tablicy w ten sposób może spowodować nieprzewidywalne zachowanie z tablicy, szczególnie w przypadku, gdy przy użyciu sieci Wi-Fi lub zwiększają motors.

### <a name="connect-edison-to-your-computer"></a>Łączenie się z komputerem Edison

1. Przełącz dół mikroprzełącznika kierunku dwóch micro portów USB, dzięki czemu Edison jest w trybie urządzenia. Różnice między trybu urządzenia ani hosta, odwołanie [tutaj](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Przełącz mikroprzełącznika w dół](media/iot-hub-intel-edison-kit-c-get-started/9_toggle_down_microswitch.jpg)

2. Podłącz kabel USB micro do górnej micro portu USB.

   ![Górny micro portu USB](media/iot-hub-intel-edison-kit-c-get-started/10_top_usbport.jpg)

3. Podłącz drugi koniec kabla USB do komputera.

   ![Komputer USB](media/iot-hub-intel-edison-kit-c-get-started/11_computer_usb.jpg)

4. Będzie wiadomo, że tablicy jest w pełni zainicjowany, gdy komputer instaluje nowy dysk (podobne jak w przypadku Wstawianie karta SD do komputera).

## <a name="download-and-run-the-configuration-tool"></a>Pobierz i uruchom narzędzie konfiguracji
Pobierz najnowsze narzędzia konfiguracji z [to łącze](https://software.intel.com/en-us/iot/hardware/edison/downloads) kategorii `Installers` nagłówka. Wykonywanie narzędzia i postępuj zgodnie z jego na ekranie instrukcjami, klikając przycisk Dalej, w razie potrzeby

### <a name="flash-firmware"></a>Oprogramowanie układowe Flash
1. Na `Set up options` kliknij `Flash Firmware`.
2. Wybierz obraz do flash na tablicy, wykonując jedną z następujących czynności:
   - Aby pobrać i flash tablicy przy użyciu najnowszej dostępnej w sklepie Intel obrazu oprogramowania układowego, wybierz `Download the latest image version xxxx`.
   - Aby flash tablicy z obrazem, który został zapisany już na komputerze, wybierz `Select the local image`. Wyszukaj i wybierz obraz, który ma być flash do tablicy.
3. Narzędzie instalacji będzie podejmować próby flash tablicy. Cały proces miga może potrwać do 10 minut.

### <a name="set-password"></a>Ustaw hasło
1. Na `Set up options` kliknij `Enable Security`.
2. Można ustawić niestandardową nazwę tablicy Intel® Edison. Jest to opcjonalne.
3. Wpisz hasło do tablicy, a następnie kliknij przycisk `Set password`.
4. Oznacz hasła, które jest później używany w dół.

### <a name="connect-wi-fi"></a>Połączenia sieci Wi-Fi
1. Na `Set up options` kliknij `Connect Wi-Fi`. Poczekaj maksymalnie jedną minutę, co komputer szuka dostępnych sieci Wi-Fi.
2. Z `Detected Networks` listy rozwijanej wybierz sieci.
3. Z `Security` listy rozwijanej wybierz typ zabezpieczeń sieci.
4. Udostępnić informacje logowania i hasło, a następnie kliknij przycisk `Configure Wi-Fi`.
5. Zaznacz adres IP, który jest później używany.

> [!NOTE]
> Upewnij się, że Edison jest podłączony do sieci z komputera. Komputer łączy się z Edison przy użyciu adresu IP.

   ![Czujnik temperatury nawiązać połączenie](media/iot-hub-intel-edison-kit-c-get-started/12_configuration_tool.png)

Gratulacje! Pomyślnie skonfigurowano Edison.

## <a name="run-a-sample-application-on-intel-edison"></a>Uruchom przykładową aplikację na Intel Edison

### <a name="prepare-the-azure-iot-device-sdk"></a>Przygotuj urządzenie IoT Azure SDK

1. Aby nawiązać połączenie z Edison Intel, użyj jednej z następujących klientów SSH z komputera hosta. Adres IP jest z narzędzia do konfiguracji i hasło jest ustawione w tym narzędziem.
    - [PuTTY](http://www.putty.org/) dla systemu Windows.
    - Wbudowany klient SSH na Ubuntu lub macOS (Uruchom `ssh root@"the IP address"`).

2. Klonowanie przykładową aplikację klienta do urządzenia. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-intel-edison-client-app.git
   ```

3. Następnie przejdź do folderu repozytorium, aby uruchomić następujące polecenie, aby utworzyć zestaw SDK usługi Azure IoT

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

   Istnieją dwa makra w tym pliku mogą configurate. Pierwsza z nich jest `INTERVAL`, który definiuje odstęp czasu między dwa komunikaty, które wysyłają do chmury. Drugi `SIMULATED_DATA`, która jest wartość logiczna, czy należy użyć danych czujnika symulowane lub nie.

   Jeśli użytkownik **nie ma czujnika**, ustaw `SIMULATED_DATA` do wartości `1` dokonanie przykładowej aplikacji, tworzenia i używania danych czujnika symulowane.

2. Zapisz i zamknij naciskając formantu O > Enter > Control X.

### <a name="build-and-run-the-sample-application"></a>Tworzenie i uruchamianie przykładowej aplikacji

1. Tworzenie przykładowej aplikacji, uruchamiając następujące polecenie:

   ```bash
   cmake . && make
   ```
   ![Dane wyjściowe kompilacji](media/iot-hub-intel-edison-kit-c-get-started/14_build_output.png)

1. Uruchom przykładową aplikację, uruchamiając następujące polecenie:

   ```bash
   sudo ./app '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Upewnij się, możesz kopiowania i wklejania ciąg połączenia urządzenia w pojedynczy cudzysłów.

Powinny zostać wyświetlone następujące dane wyjściowe, który zawiera dane czujników i komunikaty, które są wysyłane do Centrum IoT.

![Dane wyjściowe — dane czujników wysłanych z Intel Edison Centrum IoT](media/iot-hub-intel-edison-kit-c-get-started/15_message_sent.png)

## <a name="next-steps"></a>Kolejne kroki

Uruchomiono przykładowej aplikacji do zbierania danych czujników i wysyłania go do Centrum IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
