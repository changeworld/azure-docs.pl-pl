---
title: Łączenie urządzenia Raspberry Pi przy użyciu środowiska Node.js do obsługi aktualizacji oprogramowania układowego pakietu Azure IoT Suite | Dokumentacja firmy Microsoft
description: Użyj startowy IoT platformy Microsoft Azure dla urządzenia Raspberry Pi 3 i pakietu Azure IoT Suite. Za pomocą środowiska Node.js do łączenia z urządzenia Raspberry Pi z rozwiązaniem do zdalnego monitorowania, wysyłanie danych telemetrycznych z czujników do chmury i wykonać aktualizację oprogramowania układowego zdalnego.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 31bbeff8049c6005671b991f965fae7316e3adf6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309595"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-nodejs"></a>Łączenie z urządzeniem Raspberry Pi 3 z rozwiązaniem do zdalnego monitorowania i Włącz aktualizacje oprogramowania układowego zdalnego przy użyciu środowiska Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

W tym samouczku dowiesz się, jak używać programu Microsoft Azure IoT Starter Kit dla Raspberry Pi 3, aby:

* Tworzenie czytnika temperatury i wilgotności, który może komunikować się z chmurą.
* Włącz i wykonywać zdalnego oprogramowania układowego aktualizacji do zaktualizowania aplikacji klienckiej w Raspberry Pi.

W tym samouczku obejmuje:

- Raspbian systemu operacyjnego, języka programowania Node.js i dla systemu Microsoft Azure IoT SDK for Node.js do zaimplementowania urządzenia próbki.
- Zdalnego monitorowania pakietu IoT wstępnie skonfigurowanego rozwiązania jako zaplecze oparte na chmurze.

## <a name="overview"></a>Przegląd

W tym samouczku wykonasz następujące czynności:

- Wdrożenie wystąpienia zdalnej wstępnie skonfigurowanego rozwiązania monitorowania do subskrypcji platformy Azure. W tym kroku wdraża i automatycznie konfiguruje wiele usług platformy Azure.
- Skonfiguruj czujniki i urządzenia do komunikowania się z komputerem i rozwiązania do monitorowania zdalnego.
- Aktualizowanie przykładowego kodu urządzenia do łączenia z rozwiązaniem do zdalnego monitorowania i wysyłania telemetrii, który można wyświetlić na pulpicie nawigacyjnym rozwiązania.
- Aby zaktualizować aplikację klienta, należy użyć przykładowego kodu urządzenia.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Rozwiązania do monitorowania zdalnego udostępnia zestaw usług platformy Azure w ramach subskrypcji platformy Azure. Wdrożenie odzwierciedla architektury przedsiębiorstwa rzeczywistych. Aby uniknąć opłat niepotrzebne użycia platformy Azure, usuń wystąpienia wstępnie skonfigurowanego rozwiązania w witrynie azureiotsuite.com po zakończeniu z nim. Jeśli potrzebujesz ponownie wstępnie skonfigurowanego rozwiązania, można go łatwo odtworzyć. Aby uzyskać więcej informacji na temat zmniejszenie zużycia, podczas uruchamiania rozwiązania do monitorowania zdalnego, zobacz [konfigurowania usługi Azure IoT Suite wstępnie skonfigurowanych rozwiązań dla celów demonstracyjnych][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładowych

Można teraz pobrać i skonfigurować zdalnego monitorowania aplikacji klienckiej na urządzenia Raspberry Pi.

### <a name="install-nodejs"></a>Instalowanie środowiska Node.js

Jeśli użytkownik jeszcze tego nie zrobiono, zainstaluj środowisko Node.js na urządzenia Raspberry Pi. Zestawu SDK usługi IoT dla środowiska Node.js wymaga wersji 0.11.5 Node.js lub nowszej. Poniższe kroki pokazują, jak zainstalować środowisko Node.js v6.10.2 na urządzenia Raspberry Pi:

1. Aby zaktualizować urządzenia Raspberry Pi, użyj następującego polecenia:

    ```sh
    sudo apt-get update
    ```

1. Aby pobrać pliki binarne środowiska Node.js do urządzenia Raspberry Pi, użyj następującego polecenia:

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Aby zainstalować pliki binarne, użyj następującego polecenia:

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. Użyj następującego polecenia, aby sprawdzić, czy pomyślnie zainstalowano Node.js v6.10.2:

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>Klonowanie repozytoriów

Jeśli użytkownik jeszcze tego nie zrobiono, klonowanie repozytoriów wymagane, uruchamiając następujące polecenia na swojej Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Zaktualizuj parametry połączenia urządzenia

Otwórz przykładowy plik konfiguracji w **nano** edytora, korzystając z następującego polecenia:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Zastąp wartości symboli zastępczych z identyfikatorem urządzenia i informacji o usłudze IoT Hub została utworzona i zapisana na początku tego samouczka.

Gdy wszystko będzie gotowe, zawartość pliku deviceinfo powinien wyglądać następująco:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Zapisz zmiany (**Ctrl-O**, **Enter**) i zamknąć Edytor (**Ctrl-X**).

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Uruchom następujące polecenia, aby zainstalować wstępnie wymagane pakiety dla przykładu:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advance/1.0
npm install
```

Możesz teraz uruchomić przykładowy program w Raspberry Pi. Wpisz polecenie:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/1.0/remote_monitoring.js
```

Następujące przykładowe dane wyjściowe znajduje się przykład danych wyjściowych, który widzisz na urządzenia Raspberry Pi w wierszu polecenia:

![Dane wyjściowe z aplikacji urządzenia Raspberry Pi][img-raspberry-output]

Naciśnij klawisz **Ctrl-C** aby zakończyć program w dowolnym momencie.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. Na pulpicie nawigacyjnym rozwiązania, kliknij przycisk **urządzeń** do odwiedzenia **urządzeń** strony. Wybierz urządzenia Raspberry Pi w **listy urządzeń**. Następnie wybierz **metody**:

    ![Lista urządzeń na pulpicie nawigacyjnym][img-list-devices]

1. Na **Wywołaj metodę** wybierz **InitiateFirmwareUpdate** w **metoda** listy rozwijanej.

1. W **FWPackageURI** wprowadź **https://raw.githubusercontent.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit/master/advanced/2.0/raspberry.js**. Ten plik zawiera implementacji oprogramowania układowego w wersji 2.0.

1. Wybierz **InvokeMethod**. Aplikacji na urządzenia Raspberry Pi wysyła potwierdzenie z powrotem do pulpitu nawigacyjnego rozwiązania. Następnie uruchamia proces aktualizacji oprogramowania układowego, pobierając nowej wersji oprogramowania układowego:

    ![Pokaż historię — metoda][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Obserwuj proces aktualizacji oprogramowania układowego

Możesz obserwować oprogramowania układowego, zaktualizować procesu, ponieważ działa na urządzeniu i wyświetlając zgłaszanych właściwości na pulpicie nawigacyjnym rozwiązania:

1. Możesz wyświetlić postęp procesu aktualizacji na urządzenia Raspberry Pi:

    ![Pokaż postęp aktualizacji][img-update-progress]

    > [!NOTE]
    > Zdalnego monitorowania liczby ponownych uruchomień aplikacji dyskretnie po zakończeniu aktualizacji. Użyj polecenia `ps -ef` Aby sprawdzić, jest on uruchomiony. Aby zakończyć ten proces, należy użyć `kill` polecenia z identyfikatorem procesu.

1. Możesz wyświetlić stan aktualizacji oprogramowania układowego, zgłaszaną przez urządzenie, w portalu rozwiązania. Poniższy zrzut ekranu przedstawia stan i czas trwania każdego etapu procesu aktualizacji i nowa wersja oprogramowania układowego:

    ![Pokaż stan zadania][img-job-status]

    Jeśli przejdziesz do pulpitu nawigacyjnego, możesz sprawdzić, czy urządzenie nadal wysyła dane telemetryczne po aktualizacji oprogramowania układowego.

> [!WARNING]
> Jeśli pole jest uruchomiony na Twoim koncie platformy Azure rozwiązania do monitorowania zdalnego, są rozliczane za czas, w których ono działa. Aby uzyskać więcej informacji na temat zmniejszenie zużycia, podczas uruchamiania rozwiązania do monitorowania zdalnego, zobacz [konfigurowania usługi Azure IoT Suite wstępnie skonfigurowanych rozwiązań dla celów demonstracyjnych][lnk-demo-config]. Usunąć wstępnie skonfigurowane rozwiązanie z Twojego konta platformy Azure, po zakończeniu korzystania z niego.

## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę [Centrum deweloperów Azure IoT](https://azure.microsoft.com/develop/iot/) Aby uzyskać więcej przykładów i dokumentację dotyczącą usługi Azure IoT.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
