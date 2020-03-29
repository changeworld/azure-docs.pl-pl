---
title: Aprowizuj urządzenia z systemem Linux do zdalnego monitorowania w języku C — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób podłączania urządzenia do akceleratora rozwiązań zdalnego monitorowania przy użyciu aplikacji napisanej w języku C działającej w systemie Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61454502"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Podłącz urządzenie do akceleratora rozwiązań do zdalnego monitorowania (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku pokazano, jak podłączyć rzeczywiste urządzenie do akceleratora rozwiązań zdalnego monitorowania.

Podobnie jak w przypadku większości osadzonych aplikacji, które działają na urządzeniach z ograniczeniami, kod klienta dla aplikacji urządzenia jest zapisywany w języku C. W tym samouczku tworzysz aplikację na komputerze z systemem Ubuntu (Linux).

Jeśli wolisz symulować urządzenie, zobacz [Tworzenie i testowanie nowego symulowanego urządzenia](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz urządzenia z systemem Ubuntu w wersji 15.04 lub nowszej. Przed kontynuowaniem [skonfiguruj środowisko programistyczne Linuksa](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

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
