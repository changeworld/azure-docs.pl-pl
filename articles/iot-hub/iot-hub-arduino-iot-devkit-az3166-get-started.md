---
title: IoT Mxchip z chmurą — nawiązać połączenie z IoT DevKit az3166 usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się, jak skonfigurować i połączyć DevKit az3166 usługi IoT dla usługi Azure IoT Hub, dzięki czemu może wysłać dane do platformy w chmurze.
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 6d5b0036bb44f301ea0b11e5d984fcd5b4bfac71
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599833"
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

Teraz nadszedł czas na konfigurowanie środowiska deweloperskiego: narzędzia i pakiety do tworzenia niesamowitych aplikacji IoT. Według systemu operacyjnego, możesz wybrać Windows lub wersję systemu macOS.

### <a name="windows"></a>Windows

Zachęcamy do użycia pakietu instalacyjnego, aby przygotować środowisko programistyczne. Jeśli wystąpią problemy, możesz wykonać [ręczne instrukcjami dotyczącymi instalacji mxchip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) do wykonywania pracy.

#### <a name="download-the-latest-package"></a>Pobierz najnowszy pakiet

Pobrany plik zip zawiera wszystkie niezbędne narzędzia i pakiety Mxchip rozwoju.

> [!div class="button"]
[Pobieranie](https://aka.ms/devkit/prod/installpackage/latest)

Plik zip zawiera następujące narzędzia i pakiety. Jeśli masz już pewne składniki zainstalowane, skrypt wykrywania i je pominąć.

* Środowisko node.js i usługi Yarn: program obsługi dla skryptu Instalatora i automatycznych zadań.

* [Platformy Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): Międzyplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure. Plik MSI zawiera zależne Python i narzędzia pip.

* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): lekkiemu edytorowi kodu Mxchip rozwoju.

* [Rozszerzenie programu Visual Studio Code dla Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): rozszerzenie, które umożliwia programowanie Arduino w programie Visual Studio Code.

* [Środowisku IDE Arduino](https://www.arduino.cc/en/Main/Software): narzędzia, która korzysta z rozszerzenia dla Arduino.

* Pakiet Mxchip tablicy: Narzędzie do łańcuchów, biblioteki i projektów dla Mxchip.

* Narzędzie ST łącze: Podstawowe narzędzia i sterowniki.

#### <a name="run-the-installation-script"></a>Uruchom skrypt instalacji

W Eksploratorze plików Windows znajdź plik zip i Wyodrębnij jego zawartość. Znajdź `install.cmd`, kliknij go prawym przyciskiem myszy i wybierz **Uruchom jako administrator**.

![Eksplorator plików](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Podczas instalacji zostanie wyświetlony postęp każdego narzędzia lub pakietu.

![Postęp instalacji](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> W zależności od środowiska czasami wystąpi błąd podczas instalowania środowisku IDE Arduino. W takim przypadku można spróbować [oddzielnie zainstalować środowisku IDE Arduino](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) i ponownie uruchom install.cmd. W przeciwnym razie wykonaj [ręczne instrukcjami dotyczącymi instalacji mxchip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) zainstalować wszystkie niezbędne narzędzia i pakiety.

#### <a name="install-drivers"></a>Instalowanie sterowników

Program VS Code Arduino rozszerzenia opiera się na środowisku IDE Arduino. Jeśli instalujesz środowisku IDE Arduino po raz pierwszy monit o zainstalowanie odpowiednich sterowników.

![Rozpoczynanie pracy — sterowników](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Instalacja trwa około 10 minut w zależności od szybkości Internetu. Po zakończeniu instalacji, zobaczysz skróty programu Visual Studio Code i środowisku IDE Arduino na pulpicie.

> [!NOTE] 
> Od czasu do czasu po uruchomieniu programu VS Code, zostanie wyświetlony monit z powodu błędu, nie można znaleźć w środowisku IDE Arduino lub pakietu powiązane tablicy. Aby rozwiązać problem, zamknij program VS Code i uruchom ponownie w środowisku IDE Arduino. VS kod powinien odszukaj ścieżki środowisku IDE Arduino poprawnie.

### <a name="macos"></a>macOS

Firma Microsoft zachęca do przy użyciu środowiska instalacji jednym kliknięciem przygotowywanie środowiska deweloperskiego. Jeśli wystąpią problemy, możesz wykonać [ręczne instrukcjami dotyczącymi instalacji mxchip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) do wykonywania pracy.

#### <a name="install-homebrew"></a>Zainstaluj Homebrew

> [!NOTE] 
> Jeśli zainstalowano Homebrew, możesz pominąć ten krok.

Postępuj zgodnie z [instrukcje dotyczące instalacji Homebrew](https://docs.brew.sh/Installation.html) go zainstalować.

#### <a name="download-the-latest-package"></a>Pobierz najnowszy pakiet

Pobrany plik zip zawiera wszystkie niezbędne narzędzia i pakiety Mxchip rozwoju.

> [!div class="button"]
[Pobieranie](https://aka.ms/devkit/prod/installpackage/mac/latest)

Plik zip zawiera następujące narzędzia i pakiety. Jeśli masz już pewne składniki zainstalowane, skrypt wykrywania i je pominąć.

* Środowisko node.js i usługi Yarn: program obsługi dla skryptu Instalatora i automatycznych zadań.

* [Interfejs wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): Międzyplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure.

* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): lekkiemu edytorowi kodu Mxchip rozwoju.

* [Rozszerzenie programu Visual Studio Code dla Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): rozszerzenie, które umożliwia programowanie Arduino w programie Visual Studio Code.

* [Środowisku IDE Arduino](https://www.arduino.cc/en/Main/Software): narzędzia, która korzysta z rozszerzenia dla Arduino.

* Pakiet Mxchip tablicy: Narzędzie do łańcuchów, biblioteki i projektów dla Mxchip.

* Narzędzie ST łącze: Podstawowe narzędzia i sterowniki.

#### <a name="run-the-installation-script"></a>Uruchom skrypt instalacji

W programie Finder zlokalizuj plik zip i Wyodrębnij jego zawartość:

![wyszukiwania z systemem macOS](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

Uruchom aplikację Terminal, Znajdź folder, Wyodrębnij plik zip i uruchom następujące kroki:

```bash
./install.sh
```

![instalacji systemu macOS](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Jeśli spełniasz błąd uprawnień Homebrew, uruchom `brew doctor` Aby rozwiązać ten problem. Sprawdź ["homebrew error" sekcji często zadawanych pytań Mxchip IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos) Aby uzyskać więcej informacji.

Masz teraz wszystkie niezbędne narzędzia i zainstalowane pakiety dla systemu macOS.

## <a name="open-the-project-folder"></a>Otwórz folder projektu

Uruchom po otwarciu folderu projektu. 

### <a name="start-vs-code"></a>Uruchom program VS Code

Upewnij się, że Twoje Mxchip nie jest połączony. Uruchom program VS Code, a następnie połącz Mxchip do komputera. Program VS Code automatycznie wyszukuje Mxchip i zostanie otwarty na stronie wprowadzenie.

![Strona wprowadzenia](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> Od czasu do czasu po uruchomieniu programu VS Code, zostanie wyświetlony monit z powodu błędu, nie można znaleźć w środowisku IDE Arduino lub pakietu powiązane tablicy. Zamknij program VS Code i ponownie w środowisku IDE Arduino. VS kod powinien odszukaj ścieżki środowisku IDE Arduino poprawnie.

### <a name="open-the-arduino-examples-folder"></a>Otwórz folder przykłady Arduino

Na **przykłady Arduino** kartę, przejdź do **przykłady zestawu DEWELOPERSKIEGO az3166 usługi** > **AzureIoT**i wybierz **GetStarted**.

![Karta przykłady Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

Jeśli występuje Zamknij okienko zostanie ponownie otwarty. Użyj `Ctrl+Shift+P` (z systemem macOS: `Cmd+Shift+P`) aby otworzyć paletę poleceń, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: przykłady**.

## <a name="provision-azure-services"></a>Aprowizacja usług platformy Azure

W oknie rozwiązania należy uruchomić zadanie przy użyciu `Ctrl+P` (z systemem macOS: `Cmd+P`), wprowadzając `task cloud-provision`.

W terminalu programu VS Code interaktywne wiersza polecenia przeprowadzi Cię przez Inicjowanie obsługi administracyjnej wymaganych usług platformy Azure.

![Interaktywne wiersza polecenia](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Tworzenie i przekazywanie szkic Arduino

Następnie skompilować i przekazać szkic Arduino.

### <a name="windows"></a>Windows

1. Użyj `Ctrl+P` do uruchomienia `task device-upload`.

2. Naciśnij w terminalu wyświetli monit o wprowadzenie tryb konfiguracji. Aby to zrobić, przytrzymaj naciśnięty przycisk A, a następnie wypychania, a następnie zwolnij przycisk resetowania. Na ekranie zostanie wyświetlony identyfikator Mxchip i "Konfiguracja".

   To jest, aby ustawić parametry połączenia, które pobiera z `task cloud-provision` kroku.

   Program VS Code uruchamia, weryfikowanie i przekazywanie szkic Arduino.

   ![Weryfikacja i przekazywania szkic Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

   Mxchip rozruchu i uruchamiania kodu.

   > [!NOTE] 
   > Czasami występuje błąd "Błąd: az3166 usługi: Nieznany pakiet". Jest to spowodowane tablicy indeksu pakietów nie zostanie odświeżona. Zaznacz tę [kroki — często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) rozwiązać problem.

### <a name="macos"></a>macOS

1. Umieść Mxchip tryb konfiguracji: naciśnij i przytrzymaj A przycisk, a następnie wypychania i zwolnij przycisk resetowania. Na ekranie są wyświetlane "Konfiguracja".

2. Użyj `Cmd+P` do uruchomienia `task device-upload`.

   To jest, aby ustawić parametry połączenia, które pobiera z `task cloud-provision` kroku.

   Program VS Code uruchamia, weryfikowanie i przekazywanie szkic Arduino:

   ![przekazywanie urządzeń](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

   Mxchip rozruchu i uruchamiania kodu.

   > [!NOTE] 
   > Czasami występuje błąd "Błąd: az3166 usługi: Nieznany pakiet". Jest to spowodowane tablicy indeksu pakietów nie zostanie odświeżona. Zobacz, jakie kroki [często zadawane pytania IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) rozwiązać problem.


## <a name="test-the-project"></a>Projekt testowy

W programie VS Code wykonaj następujące kroki, aby otworzyć i skonfigurować Serial Monitor:

1. Kliknij przycisk `COM[X]` word na pasku stanu, aby ustawić prawy port COM `STMicroelectronics`.

   ![COM port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Kliknij ikonę wtyczki zasilania na pasku stanu, aby otworzyć Monitor szeregowe.

   ![monitor seryjnych](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png)

3. Na pasku stanu kliknij numer, który reprezentuje szybkość transmisji i ustaw go na `115200`.

   ![szybkość transmisji](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

4. Przykładowa aplikacja jest uruchomiona pomyślnie, gdy pojawi się następujące wyniki:

   * Serial Monitor wyświetla te same informacje jako zawartość na poniższym zrzucie ekranu.
   
   * Migające jest LED RGB w zestawu deweloperskiego IoT Mxchip.

   ![Końcowe dane wyjściowe w programie VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, możesz sprawdzić rozwiązania w [często zadawane pytania IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Możesz również dostarczyć nam opinię, pozostawiając komentarz na tej stronie.

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie nawiązano zestawu deweloperskiego IoT Mxchip z Centrum IoT i danych przechwyconych czujnika zostało wysłane do Centrum IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
