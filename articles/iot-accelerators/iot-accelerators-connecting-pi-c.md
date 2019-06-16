---
title: Aprowizowanie urządzeń Raspberry Pi, zdalne monitorowanie za pomocą języka C — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób łączenia urządzeń Raspberry Pi do akceleratora rozwiązania monitorowania zdalnego przy użyciu aplikacji napisanych w C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61454507"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Łączenie urządzenia Raspberry Pi do akceleratora rozwiązań zdalnego monitorowania (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ten samouczek pokazuje, jak połączyć prawdziwe urządzenie do akceleratora rozwiązania monitorowania zdalnego. Podobnie jak w przypadku najbardziej osadzone aplikacje działają na urządzeniach ograniczone, kod klienta dla aplikacji urządzenia Raspberry Pi są zapisywane w C. W tym samouczku utworzysz aplikację na urządzeniach Raspberry Pi z systemem operacyjnym Raspbian.

Jeśli wolisz symulowanie urządzenia, zobacz [tworzenie i testowanie nowych symulowanych urządzeń](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Wymagany sprzęt

Komputer stacjonarny, aby włączyć zdalne łączenie się z wiersza polecenia na urządzenia Raspberry Pi.

[Pakiet startowy IoT firmy Microsoft do Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) lub równoważne składników. W tym samouczku korzysta z następujących elementów z zestawu SDK:

- Raspberry Pi 3
- Karta MicroSD (z NOOBS)
- Kabel USB Mini
- Kabel Ethernet

### <a name="required-desktop-software"></a>Wymagane oprogramowania dla komputerów stacjonarnych

Klient SSH jest niezbędne w komputera stacjonarnego, aby umożliwić dostęp zdalny do wiersza polecenia na urządzenia Raspberry Pi.

- Windows nie zawiera klienta SSH. Firma Microsoft zaleca używanie [PuTTY](https://www.putty.org/).
- Większość dystrybucje systemu Linux i Mac OS obejmują narzędzia wiersza polecenia SSH. Aby uzyskać więcej informacji, zobacz [SSH przy użyciu systemu Linux lub Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Wymagane oprogramowanie urządzenia Raspberry Pi

W tym artykule założono, zainstalowano najnowszą wersję [OS Raspbian na urządzenia Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Poniższe kroki pokazują, jak przygotować urządzenia Raspberry Pi do tworzenia aplikacji języka C, który nawiązuje połączenie z akceleratora rozwiązań:

1. Łączenie z urządzeniem Raspberry Pi z wykorzystaniem **ssh**. Aby uzyskać więcej informacji, zobacz [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) na [witryny sieci Web urządzenia Raspberry Pi](https://www.raspberrypi.org/).

1. Aby zaktualizować urządzenia Raspberry Pi, użyj następującego polecenia:

    ```sh
    sudo apt-get update
    ```

1. Aby wykonać kroki opisane w tym przewodniku z instrukcjami postępuj zgodnie z instrukcjami w [Konfigurowanie środowiska projektowego systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) dodać bibliotek i narzędzi projektowania wymagane do urządzenia Raspberry Pi.

## <a name="view-the-code"></a>Wyświetl kod

[Przykładowego kodu](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) użyto w tym przewodniku jest dostępny w repozytorium zestawów SDK C pakietu Azure IoT w witrynie GitHub.

### <a name="download-the-source-code-and-prepare-the-project"></a>Pobierz kod źródłowy i przygotowywanie projektu

Aby przygotować projekt, Klonuj lub Pobierz [repozytorium zestawów SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub.

Przykład znajduje się w **rozwiązania/samples/remote_monitoring_client** folderu.

Otwórz **remote_monitoring.c** w pliku **rozwiązania/samples/remote_monitoring_client** folderu w edytorze tekstów.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

W poniższych krokach opisano sposób użycia *CMake* do tworzenia aplikacji klienckiej. Aplikacja klienta do monitorowania zdalnego powstała jako część procesu kompilacji dla zestawu SDK.

1. Edytuj **remote_monitoring.c** plik, aby zastąpić `<connectionstring>` przy użyciu parametrów połączenia urządzenia wymienionych na początku, w tym przewodniku z instrukcjami, po dodaniu urządzenia do akceleratora rozwiązań.

1. Przejdź do katalogu głównym sklonowanej kopii [repozytorium zestawów SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c) repozytorium i uruchom następujące polecenia, aby skompilować aplikację klienta:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Uruchom aplikację klienta i wysyłanie danych telemetrycznych do Centrum IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    W konsoli są wyświetlane komunikaty w postaci:

    - Aplikacja wysyła telemetrię próbki do akceleratora rozwiązań.
    - Odpowiada na metody wywołane z poziomu pulpitu nawigacyjnego rozwiązania.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
