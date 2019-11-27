---
title: Opracowywanie i debugowanie modułów w programie Visual Studio — Azure IoT Edge | Microsoft Docs
description: Użyj programu Visual Studio 2019 do tworzenia i debugowania modułów dla Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 2f8b0fe83e10beb3b65dca08e18b03f4fc11947e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457113"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Użyj programu Visual Studio 2019 do tworzenia i debugowania modułów dla Azure IoT Edge

Logikę biznesową można przekształcić w moduły, dla usługi Azure IoT Edge. W tym artykule pokazano, jak używać programu Visual Studio 2019 jako głównego narzędzia do tworzenia i debugowania modułów.

Narzędzia usługi Azure IoT Edge dla programu Visual Studio zapewnia następujące korzyści:

- Twórz, Edytuj, Kompiluj, uruchamiaj i Debuguj Azure IoT Edge rozwiązania i moduły na lokalnym komputerze deweloperskim.
- Wdrażanie rozwiązania Azure IoT Edge na urządzeniu usługi Azure IoT Edge za pośrednictwem usługi Azure IoT Hub.
- Zakoduj moduły usługi Azure IoT w C# języku C lub korzystając ze wszystkich korzyści związanych z programowaniem w programie Visual Studio.
- Zarządzanie urządzeniami usługi Azure IoT Edge i modułów przy użyciu interfejsu użytkownika.

W tym artykule pokazano, jak za pomocą narzędzi Azure IoT Edge Tools for Visual Studio 2019 opracowywać moduły IoT Edge. Poznasz również sposób wdrażania projektu do Twojego urządzenia usługi Azure IoT Edge. Obecnie program Visual Studio 2019 zapewnia obsługę modułów pisanych w języku C i C#. Obsługiwane architektury urządzeń to Windows x64 i Linux x64 lub ARM32. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych, języków i architektur, zobacz temat [Obsługa języków i architektury](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że używasz komputera lub maszyny wirtualnej z systemem Windows jako komputerze deweloperskim. Na komputerach z systemem Windows można opracowywać moduły systemu Windows lub Linux. Aby opracowywać moduły systemu Windows, należy użyć komputera z systemem Windows w wersji 1809/Build 17763 lub nowszej. Aby utworzyć moduły systemu Linux, należy użyć komputera z systemem Windows, który spełnia [wymagania programu Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). 

Ponieważ ten artykuł używa programu Visual Studio 2019 jako głównego narzędzia deweloperskiego, zainstaluj program Visual Studio. Upewnij się, że w instalacji programu Visual Studio 2019 zawarto  **C++ aplikacje** deweloperskie i programowanie na **platformie Azure** . Aby dodać wymagane obciążenia, można [zmodyfikować program Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) .

Po przygotowaniu programu Visual Studio 2019 potrzebne są również następujące narzędzia i składniki:

- Pobierz i zainstaluj [narzędzia Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) z witryny Visual Studio Marketplace, aby utworzyć projekt IoT Edge w programie visual Studio 2019.

> [!TIP]
> Jeśli używasz programu Visual Studio 2017, Pobierz i zainstaluj [Azure IoT Edge narzędzia](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) dla programu vs 2017 z witryny Visual Studio Marketplace

- Pobierz i zainstaluj program [Docker Community Edition](https://docs.docker.com/install/) na komputerze deweloperskim, aby skompilować i uruchomić obrazy modułu. Należy ustawić platformę Docker CE do uruchamiania w trybie kontenera systemu Linux lub w trybie kontenera Windows.

- Skonfiguruj lokalne środowisko programistyczne, aby debugować, uruchamiać i testować rozwiązanie IoT Edge przez zainstalowanie [narzędzia deweloperskiego usługi Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/). Zainstaluj język [Python (2.7/3.6 +) i PIP](https://www.python.org/) , a następnie zainstaluj pakiet **iotedgehubdev** , uruchamiając następujące polecenie w terminalu. Upewnij się, że używana wersja narzędzia deweloperskiego EdgeHub IoT Azure jest większa niż 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Sklonuj repozytorium i zainstaluj program Vcpkg Library Manager, a następnie zainstaluj **pakiet Azure-IoT-SDK-c** dla systemu Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub [centrum Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Można użyć lokalnego rejestru platformy Docker prototypów i testowania zamiast rejestru chmury.

- Aby przetestować moduł na urządzeniu, musisz mieć aktywne Centrum IoT z co najmniej jednym urządzeniem IoT Edge. Aby użyć komputera jako urządzenia IoT Edge, wykonaj kroki opisane w przewodniku szybki start dla systemu [Linux](quickstart-linux.md) lub [Windows](quickstart.md). Jeśli uruchamiasz demona IoT Edge na komputerze deweloperskim, może być konieczne zatrzymanie EdgeHub i EdgeAgent przed rozpoczęciem tworzenia aplikacji w programie Visual Studio.

### <a name="check-your-tools-version"></a>Sprawdź swoją wersję narzędzia

1. W menu **Narzędzia** wybierz pozycję **rozszerzenia i aktualizacje**. Rozwiń węzeł **zainstalowane > narzędzia** i Znajdź **Azure IoT Edge narzędzia** i **Cloud Explorer dla programu Visual Studio**.

1. Zwróć uwagę na wersję zainstalowanego. Tę wersję można porównać z najnowszą wersją na Visual Studio Marketplace ([Eksplorator chmury](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. Jeśli Twoja wersja jest starsza niż dostępna w Visual Studio Marketplace, zaktualizuj swoje narzędzia w programie Visual Studio, jak pokazano w poniższej sekcji.

### <a name="update-your-tools"></a>Aktualizowanie narzędzi

1. W oknie dialogowym **rozszerzenia i aktualizacje** rozwiń węzeł **Aktualizacje > Visual Studio Marketplace**, wybierz pozycję **narzędzia Azure IoT Edge** lub **Eksplorator chmury dla programu Visual Studio** , a następnie wybierz pozycję **Aktualizuj**.

1. Po pobraniu aktualizacji narzędzi, zamknij program Visual Studio, aby wywoływany narzędzia aktualizacji za pomocą Instalatora VSIX.

1. W instalatorze wybierz **przycisk OK** , aby rozpocząć, a następnie **zmodyfikuj** , aby zaktualizować narzędzia.

1. Po zakończeniu aktualizacji wybierz pozycję **Zamknij** i uruchom ponownie program Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Tworzenie projektu usługi Azure IoT Edge

Szablon projektu usługi Azure IoT Edge w programie Visual Studio tworzy projekt, który można wdrożyć na urządzeniach z usługą Azure IoT Edge w usłudze Azure IoT Hub. Najpierw należy utworzyć rozwiązanie Azure IoT Edge, a następnie wygenerować pierwszy moduł w tym rozwiązaniu. Każde rozwiązanie IoT Edge może zawierać więcej niż jeden moduł.

> [!TIP]
> Struktura projektu IoT Edge utworzona przez program Visual Studio nie jest taka sama jak w programie Visual Studio Code.

1. W oknie dialogowym Nowy projekt programu Visual Studio Wyszukaj i wybierz pozycję **Azure IoT Edge** projekt, a następnie kliknij przycisk **dalej**. W oknie Konfiguracja projektu wprowadź nazwę projektu i określ lokalizację, a następnie wybierz pozycję **Utwórz**. Domyślna nazwa projektu to **AzureIoTEdgeApp1**.

   ![Utwórz nowy projekt](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. W oknie **Dodawanie IoT Edge aplikacji i modułu** wybierz pozycję  **C# moduł** lub **C** , a następnie określ nazwę modułu i repozytorium obrazu modułu. Program Visual Studio automatycznie wypełnia nazwę modułu nazwą **localhost: 5000/< nazwą modułu\>** . Zastąp go własną informacje rejestru. Jeśli do testowania używasz lokalnego rejestru platformy Docker, **hosty localhost** jest w prawidłowym zakresie. Jeśli korzystasz z usługi Azure Container Registry, Użyj serwera logowania z ustawień w rejestrze. Serwer logowania wygląda jak  **_\<nazwa rejestru\>_ . azurecr.IO**. Zastąp tylko wartość **localhost: 5000** części ciągu, aby wynik końcowy wyglądał jak **\<*nazwa rejestru*\>. azurecr.IO/ _\<nazwę modułu\>_** . Domyślna nazwa modułu to **IotEdgeModule1**

   ![Dodaj aplikację i moduł](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Wybierz **przycisk OK** , aby utworzyć rozwiązanie Azure IoT Edge przy użyciu modułu korzystającego C# z języka lub C.

Teraz masz projekt **AzureIoTEdgeApp1. Linux. amd64** lub projekt **AzureIoTEdgeApp1. Windows. amd64** , a także projekt **IotEdgeModule1** w rozwiązaniu. Każdy projekt **AzureIoTEdgeApp1** ma plik `deployment.template.json`, który definiuje moduły, które mają zostać skompilowane i wdrożone dla rozwiązania IoT Edge, a także definiuje trasy między modułami. Domyślne rozwiązanie ma moduł **SimulatedTemperatureSensor** i moduł **IotEdgeModule1** . Moduł **SimulatedTemperatureSensor** generuje symulowane dane do modułu **IotEdgeModule1** , podczas gdy domyślny kod w module **IotEdgeModule1** bezpośrednio potoki odbierają komunikaty do IoT Hub platformy Azure.

Projekt **IotEdgeModule1** jest aplikacją konsolową programu .net Core 2,1, jeśli jest to C# moduł. Zawiera wymagane pliki platformy Docker, które są potrzebne w przypadku urządzenia IoT Edge działającego z kontenerem kontenera systemu Windows lub Linux. Plik `module.json` opisuje metadane modułu. Rzeczywisty kod modułu, który pobiera zestaw SDK urządzenia usługi Azure IoT jako zależność, znajduje się w pliku `Program.cs` lub `main.c`.

## <a name="develop-your-module"></a>Tworzenie modułu

Domyślny kod modułu, który jest dostarczany z rozwiązaniem, znajduje się w lokalizacji **IotEdgeModule1** > program.cs C#(for) lub **Main. c** (c). Moduł i plik `deployment.template.json` są skonfigurowane tak, aby można było skompilować rozwiązanie, wypchnąć je do rejestru kontenerów i wdrożyć je na urządzeniu w celu uruchomienia testowania bez dotykania kodu. Moduł jest zbudowany z myślą o wejściu ze źródła (w tym przypadku modułu **SimulatedTemperatureSensor** , który symuluje dane) i potoku do IoT Hub platformy Azure.

Gdy wszystko jest gotowe do dostosowania szablonu modułu przy użyciu własnego kodu, użyj [zestawów sdk IoT Hub platformy Azure](../iot-hub/iot-hub-devguide-sdks.md) do kompilowania modułów, które zaspokoją kluczowe potrzeby rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodność.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Zainicjuj iotedgehubdev z parametrami połączenia urządzenia IoT Edge

1. Skopiuj parametry połączenia dowolnego IoT Edge urządzenia z **podstawowych parametrów połączenia** w programie Visual Studio Cloud Explorer. Nie należy kopiować parametrów połączenia urządzenia niegranicznego, ponieważ ikona urządzenia IoT Edge różni się od ikony urządzenia niebędącego krawędzią.

   ![Skopiuj parametry połączenia urządzenia usługi Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Przejdź do pozycji **narzędzia** > **narzędzia Azure IoT Edge** > **Instalatora IoT Edge symulator**, wklej parametry połączenia i kliknij przycisk **OK**.

   ![Otwórz okno ciąg połączenia krawędzi zestawu](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Wprowadź parametry połączenia z pierwszego kroku, a następnie wybierz przycisk **OK**.

> [!NOTE]
> Należy wykonać te czynności tylko raz na komputerze deweloperskim, ponieważ wyniki są automatycznie stosowane do wszystkich kolejnych rozwiązań Azure IoT Edge. Po wykonaniu tej procedury można zmienić na inne parametry połączenia.

## <a name="build-and-debug-single-module"></a>Kompiluj i Debuguj pojedynczy moduł

Zazwyczaj należy przetestować i debugować każdy moduł przed uruchomieniem go w całym rozwiązaniu z wieloma modułami.

1. Kliknij prawym przyciskiem myszy pozycję **IotEdgeModule1** , a następnie wybierz pozycję **Ustaw jako projekt startowy** z menu kontekstowego.

   ![Ustaw projekt uruchamiania](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Naciśnij klawisz **F5** lub kliknij poniższy przycisk, aby uruchomić moduł; po raz pierwszy może upłynąć 10&ndash;20 sekund.

   ![Uruchom moduł](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Jeśli moduł został zainicjowany pomyślnie, powinna zostać wyświetlona Aplikacja konsolowa platformy .NET Core.

   ![Moduł uruchamiania](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. W przypadku programowania C#w programie Ustaw punkt przerwania w funkcji `PipeMessage()` w **program.cs**; Jeśli używany jest język C, ustaw punkt przerwania w funkcji `InputQueue1Callback()` w **Main. C**. Następnie można go przetestować, wysyłając komunikat, uruchamiając następujące polecenie w powłoce **git bash** lub **WSL bash** . (Nie można uruchomić polecenia `curl` z programu PowerShell lub wiersza polecenia).

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Debugowanie jednego modułu](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Powinna być wyzwolona punkt przerwania. Możesz obejrzeć zmienne w oknie zmiennych **lokalnych** programu Visual Studio.

   > [!TIP]
   > Możesz również użyć narzędzia do [publikowania](https://www.getpostman.com/) lub innych narzędzi API do wysyłania komunikatów zamiast `curl`.

1. Naciśnij **klawisze CTRL + F5** lub kliknij przycisk Zatrzymaj, aby zatrzymać debugowanie.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Kompilowanie i debugowanie rozwiązania usługi IoT Edge z wieloma modułami

Po zakończeniu opracowywania jednego modułu możesz chcieć uruchomić i debugować całe rozwiązanie z wieloma modułami.

1. Aby dodać drugi moduł do rozwiązania, kliknij prawym przyciskiem myszy pozycję **AzureIoTEdgeApp1** i wybierz pozycję **dodaj** > **nowym IoT Edge module**. Domyślną nazwą drugiego modułu jest **IotEdgeModule2** i będzie działać jako inny moduł potoku.

1. Otwórz plik `deployment.template.json` i zobaczysz, że w sekcji **modułów** został dodany **IotEdgeModule2** . Zastąp sekcję **Routes** poniższymi. Jeśli dostosowano nazwy modułów, upewnij się, że te nazwy zostały zaktualizowane tak, aby były zgodne.

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. Kliknij prawym przyciskiem myszy pozycję **AzureIoTEdgeApp1** , a następnie wybierz pozycję **Ustaw jako projekt startowy** z menu kontekstowego.

1. Utwórz punkty przerwania, a następnie naciśnij klawisz **F5** , aby uruchomić i debugować jednocześnie wiele modułów. Powinna zostać wyświetlona wiele okien aplikacji konsoli .NET Core, których każde okno reprezentuje inny moduł.

   ![Debuguj wiele modułów](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Naciśnij **klawisze CTRL + F5** lub wybierz przycisk Zatrzymaj, aby zatrzymać debugowanie.

## <a name="build-and-push-images"></a>Kompilowanie i wypychanie obrazów

1. Upewnij się, że **AzureIoTEdgeApp1** jest projektem startowym. Wybierz opcję **Debuguj** lub **Zwolnij** jako konfigurację, aby skompilować obrazy modułu.

    > [!NOTE]
    > Podczas wybierania **debugowania**program Visual Studio używa `Dockerfile.(amd64|windows-amd64).debug` do kompilowania obrazów platformy Docker. W tym VSDBG debuger wiersza polecenia platformy .NET Core w obrazie kontenera podczas jego tworzenia. W przypadku modułów IoT Edge gotowych do produkcji zalecamy użycie konfiguracji **wydania** , która używa `Dockerfile.(amd64|windows-amd64)` bez VSDBG.

1. Jeśli używasz prywatnego rejestru, takiego jak Azure Container Registry, użyj następującego polecenia platformy Docker, aby zalogować się do niego. W przypadku korzystania z rejestru lokalnego można [uruchomić rejestr lokalny](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Jeśli używasz prywatnego rejestru, takiego jak Azure Container Registry, musisz dodać informacje logowania do rejestru do ustawień środowiska uruchomieniowego znalezionych w pliku `deployment.template.json`. Zastąp symbole zastępcze rzeczywistą nazwą użytkownika administratora ACR, hasło i nazwę rejestru.

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

1. Kliknij prawym przyciskiem myszy pozycję **AzureIoTEdgeApp1** i wybierz opcję **Kompiluj i wypchnij rozwiązanie** w celu skompilowania i wypchnięcia obrazu platformy Docker dla każdego modułu.

   ![Kompilowanie i wypychanie obrazów](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

W artykule Szybki start, który był używany do skonfigurowania urządzenia usługi IoT Edge, wdrożono moduł za pomocą witryny Azure Portal. Można także wdrożyć modułów za pomocą Eksploratora chmury dla programu Visual Studio. Masz już wdrożony manifest wdrożenia dla danego scenariusza, plik `deployment.json` i wszystko, co musisz zrobić, to wybierz urządzenie, na którym ma zostać odebrane wdrożenie.

1. Otwórz **Eksploratora chmury** , klikając pozycję **Wyświetl** > w **Eksploratorze chmury**. Upewnij się, że zalogowano się do programu Visual Studio 2019.

1. W programie **Cloud Explorer**Rozwiń swoją subskrypcję, Znajdź IoT Hub platformy Azure i urządzenie Azure IoT Edge, które chcesz wdrożyć.

1. Kliknij prawym przyciskiem myszy urządzenie IoT Edge, aby utworzyć dla niego wdrożenie, należy wybrać plik manifestu wdrożenia w `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Nie należy wybierać `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Kliknij przycisk Odśwież, aby zobaczyć nowe moduły z uruchomionym modułem **SimulatedTemperatureSensor** oraz **$edgeAgent** i **$edgeHub**.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

1. Aby monitorować komunikat D2C dla określonego urządzenia, wybierz urządzenie z listy, a następnie kliknij pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia** w oknie **akcji** .

1. Aby zatrzymać monitorowanie danych, wybierz urządzenie z listy, a następnie wybierz pozycję **Zatrzymaj monitorowanie wbudowanego punktu końcowego zdarzenia** w oknie **akcji** .

## <a name="next-steps"></a>Następne kroki

Aby opracowywać niestandardowe moduły dla IoT Edge urządzeń, należy zapoznać się z zestawami [sdk IoT Hub platformy Azure i korzystać](../iot-hub/iot-hub-devguide-sdks.md)z nich.
