---
title: Aprowizuj raspberry pi do zdalnego monitorowania przy użyciu języka C — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób podłączania urządzenia Raspberry Pi do akceleratora rozwiązań zdalnego monitorowania przy użyciu aplikacji napisanej w języku C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454507"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Podłącz urządzenie Raspberry Pi do akceleratora rozwiązań do zdalnego monitorowania (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku pokazano, jak podłączyć rzeczywiste urządzenie do akceleratora rozwiązań zdalnego monitorowania. Podobnie jak w przypadku większości osadzonych aplikacji, które działają na urządzeniach z ograniczeniami, kod klienta aplikacji urządzenia Raspberry Pi jest zapisywany w języku C. W tym samouczku tworzysz aplikację na Raspberry Pi z systemem Raspbian OS.

Jeśli wolisz symulować urządzenie, zobacz [Tworzenie i testowanie nowego symulowanego urządzenia](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Wymagany sprzęt

Komputer stacjonarny umożliwiający zdalne łączenie się z wierszem polecenia w raspberry pi.

[Zestaw startowy Microsoft IoT dla Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) lub równoważnych składników. W tym samouczku użyto następujących elementów z zestawu:

- Raspberry Pi 3
- Karta MicroSD (z NOOBS)
- Mini kabel USB
- Kabel Ethernet

### <a name="required-desktop-software"></a>Wymagane oprogramowanie komputerowe

Potrzebujesz klienta SSH na komputerze stacjonarnym, aby umożliwić zdalny dostęp do wiersza polecenia na Raspberry Pi.

- System Windows nie zawiera klienta SSH. Zalecamy stosowanie [PuTTY](https://www.putty.org/).
- Większość dystrybucji Linuksa i Mac OS zawiera narzędzie SSH wiersza polecenia. Aby uzyskać więcej informacji, zobacz [SSH Using Linux lub Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Wymagane oprogramowanie Raspberry Pi

W tym artykule założono, że zainstalowano najnowszą wersję [systemu operacyjnego Raspbian na Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Poniższe kroki pokazują, jak przygotować Raspberry Pi do tworzenia aplikacji C, która łączy się z akceleratorem rozwiązań:

1. Połącz się ze swoim Raspberry Pi za pomocą **ssh**. Aby uzyskać więcej informacji, zobacz [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) w [witrynie Raspberry Pi](https://www.raspberrypi.org/).

1. Użyj następującego polecenia, aby zaktualizować Raspberry Pi:

    ```sh
    sudo apt-get update
    ```

1. Aby wykonać kroki opisane w tym przewodniku, wykonaj kroki opisane w [konfiguracji środowiska programistycznego systemu Linux,](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) aby dodać wymagane narzędzia programistyczne i biblioteki do raspberry pi.

## <a name="view-the-code"></a>Zobacz kod

[Przykładowy kod](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) użyty w tym przewodniku jest dostępny w repozytorium Zestawów SDK języka Azure IoT C GitHub.

### <a name="download-the-source-code-and-prepare-the-project"></a>Pobierz kod źródłowy i przygotuj projekt

Aby przygotować projekt, sklonuj lub pobierz [repozytorium zestawów SDK C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub.

Próbka znajduje się w folderze **próbki/roztwory/remote_monitoring_client.**

Otwórz plik **remote_monitoring.c** w folderze **samples/solutions/remote_monitoring_client** w edytorze tekstu.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

W poniższych krokach opisano, jak używać *CMake* do tworzenia aplikacji klienckiej. Aplikacja klienta zdalnego monitorowania jest zbudowany jako część procesu kompilacji dla SDK.

1. Edytuj plik **remote_monitoring.c,** aby `<connectionstring>` zastąpić ciąg połączenia urządzenia, który został odnotowany na początku tego przewodnika instruktora po dodaniu urządzenia do akceleratora rozwiązań.

1. Przejdź do katalogu głównego sklonowanej kopii [repozytorium sdek sdek sedek SDKs usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) C i uruchom następujące polecenia w celu utworzenia aplikacji klienckiej:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Uruchom aplikację kliencką i wyślij dane telemetryczne do usługi IoT Hub:

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    Konsola wyświetla komunikaty w następujący sposób:

    - Aplikacja wysyła przykładowe dane telemetryczne do akceleratora rozwiązania.
    - Odpowiada na metody wywoływane z pulpitu nawigacyjnego rozwiązania.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
