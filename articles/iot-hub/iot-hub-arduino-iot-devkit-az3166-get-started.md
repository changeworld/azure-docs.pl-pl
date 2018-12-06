---
title: IoT Mxchip z chmurą — nawiązać połączenie z IoT DevKit az3166 usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się, jak skonfigurować i połączyć DevKit az3166 usługi IoT dla usługi Azure IoT Hub, dzięki czemu może wysłać dane do platformy w chmurze.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 08/27/2018
ms.author: wesmc
ms.openlocfilehash: e6412ce36110f2e9a800262c968039112f2e4b1c
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52955687"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit az3166 usługi nawiązać połączenie z usługi Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Możesz użyć [zestawu deweloperskiego IoT Mxchip](https://microsoft.github.io/azure-iot-developer-kit/) do opracowywania i prototypów rozwiązań Internetu rzeczy (IoT), które korzystać z usług Microsoft Azure. Zawiera on tablicę z platformą arduino zaawansowanych urządzeń peryferyjnych i czujniki, pakietu tablicy typu open source i rosnącej [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Co należy zrobić

Połącz [Mxchip](https://microsoft.github.io/azure-iot-developer-kit/) do usługi Azure IoT hub, którą tworzysz, zbierać dane temperatury i wilgotności z czujników i wysłać dane do usługi IoT hub.

Nie masz jeszcze Mxchip? Spróbuj [symulator Mxchip](https://azure-samples.github.io/iot-devkit-web-simulator/) lub [zakupu Mxchip](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak mxchip IoT DevKit nawiązać połączenie z punktem dostępu bezprzewodowego i przygotowywanie środowiska projektowego.
* Jak utworzyć Centrum IoT hub i rejestrowanie urządzenia dla zestawu deweloperskiego IoT Mxchip.
* Jak zbierać dane z czujników po uruchomieniu aplikacji przykładowej na zestawu deweloperskiego IoT Mxchip.
* Jak wysyłać danych czujnika do usługi IoT hub.

## <a name="what-you-need"></a>Co jest potrzebne

* Tablica zestawu deweloperskiego IoT Mxchip za pomocą kabla Micro USB. [Pobierz teraz](https://aka.ms/iot-devkit-purchase).
* Komputer z systemem Windows 10 lub macOS 10.10 +.
* Aktywna subskrypcja platformy Azure. [Aktywowanie bezpłatnej 30-dniowej wersji próbnej Microsoft Azure konto](https://azureinfo.microsoft.com/us-freetrial.html).
  
## <a name="prepare-your-hardware"></a>Przygotowania sprzętu

Podpinanie następujący sprzęt na komputerze:

* Tablica Mxchip
* Kabel Micro USB

![Wymagany sprzęt](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Aby połączyć Mxchip z komputera, wykonaj następujące kroki:

1. Połącz koniec USB do komputera.

2. Łączyć się Mxchip z zakończenia Micro-USB.

3. Zielona LED moc potwierdza połączenia.

   ![Połączenia sprzętowe](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Konfigurowanie sieci Wi-Fi

Projekty IoT, zależy od łączności z Internetem. Skorzystaj z poniższych instrukcji, aby skonfigurować Mxchip nawiązać połączenia z sieci Wi-Fi.

### <a name="enter-ap-mode"></a>Tryb AP

Wciśnij przycisk B, wypychania i zwolnieniu przycisku resetowania, a następnie zwolnij przycisk B. Twoje Mxchip przechodzi w tryb wschodni Region Azji i konfigurowania sieci Wi-Fi. Na ekranie zostanie wyświetlony identyfikator zestawu usług (SSID) Mxchip i adresu IP portalu w konfiguracji.

![Resetuj przycisk, B i identyfikator SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Nawiązać połączenie z Mxchip AP

Teraz za pomocą innego urządzenia sieci Wi-Fi, włączone (komputera lub telefonu), połączyć z SSID Mxchip (wyróżnione na poprzedniej ilustracji). Hasło należy pozostawić pusty.

![Informacje o sieci i przycisk Połącz](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Konfigurowanie sieci Wi-Fi dla Mxchip

Otwórz adres IP wyświetlany na ekranie Mxchip na komputerze lub w przeglądarce telefonu komórkowego, wybierz sieć Wi-Fi, która ma Mxchip, aby nawiązać połączenie, a następnie wpisz hasło. Wybierz przycisk **Połącz**.

![Pole hasła i przycisk Połącz](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Gdy połączenie zakończy się powodzeniem, Mxchip ponowny rozruch w ciągu kilku sekund. Nazwa sieci Wi-Fi i adres IP zostanie wyświetlony na ekranie:

![Nazwa sieci Wi-Fi i adres IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> Adres IP wyświetlany w zdjęcia mogą być niezgodne z rzeczywistego adresu IP, które są przypisane i wyświetlane na ekranie Mxchip. Jest to normalne, ponieważ sieci Wi-Fi dynamiczne przypisywanie adresów IP za pomocą protokołu DHCP.

Po skonfigurowaniu sieci Wi-Fi poświadczeń zostanie utrzymany na urządzeniu na potrzeby tego połączenia, nawet jeśli urządzenie jest odłączone. Na przykład jeśli Konfigurowanie Mxchip dla sieci Wi-Fi w domu, a następnie podjąć Mxchip do urzędu należy ponownie skonfigurować tryb AP (rozpoczyna się od w sekcji "Wprowadź AP tryb") do łączenia z Mxchip sieci Wi-Fi usługi Office. 

## <a name="start-using-the-devkit"></a>Rozpoczynanie korzystania z Mxchip

Domyślna aplikacja uruchomiona na Mxchip sprawdza najnowszą wersję oprogramowania układowego i wyświetla dane czujników diagnostyki dla Ciebie.

### <a name="upgrade-to-the-latest-firmware"></a>Uaktualnienie do najnowszego oprogramowania układowego

> [!NOTE]
> Od wersji 1.1 Mxchip umożliwia bezpieczne ST w programu inicjującego. Należy uaktualnić oprogramowanie układowe, jeśli używasz wersji wcześniejszej niż w wersji 1.1.

Jeśli konieczne jest uaktualnienie oprogramowania układowego na ekranie będzie wyświetlany wersji bieżącej i najnowsze oprogramowanie układowe. Aby przeprowadzić uaktualnienie, wykonaj [uaktualnienie oprogramowania układowego](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) przewodnik.

![Wyświetlanie wersji oprogramowania układowego bieżące i planowane najnowsze](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> Jest to jednorazowa nakładu pracy. Po Rozpocznij programowanie na Mxchip i przekazać aplikację, najnowsze oprogramowanie układowe będą pochodzić z aplikacją.

### <a name="test-various-sensors"></a>Testowanie różne czujniki

Naciśnij przycisk B, aby przetestować czujników. Kontynuuj, naciskając klawisz i zwolnieniem przycisku B, aby przechodzić między każdym czujnika.

![Wyświetlanie przycisku B i czujnik](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

### <a name="install-azure-iot-workbench"></a>Zainstaluj środowisko robocze usługi Azure IoT

Firma Microsoft zaleca [usługi Azure IoT Workbench](https://aka.ms/iot-workbench) rozszerzenie dla programu Visual Studio Code umożliwiające tworzenie aplikacji na Mxchip.

Środowisko robocze usługi Azure IoT udostępnia zintegrowane środowisko do opracowywania rozwiązań IoT. Pomaga ona zarówno na urządzeniu i w chmurze tworzenie rozwiązań za pomocą usługi Azure IoT i innych usług. Możesz obejrzeć to [wideo Channel 9](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code) można się z omówieniem jej działania.

Wykonaj następujące kroki, aby przygotować środowisko programistyczne dla Mxchip:

1. Pobierz i zainstaluj [środowisku IDE Arduino](https://www.arduino.cc/en/Main/Software). Udostępnia niezbędne łańcucha narzędzi do kompilowania i przekazywanie kodu Arduino.
    * **Windows**: Korzystanie z Instalatora Windows w wersji. Nie należy instalować ze sklepu z aplikacjami.
    * **System macOS**: przeciągnij i upuść wyodrębnione **Arduino.app** do `/Applications` folderu.
    * **Ubuntu**: takie jak Rozpakuj go do folderu `$HOME/Downloads/arduino-1.8.5`

2. Zainstaluj [programu Visual Studio Code](https://code.visualstudio.com/), Edytor kodu źródłowego dla wielu platform, za pomocą zaawansowanych deweloperów narzędzi, takich jak uzupełniania kodu IntelliSense i debugowania.

3. Poszukaj **usługi Azure IoT Workbench** rynku rozszerzenia i zainstalować ją.
    ![Zainstaluj aplikację Azure IoT Workbench](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-workbench.png) wraz z aplikacji IoT Workbench, inne rozszerzenia zależne zostaną zainstalowane.

4. Konfigurowanie programu Visual Studio Code przy użyciu ustawień Arduino. 

    W programie Visual Studio Code kliknij **Plik > Preferencje > Ustawienia**. Następnie kliknij przycisk **...**  i **Otwórz settings.json**. 
    
    Dodaj następujące wiersze do skonfigurowania Arduino w zależności od używanej platformy: 

    * **Windows**:
      
        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **System macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:
    
        Zastąp **{username}** symbol zastępczy poniżej swoją nazwę użytkownika.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.5",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

5. Kliknij przycisk `F1` aby otworzyć paletę poleceń, typ i wybierz **Arduino: Menedżer tablicy**. Wyszukaj **az3166 usługi** i zainstaluj najnowszą wersję.
    ![Zainstaluj zestaw SDK Mxchip](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-sdk.png)

### <a name="install-st-link-drivers"></a>Zainstaluj sterowniki łącze ST

[ST-łącze/V2](http://www.st.com/en/development-tools/st-link-v2.html) jest interfejsem USB, który mxchip IoT DevKit używa do komunikacji z komputera deweloperskiego. Wykonaj kroki specyficzne dla systemu operacyjnego, aby zezwolić na dostęp do komputera, na urządzeniu.

* **Windows**: Pobierz i zainstaluj sterownik USB z [STMicroelectronics witryny sieci Web](http://www.st.com/en/development-tools/stsw-link009.html).
* **System macOS**: sterownik nie jest wymagany dla systemu macOS.
* **Ubuntu**: Uruchom następujące polecenie w terminalu i wylogowania i zaloguj się na zmianę grupy zaczęły obowiązywać:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Teraz wszystko jest ustawione przy użyciu przygotowywanie i konfigurowanie środowiska projektowego. Daj nam stworzyć próbkę "Hello World" dla IoT: wysyłanie danych telemetrycznych temperatury do usługi Azure IoT Hub.

## <a name="build-your-first-project"></a>Tworzenie pierwszego projektu

1. Upewnij się, jest Twoja mxchip IoT DevKit **niepołączony** do komputera. Najpierw uruchom program VS Code, a następnie podłącz Mxchip do komputera.

1. Na pasku stanu w prawym dolnym Sprawdź **az3166 usługi zestawu DEWELOPERSKIEGO** jest przedstawiana w postaci wybranej tablicy i port szeregowy przy użyciu **STMicroelectronics** jest używany.
    ![Wybierz tablicy i COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-board.png)

1. Kliknij przycisk `F1` aby otworzyć paletę poleceń, typ i wybierz **IoT Workbench: przykłady**. Następnie wybierz pozycję **mxchip IoT DevKit** jako tablica.

1. Na stronie Przykłady aplikacji Workbench IoT, Znajdź **wprowadzenie** i kliknij przycisk **Otwórz przykładowy**. Następnie wybiera ścieżki domyślnej, aby pobrać przykładowy kod.
    ![Otwórz przykładowy](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

1. Jeśli nie masz w programie VS Code zainstalowane rozszerzenie Arduino, kliknij przycisk **zainstalować** w okienku powiadomienia.
    ![Zainstaluj rozszerzenie Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino-ext.png)

1. W nowym oknie otwartym projekcie, kliknij przycisk `F1` aby otworzyć paletę poleceń, typ i wybierz **IoT Workbench: chmura**, a następnie wybierz **świadczenia usługi Azure**. Postępuj zgodnie z przewodnika krok po kroku do zakończenia inicjowania obsługi usługi Azure IoT Hub i utworzenie urządzenia.
    ![Aprowizowanie chmury](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/cloud-provision.png)

1. Kliknij przycisk `F1` aby otworzyć paletę poleceń, typ i wybierz **IoT Workbench: urządzenie**, a następnie wybierz **konfiguracji ustawień urządzenia > Parametry połączenia urządzenia konfiguracji > Wybierz IoT Hub parametry połączenia urządzenia**.

1. Na Mxchip, przytrzymaj wciśnięty **przycisk A**, wypychania i wersji **resetowania** przycisk, a następnie zwolnij **przycisk A**. Twoje Mxchip przechodzi w tryb konfiguracji i zapisuje parametry połączenia.
    ![Parametry połączenia](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Kliknij przycisk `F1` wpisz ponownie, a następnie wybierz **IoT Workbench: urządzenie**, a następnie wybierz **przekazać urządzenia**.
    ![Przekazywanie Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

Mxchip rozruchu i uruchamiania kodu.

> [!NOTE]
> Jeśli istnieje, błędy lub przerw w działaniu, zawsze można odzyskać, uruchamiając polecenie ponownie.

## <a name="test-the-project"></a>Projekt testowy

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Wyświetl dane telemetryczne wysyłane do usługi Azure IoT Hub

Kliknij ikonę wtyczki zasilania, na pasku stanu, aby otworzyć Monitor szeregowego: ![Serial monitora](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Przykładowa aplikacja jest uruchomiona pomyślnie, gdy pojawi się następujące wyniki:

* Serial Monitor wyświetla komunikat wysłany do usługi IoT Hub.
* Migające jest dioda LED na zestawu deweloperskiego IoT Mxchip.

![Dane wyjściowe Serial monitora](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Widok danych telemetrycznych odebranych przez usługę Azure IoT Hub

Możesz użyć [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) do monitorowania komunikatów wysyłanych z urządzenia do chmury (D2C) w usłudze IoT Hub.

1. W programie Visual Studio Code, poszukaj **Azure IoT Toolkit** rynku rozszerzenia i zainstalować ją.

1. Zaloguj się [witryny Azure portal](https://portal.azure.com/), Znajdź Centrum IoT utworzonego.
    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. W **zasady dostępu współdzielonego** okienku kliknij **zasad iothubowner**i zanotuj parametry połączenia Centrum IoT Hub.
    ![Parametry połączenia w usłudze Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. W programie Visual Studio Code rozwiń **AZURE IOT HUB DEVICES** w lewym dolnym rogu, kliknij polecenie **Ustaw parametry połączenia Centrum IoT**.
    ![Ustawianie parametrów połączenia usługi Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-conn-string.png)

1. Kliknij przycisk **IoT: Rozpocznij monitorowania komunikatu D2C** w menu kontekstowym.

1. W **dane wyjściowe** okienku widać komunikaty D2C przychodzące do usługi IoT Hub.
    ![Komunikatu D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-console.png)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, możesz sprawdzić rozwiązania w [— często zadawane pytania dla Mxchip IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub się z nami od [dotyczącym oprogramowania Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Możesz również dostarczyć nam opinię, pozostawiając komentarz na tej stronie.

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie nawiązano zestawu deweloperskiego IoT Mxchip z Centrum IoT i danych przechwyconych czujnika zostało wysłane do Centrum IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
