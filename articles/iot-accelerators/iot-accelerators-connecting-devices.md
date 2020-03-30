---
title: Aprowizuj urządzenia z systemem Windows do zdalnego monitorowania w języku C — Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób podłączania urządzenia do akceleratora rozwiązań do zdalnego monitorowania przy użyciu aplikacji napisanej w języku C działającej w systemie Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61450236"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Podłączanie urządzenia do akceleratora rozwiązań do zdalnego monitorowania (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku pokazano, jak podłączyć rzeczywiste urządzenie do akceleratora rozwiązań zdalnego monitorowania.

Podobnie jak w przypadku większości osadzonych aplikacji, które działają na urządzeniach z ograniczeniami, kod klienta dla aplikacji urządzenia jest zapisywany w języku C. W tym samouczku tworzysz aplikację kliencką urządzenia na komputerze z systemem Windows.

Jeśli wolisz symulować urządzenie, zobacz [Tworzenie i testowanie nowego symulowanego urządzenia](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, wykonaj kroki opisane w [konfiguracji środowiska programistycznego systemu Windows,](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) aby dodać wymagane narzędzia programistyczne i biblioteki do komputera z systemem Windows.

## <a name="view-the-code"></a>Zobacz kod

[Przykładowy kod](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) użyty w tym przewodniku jest dostępny w repozytorium Zestawów SDK języka Azure IoT C GitHub.

### <a name="download-the-source-code-and-prepare-the-project"></a>Pobierz kod źródłowy i przygotuj projekt

Aby przygotować projekt, [sklonuj repozytorium zestawów SDK C usługi Azure](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) z usługi GitHub.

Próbka znajduje się w folderze **próbki/roztwory/remote_monitoring_client.**

Otwórz plik **remote_monitoring.c** w folderze **samples/solutions/remote_monitoring_client** w edytorze tekstu.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Edytuj plik **remote_monitoring.c,** aby `<connectionstring>` zastąpić ciąg połączenia urządzenia, który został odnotowany na początku tego przewodnika instruktora po dodaniu urządzenia do akceleratora rozwiązań.

1. Wykonaj kroki opisane w [programie SDK kompilacji C w systemie Windows,](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) aby utworzyć sdk i aplikację kliencką zdalnego monitorowania.

1. W wierszu polecenia użytym do utworzenia rozwiązania uruchom:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    Konsola wyświetla komunikaty w następujący sposób:

    - Aplikacja wysyła przykładowe dane telemetryczne do akceleratora rozwiązania.
    - Odpowiada na metody wywoływane z pulpitu nawigacyjnego rozwiązania.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
