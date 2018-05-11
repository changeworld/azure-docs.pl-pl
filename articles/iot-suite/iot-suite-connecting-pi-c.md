---
title: Zapewnij Pi malina do monitorowania zdalnego za pomocą C - Azure | Dokumentacja firmy Microsoft
description: Opisano sposób podłączania urządzeń Pi malina do akcelerator rozwiązań monitorowania zdalnego przy użyciu aplikacji napisanych w C.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 9de7616ec7174f6c55888a659e9a12bca1e07f94
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>Podłącz urządzenie Pi malina do zdalnego akcelerator rozwiązań monitorowania (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ten samouczek pokazuje, jak nawiązać zdalnego akcelerator rozwiązań monitorowania urządzenia fizycznego. Podobnie jak w przypadku najbardziej osadzonych aplikacji na urządzeniach ograniczone, kodu klienta dla aplikacji urządzeń malina Pi jest napisany w C. W tym samouczku tworzenia aplikacji na Pi malina systemem Raspbian systemu operacyjnego.

### <a name="required-hardware"></a>Wymagany sprzęt

Komputer stacjonarny, aby włączyć zdalne łączenie się z wiersza polecenia na malina Pi.

[Microsoft IoT Starter Kit malina Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) lub równoważne składników. W tym samouczku wykorzystuje następujące składniki z zestawu:

- Pi malinowe 3
- Karta MicroSD (z NOOBS)
- Kabla USB Mini
- Kabla Ethernet

### <a name="required-desktop-software"></a>Wymagane oprogramowanie komputerowe

Należy klient SSH na komputerze pulpitu umożliwia zdalny dostęp do wiersza polecenia na malina Pi.

- System Windows nie zawiera klienta SSH. Firma Microsoft zaleca używanie [PuTTY](http://www.putty.org/).
- Większość dystrybucje systemu Linux i Mac OS obejmują narzędzia wiersza polecenia SSH. Aby uzyskać więcej informacji, zobacz [SSH za pomocą systemu Linux lub Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Wymagane oprogramowanie Pi malina

W tym artykule przyjęto założenie, zainstalowano najnowszą wersję [OS Raspbian na Twoje Pi malina](https://www.raspberrypi.org/learning/software-guide/quickstart/).

Poniższe kroki pokazują, jak przygotować Twojej Pi malina do tworzenia aplikacji C, która łączy się akcelerator rozwiązań:

1. Nawiązać połączenie przy użyciu Pi malina **ssh**. Aby uzyskać więcej informacji, zobacz [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) na [witryny sieci Web Pi malina](https://www.raspberrypi.org/).

1. Można zaktualizować Twojego Pi malina, użyj następującego polecenia:

    ```sh
    sudo apt-get update
    ```

1. Aby dodać biblioteki i narzędzia deweloperskie wymagane do Twojej Pi malina, użyj następującego polecenia:

    ```sh
    sudo apt-get purge libssl-dev
    sudo apt-get install g++ make cmake gcc git libssl1.0-dev build-essential curl libcurl4-openssl-dev uuid-dev
    ```

1. Aby pobrać, kompilacji i zainstalować bibliotek klienckich Centrum IoT na Twoje Pi malina, użyj następujących poleceń:

    ```sh
    cd ~
    git clone --recursive https://github.com/azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c/build_all/linux
    ./build.sh --no-make
    cd ../../cmake/iotsdk_linux
    make
    sudo make install
    ```

## <a name="create-a-project"></a>Tworzenie projektu

Wykonaj następujące czynności, za pomocą **ssh** połączenie z Pi malina:

1. Utwórz folder o nazwie `remote_monitoring` w folderze macierzysty na malina Pi. Przejdź do tego folderu w powłoki:

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. Utwórz cztery pliki **main.c**, **remote_monitoring.c**, **remote_monitoring.h**, i **CMakeLists.txt** w `remote_monitoring` folder.

1. W edytorze tekstu Otwórz **remote_monitoring.c** pliku. Na Pi malina, możesz użyć dowolnej **nano** lub **vi** edytora tekstu. Dodaj następujące instrukcje `#include`:

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
