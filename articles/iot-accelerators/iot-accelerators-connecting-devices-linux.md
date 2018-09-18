---
title: Aprowizowanie urządzeniami z systemem Linux do zdalnego monitorowania w języku C — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób łączenia urządzenia do akceleratora rozwiązania monitorowania zdalnego przy użyciu aplikacji napisanych w języku C, działającej w systemie Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: fcc11347d06cde43c79ef4272f5c2ad87555c040
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734492"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Podłączanie urządzenia do akceleratora rozwiązań zdalnego monitorowania (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku dowiesz się, jak połączyć urządzenie fizyczne do akceleratora rozwiązania monitorowania zdalnego.

Podobnie jak w przypadku najbardziej osadzone aplikacje działają na urządzeniach ograniczone, kod klienta dla aplikacji urządzeń są zapisywane w C. W tym samouczku utworzysz aplikację na komputerze z systemem Ubuntu (Linux).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, konieczne jest urządzenie z systemem Ubuntu wersji 15.04 lub nowszej. Przed kontynuowaniem [Konfigurowanie środowiska projektowego systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux).

## <a name="view-the-code"></a>Wyświetl kod

[Przykładowego kodu](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring) użyto w tym przewodniku jest dostępny w repozytorium zestawów SDK C pakietu Azure IoT w witrynie GitHub.

### <a name="download-the-source-code-and-prepare-the-project"></a>Pobierz kod źródłowy i przygotowywanie projektu

Aby przygotować projekt, Klonuj lub Pobierz [repozytorium zestawów SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub.

Przykład znajduje się w **rozwiązania/samples/remote_monitoring** folderu.

Otwórz **remote_monitoring.c** w pliku **rozwiązania/samples/remote_monitoring** folderu w edytorze tekstów.

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
    ./samples/solutions/remote_monitoring/remote_monitoring_client
    ```

    W konsoli są wyświetlane komunikaty w postaci:

    - Aplikacja wysyła telemetrię próbki do akceleratora rozwiązań.
    - Odpowiada na metody wywołane z poziomu pulpitu nawigacyjnego rozwiązania.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
