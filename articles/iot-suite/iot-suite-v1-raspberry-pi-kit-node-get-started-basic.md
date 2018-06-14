---
title: Pi malina nawiązać połączenie przy użyciu środowiska Node.js w usłudze rzeczywistych czujników pakiet IoT Azure | Dokumentacja firmy Microsoft
description: Za pomocą usługi Microsoft Azure IoT Starter Kit malinowe pi 3 i pakiet Azure IoT. Użyj Node.js do nawiązania połączenia zdalnego rozwiązanie monitorowania sieci Pi malina wysłać dane telemetryczne z czujników w chmurze i Odpowiedz na metody wywoływane z poziomu pulpitu nawigacyjnego rozwiązania.
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
ms.openlocfilehash: 9bb4f62b1a3de68ce8796b60fe76cd97b9ab9ade
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2017
ms.locfileid: "24011966"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-nodejs"></a>Twoje malina Pi 3 nawiązać połączenie zdalne rozwiązanie monitorowania i wysyłania danych telemetrycznych z czujnika rzeczywistego przy użyciu środowiska Node.js

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

W tym samouczku przedstawiono sposób Microsoft Azure IoT startowy do malina Pi 3 umożliwia tworzenie czytnik temperatury i wilgotności, który może komunikować się z chmurą. W samouczku:

- Raspbian systemu operacyjnego, język programowania Node.js i Microsoft Azure IoT SDK dla środowiska Node.js do zaimplementowania urządzenia próbki.
- Pakiet IoT zdalne monitorowanie wstępnie skonfigurowane rozwiązanie jako zaplecza opartej na chmurze.

## <a name="overview"></a>Omówienie

W tym samouczku zostaną wykonane następujące kroki:

- Wdrożenie wystąpienia programu zdalnego wstępnie skonfigurowane rozwiązanie monitorowania do subskrypcji platformy Azure. Ten krok automatycznie wdraża i konfiguruje wiele usług platformy Azure.
- Konfigurowanie czujniki i urządzenia do komunikowania się z komputerem i zdalnego rozwiązanie monitorowania.
- Zaktualizuj przykładowy kod urządzenia do nawiązania połączenia zdalnego rozwiązanie monitorowania i wysłać dane telemetryczne, które można wyświetlić na pulpicie nawigacyjnym rozwiązania.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Zdalne rozwiązanie monitorowania udostępnia zestaw usług platformy Azure w ramach subskrypcji platformy Azure. Wdrożenie odzwierciedla architektury przedsiębiorstwa prawdziwe. Aby uniknąć niepotrzebnych wykorzystania platformy Azure, opłat, należy usunąć wystąpienia wstępnie skonfigurowanego rozwiązania na azureiotsuite.com po zakończeniu z nim. Jeśli potrzebujesz ponownie wstępnie skonfigurowanego rozwiązania, można go łatwo odtworzyć. Aby uzyskać więcej informacji na temat zmniejszenie zużycia podczas wykonywania zdalnego rozwiązanie monitorowania, zobacz [Konfigurowanie pakiet IoT Azure wstępnie rozwiązań dla celów demonstracyjnych][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Pobierz i skonfiguruj próbki

Można teraz pobrać i konfigurowania zdalnego monitorowania aplikacji klienckiej na Twoje malina Pi.

### <a name="install-nodejs"></a>Instalowanie środowiska Node.js

Zainstaluj środowisko Node.js na Twoje malinowe Pi. Zestaw SDK IoT dla środowiska Node.js wymaga wersji 0.11.5 Node.js lub nowszej. Poniższe kroki pokazują, jak zainstalować Node.js v6.10.2 na Twoje Pi malina:

1. Można zaktualizować Twojego Pi malina, użyj następującego polecenia:

    ```sh
    sudo apt-get update
    ```

1. Podczas pobierania plików binarnych Node.js z Pi malina, użyj następującego polecenia:

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

### <a name="clone-the-repositories"></a>Klonowanie repozytoria

Jeśli jeszcze tego nie zrobiono, klonowanie wymagane repozytoriów, uruchamiając następujące polecenia na Twoje Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git`
```

### <a name="update-the-device-connection-string"></a>Zaktualizuj parametry połączenia urządzenia

Otwieranie przykładowego pliku źródłowego w **nano** edytora za pomocą następującego polecenia:

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js
```

Znajdź wiersz:

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

Zastąp symbole zastępcze urządzenia i Centrum IoT informacji utworzony i zapisany na początku tego samouczka. Zapisz zmiany (**Ctrl-O**, **Enter**) i zamknij Edytor (**Ctrl-X**).

## <a name="run-the-sample"></a>Uruchom próbki

Uruchom następujące polecenia, aby zainstalować wstępnie wymagane pakiety przykładowej:

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic
npm install
```

Teraz możesz uruchomić program przykładowy na malina Pi. Wprowadź polecenie:

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js
```

Następujące przykładowe dane wyjściowe jest przykład danych wyjściowych, widocznej na Pi malina w wierszu polecenia:

![Dane wyjściowe z aplikacji malinowe Pi][img-raspberry-output]

Naciśnij klawisz **Ctrl-C** aby zamknąć program w dowolnym momencie.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>Następne kroki

Odwiedź stronę [Centrum deweloperów systemu Azure IoT](https://azure.microsoft.com/develop/iot/) więcej przykłady i dokumentacja Azure IoT.

[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-basic/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
