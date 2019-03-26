---
title: IoT Mxchip z chmurą — nawiązać połączenie z IoT DevKit az3166 usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się, jak skonfigurować i połączyć DevKit az3166 usługi IoT dla usługi Azure IoT Hub, dzięki czemu może wysłać dane do platformy w chmurze.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/21/2019
ms.author: wesmc
ms.openlocfilehash: 0c6189dfd02a4bdd3662f4fa50dbb812fe995884
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438486"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit az3166 usługi nawiązać połączenie z usługi Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Możesz użyć [zestawu deweloperskiego IoT Mxchip](https://microsoft.github.io/azure-iot-developer-kit/) do opracowywania i prototypów rozwiązań Internetu rzeczy (IoT), które korzystać z usług Microsoft Azure. Zawiera on tablicę z platformą arduino zaawansowanych urządzeń peryferyjnych i czujniki, pakietu tablicy typu open source i rosnącej [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Co należy zrobić

W tym artykule używasz [programu Visual Studio Code](https://code.visualstudio.com/), źródła dla wielu platform code editor, wraz z [narzędzia IoT Azure](https://aka.ms/azure-iot-tools) pakietu rozszerzenia.

Mxchip połączy się do usługi Azure IoT hub, którą tworzysz. Zbieranie danych temperatury i wilgotności z czujników i wysłać dane do usługi IoT hub.

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

![Ustaw tryb AP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-ap-mode.gif)

### <a name="connect-to-devkit-ap"></a>Nawiązać połączenie z Mxchip AP

Teraz za pomocą innego urządzenia sieci Wi-Fi, włączone (komputera lub telefonu), połączyć z SSID Mxchip (wyróżnione na poprzedniej ilustracji). Hasło należy pozostawić pusty.

![Informacje o sieci i przycisk Połącz](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Konfigurowanie sieci Wi-Fi dla Mxchip

Otwórz adres IP wyświetlany na ekranie Mxchip na komputerze lub w przeglądarce telefonu komórkowego, wybierz sieć Wi-Fi, która ma Mxchip, aby nawiązać połączenie, a następnie wpisz hasło. Wybierz przycisk **Połącz**.

![Pole hasła i przycisk Połącz](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Gdy połączenie zakończy się powodzeniem, Mxchip ponowny rozruch w ciągu kilku sekund. Nazwa sieci Wi-Fi i adres IP zostanie wyświetlony na ekranie:

![Nazwa sieci Wi-Fi i adres IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE]
> Aby mxchip IoT DevKit pracy należy sieci 2,4 GHz. Moduł sieci Wi-Fi na mxchip IoT DevKit nie jest zgodny z siecią 5GHz. Sprawdź [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) Aby uzyskać więcej informacji.

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

### <a name="install-azure-iot-tools"></a>Instalowanie narzędzi usługi Azure IoT

W tej sekcji zostanie zainstalowana [środowisku IDE Arduino](https://www.arduino.cc/en/Main/Software) wraz z [programu Visual Studio Code](https://code.visualstudio.com/), Edytor kodu źródłowego dla wielu platform.

Zostanie również zainstalowana [narzędzia IoT Azure](https://aka.ms/azure-iot-tools) pakiet rozszerzenia programu Visual Studio Code. Firma Microsoft zaleca używanie [narzędzia IoT Azure](https://aka.ms/azure-iot-tools) pakiet rozszerzenia programu Visual Studio Code do tworzenia aplikacji na Mxchip. Pakiet rozszerzenia Azure IoT Tools zawiera [Workbench urządzenia IoT Azure](https://aka.ms/iot-workbench) używany do tworzenia i debugowania na różnych urządzeniach mxchip IoT. [Azure IoT Hub Toolkit](https://aka.ms/iot-toolkit), również składnikiem pakietu rozszerzenia Azure IoT Tools, jest używany do zarządzania i wchodzić w interakcje z usługą Azure IoT Hubs.

Możesz obserwować te [witryny Channel 9](https://channel9.msdn.com/) filmy wideo, aby mieć Przegląd temat ich działania:
* [Wprowadzenie do nowego rozszerzenia IoT Workbench dla programu VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code)
* [What's new in rozszerzenia IoT Toolkit dla programu VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Whats-new-in-the-IoT-Toolkit-extension-for-VS-Code)

Wykonaj następujące kroki, aby przygotować środowisko programistyczne dla Mxchip:

1. Zainstaluj [środowisku IDE Arduino](https://www.arduino.cc/en/Main/Software). Udostępnia niezbędne łańcucha narzędzi do kompilowania i przekazywanie kodu Arduino.
    * **Windows**: Użyj wersji Instalatora Windows. Nie należy instalować z Store aplikacji.
    * **macOS**: Przeciąganie i upuszczanie wyodrębnione **Arduino.app** do `/Applications` folderu.
    * **Ubuntu**: Takie jak Rozpakuj go do folderu `$HOME/Downloads/arduino-1.8.8`

2. Zainstaluj [programu Visual Studio Code](https://code.visualstudio.com/), Edytor kodu źródłowego dla wielu platform, za pomocą zaawansowanych deweloperów narzędzi, takich jak uzupełniania kodu IntelliSense i debugowania.

3. Uruchom program VS Code, poszukaj **Arduino** rynku rozszerzenia i zainstalować ją. To rozszerzenie zapewnia rozszerzonych środowisk wynika do programowania na platformę Arduino.
    ![Zainstaluj Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Wyszukaj **narzędzia IoT Azure** rynku rozszerzenia i zainstalować ją.
    ![Instalowanie narzędzi usługi Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    > [!div class="nextstepaction"]
    > [Zainstaluj pakiet rozszerzenia Azure IoT Tools](vscode:extension/vsciot-vscode.azure-iot-tools)

5. Skonfiguruj program VS Code za pomocą ustawień Arduino.

    W programie Visual Studio Code kliknij **Plik > Preferencje > Ustawienia**. Następnie kliknij przycisk **...**  i **Otwórz settings.json**.
    ![Instalowanie narzędzi usługi Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    Dodaj następujące wiersze do skonfigurowania Arduino w zależności od używanej platformy: 

    * **Windows**:
      
        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:
    
        Zastąp **{username}** symbol zastępczy poniżej swoją nazwę użytkownika.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Kliknij przycisk `F1` , aby otworzyć paletę poleceń, wpisz i wybierz **Arduino: Menedżer tablicy**. Wyszukaj **az3166 usługi** i zainstaluj najnowszą wersję.
    ![Zainstaluj zestaw SDK Mxchip](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Zainstaluj sterowniki łącze ST

[ST-łącze/V2](https://www.st.com/en/development-tools/st-link-v2.html) jest interfejsem USB, który mxchip IoT DevKit używa do komunikacji z komputera deweloperskiego. Należy go zainstalować na Windows do kodu skompilowanego urządzenia do Mxchip flash. Wykonaj kroki specyficzne dla systemu operacyjnego, aby zezwolić na dostęp do komputera, na urządzeniu.

* **Windows**: Pobierz i zainstaluj sterownik USB z [STMicroelectronics witryny sieci Web](https://www.st.com/en/development-tools/stsw-link009.html) dla [bezpośredni link](https://aka.ms/stlink-v2-windows).
* **macOS**: Sterownik nie jest wymagany dla systemu macOS.
* **Ubuntu**: Uruchom polecenia w terminalu i wyloguj się i zaloguj się, aby zaczęły obowiązywać zmiany grupy:
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

### <a name="open-sample-code-from-sample-gallery"></a>Otwórz przykładowy kod z galerii przykładów

1. Upewnij się, jest Twoja mxchip IoT DevKit **niepołączony** do komputera. Najpierw uruchom program VS Code, a następnie podłącz Mxchip do komputera.

1. Kliknij przycisk `F1` , aby otworzyć paletę poleceń, wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Otwórz przykłady...** . Następnie wybierz pozycję **mxchip IoT DevKit** jako tablica.

1. Na stronie Przykłady aplikacji Workbench IoT, Znajdź **wprowadzenie** i kliknij przycisk **Otwórz przykładowy**. Następnie wybiera ścieżki domyślnej, aby pobrać przykładowy kod.
    ![Otwórz przykładowy](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Aprowizowanie usługi Azure IoT Hub i urządzenia

1. W nowym oknie otwartym projekcie, kliknij przycisk `F1` , aby otworzyć paletę poleceń, wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Aprowizacja usług platformy Azure...** . Postępuj zgodnie z przewodnika krok po kroku, aby zakończyć inicjowanie obsługi administracyjnej w usłudze Azure IoT Hub i tworzenie urządzenia usługi IoT Hub.
    ![Polecenie aprowizacji](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Jeśli nie zostało zarejestrowane w systemie Azure. Postępuj zgodnie z wyskakujące powiadomienie w przypadku logowania się.

1. wybierz subskrypcję, której chcesz użyć.
    ![Wybierz sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Następnie wybierz lub Utwórz nową [grupy zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).
    ![Wybierz grupę zasobów](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. W grupie zasobów, który określiłeś postępuj zgodnie z przewodnikiem, aby wybrać lub utworzyć nowe usługi Azure IoT Hub.
    ![Wybierz kroki usługi IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Wybierz Centrum IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Wybrane centrum IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. W oknie danych wyjściowych zostanie wyświetlony aprowizowane usługi Azure IoT Hub ![Aprowizowane Centrum IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Wybierz lub Utwórz nowe urządzenie w usłudze Azure IoT Hub można zainicjować obsługi administracyjnej.
    ![Wybierz kroki urządzeń IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Wybierz urządzenie IoT zainicjowano obsługę administracyjną](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Masz teraz usługi Azure IoT Hub zainicjowano obsługę administracyjną i utworzono w niej urządzenie. Również parametry połączenia urządzenia zostaną zapisane w programie VS Code konfigurowania mxchip IoT DevKit później.
    ![Aprowizacja gotowe](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Konfigurowanie i kompilowanie kodu urządzenia

1. Na pasku stanu w prawym dolnym rogu Sprawdź **az3166 usługi zestawu DEWELOPERSKIEGO** jest przedstawiana w postaci wybranej tablicy i port szeregowy przy użyciu **STMicroelectronics** jest używany.
    ![Wybierz tablicy i COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Kliknij przycisk `F1` , aby otworzyć paletę poleceń, wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Konfigurowanie ustawień urządzenia...** , a następnie wybierz **parametry połączenia urządzenia konfiguracji > Wybierz parametry połączenia urządzenia Centrum IoT**.

1. Na Mxchip, przytrzymaj wciśnięty **przycisk A**, wypychania i wersji **resetowania** przycisk, a następnie zwolnij **przycisk A**. Twoje Mxchip przechodzi w tryb konfiguracji i zapisuje parametry połączenia.
    ![Parametry połączenia](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Kliknij przycisk `F1` wpisz ponownie, a następnie wybierz **Workbench urządzenia IoT platformy Azure: Przekazywanie kodu urządzenia**. Rozpoczyna kompilacji i Przekaż kod do Mxchip.
    ![Przekazywanie Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

Mxchip rozruchu i uruchamiania kodu.

> [!NOTE]
> Jeśli ma żadnych błędów lub przerw w działaniu, zawsze można odzyskać, uruchamiając polecenie ponownie.

## <a name="test-the-project"></a>Projekt testowy

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Wyświetl dane telemetryczne wysyłane do usługi Azure IoT Hub

Kliknij ikonę wtyczki zasilania, na pasku stanu, aby otworzyć Monitor szeregowego: ![Monitor szeregowej](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Przykładowa aplikacja jest uruchomiona pomyślnie, gdy pojawi się następujące wyniki:

* Serial Monitor wyświetla komunikat wysłany do usługi IoT Hub.
* Migające jest dioda LED na zestawu deweloperskiego IoT Mxchip.

![Dane wyjściowe Serial monitora](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Widok danych telemetrycznych odebranych przez usługę Azure IoT Hub

Możesz użyć [narzędzia IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) do monitorowania komunikatów wysyłanych z urządzenia do chmury (D2C) w usłudze IoT Hub.

1. Zaloguj się w [witryny Azure portal](https://portal.azure.com/), Znajdź Centrum IoT utworzonego.
    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. W **zasady dostępu współdzielonego** okienku kliknij **zasad iothubowner**i zanotuj parametry połączenia Centrum IoT Hub.
    ![Parametry połączenia w usłudze Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. W programie VS Code kliknij `F1`wpisz i wybierz **usługi Azure IoT Hub: Ustaw parametry połączenia Centrum IoT Hub**. Skopiuj parametry połączenia do niego.
    ![Ustawianie parametrów połączenia usługi Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Rozwiń **AZURE IOT HUB DEVICES** w okienku po prawej stronie, kliknij prawym przyciskiem myszy nazwę urządzenia, został utworzony i wybierz **Rozpocznij monitorowania komunikatu D2C**.
    ![Monitorowanie komunikatu D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. W **dane wyjściowe** okienku widać komunikaty D2C przychodzące do usługi IoT Hub.
    ![Komunikatu D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Przeglądanie kodu

`GetStarted.ino` Jest główny plik szkic Arduino.

![Komunikatu D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Aby zobaczyć, jak danych telemetrycznych z urządzenia są wysyłane do usługi Azure IoT Hub, otwórz `utility.cpp` pliku, w tym samym folderze. Widok [dokumentacja interfejsu API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) dowiesz się, jak na korzystanie z czujników i urządzeń peryferyjnych mxchip IoT DevKit.

`DevKitMQTTClient` Używany jest otoką **iothub_client** z [Microsoft Azure IoT SDK i bibliotek dla języka C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) do interakcji z usługą Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, możesz sprawdzić rozwiązania w [— często zadawane pytania dla Mxchip IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub się z nami od [dotyczącym oprogramowania Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Możesz również dostarczyć nam opinię, pozostawiając komentarz na tej stronie.

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie nawiązano zestawu deweloperskiego IoT Mxchip z Centrum IoT i danych przechwyconych czujnika zostało wysłane do Centrum IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
