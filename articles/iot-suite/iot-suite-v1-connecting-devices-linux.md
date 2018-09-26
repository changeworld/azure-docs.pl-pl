---
title: Podłącz urządzenie przy użyciu języka C w systemie Linux | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak połączyć urządzenie z usługi Azure IoT Suite, wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego przy użyciu aplikacji napisanych w języku C, działającej w systemie Linux.
services: ''
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
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: a5768041a13d5ddc355c054dc85ba651b0752aba
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094538"
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>Podłączanie urządzenia do wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego (Linux)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>Kompilowanie i uruchamianie przykładowych C klient systemu Linux
Poniższe kroki pokazują, jak utworzyć aplikację kliencką, która komunikuje się za pomocą wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego. Ta aplikacja jest napisany w języku C i skompilować i uruchomić w Ubuntu Linux.

Aby wykonać te kroki, należy na urządzenie z systemem Ubuntu wersji 15.04 lub 15.10. Przed kontynuowaniem należy zainstalować wstępnie wymagane pakiety na urządzeniu z systemem Ubuntu przy użyciu następującego polecenia:

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>Instalowanie bibliotek klienta na urządzeniu z systemem
Biblioteki klienta usługi Azure IoT Hub są dostępne jako pakiet, który można zainstalować na Twoim Ubuntu urządzenia za pomocą **polecenia apt-get** polecenia. Wykonaj poniższe kroki, aby zainstalować pakiet, który zawiera usługi IoT Hub biblioteki i nagłówek pliki klienta na komputerze z systemem Ubuntu:

1. W powłoce Dodaj repozytorium AzureIoT do komputera:
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. Zainstaluj pakiet azure-iot-sdk-c-dev
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="install-the-parson-json-parser"></a>Zainstaluj analizatora Parson JSON
Biblioteki klienta usługi IoT Hub umożliwia analizowanie ładunków komunikat analizatora składni Parson JSON. W odpowiedni folder na komputerze sklonuj repozytorium Parson GitHub, używając następującego polecenia:

```
git clone https://github.com/kgabis/parson.git
```

## <a name="prepare-your-project"></a>Przygotowanie projektu programu
Na maszynie z systemem Ubuntu, Utwórz folder o nazwie **zdalnego\_monitorowania**. W **zdalnego\_monitorowania** folderu:

- Utworzyć cztery pliki **main.c**, **zdalnego\_monitoring.c**, **zdalnego\_monitoring.h**, i **CMakeLists.txt**.
- Utwórz folder o nazwie **parson**.

Skopiuj pliki **parson.c** i **parson.h** z lokalnej kopii repozytorium Parson do **zdalnego\_monitorowania parson** folderu.

W edytorze tekstu Otwórz **zdalnego\_monitoring.c** pliku. Dodaj następujące instrukcje `#include`:
   
```
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "parson.h"
```

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="call-the-remotemonitoringrun-function"></a>Wywołania zdalnej\_monitorowania\_Uruchom — funkcja
W edytorze tekstu Otwórz **remote_monitoring.h** pliku. Dodaj następujący kod:

```
void remote_monitoring_run(void);
```

W edytorze tekstu Otwórz **main.c** pliku. Dodaj następujący kod:

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="build-and-run-the-application"></a>Kompilowanie i uruchamianie aplikacji
W poniższych krokach opisano sposób użycia *CMake* do tworzenia aplikacji klienckiej.

1. W edytorze tekstu Otwórz **CMakeLists.txt** w pliku **remote_monitoring** folderu.

1. Dodaj następujące instrukcje, aby zdefiniować sposób kompilowania aplikacji klienckiej:
   
    ```
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
1. W **remote_monitoring** folderze utwórz folder do przechowywania *upewnij* narzędzie CMake generuje pliki, a następnie uruchom **cmake** i **wprowadzić** polecenia w następujący sposób:
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. Uruchom aplikację klienta i wysyłanie danych telemetrycznych do Centrum IoT Hub:
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

