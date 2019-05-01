---
title: IoT Mxchip z chmurą — nawiązać połączenie z IoT DevKit az3166 usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się, jak skonfigurować i połączyć DevKit az3166 usługi IoT dla usługi Azure IoT Hub, dzięki czemu może wysłać dane do platformy w chmurze.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/17/2019
ms.author: wesmc
ms.openlocfilehash: 2f86b74299b5d47a87ed0b8e89a992f0f91a84be
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924630"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit az3166 usługi nawiązać połączenie z usługi Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Możesz użyć [zestawu deweloperskiego IoT Mxchip](https://microsoft.github.io/azure-iot-developer-kit/) do opracowywania i prototypów rozwiązań Internetu rzeczy (IoT), które korzystać z usług Microsoft Azure. Zawiera on tablicę z platformą arduino za pomocą zaawansowanych urządzeń peryferyjnych i czujniki, pakietu tablicy typu open source i zaawansowane [galerii przykładów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć Centrum IoT hub i rejestrowanie urządzenia dla zestawu deweloperskiego IoT Mxchip.
* Jak połączyć mxchip IoT DevKit sieć Wi-Fi i skonfigurować parametry połączenia Centrum IoT Hub.
* Jak wysyłać dane telemetryczne Mxchip czujnika do usługi IoT hub.
* Jak przygotować środowisko projektowe i tworzenie aplikacji dla mxchip IoT DevKit.

Nie masz jeszcze Mxchip? Spróbuj [symulator Mxchip](https://azure-samples.github.io/iot-devkit-web-simulator/) lub [zakupu Mxchip](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-need"></a>Co jest potrzebne

* Zestawu deweloperskiego IoT Mxchip tablicy za pomocą kabla Micro USB. [Pobierz teraz](https://aka.ms/iot-devkit-purchase).
* Komputer z systemem Windows 10 z systemem macOS 10.10 + lub Ubuntu 18.04 +.
* Aktywna subskrypcja platformy Azure. [Aktywowanie bezpłatnej 30-dniowej wersji próbnej Microsoft Azure konto](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
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

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Szybki start: Wysyłanie danych telemetrycznych z Mxchip do usługi IoT Hub

Przewodnik Szybki Start używa wstępnie skompilowanym Mxchip oprogramowania układowego do wysyłania danych telemetrycznych do Centrum IoT. Przed uruchomieniem go utworzyć Centrum IoT hub i rejestrowanie urządzenia w Centrum.

### <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w usłudze Azure Cloud Shell do tworzenia tożsamości urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyNodeDevice**: nazwa rejestrowanego urządzenia. Użyj elementu **MyNodeDevice** w pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```
   > [!NOTE]
   > Jeśli zostanie wyświetlony błąd podczas uruchamiania `device-identity`, zainstaluj [rozszerzenia usługi Azure IOT dla wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md) Aby uzyskać więcej informacji.
  
1. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

### <a name="send-devkit-telemetry"></a>Wysyłanie danych telemetrycznych Mxchip

Mxchip nawiązuje połączenie z punktem końcowym specyficznych dla urządzenia w Centrum IoT i wysyła dane telemetryczne dotyczące temperatury i wilgotności.

1. Pobierz najnowszą wersję [oprogramowania układowego GetStarted](https://aka.ms/devkit/prod/getstarted/latest) dla mxchip IoT DevKit.

1. Upewnij się, że mxchip IoT DevKit Podłącz do komputera przy użyciu kabla USB. Otwórz Eksplorator plików istnieje urządzenie pamięci masowej USB, nazywane **az3166 usługi**.
    ![Otwórz Windows Explorer](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Przeciąganie i upuszczanie oprogramowanie układowe pobrany właśnie do urządzenia pamięci masowej i zostanie automatycznie flash.
    ![Skopiuj oprogramowania układowego](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. Na Mxchip, przytrzymaj naciśnięty przycisk **B**, wypychania i zwolnij **resetowania** przycisk, a następnie przycisk **B**. Twoje Mxchip przechodzi w tryb AP. Aby upewnić się, na ekranie zostanie wyświetlony identyfikator zestawu usług (SSID) Mxchip i adresu IP portalu w konfiguracji.
    ![Resetuj przycisk, B i identyfikator SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Ustaw tryb AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Użyj przeglądarki internetowej na różnych sieci Wi-Fi włączone urządzenie (komputera lub telefon komórkowy) do połączenia z SSID Mxchip IoT, które są wyświetlane w poprzednim kroku. Jeśli prosi o podanie hasła, należy pozostawić je puste.
    ![Połącz SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Otwórz **192.168.0.1** w przeglądarce. Wybierz sieci Wi-Fi, który ma być mxchip IoT DevKit nawiązać połączenie, wpisz hasło sieci Wi-Fi, a następnie wklej parametry połączenia urządzenia wprowadzone wcześniej należy pamiętać o. Następnie kliknij przycisk Save (Zapisz).
    ![Interfejs użytkownika konfiguracji](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > Mxchip IoT DevKit obsługuje tylko zegarem 2,4 GHz sieci. Sprawdź [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) Aby uzyskać więcej informacji.

1. Parametry połączenia informacji i urządzeń sieci Wi-Fi zostanie umieszczony w mxchip IoT DevKit, gdy zostanie wyświetlona strona wyników.
    ![Wynik konfiguracji](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Po skonfigurowaniu sieci Wi-Fi poświadczeń zostanie utrzymany na urządzeniu na potrzeby tego połączenia, nawet jeśli urządzenie jest odłączone.

1. Mxchip IoT DevKit ponowny rozruch w ciągu kilku sekund. Na ekranie Mxchip zostanie wyświetlony adres IP dla Mxchip następuje przez dane telemetrii, w tym temperatury i wilgotności wartością liczby komunikatów wysyłanie do usługi Azure IoT Hub.
    ![IP sieci Wi-Fi](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Wysyłanie danych](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

Wykonaj następujące kroki, aby przygotować środowisko programistyczne dla Mxchip:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Instalowanie programu Visual Studio Code za pomocą narzędzia IoT Azure pakiet rozszerzenia

1. Zainstaluj [środowisku IDE Arduino](https://www.arduino.cc/en/Main/Software). Udostępnia niezbędne łańcucha narzędzi do kompilowania i przekazywanie kodu Arduino.
    * **Windows**: Użyj wersji Instalatora Windows. Nie należy instalować z Store aplikacji.
    * **macOS**: Przeciąganie i upuszczanie wyodrębnione **Arduino.app** do `/Applications` folderu.
    * **Ubuntu**: Takie jak Rozpakuj go do folderu `$HOME/Downloads/arduino-1.8.8`

2. Zainstaluj [programu Visual Studio Code](https://code.visualstudio.com/), Edytor kodu źródłowego dla wielu platform, za pomocą zaawansowanych funkcji intellisense, uzupełnianie kodu i debugowania pomocy technicznej, a także zaawansowane rozszerzenia można zainstalować z witryny marketplace.

3. Uruchom program VS Code, poszukaj **Arduino** rynku rozszerzenia i zainstalować ją. To rozszerzenie zapewnia rozszerzonych środowisk wynika do programowania na platformę Arduino.
    ![Zainstaluj Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Wyszukaj [narzędzia IoT Azure](https://aka.ms/azure-iot-tools) rynku rozszerzenia i zainstalować ją.
    ![Instalowanie narzędzi usługi Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Lub użyj tego linku bezpośredniego:
    > [!div class="nextstepaction"]
    > [Zainstaluj pakiet rozszerzenia Azure IoT Tools](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Pakiet rozszerzenia Azure IoT Tools zawiera [Workbench urządzenia IoT Azure](https://aka.ms/iot-workbench) używany do tworzenia i debugowania na różnych urządzeniach mxchip IoT. [Azure IoT Hub Toolkit](https://aka.ms/iot-toolkit), również składnikiem pakietu rozszerzenia Azure IoT Tools, jest używany do zarządzania i wchodzić w interakcje z usługą Azure IoT Hubs.

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

* **Windows**: Pobierz i zainstaluj sterownik USB z [STMicroelectronics witryny sieci Web](https://www.st.com/en/development-tools/stsw-link009.html) lub [bezpośredni link](https://aka.ms/stlink-v2-windows).
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

Teraz wszystko jest ustawione przy użyciu przygotowywanie i konfigurowanie środowiska projektowego. Daj nam tworzyć przykładowy GetStarted, który właśnie został uruchomiony.

## <a name="build-your-first-project"></a>Tworzenie pierwszego projektu

### <a name="open-sample-code-from-sample-gallery"></a>Otwórz przykładowy kod z galerii przykładów

Mxchip IoT DevKit zawiera obszernej galerii przykładów, które można użyć, aby dowiedzieć się nawiązać połączenie z Mxchip różne usługi platformy Azure.

1. Upewnij się, jest Twoja mxchip IoT DevKit **niepołączony** do komputera. Najpierw uruchom program VS Code, a następnie podłącz Mxchip do komputera.

1. Kliknij przycisk `F1` , aby otworzyć paletę poleceń, wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Otwórz przykłady...** . Następnie wybierz pozycję **mxchip IoT DevKit** jako tablica.

1. Na stronie Przykłady aplikacji Workbench IoT, Znajdź **wprowadzenie** i kliknij przycisk **Otwórz przykładowy**. Następnie wybiera ścieżki domyślnej, aby pobrać przykładowy kod.
    ![Otwórz przykładowy](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Aprowizowanie usługi Azure IoT Hub i urządzenia

Zamiast inicjowania obsługi usługi Azure IoT Hub i urządzeń w witrynie Azure portal możesz zrobić to w programie VS Code bez opuszczania środowiska programistycznego.

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
