---
title: Zestaw deweloperski IoT do chmury — nawiązać Centrum IoT Azure IoT zestaw deweloperski AZ3166 | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się, jak skonfigurować i AZ3166 zestaw deweloperski IoT połączyć się z Centrum IoT Azure tak przesyłania danych do platformy w chmurze Azure.
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: ce20ae800887d8c9e865ecec46d0cf9a49fb7c3c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631629"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>AZ3166 zestaw deweloperski IoT nawiązać połączenia z Centrum IoT Azure w chmurze

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Można użyć [zestaw deweloperski IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) do opracowywania i prototypu Internetu rzeczy (IoT) rozwiązania, które korzystają z usług Microsoft Azure. Obejmuje on tablicy Arduino zgodnego z zaawansowanych urządzeń peryferyjnych i czujników, pakiet tablicy open source i rosnącym [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Co zrobić
Połącz [zestaw deweloperski](https://microsoft.github.io/azure-iot-developer-kit/) w Centrum Azure IoT utworzony zbierania danych temperatury i wilgotności z czujników i wysyłać dane do Centrum IoT.

Nie masz jeszcze zestaw deweloperski? Spróbuj [symulatora zestaw deweloperski](https://azure-samples.github.io/iot-devkit-web-simulator/) lub [kupić](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak zestaw deweloperski IoT nawiązać połączenia z punktem dostępu bezprzewodowego i przygotowywanie środowiska projektowego.
* Jak utworzyć Centrum IoT i zarejestrować urządzenie zestaw deweloperski IoT MXChip.
* Jak zbierać dane czujników, uruchamiając na zestaw deweloperski IoT MXChip przykładowej aplikacji.
* Jak wysyłać dane czujników do Centrum IoT.

## <a name="what-you-need"></a>Co jest potrzebne

* Zestaw deweloperski IoT MXChip tablicy za pomocą kabla Micro USB. [Pobierz teraz](https://aka.ms/iot-devkit-purchase).
* Komputer z systemem Windows 10 lub macOS 10.10 +.
* Aktywna subskrypcja platformy Azure. [Aktywuj bezpłatnej 30-dniowej wersji próbnej Microsoft Azure konto](https://azureinfo.microsoft.com/us-freetrial.html).
  

## <a name="prepare-your-hardware"></a>Przygotowanie sprzętu

Podłączanie sprzętu do komputera.

Należy tego sprzętu:

* Zestaw deweloperski tablicy
* Kabel Micro USB

![Wymagany sprzęt](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Aby podłączyć zestaw deweloperski do komputera:

1. Połącz koniec USB do komputera.
2. Połącz koniec Micro-USB do zestaw deweloperski.
3. Zielona LED zasilania potwierdza połączenia.

![Połączenia sprzętowe](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Konfigurowanie sieci Wi-Fi

Projektami IoT jest zależne od łączności z Internetem. Użyj poniższych instrukcji, aby skonfigurować zestaw deweloperski, aby nawiązać połączenie Wi-Fi.

### <a name="enter-ap-mode"></a>Tryb region

Przytrzymaj naciśnięty przycisk B, wypychania i zwolnij przycisk reset, a następnie zwolnij przycisk B. Twoje zestaw deweloperski przejdzie do trybu region konfigurowania sieci Wi-Fi. Na ekranie zostanie wyświetlony identyfikator zestawu usług (SSID) zestaw deweloperski i adres IP portalu w konfiguracji.

![Resetuj przycisk, B i identyfikator SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Nawiązać zestaw deweloperski region

Teraz Użyj innego urządzenia sieci Wi-Fi włączone (komputera lub telefonu komórkowego), do nawiązania połączenia SSID zestaw deweloperski (wyróżnione na poprzedniej ilustracji). Hasło może pozostać puste.

![Informacje o sieci i przycisk Połącz](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Konfigurowanie sieci Wi-Fi dla zestaw deweloperski

Otwórz adres IP wyświetlany na ekranie zestaw deweloperski na komputerze lub w przeglądarce telefonu komórkowego, wybierz sieć Wi-Fi, który ma zestaw deweloperski, aby nawiązać połączenie, a następnie wpisz hasło. Wybierz przycisk **Połącz**.

![Pole hasła i przycisk Połącz](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Jeśli połączenie powiedzie się, zestaw deweloperski wykonuje ponowny rozruch w ciągu kilku sekund. Zostanie wyświetlony sieci Wi-Fi nazwy i adresu IP na ekranie:

![Nazwa sieci Wi-Fi i adres IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> Adres IP wyświetlany w zdjęcia mogą być niezgodne rzeczywisty adres IP przypisany i wyświetlane na ekranie zestaw deweloperski. Jest to normalne, ponieważ dynamiczne przypisywanie adresów IP sieci Wi-Fi korzysta z protokołu DHCP.

Po skonfigurowaniu sieci Wi-Fi poświadczeń będzie umieszczony na urządzeniu dla tego połączenia, nawet jeśli urządzenie jest odłączony. Na przykład jeśli skonfigurować zestaw deweloperski dla sieci Wi-Fi w domu, a następnie wykonać zestaw deweloperski, aby urząd, należy ponownie skonfigurować tryb region (rozpoczyna się od kroku w sekcji "Wprowadź tryb region") do nawiązania połączenia z biurem sieci Wi-Fi zestaw deweloperski. 

## <a name="start-using-the-devkit"></a>Uruchom przy użyciu zestaw deweloperski

Domyślna aplikacja uruchomiona na zestaw deweloperski sprawdza najnowszej wersji oprogramowania układowego i przedstawia niektóre dane diagnostyki czujnika.

### <a name="upgrade-to-the-latest-firmware"></a>Uaktualnij do najnowszego oprogramowania układowego

> [!NOTE] 
> Od wersji 1.1 zestaw deweloperski umożliwia bezpieczne ST w program inicjujący. Konieczne jest uaktualnienie oprogramowania układowego, jeśli są uruchomione w wersji 1.1, aby stał się prawdopodobnie działa.

Jeśli konieczne jest uaktualnienie oprogramowania układowego ekranu wyświetli wersji bieżących i najnowszego oprogramowania układowego. Aby przeprowadzić uaktualnienie, należy wykonać [uaktualnienie oprogramowania układowego](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) przewodnik.

![Wyświetlanie wersji bieżących i najnowszego oprogramowania układowego](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> Jest to jednorazowa nakładu pracy. Po rozpocząć tworzenie na zestaw deweloperski i przekazać aplikację najnowszego oprogramowania układowego będzie pochodził z aplikacją.

### <a name="test-various-sensors"></a>Testowanie różnych czujników

Naciśnij przycisk B, aby przetestować czujników. Nadal naciśnięcie klawisza i zwolnieniem przycisku B, aby przechodzić kolejno przez każdy czujnika.

![Wyświetlanie przycisk B i czujnik](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

Teraz nadszedł czas, aby skonfigurować środowisko projektowe: narzędzia i pakietów do tworzenia niezwykłych aplikacji IoT. Można wybrać systemu Windows lub wersji macOS zgodnie z systemu operacyjnego.

### <a name="windows"></a>Windows

Firma Microsoft zachęca do użycia pakietu instalacyjnego, aby przygotować Środowisko deweloperskie. Jeśli wystąpią problemy, można wykonać [ręczne](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) do wykonywania pracy.

#### <a name="download-the-latest-package"></a>Pobierz najnowszy pakiet

Pobrany plik zip zawiera wszystkie niezbędne narzędzia i pakietów dla rozwoju zestaw deweloperski.

> [!div class="button"]
[Pobieranie](https://aka.ms/devkit/prod/installpackage/latest)

Plik zip zawiera następujące narzędzia i pakietów. Jeśli masz już pewne składniki zainstalowane, skrypt wykryje i Pomiń nich.

* Node.js i Yarn: program obsługi dla skryptu Instalatora i automatycznych zadań.
* [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): wieloplatformowych środowiska wiersza polecenia do zarządzania zasobami platformy Azure. Plik MSI zawiera zależnej Python i pip.
* [Visual Studio Code](https://code.visualstudio.com/) (kodzie VS): Edytor niewielki kod dla rozwoju zestaw deweloperski.
* [Rozszerzenia programu Visual Studio Code Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): rozszerzenia umożliwiające Arduino Programowanie w Visual Studio Code.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): narzędzie, które wykorzystuje rozszerzenie Arduino.
* Pakiet tablicy zestaw deweloperski: Narzędzie łańcuchy, biblioteki i projektów dla zestaw deweloperski.
* Narzędzie ST łącza: Niezbędne narzędzia i sterowniki.

#### <a name="run-the-installation-script"></a>Uruchom skrypt instalacji

W Eksploratorze plików systemu Windows znajdź plik zip i wyodrębnij go. Znajdź `install.cmd`, kliknij go prawym przyciskiem myszy i wybierz **Uruchom jako administrator**.

![Eksploratora plików](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Podczas instalacji zostanie wyświetlony postęp każdego narzędzia lub pakietu.

![Postęp instalacji](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> W zależności od środowiska czasami wystąpi awaria podczas instalowania Arduino IDE. W takim przypadku można spróbować [oddzielnie zainstalować Arduino IDE](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) i ponownie uruchom install.cmd. W przeciwnym razie wykonaj [ręczne](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows) zainstalować wszystkie niezbędne narzędzia i pakietów.

#### <a name="install-drivers"></a>Instalowanie sterowników

Kod VS rozszerzenia Arduino zależy od Arduino IDE. Jeśli instalujesz Arduino IDE po raz pierwszy, zostanie wyświetlony monit o zainstalowanie odpowiednich sterowników:

![pobieranie rozpoczęto sterowników](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Instalacja powinien zająć około 10 minut, w zależności od szybkości internet. Po zakończeniu instalacji programu Visual Studio Code i Arduino IDE skróty powinny być widoczne na pulpicie.

> [!NOTE] 
> Czasami podczas uruchamiania kodu programu VS, zostanie wyświetlony monit z powodu błędu czy Arduino IDE lub pakietu pokrewne tablicy nie może znaleźć. Aby go rozwiązać, zamknij kodzie VS i ponownie Arduino IDE. VS kod powinien zlokalizuj ścieżki Arduino IDE poprawnie.

### <a name="macos"></a>macOS

Firma Microsoft zachęca do obsługi instalacji jednym kliknięciem umożliwia przygotowanie środowiska programowania. Jeśli wystąpią problemy, można wykonać [ręczne](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/) do wykonywania pracy.

#### <a name="install-homebrew"></a>Instalowanie oprogramowania Homebrew

> [!NOTE] 
> Po zainstalowaniu oprogramowania Homebrew, możesz pominąć ten krok.

Postępuj zgodnie z [instrukcje dotyczące instalacji oprogramowania Homebrew](https://docs.brew.sh/Installation.html) go zainstalować.

#### <a name="download-the-latest-package"></a>Pobierz najnowszy pakiet
Pobrany plik zip zawiera wszystkie niezbędne narzędzia i pakietów dla rozwoju zestaw deweloperski.

> [!div class="button"]
[Pobieranie](https://aka.ms/devkit/prod/installpackage/mac/latest)

Plik zip zawiera następujące narzędzia i pakietów. Jeśli masz już pewne składniki zainstalowane, skrypt wykryje i Pomiń nich.

* Node.js i Yarn: program obsługi dla skryptu Instalatora i automatycznych zadań.
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): wieloplatformowych środowiska wiersza polecenia do zarządzania zasobami platformy Azure.
* [Visual Studio Code](https://code.visualstudio.com/) (kodzie VS): Edytor niewielki kod dla rozwoju zestaw deweloperski.
* [Rozszerzenia programu Visual Studio Code Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): rozszerzenia umożliwiające Arduino Programowanie w Visual Studio Code.
* [Arduino IDE](https://www.arduino.cc/en/Main/Software): narzędzie, które wykorzystuje rozszerzenie Arduino.
* Pakiet tablicy zestaw deweloperski: Narzędzie łańcuchy, biblioteki i projektów dla zestaw deweloperski.
* Narzędzie ST łącza: Niezbędne narzędzia i sterowniki.

#### <a name="run-the-installation-script"></a>Uruchom skrypt instalacji

W oknie Wyszukiwanie zlokalizuj zip i wyodrębnij go:

![System macOS wyszukiwania](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

Uruchom aplikację terminali, zlokalizuj folder Wyodrębnij plik zip i uruchom:

```bash
./install.sh
```

![Zainstaluj system macOS](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Jeśli spełniasz Homebrew błąd uprawnień, uruchom `brew doctor` aby został rozwiązany. Sprawdź [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos) więcej szczegółów.

Masz teraz wszystkie niezbędne narzędzia i pakiety zainstalowane dla macOS.


## <a name="open-the-project-folder"></a>Otwórz folder projektu

### <a name="start-vs-code"></a>Kod uruchomienia programu VS

Upewnij się, że nie jest połączona z zestaw deweloperski. Najpierw uruchom kodzie VS i połącz zestaw deweloperski do komputera. Kod VS automatycznie wyszukuje zestaw deweloperski i spowoduje otwarcie strony wprowadzenie:

![Strona wprowadzenia](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> Czasami podczas uruchamiania kodu programu VS, zostanie wyświetlony monit z powodu błędu czy Arduino IDE lub pakietu pokrewne tablicy nie może znaleźć. Zamknij kodzie VS i ponownie Arduino IDE. VS kod powinien zlokalizuj ścieżki Arduino IDE poprawnie.


### <a name="open-the-arduino-examples-folder"></a>Otwórz folder Arduino przykłady

Na **przykłady Arduino** karcie, przejdź do **przykłady MXCHIP AZ3166** > **AzureIoT**i wybierz **GetStarted**.

![Przykłady Arduino kartę](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

Jeśli wystąpi aby zamknąć okienko, zostanie ponownie otwarty. Użyj `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) do otwarcia palety polecenia, wpisz **Arduino**, a następnie znajdź i wybierz **Arduino: przykłady**.

## <a name="provision-azure-services"></a>Udostępnianie usług platformy Azure

W oknie rozwiązania, uruchom zadanie za pośrednictwem `Ctrl+P` (macOS: `Cmd+P`), wprowadzając `task cloud-provision`.

W terminalu VS kodu interaktywnego wiersza polecenia prowadzi użytkownika przez Inicjowanie obsługi administracyjnej wymaganych usług Azure:

![Interakcyjne wiersza polecenia](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Tworzenie i przekazywanie szkicu Arduino

### <a name="windows"></a>Windows

1. Użyj `Ctrl+P` do uruchomienia `task device-upload`.
2. Terminal wyświetli monit o wprowadzenie trybu konfiguracji. Aby to zrobić, przytrzymaj naciśnięty przycisk A, a następnie wypychania, a następnie zwolnij przycisk resetowania. Na ekranie zostanie wyświetlony identyfikator zestaw deweloperski i "Konfiguracja".

Jest to, aby ustawić parametry połączenia, które są pobierane z `task cloud-provision` kroku.

Następnie kod VS uruchamia Weryfikowanie i przekazywanie szkicu Arduino:

![Weryfikacja i przekazywanie szkicu Arduino](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

Zestaw deweloperski wykonuje ponowny rozruch i uruchamiania kodu.

> [!NOTE] 
> Od czasu do czasu, błąd "Błąd: AZ3166: Nieznany pakiet". Jest to spowodowane tablicy indeksu pakietów nie jest odświeżona. Sprawdź [kroki — często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) celu jego rozwiązania.

### <a name="macos"></a>macOS

1. Ustawić zestaw deweloperski trybu konfiguracji: przytrzymaj naciśnięty przycisk A, a następnie wypychania i zwolnij przycisk resetowania. Na ekranie "Konfiguracja".
2. Użyj `Cmd+P` do uruchomienia `task device-upload`.

Jest to, aby ustawić parametry połączenia, które są pobierane z `task cloud-provision` kroku.

Następnie kod VS uruchamia Weryfikowanie i przekazywanie szkicu Arduino:

![przekazywanie urządzeń](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

Zestaw deweloperski wykonuje ponowny rozruch i uruchamiania kodu.

> [!NOTE] 
> Od czasu do czasu, błąd "Błąd: AZ3166: Nieznany pakiet". Jest to spowodowane tablicy indeksu pakietów nie jest odświeżona. Sprawdź [kroki — często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) celu jego rozwiązania.


## <a name="test-the-project"></a>Projekt testowy

W kodzie VS, wykonaj następujące kroki, aby otworzyć i skonfigurować Serial Monitor:

1. Kliknij przycisk `COM[X]` słowa na pasku stanu, aby ustawić prawy port COM z `STMicroelectronics`: ![com port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. Kliknij ikonę plug zasilania na pasku stanu, aby otworzyć Serial Monitor: ![seryjny monitora](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png)

3. Na pasku stanu, kliknij pozycję Liczba, która reprezentuje szybkość transmisji i ustaw `115200`: ![szybkość transmisji](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

Przykładowa aplikacja działa prawidłowo, gdy pojawi się następujące wyniki:

* Serial Monitor wyświetla te same informacje jak zawartość na poniższym zrzucie ekranu.
* LED na zestaw deweloperski IoT MXChip jest migający.

![Ostateczne dane wyjściowe w kodzie VS](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Problemy i opinie

W razie wystąpienia problemów można znaleźć [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/). Możesz również zapewnić nam opinii pozostawić komentarz na tej stronie.

## <a name="next-steps"></a>Kolejne kroki

Pomyślnie nawiązano zestaw deweloperski IoT MXChip do Centrum IoT i dane czujników przechwyconych zostały wysłane do Centrum IoT.

Aby kontynuować, wprowadzenie do korzystania z Centrum IoT Azure oraz zapoznać się z innymi scenariuszami IoT, zobacz:

- [Zarządzanie przesyłaniem komunikatów między chmurą a urządzeniem za pomocą narzędzia iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Zapisywanie komunikatów usługi IoT Hub w magazynie danych platformy Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Użyj usługi Power BI do wizualizacji danych czujnika w czasie rzeczywistym z Centrum IoT Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Korzystanie z aplikacji sieci Web do wizualizacji danych czujnika w czasie rzeczywistym z Centrum IoT Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Pogody prognozy przy użyciu danych czujnika z Centrum IoT w usłudze Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Zarządzanie urządzeniami za pomocą narzędzia iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Zdalne monitorowanie i powiadomienia w usłudze Logic Apps](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
