---
title: Zapewnij urządzeniami z systemem Linux do monitorowania zdalnego w języku C - Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób urządzenie podłączone do akcelerator rozwiązań monitorowania zdalnego przy użyciu Aplikacja napisana w języku C uruchomiony w systemie Linux.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 9ccf2b185c5aa67b41aab650989ad0326aba0215
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>Podłącz urządzenie do zdalnego akcelerator rozwiązań monitorowania (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ten samouczek pokazuje, jak nawiązać zdalnego akcelerator rozwiązań monitorowania urządzenia fizycznego.

## <a name="create-a-c-client-project-on-linux"></a>Utwórz projekt klienta C w systemie Linux

Podobnie jak w przypadku najbardziej osadzonych aplikacji na urządzeniach ograniczone, kodu klienta dla aplikacji urządzenia są zapisywane w C. W tym samouczku tworzenia aplikacji na maszynie z systemem Ubuntu (Linux).

Aby wykonać te kroki, należy na urządzenie z systemem Ubuntu wersji 15.04 lub nowszej. Przed kontynuowaniem należy zainstalować wstępnie wymagane pakiety na urządzenie Ubuntu przy użyciu następującego polecenia:

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>Zainstaluj biblioteki klienta na urządzeniu

Biblioteki klienta Centrum IoT Azure są dostępne w pakiecie można zainstalować przy użyciu urządzenia Ubuntu **stanie get** polecenia. Wykonaj poniższe kroki, aby zainstalować pakiet, który zawiera Centrum IoT biblioteki i nagłówek pliki klienta na komputerze Ubuntu:

1. W powłoce Dodaj repozytorium AzureIoT do komputera:

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. Zainstaluj pakiet azure-iot-sdk-c deweloperów

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Zainstaluj analizatora składni Parson JSON

Centrum IoT bibliotek klienckich użyć analizatora składni Parson JSON do analizowania ładunek komunikatu. W odpowiednich folderu na komputerze klonowanie repozytorium Parson GitHub przy użyciu następującego polecenia:

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>Przygotowanie projektu

Na komputerze Ubuntu Utwórz folder o nazwie `remote_monitoring`. W `remote_monitoring` folderu:

- Utwórz cztery pliki `main.c`, `remote_monitoring.c`, `remote_monitoring.h`, i `CMakeLists.txt`.
- Utwórz folder o nazwie `parson`.

Skopiuj pliki `parson.c` i `parson.h` z kopii lokalnej do repozytorium Parson `remote_monitoring/parson` folderu.

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

1. Dodaj następujące instrukcje, aby zdefiniować sposób kompilowania aplikacji klienta:

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

1. W `remote_monitoring` folderu, Utwórz folder do przechowywania *upewnij* pliki, które generuje CMake. Następnie uruchom **cmake** i **upewnij** polecenia w następujący sposób:

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Uruchom aplikację klienta oraz wysyłania danych telemetrycznych do Centrum IoT:

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
