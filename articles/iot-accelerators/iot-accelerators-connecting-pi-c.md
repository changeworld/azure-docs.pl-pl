---
title: Aprowizowanie urządzeń Raspberry Pi, zdalne monitorowanie za pomocą języka C — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób łączenia urządzeń Raspberry Pi do akceleratora rozwiązania monitorowania zdalnego przy użyciu aplikacji napisanych w C.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 23e84a8d577bb1c4950de3acd76b0f8528551ae0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611445"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Łączenie urządzenia Raspberry Pi do akceleratora rozwiązań zdalnego monitorowania (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku dowiesz się, jak połączyć urządzenie fizyczne do akceleratora rozwiązania monitorowania zdalnego. Podobnie jak w przypadku najbardziej osadzone aplikacje działają na urządzeniach ograniczone, kod klienta dla aplikacji urządzenia Raspberry Pi są zapisywane w C. W tym samouczku utworzysz aplikację na urządzeniach Raspberry Pi z systemem operacyjnym Raspbian.

### <a name="required-hardware"></a>Wymagany sprzęt

Komputer stacjonarny, aby włączyć zdalne łączenie się z wiersza polecenia na urządzenia Raspberry Pi.

[Pakiet startowy IoT firmy Microsoft do Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) lub równoważne składników. W tym samouczku korzysta z następujących elementów z zestawu SDK:

- Urządzenie raspberry Pi 3
- Karta MicroSD (z NOOBS)
- Kabel USB Mini
- Kabel Ethernet

### <a name="required-desktop-software"></a>Wymagane oprogramowania dla komputerów stacjonarnych

Klient SSH jest niezbędne w komputera stacjonarnego, aby umożliwić dostęp zdalny do wiersza polecenia na urządzenia Raspberry Pi.

- Windows nie zawiera klienta SSH. Firma Microsoft zaleca używanie [PuTTY](http://www.putty.org/).
- Większość dystrybucje systemu Linux i Mac OS obejmują narzędzia wiersza polecenia SSH. Aby uzyskać więcej informacji, zobacz [SSH przy użyciu systemu Linux lub Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Wymagane oprogramowanie urządzenia Raspberry Pi

W tym artykule założono, zainstalowano najnowszą wersję [OS Raspbian na urządzenia Raspberry Pi](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Poniższe kroki pokazują, jak przygotować urządzenia Raspberry Pi do tworzenia aplikacji języka C, który nawiązuje połączenie z akceleratora rozwiązań:

1. Łączenie z urządzeniem Raspberry Pi z wykorzystaniem **ssh**. Aby uzyskać więcej informacji, zobacz [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) na [witryny sieci Web urządzenia Raspberry Pi](https://www.raspberrypi.org/).

1. Aby zaktualizować urządzenia Raspberry Pi, użyj następującego polecenia:

    ```sh
    sudo apt-get update
    ```

1. Aby dodać narzędzia programistyczne wymagane i biblioteki do urządzenia Raspberry Pi, użyj następującego polecenia:

    ```sh
    sudo apt-get install g++ make cmake gcc git libssl1.0-dev build-essential curl libcurl4-openssl-dev uuid-dev
    ```

1. Aby pobrać, tworzenie i instalowanie bibliotek klienckich usługi IoT Hub na urządzenia Raspberry Pi, użyj następujących poleceń:

    ```sh
    cd ~
    git clone --recursive https://github.com/azure/azure-iot-sdk-c.git
    mkdir cmake
    cd cmake
    cmake ..
    make
    sudo make install
    ```

## <a name="create-a-project"></a>Tworzenie projektu

Wykonaj następujące czynności za pomocą **ssh** połączenie urządzenia Raspberry Pi:

1. Utwórz folder o nazwie `remote_monitoring` w folderze głównym na urządzenia Raspberry Pi. Przejdź do tego folderu, w powłoce:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Utworzyć cztery pliki **main.c**, **remote_monitoring.c**, **remote_monitoring.h**, i **CMakeLists.txt** w `remote_monitoring` folder.

1. W edytorze tekstu Otwórz **remote_monitoring.c** pliku. Na urządzenia Raspberry Pi, można użyć dowolnego **nano** lub **vi** edytora tekstu. Dodaj następujące instrukcje `#include`:

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include <string.h>
    ```

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

Zapisz **remote_monitoring.c** plik i zamknij Edytor.

## <a name="add-code-to-run-the-app"></a>Dodaj kod, aby uruchomić aplikację

W edytorze tekstu Otwórz **remote_monitoring.h** pliku. Dodaj następujący kod:

```c
void remote_monitoring_run(void);
```

Zapisz **remote_monitoring.h** plik i zamknij Edytor.

W edytorze tekstu Otwórz **main.c** pliku. Dodaj następujący kod:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

Zapisz **main.c** plik i zamknij Edytor.

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji

W poniższych krokach opisano sposób użycia *CMake* do tworzenia aplikacji klienckiej.

1. W edytorze tekstu Otwórz **CMakeLists.txt** w pliku `remote_monitoring` folderu.

1. Dodaj następujące instrukcje, aby zdefiniować sposób kompilowania aplikacji klienckiej:

    ```cmake
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "/usr/local/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
      serializer
      iothub_client_mqtt_transport
      umqtt
      iothub_client
      aziotsharedutil
      parson
      pthread
      curl
      ssl
      crypto
      m
    )
    ```

1. Zapisz **CMakeLists.txt** plik i zamknij Edytor.

1. W `remote_monitoring` folderze utwórz folder do przechowywania *wprowadzić* pliki, które narzędzie CMake generuje. Następnie uruchom **cmake** i **wprowadzić** poleceń w następujący sposób:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Uruchom aplikację klienta i wysyłanie danych telemetrycznych do Centrum IoT Hub:

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
