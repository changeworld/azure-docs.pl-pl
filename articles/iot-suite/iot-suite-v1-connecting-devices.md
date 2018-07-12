---
title: Podłącz urządzenie w Windows przy użyciu języka C | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak połączyć urządzenie z usługi Azure IoT Suite we wstępnie skonfigurowanym rozwiązaniu monitorowania zdalnego przy użyciu aplikacji napisanych w języku C w systemie Windows.
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 56c58a31c6b20bd8da7d1442ae75425cb3e20f3d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38724015"
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Podłączanie urządzenia do wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego (Windows)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Tworzenie rozwiązania przykładowe C na Windows
Poniższe kroki pokazują, jak utworzyć aplikację kliencką, która komunikuje się za pomocą wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego. Ta aplikacja jest napisany w języku C i skompilować i uruchomić w Windows.

Utwórz projekt startowy w programie Visual Studio 2015 lub Visual Studio 2017 i Dodaj pakiety NuGet klienta urządzenia usługi IoT Hub:

1. W programie Visual Studio Utwórz aplikację konsolową C przy użyciu języka Visual C++ **Aplikacja konsoli Win32** szablonu. Nadaj projektowi nazwę **RMDevice**.
2. Na **ustawienia aplikacji** strony w **Kreatora aplikacji Win32**, upewnij się, że **aplikacji konsolowej programu** jest zaznaczone, a następnie usuń zaznaczenie pola wyboru **prekompilowany nagłówek** i **cykl projektowania zabezpieczeń (SDL) sprawdza**.
3. W **Eksploratora rozwiązań**, usuwanie plików w pliku stdafx.h, targetver.h i stdafx.cpp.
4. W **Eksploratora rozwiązań**, Zmień nazwę pliku RMDevice.cpp RMDevice.c.
5. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **RMDevice** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**. Kliknij przycisk **Przeglądaj**, a następnie wyszukaj i zainstaluj następujące pakiety NuGet:
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **RMDevice** projektu, a następnie kliknij przycisk **właściwości** można otworzyć projektu **stron właściwości** okno dialogowe. Aby uzyskać więcej informacji, zobacz [ustawienie właściwości projektu Visual C++][lnk-c-project-properties]. 
7. Kliknij przycisk **konsolidatora** folderu, następnie kliknij przycisk **dane wejściowe** stronę właściwości.
8. Dodaj **crypt32.lib** do **dodatkowe zależności** właściwości. Kliknij przycisk **OK** i następnie **OK** ponownie, aby zapisać projekt wartości właściwości.

Dodaj bibliotekę Parson JSON do **RMDevice** projektu, a następnie dodaj wymagane `#include` instrukcji:

1. W odpowiedni folder na komputerze sklonuj repozytorium Parson GitHub, używając następującego polecenia:

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Skopiuj pliki parson.h i parson.c z lokalnej kopii repozytorium Parson do Twojej **RMDevice** folderu projektu.

1. W programie Visual Studio, kliknij prawym przyciskiem myszy **RMDevice** projektu, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **istniejący element**.

1. W **Dodaj istniejący element** okno dialogowe, wybierz opcję parson.h i parson.c pliki **RMDevice** folderu projektu. Następnie kliknij przycisk **Dodaj** można dodać te dwa pliki do projektu.

1. W programie Visual Studio Otwórz plik RMDevice.c. Zastąp istniejące `#include` instrukcji z następującym kodem:
   
    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

    > [!NOTE]
    > Teraz możesz zweryfikować, że projekt ma prawidłowe zależności, konfigurowanie, tworząc go.

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

Dodaj kod, aby wywołać **zdalnego\_monitorowania\_Uruchom** funkcji, a następnie kompilowanie i uruchamianie aplikacji urządzenia.

1. Zastąp **głównego** funkcji przy użyciu następującego kodu, aby wywołać **zdalnego\_monitorowania\_Uruchom** funkcji:
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Kliknij przycisk **kompilacji** i następnie **Kompiluj rozwiązanie** do tworzenia aplikacji urządzenia.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **RMDevice** projektu, kliknij przycisk **debugowania**, a następnie kliknij przycisk **Uruchom nowe wystąpienie** do uruchomienia przykładu. W konsoli są wyświetlane komunikaty, jak aplikacja wysyła przykładowe dane telemetryczne do wstępnie skonfigurowanego rozwiązania, otrzymuje wartości żądane właściwości ustawione na pulpicie nawigacyjnym rozwiązania i reaguje na metody wywołane z poziomu pulpitu nawigacyjnego rozwiązania.

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx
