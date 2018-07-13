---
title: Aprowizowanie urządzeniami z systemem Linux do zdalnego monitorowania w języku C — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób łączenia urządzenia do akceleratora rozwiązania monitorowania zdalnego przy użyciu aplikacji napisanych w języku C, działającej w systemie Linux.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 5d7d6522dc663f13ce40cc638ba90ac4043d435c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611416"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Podłączanie urządzenia do akceleratora rozwiązań zdalnego monitorowania (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku dowiesz się, jak połączyć urządzenie fizyczne do akceleratora rozwiązania monitorowania zdalnego.

## <a name="create-a-c-client-project-on-linux"></a>Utwórz projekt klienta C w systemie Linux

Podobnie jak w przypadku najbardziej osadzone aplikacje działają na urządzeniach ograniczone, kod klienta dla aplikacji urządzeń są zapisywane w C. W tym samouczku utworzysz aplikację na komputerze z systemem Ubuntu (Linux).

Aby wykonać te kroki, należy na urządzenie z systemem Ubuntu wersji 15.04 lub nowszej. Przed kontynuowaniem należy zainstalować wstępnie wymagane pakiety na urządzeniu z systemem Ubuntu przy użyciu następującego polecenia:

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>Instalowanie bibliotek klienta na urządzeniu z systemem

Biblioteki klienta usługi Azure IoT Hub są dostępne jako pakiet, który można zainstalować na Twoim Ubuntu urządzenia za pomocą **polecenia apt-get** polecenia. Wykonaj poniższe kroki, aby zainstalować pakiet, który zawiera usługi IoT Hub biblioteki i nagłówek pliki klienta na komputerze z systemem Ubuntu:

1. W powłoce Dodaj repozytorium AzureIoT do komputera:

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. Zainstaluj pakiet azure-iot-sdk-c-dev

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Zainstaluj analizatora Parson JSON

Biblioteki klienta usługi IoT Hub umożliwia analizowanie ładunków komunikat analizatora składni Parson JSON. W odpowiedni folder na komputerze sklonuj repozytorium Parson GitHub, używając następującego polecenia:

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>Przygotowanie projektu programu

Na maszynie z systemem Ubuntu, Utwórz folder o nazwie `remote_monitoring`. W `remote_monitoring` folderu:

- Utworzyć cztery pliki `main.c`, `remote_monitoring.c`, `remote_monitoring.h`, i `CMakeLists.txt`.
- Utwórz folder o nazwie `parson`.

Skopiuj pliki `parson.c` i `parson.h` z lokalnej kopii repozytorium Parson do `remote_monitoring/parson` folderu.

W edytorze tekstu Otwórz `remote_monitoring.c` pliku. Dodaj następujące instrukcje `#include`:

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

## <a name="add-code-to-run-the-app"></a>Dodaj kod, aby uruchomić aplikację

W edytorze tekstu Otwórz `remote_monitoring.h` pliku. Dodaj następujący kod:

```c
void remote_monitoring_run(void);
```

W edytorze tekstu Otwórz `main.c` pliku. Dodaj następujący kod:

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

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

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```

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
