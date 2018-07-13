---
title: Łączenie urządzenia Raspberry Pi przy użyciu środowiska Node.js przy użyciu symulowanych danych telemetrycznych pakietu Azure IoT Suite | Dokumentacja firmy Microsoft
description: Użyj startowy IoT platformy Microsoft Azure dla urządzenia Raspberry Pi 3 i pakietu Azure IoT Suite. Za pomocą środowiska Node.js do łączenia z urządzenia Raspberry Pi z rozwiązaniem do zdalnego monitorowania, wysyłanie symulowanych danych telemetrycznych do chmury i odpowiadać na metody wywołane z poziomu pulpitu nawigacyjnego rozwiązania.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: node.js
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 53297049fd36eae3839c6a8146afc336b8f5cd02
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38676491"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-simulated-telemetry-using-nodejs"></a>Połącz swoje urządzenia Raspberry Pi 3 z rozwiązaniem do zdalnego monitorowania i wysyłanie symulowanych danych telemetrycznych za pomocą środowiska Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

W tym samouczku dowiesz się, jak zasymulować temperatury i wilgotności dane do wysłania do chmury za pomocą urządzenia Raspberry Pi 3. W tym samouczku obejmuje:

- Raspbian systemu operacyjnego, języka programowania Node.js i dla systemu Microsoft Azure IoT SDK for Node.js do zaimplementowania urządzenia próbki.
- Zdalnego monitorowania pakietu IoT wstępnie skonfigurowanego rozwiązania jako zaplecze oparte na chmurze.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-overview-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-overview-simulator.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Rozwiązania do monitorowania zdalnego udostępnia zestaw usług platformy Azure w ramach subskrypcji platformy Azure. Wdrożenie odzwierciedla architektury przedsiębiorstwa rzeczywistych. Aby uniknąć opłat niepotrzebne użycia platformy Azure, usuń wystąpienia wstępnie skonfigurowanego rozwiązania w witrynie azureiotsuite.com po zakończeniu z nim. Jeśli potrzebujesz ponownie wstępnie skonfigurowanego rozwiązania, można go łatwo odtworzyć. Aby uzyskać więcej informacji na temat zmniejszenie zużycia, podczas uruchamiania rozwiązania do monitorowania zdalnego, zobacz [konfigurowania usługi Azure IoT Suite wstępnie skonfigurowanych rozwiązań dla celów demonstracyjnych][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator.md)]

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

Jeśli jeszcze tego nie zrobiono, klonowanie repozytoriów wymagane, uruchamiając następujące polecenia w terminalu na Twoje Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Zaktualizuj parametry połączenia urządzenia

Otwórz przykładowy plik źródłowy w **nano** edytora, korzystając z następującego polecenia:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

Znajdź wiersz:

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

Zastąp wartości symboli zastępczych z urządzeniem i Centrum IoT Hub informacji została utworzona i zapisana na początku tego samouczka. Zapisz zmiany (**Ctrl-O**, **Enter**) i zamknąć Edytor (**Ctrl-X**).

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Uruchom następujące polecenia, aby zainstalować wstępnie wymagane pakiety dla przykładu:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator
npm install
```

Możesz teraz uruchomić przykładowy program w Raspberry Pi. Wpisz polecenie:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/simulator/remote_monitoring.js
```

Następujące przykładowe dane wyjściowe znajduje się przykład danych wyjściowych, który widzisz na urządzenia Raspberry Pi w wierszu polecenia:

![Dane wyjściowe z aplikacji urządzenia Raspberry Pi][img-raspberry-output]

Naciśnij klawisz **Ctrl-C** aby zakończyć program w dowolnym momencie.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator.md)]

## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę [Centrum deweloperów Azure IoT](https://azure.microsoft.com/develop/iot/) Aby uzyskać więcej przykładów i dokumentację dotyczącą usługi Azure IoT.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-simulator/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
