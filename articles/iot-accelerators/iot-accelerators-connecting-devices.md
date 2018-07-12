---
title: Aprowizacja urządzeń Windows do zdalnego monitorowania w języku C — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób łączenia urządzenia do akceleratora rozwiązania monitorowania zdalnego przy użyciu aplikacji napisanych w języku C w systemie Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 139daea3e885636b352d4c9a1ba2651a24195b21
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309874"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Podłączanie urządzenia do akceleratora rozwiązań zdalnego monitorowania (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

W tym samouczku dowiesz się, jak połączyć urządzenie fizyczne do akceleratora rozwiązania monitorowania zdalnego.

## <a name="create-a-c-client-solution-on-windows"></a>Tworzenie rozwiązania klienta C na Windows

Podobnie jak w przypadku najbardziej osadzone aplikacje działają na urządzeniach ograniczone, kod klienta dla aplikacji urządzeń są zapisywane w C. W tym samouczku utworzysz aplikację na komputerze z systemem Windows.

### <a name="create-the-starter-project"></a>Utwórz projekt startowy

Utwórz projekt startowy w programie Visual Studio 2017 i Dodaj pakiety NuGet klienta urządzenia usługi IoT Hub:

1. W programie Visual Studio Utwórz aplikację konsolową C przy użyciu języka Visual C++ **aplikacji konsoli Windows** szablonu. Nadaj projektowi nazwę **RMDevice**.

    ![Tworzenie aplikacji konsoli Visual C++ Windows](./media/iot-accelerators-connecting-devices/visualstudio01.png)

1. W **Eksploratora rozwiązań**, Usuń pliki `stdafx.h`, `targetver.h`, i `stdafx.cpp`.

1. W **Eksploratora rozwiązań**, Zmień nazwę pliku `RMDevice.cpp` do `RMDevice.c`.

    ![Przedstawiający Eksploratora rozwiązań, zmieniono nazwę pliku RMDevice.c](./media/iot-accelerators-connecting-devices/visualstudio02.png)

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **RMDevice** projektu, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**. Wybierz **Przeglądaj**, a następnie wyszukaj i zainstaluj następujące pakiety NuGet:

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![Menedżer pakietów NuGet zawiera zainstalowane pakiety Microsoft.Azure.IoTHub](./media/iot-accelerators-connecting-devices/visualstudio03.png)

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **RMDevice** projektu, a następnie wybierz **właściwości** można otworzyć projektu **stron właściwości** okno dialogowe. Aby uzyskać więcej informacji, zobacz [ustawienie właściwości projektu Visual C++](https://docs.microsoft.com/cpp/ide/working-with-project-properties).

1. Wybierz **C/C++** folderu, wybierz **prekompilowanych nagłówków** stronę właściwości.

1. Ustaw **Prekompilowanego nagłówka** do **prekompilowane nagłówki nie są używane**. Następnie wybierz **Zastosuj**.

    ![Właściwości projektu, wyświetlić projekt nie używa prekompilowanych nagłówków](./media/iot-accelerators-connecting-devices/visualstudio04.png)

1. Wybierz **konsolidatora** folderu, wybierz **dane wejściowe** stronę właściwości.

1. Dodaj `crypt32.lib` do **dodatkowe zależności** właściwości. Aby zapisać projekt wartości właściwości, wybierz **OK** i następnie **OK** ponownie.

    ![Właściwości projektu Pokaż tym crypt32.lib konsolidatora](./media/iot-accelerators-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>Dodaj bibliotekę Parson JSON

Dodaj bibliotekę Parson JSON do **RMDevice** projektu, a następnie dodaj wymagane `#include` instrukcji:

1. W odpowiedni folder na komputerze sklonuj repozytorium Parson GitHub, używając następującego polecenia:

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Kopia `parson.h` i `parson.c` pliki z lokalnej kopii repozytorium Parson do Twojej **RMDevice** folderu projektu.

1. W programie Visual Studio, kliknij prawym przyciskiem myszy **RMDevice** projektu, wybierz **Dodaj**, a następnie wybierz **istniejący element**.

1. W **Dodaj istniejący element** okno dialogowe, wybierz opcję `parson.h` i `parson.c` pliki **RMDevice** folderu projektu. Aby dodać te dwa pliki do projektu, wybierz **Dodaj**.

    ![Eksplorator rozwiązań pokazuje parson.h i parson.c plików](./media/iot-accelerators-connecting-devices/visualstudio06.png)

1. W programie Visual Studio, otwórz `RMDevice.c` pliku. Zastąp istniejące `#include` instrukcji z następującym kodem:

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

    > [!NOTE]
    > Teraz możesz zweryfikować, że projekt ma prawidłowe zależności, konfigurowanie, tworząc rozwiązania.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Kompilowanie i uruchamianie przykładu

Dodaj kod, aby wywołać **zdalnego\_monitorowania\_Uruchom** funkcji, a następnie kompilowanie i uruchamianie aplikacji urządzenia:

1. Do wywołania **zdalnego\_monitorowania\_Uruchom** funkcji, Zastąp **głównego** funkcja poniższym kodem:

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Wybierz **kompilacji** i następnie **Kompiluj rozwiązanie** do tworzenia aplikacji urządzenia.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy **RMDevice** projektu, wybierz **debugowania**, a następnie wybierz **Uruchom nowe wystąpienie** do uruchomienia przykładu . W konsoli są wyświetlane komunikaty w postaci:

    * Aplikacja wysyła telemetrię próbki do akceleratora rozwiązań.
    * Odbiera wartości żądane właściwości ustawione na pulpicie nawigacyjnym rozwiązania.
    * Odpowiada na metody wywołane z poziomu pulpitu nawigacyjnego rozwiązania.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
