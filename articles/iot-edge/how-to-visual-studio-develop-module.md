---
title: Tworzenie i debugowanie modułów w programie Visual Studio — usługa Azure IoT Edge
description: Użyj programu Visual Studio z narzędziami Usługi Azure IoT, aby opracować moduł usługi C lub C# IoT Edge i wypchnąć go z centrum IoT hub do urządzenia IoT, zgodnie z manifestem wdrożenia.
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 3/27/2020
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 9722c7dec3a066d8f776424cb599be0d463416d9
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384861"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Tworzenie i debugowanie modułów usługi Azure IoT Edge za pomocą programu Visual Studio 2019

Możesz przekształcić logikę biznesową w moduły dla usługi Azure IoT Edge. W tym artykule pokazano, jak używać programu Visual Studio 2019 jako głównego narzędzia do tworzenia i debugowania modułów.

Narzędzia usługi Azure IoT Edge tools for Visual Studio zapewniają następujące korzyści:

- Tworzenie, edytowanie, tworzenie, uruchamianie i debugowanie rozwiązań i modułów usługi Azure IoT Edge na lokalnym komputerze deweloperskim.
- Wdrażanie rozwiązania usługi Azure IoT Edge na urządzeniu usługi Azure IoT Edge za pośrednictwem usługi Azure IoT Hub.
- Kodowanie modułów IoT platformy Azure w języku C lub C# przy jednoczesnym uzyskaniu wszystkich korzyści płynących z tworzenia programu Visual Studio.
- Zarządzanie urządzeniami i modułami usługi Azure IoT Edge za pomocą interfejsu użytkownika.

W tym artykule pokazano, jak używać narzędzi Azure IoT Edge Tools for Visual Studio 2019 do tworzenia modułów usługi IoT Edge. Dowiesz się również, jak wdrożyć projekt na urządzeniu usługi Azure IoT Edge. Obecnie visual studio 2019 zapewnia obsługę modułów napisanych w języku C i C#. Obsługiwane architektury urządzeń to Windows X64 i Linux X64 lub ARM32. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych, języków i architektur, zobacz [Obsługa języka i architektury](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że używasz komputera lub maszyny wirtualnej z systemem Windows jako komputera dewelopera. Na komputerach z systemem Windows można tworzyć moduły systemu Windows lub Linux. Aby opracować moduły systemu Windows, należy użyć komputera z systemem Windows w wersji 1809/build 17763 lub nowszej. Aby opracować moduły systemu Linux, należy użyć komputera z systemem Windows, który spełnia [wymagania platformy Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Ponieważ w tym artykule używa programu Visual Studio 2019 jako głównego narzędzia do tworzenia, zainstaluj program Visual Studio. Upewnij się, że w instalacji programu Visual Studio 2019 uwzględniłeś **deweloperskie** platformy Azure i dewelopera pulpitu z obciążeniami **języka C++.** Można [zmodyfikować visual studio 2019,](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) aby dodać wymagane obciążenia.

Po przygotowaniu programu Visual Studio 2019 potrzebne są również następujące narzędzia i składniki:

- Pobierz i zainstaluj [narzędzia Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) z witryny Visual Studio marketplace, aby utworzyć projekt usługi IoT Edge w programie Visual Studio 2019.

> [!TIP]
> Jeśli korzystasz z programu Visual Studio 2017, pobierz i zainstaluj [narzędzia Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) for VS 2017 z portalu Visual Studio marketplace

- Pobierz i zainstaluj [docker Community Edition](https://docs.docker.com/install/) na komputerze deweloperskim, aby tworzyć i uruchamiać obrazy modułów. Musisz ustawić docker CE do pracy w trybie kontenera systemu Linux lub w trybie kontenera systemu Windows.

- Skonfiguruj lokalne środowisko programistyczne do debugowania, uruchamiania i testowania rozwiązania usługi IoT Edge, instalując [narzędzie deweloperskie Usługi Azure IoT EdgeHub.](https://pypi.org/project/iotedgehubdev/) Zainstaluj [Pythona (2.7/3.6+) i Pip,](https://www.python.org/) a następnie zainstaluj pakiet **iotedgehubdev,** uruchamiając następujące polecenie w terminalu. Upewnij się, że wersja narzędzia deweloperskiego usługi Azure IoT EdgeHub jest większa niż 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Sklonuj repozytorium i zainstaluj menedżera biblioteki Vcpkg, a następnie zainstaluj **pakiet azure-iot-sdk-c** dla systemu Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Lokalnego rejestru platformy Docker można używać do celów prototypowych i testowych zamiast rejestru w chmurze.

- Aby przetestować moduł na urządzeniu, musisz aktywnego centrum IoT hub z co najmniej jednym urządzeniem Usługi IoT Edge. Aby używać komputera jako urządzenia usługi IoT Edge, wykonaj czynności opisane w przewodniku Szybki start dla [systemu Linux](quickstart-linux.md) lub [Windows](quickstart.md). Jeśli na komputerze deweloperskim jest uruchomiony demon usługi IoT Edge, może być konieczne zatrzymanie aplikacji EdgeHub i EdgeAgent przed rozpoczęciem tworzenia programu Visual Studio.

### <a name="check-your-tools-version"></a>Sprawdzanie wersji narzędzi

1. Z menu **Rozszerzenia** wybierz polecenie **Zarządzaj rozszerzeniami**. Rozwiń **pozycję Zainstalowane narzędzia >,** aby znaleźć **narzędzia Azure IoT Edge tools dla programu Visual Studio** i Cloud Explorer dla programu Visual **Studio.**

1. Zanotuj zainstalowaną wersję. Możesz porównać tę wersję z najnowszą wersją w witrynie Visual Studio Marketplace ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Jeśli wersja jest starsza niż ta dostępna w programie Visual Studio Marketplace, zaktualizuj narzędzia w programie Visual Studio, jak pokazano w poniższej sekcji.

### <a name="update-your-tools"></a>Aktualizowanie narzędzi

1. W oknie **Zarządzanie rozszerzeniami** rozwiń pozycję **Aktualizacje > portalu Visual Studio Marketplace**, wybierz pozycję Narzędzia Usługi Azure **IoT Edge** lub Cloud Explorer dla programu Visual **Studio** i wybierz pozycję **Aktualizuj**.

1. Po pobraniu aktualizacji narzędzi zamknij program Visual Studio, aby wyzwolić aktualizację narzędzi przy użyciu instalatora VSIX.

1. W instalatorze wybierz **przycisk OK,** aby rozpocząć, a następnie **modyfikuj,** aby zaktualizować narzędzia.

1. Po zakończeniu aktualizacji wybierz pozycję **Zamknij** i uruchom ponownie program Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Tworzenie projektu usługi Azure IoT Edge

Szablon projektu usługi Azure IoT Edge w programie Visual Studio tworzy projekt, który można wdrożyć na urządzeniach usługi Azure IoT Edge w usłudze Azure IoT Hub. Najpierw utwórz rozwiązanie usługi Azure IoT Edge, a następnie wygenerujesz pierwszy moduł w tym rozwiązaniu. Każde rozwiązanie IoT Edge może zawierać więcej niż jeden moduł.

> [!TIP]
> Struktura projektu usługi IoT Edge utworzona przez program Visual Studio nie jest taka sama jak w programie Visual Studio Code.

1. W nowym oknie dialogowym projektu programu Visual Studio wyszukaj i wybierz projekt **Usługi Azure IoT Edge,** a następnie kliknij przycisk **Dalej**. W oknie konfiguracji projektu wprowadź nazwę projektu i określ lokalizację, a następnie wybierz pozycję **Utwórz**. Domyślna nazwa projektu to **AzureIoTEdgeApp1**.

   ![Tworzenie nowego projektu](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. W oknie **Dodaj aplikację i moduł usługi IoT Edge** wybierz moduł **C#** lub **moduł C,** a następnie określ nazwę modułu i repozytorium obrazów modułu. Program Visual Studio automatycznie wypełnia nazwę modułu **hostem lokalnym:5000/<nazwę\>modułu.** Zastąp go własnymi informacjami rejestru. Jeśli używasz lokalnego rejestru platformy Docker do testowania, **localhost** jest w porządku. Jeśli używasz usługi Azure Container Registry, użyj serwera logowania z ustawień rejestru. Serwer logowania wygląda jak ** _ \<nazwa\>rejestru_.azurecr.io**. Zastąp tylko **localhost:5000** część ciągu tak, aby wynik końcowy wygląda jak ** \< *nazwa*\>rejestru .azurecr.io/_\<\>nazwę modułu_**. Domyślna nazwa **modułu to IotEdgeModule1**

   ![Dodaj aplikację i moduł](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Wybierz **przycisk OK,** aby utworzyć rozwiązanie usługi Azure IoT Edge z modułem, który używa języka C# lub C.

Teraz masz projekt **AzureIoTEdgeApp1.Linux.Amd64** lub projekt **AzureIoTEdgeApp1.Windows.Amd64,** a także projekt **IotEdgeModule1 w rozwiązaniu.** Każdy projekt **AzureIoTEdgeApp1** ma `deployment.template.json` plik, który definiuje moduły, które chcesz skompilować i wdrożyć dla rozwiązania usługi IoT Edge, a także definiuje trasy między modułami. Rozwiązanie domyślne ma moduł **SimulatedTemperatureSensor** i moduł **IotEdgeModule1.** Moduł **SimulatedTemperatureSensor** generuje symulowane dane do **modułu IotEdgeModule1,** podczas gdy domyślny kod w module **IotEdgeModule1** bezpośrednio przesyła odebrane wiadomości do usługi Azure IoT Hub.

Aby zobaczyć, jak działa symulowany czujnik temperatury, wyświetl [kod źródłowy SimulatedTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

Projekt **IotEdgeModule1** jest aplikacją konsoli .NET Core 2.1, jeśli jest to moduł C#. Zawiera wymagane pliki platformy Docker potrzebne dla urządzenia Usługi IoT Edge z kontenerem systemu Windows lub kontenerem Linux. Plik `module.json` opisuje metadane modułu. Rzeczywisty kod modułu, który przyjmuje zestaw SDK urządzenia Usługi Azure `Program.cs` `main.c` IoT jako zależność, znajduje się w pliku lub.

## <a name="develop-your-module"></a>Opracowanie modułu

Domyślny kod modułu dostarczany z rozwiązaniem znajduje się w **Program.cs IotEdgeModule1** > **Program.cs** (dla języka C#) lub **main.c** (C). Moduł i `deployment.template.json` plik są skonfigurowane, dzięki czemu można utworzyć rozwiązanie, wypchnąć go do rejestru kontenerów i wdrożyć go na urządzeniu, aby rozpocząć testowanie bez dotykania kodu. Moduł jest zbudowany do podjęcia danych wejściowych ze źródła (w tym przypadku **moduł SimulatedTemperatureSensor,** który symuluje dane) i potoku go do usługi Azure IoT Hub.

Gdy chcesz dostosować szablon modułu za pomocą własnego kodu, użyj [zestawów SDK usługi Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) do tworzenia modułów, które zaspokajają kluczowe potrzeby rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodność.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicjowanie iotedgehubdev z ciągiem połączenia urządzenia usługi IoT Edge

1. Skopiuj ciąg połączenia dowolnego urządzenia usługi IoT Edge z **podstawowego ciągu połączenia** w Eksploratorze chmury programu Visual Studio. Pamiętaj, aby nie kopiować ciągu połączenia urządzenia innego niż Edge, ponieważ ikona urządzenia Usługi IoT Edge różni się od ikony urządzenia innego niż edge.

   ![Kopiuj ciąg połączenia urządzenia krawędziowego](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Z menu **Narzędzia** wybierz polecenie **Azure IoT Tools** > **Setup IoT Edge Simulator**, wklej ciąg połączenia i kliknij przycisk **OK**.

   ![Okno ciąg połączenia Otwórz ciąg połączenia set edge](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Wprowadź ciąg połączenia od pierwszego kroku, a następnie wybierz **przycisk OK**.

> [!NOTE]
> Należy wykonać te kroki tylko raz na komputerze deweloperskim, ponieważ wyniki są automatycznie stosowane do wszystkich kolejnych rozwiązań usługi Azure IoT Edge. Ta procedura może być stosowana ponownie, jeśli trzeba zmienić na inny ciąg połączenia.

## <a name="build-and-debug-single-module"></a>Tworzenie i debugowanie pojedynczego modułu

Zazwyczaj należy przetestować i debugować każdy moduł przed uruchomieniem go w ramach całego rozwiązania z wieloma modułami.

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **pozycję IotEdgeModule1** i wybierz polecenie **Ustaw jako projekt startowy** z menu kontekstowego.

   ![Ustawianie projektu rozruchu](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Naciśnij **klawisz F5** lub kliknij przycisk poniżej, aby uruchomić moduł; może upłynąć 10&ndash;20 sekund przy pierwszym uruchomieniu.

   ![Uruchom moduł](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Jeśli moduł został pomyślnie zainicjowany, powinna zostać wyświetlona aplikacja konsoli .NET Core.

   ![Moduł uruchomiony](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Jeśli rozwija się w języku C#, ustaw punkt przerwania w `PipeMessage()` funkcji w **Program.cs;** w przypadku korzystania z C, `InputQueue1Callback()` ustaw punkt przerwania w funkcji w **main.c**. Następnie można go przetestować, wysyłając wiadomość, uruchamiając następujące polecenie w powłoce **Git Bash** lub **WSL Bash.** (Nie można `curl` uruchomić polecenia z programu PowerShell lub wiersza polecenia).

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Debugowanie pojedynczego modułu](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Punkt przerwania powinien zostać wyzwolony. Zmienne można obserwować w oknie **Zmiennych** programu Visual Studio.

   > [!TIP]
   > Do wysyłania [PostMan](https://www.getpostman.com/) `curl`wiadomości zamiast .

1. Naciśnij **klawisze Ctrl + F5** lub kliknij przycisk stop, aby zatrzymać debugowanie.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Tworzenie i debugowanie rozwiązania IoT Edge z wieloma modułami

Po zakończeniu tworzenia pojedynczego modułu, można uruchomić i debugować całe rozwiązanie z wielu modułów.

1. W **Eksploratorze rozwiązań**dodaj drugi moduł do rozwiązania, klikając prawym przyciskiem myszy **pozycję AzureIoTEdgeApp1** i wybierając **pozycję Dodaj** > **nowy moduł IoT Edge**. Domyślna nazwa drugiego modułu to **IotEdgeModule2** i będzie działać jako inny moduł rury.

1. Otwórz plik, `deployment.template.json` a zobaczysz **IotEdgeModule2** został dodany w sekcji **modułów.** Wymień sekcję **tras** na następującą. Jeśli nazwy modułów zostały dostosowane, należy zaktualizować te nazwy, aby były zgodne.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Kliknij prawym przyciskiem myszy **pozycję AzureIoTEdgeApp1** i wybierz polecenie **Ustaw jako projekt startowy** z menu kontekstowego.

1. Utwórz punkty przerwania, a następnie naciśnij **klawisz F5,** aby uruchamiać i debugować wiele modułów jednocześnie. Powinno być widoczna wiele okien aplikacji konsoli .NET Core, z których każde okno reprezentuje inny moduł.

   ![Debugowanie wielu modułów](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Naciśnij **klawisze Ctrl + F5** lub wybierz przycisk stop, aby zatrzymać debugowanie.

## <a name="build-and-push-images"></a>Tworzenie i wypychanie obrazów

1. Upewnij się, że **azureiotedgeapp1** jest projektem startowym. Wybierz **debugowanie** lub **zwolnij** jako konfigurację do utworzenia dla obrazów modułu.

    > [!NOTE]
    > Wybierając **debugowanie**program Visual `Dockerfile.(amd64|windows-amd64).debug` Studio używa do tworzenia obrazów platformy Docker. Obejmuje to debugera wiersza polecenia .NET Core VSDBG w obrazie kontenera podczas jego tworzenia. W przypadku gotowych do produkcji modułów usługi IoT Edge zaleca `Dockerfile.(amd64|windows-amd64)` się użycie konfiguracji **release,** która używa bez vsdbg.

1. Jeśli używasz rejestru prywatnego, takiego jak Usługa Azure Container Registry (ACR), użyj następującego polecenia platformy Docker, aby się do niego zalogować.  Nazwę użytkownika i hasło można uzyskać ze strony **klucze dostępu** rejestru w witrynie Azure portal. Jeśli używasz rejestru lokalnego, możesz [uruchomić rejestr lokalny](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Jeśli używasz rejestru prywatnego, takiego jak Usługa Azure Container Registry, musisz dodać informacje logowania `deployment.template.json`do rejestru do ustawień środowiska uruchomieniowego znalezionych w pliku . Zastąp symbole zastępcze rzeczywistą nazwą użytkownika, hasłem i nazwą rejestru administratora ACR.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **pozycję AzureIoTEdgeApp1** i wybierz polecenie **Kompilacja i wypychanie modułów brzegowych IoT,** aby utworzyć i wypchnąć obraz platformy Docker dla każdego modułu.

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

W artykule Szybki start, który był używany do skonfigurowania urządzenia usługi IoT Edge, wdrożono moduł za pomocą witryny Azure Portal. Można również wdrożyć moduły przy użyciu Cloud Explorer dla programu Visual Studio. Masz już manifest wdrożenia przygotowany dla `deployment.json` twojego scenariusza, plik i wszystko, co musisz zrobić, to wybrać urządzenie do odbierania wdrożenia.

1. Otwórz **Eksploratora chmury,** klikając **pozycję Wyświetl** > **Eksploratora chmury**. Upewnij się, że zalogowano się do programu Visual Studio 2019.

1. W **Eksploratorze chmury**rozwiń subskrypcję, znajdź centrum Usługi Azure IoT Hub i urządzenie usługi Azure IoT Edge, które chcesz wdrożyć.

1. Kliknij prawym przyciskiem myszy urządzenie usługi IoT Edge, aby utworzyć dla niego wdrożenie. Przejdź do manifestu wdrażania skonfigurowanego dla platformy znajdującego się w folderze **konfiguracji** w rozwiązaniu programu Visual Studio, takim jak `deployment.arm32v7.json`.

1. Kliknij przycisk odświeżania, aby wyświetlić nowe moduły uruchomione wraz z **modułem SimulatedTemperatureSensor** oraz **$edgeAgent** i **$edgeHub**.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

1. Aby monitorować komunikat D2C dla określonego urządzenia usługi IoT-Edge, wybierz go w centrum IoT w **Eksploratorze chmury,** a następnie kliknij przycisk **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzeń w** oknie **Akcja.**

1. Aby zatrzymać monitorowanie danych, wybierz **pozycję Zatrzymaj wbudowany punkt końcowy zdarzenia monitorowania** w oknie **Akcja.**

## <a name="next-steps"></a>Następne kroki

Aby opracować niestandardowe moduły dla urządzeń usługi IoT Edge, [należy zrozumieć i używać zestawów SDK usługi Azure IoT Hub.](../iot-hub/iot-hub-devguide-sdks.md)
