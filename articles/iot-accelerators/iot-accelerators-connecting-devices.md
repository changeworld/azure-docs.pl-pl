---
title: Aprowizacja urządzeń Windows do zdalnego monitorowania w języku C — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób łączenia urządzenia do akceleratora rozwiązania monitorowania zdalnego przy użyciu aplikacji napisanych w języku C w systemie Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450236"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Podłączanie urządzenia do akceleratora rozwiązań zdalnego monitorowania (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ten samouczek pokazuje, jak połączyć prawdziwe urządzenie do akceleratora rozwiązania monitorowania zdalnego.

Podobnie jak w przypadku najbardziej osadzone aplikacje działają na urządzeniach ograniczone, kod klienta dla aplikacji urządzeń są zapisywane w C. W tym samouczku utworzysz aplikacja kliencka urządzenia na komputerze z systemem Windows.

Jeśli wolisz symulowanie urządzenia, zobacz [tworzenie i testowanie nowych symulowanych urządzeń](iot-accelerators-remote-monitoring-create-simulated-device.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami postępuj zgodnie z instrukcjami w [Konfigurowanie środowiska projektowego Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) Aby dodać narzędzia programistyczne wymagane i biblioteki na komputer Windows.

## <a name="view-the-code"></a>Wyświetl kod

[Przykładowego kodu](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client) użyto w tym przewodniku jest dostępny w repozytorium zestawów SDK C pakietu Azure IoT w witrynie GitHub.

### <a name="download-the-source-code-and-prepare-the-project"></a>Pobierz kod źródłowy i przygotowywanie projektu

Aby przygotować się do projektu, [sklonować repozytorium zestawów SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment) z usługi GitHub.

Przykład znajduje się w **rozwiązania/samples/remote_monitoring_client** folderu.

Otwórz **remote_monitoring.c** w pliku **rozwiązania/samples/remote_monitoring_client** folderu w edytorze tekstów.

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

1. Edytuj **remote_monitoring.c** plik, aby zastąpić `<connectionstring>` przy użyciu parametrów połączenia urządzenia wymienionych na początku, w tym przewodniku z instrukcjami, po dodaniu urządzenia do akceleratora rozwiązań.

1. Postępuj zgodnie z instrukcjami w [kompilacji zestawu SDK języka C w Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) Aby skompilować zestaw SDK i zdalnego monitorowania aplikacji klienta.

1. W wierszu polecenia użytą do skompilowania rozwiązania Uruchom polecenie:

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    W konsoli są wyświetlane komunikaty w postaci:

    - Aplikacja wysyła telemetrię próbki do akceleratora rozwiązań.
    - Odpowiada na metody wywołane z poziomu pulpitu nawigacyjnego rozwiązania.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
