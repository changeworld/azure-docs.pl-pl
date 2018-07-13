---
title: Łączenie urządzenia Raspberry Pi przy użyciu języka C z symulowanych danych telemetrycznych pakietu Azure IoT Suite | Dokumentacja firmy Microsoft
description: Użyj startowy IoT platformy Microsoft Azure dla urządzenia Raspberry Pi 3 i pakietu Azure IoT Suite. Użyj C, aby połączyć urządzenia Raspberry Pi z rozwiązaniem do zdalnego monitorowania, wysyłanie symulowanych danych telemetrycznych do chmury i odpowiadać na metody wywołane z poziomu pulpitu nawigacyjnego rozwiązania.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 19b16bff7874a03578b8766668c431553da0d875
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635612"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-simulated-telemetry-using-c"></a>Połącz swoje urządzenia Raspberry Pi 3 z rozwiązaniem do zdalnego monitorowania i wysyłanie symulowanych danych telemetrycznych przy użyciu języka C

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

W tym samouczku dowiesz się, jak zasymulować temperatury i wilgotności dane do wysłania do chmury za pomocą urządzenia Raspberry Pi 3. W tym samouczku obejmuje:

- Raspbian systemu operacyjnego, języka programowania C i zestawu Microsoft Azure IoT SDK dla języka C do zaimplementowania urządzenia próbki.
- Zdalnego monitorowania pakietu IoT wstępnie skonfigurowanego rozwiązania jako zaplecze oparte na chmurze.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-overview-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-overview-simulator.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Rozwiązania do monitorowania zdalnego udostępnia zestaw usług platformy Azure w ramach subskrypcji platformy Azure. Wdrożenie odzwierciedla architektury przedsiębiorstwa rzeczywistych. Aby uniknąć opłat niepotrzebne użycia platformy Azure, usuń wystąpienia wstępnie skonfigurowanego rozwiązania w witrynie azureiotsuite.com po zakończeniu z nim. Jeśli potrzebujesz ponownie wstępnie skonfigurowanego rozwiązania, można go łatwo odtworzyć. Aby uzyskać więcej informacji na temat zmniejszenie zużycia, podczas uruchamiania rozwiązania do monitorowania zdalnego, zobacz [konfigurowania usługi Azure IoT Suite wstępnie skonfigurowanych rozwiązań dla celów demonstracyjnych][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi-simulator.md)]

## <a name="download-and-configure-the-sample"></a>Pobieranie i konfigurowanie przykładowych

Można teraz pobrać i skonfigurować zdalnego monitorowania aplikacji klienckiej na urządzenia Raspberry Pi.

### <a name="clone-the-repositories"></a>Klonowanie repozytoriów

Jeśli jeszcze tego nie zrobiono, klonowanie repozytoriów wymagane, uruchamiając następujące polecenia w terminalu na Twoje Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Zaktualizuj parametry połączenia urządzenia

Otwórz przykładowy plik źródłowy w **nano** edytora, korzystając z następującego polecenia:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/remote_monitoring/remote_monitoring.c
```

Znajdź następujące wiersze:

```c
static const char* deviceId = "[Device Id]";
static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
```

Zastąp wartości symboli zastępczych z urządzeniem i Centrum IoT Hub informacji została utworzona i zapisana na początku tego samouczka. Zapisz zmiany (**Ctrl-O**, **Enter**) i zamknąć Edytor (**Ctrl-X**).

## <a name="build-the-sample"></a>Skompilować przykład

Zainstaluj wstępnie wymagane pakiety dla zestawu SDK urządzenia Microsoft Azure IoT dla języka C, uruchamiając następujące polecenia w terminalu na urządzenia Raspberry Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Teraz możesz tworzyć rozwiązania zaktualizowanego przykładowego na urządzenia Raspberry Pi:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/simulator/build.sh
```

Możesz teraz uruchomić przykładowy program w Raspberry Pi. Wpisz polecenie:

```sh
sudo ~/cmake/remote_monitoring/remote_monitoring
```

Następujące przykładowe dane wyjściowe znajduje się przykład danych wyjściowych, który widzisz na urządzenia Raspberry Pi w wierszu polecenia:

![Dane wyjściowe z aplikacji urządzenia Raspberry Pi][img-raspberry-output]

Naciśnij klawisz **Ctrl-C** aby zakończyć program w dowolnym momencie.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-simulator.md)]

## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę [Centrum deweloperów Azure IoT](https://azure.microsoft.com/develop/iot/) Aby uzyskać więcej przykładów i dokumentację dotyczącą usługi Azure IoT.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-simulator/appoutput.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
