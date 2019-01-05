---
title: Programowanie i debugowanie C# modułów w programie Visual Studio — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Używanie programu Visual Studio 2017 do tworzenia i debugowania modułu w języku C# dla usługi Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9a6c892a71c452a0c6c0dcd43509e345280a810e
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054927"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Używanie programu Visual Studio 2017 do tworzenia i debugowania modułów języka C# dla usługi Azure IoT Edge (wersja zapoznawcza)

Logikę biznesową można przekształcić w moduły, dla usługi Azure IoT Edge. W tym artykule pokazano, jak używać programu Visual Studio 2017 jako głównego narzędzia do tworzenia i debugowania języka C# modułów.

Narzędzia usługi Azure IoT Edge dla programu Visual Studio zapewnia następujące korzyści:

- Tworzenie, edytowanie, tworzenie, uruchamianie i debugowanie rozwiązań Azure IoT Edge i modułów na lokalnym komputerze deweloperskim.
- Wdrażanie rozwiązania Azure IoT Edge na urządzeniu usługi Azure IoT Edge za pośrednictwem usługi Azure IoT Hub.
- Kodu moduły usługi Azure IoT w języku C#, a jednocześnie ma wszystkie korzyści wynikające z programowania Visual Studio.
- Zarządzanie urządzeniami usługi Azure IoT Edge i modułów przy użyciu interfejsu użytkownika.

W tym artykule pokazano, jak tworzyć moduły usługi IoT Edge w języku C# za pomocą narzędzi usługi Azure IoT Edge dla programu Visual Studio 2017. Poznasz również sposób wdrażania projektu do Twojego urządzenia usługi Azure IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że używasz komputera lub maszyny wirtualnej z systemem Windows jako komputerze deweloperskim. Urządzenia usługi IoT Edge może być inny urządzenia fizycznego.

Ponieważ w tym artykule używa programu Visual Studio 2017, jako narzędzia programistyczne główny, zainstaluj program Visual Studio. I upewnij się, że dołączasz **obciążenie programistyczne platformy Azure** w instalacji programu Visual Studio 2017. Możesz [modyfikowanie programu Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) i Dodaj obciążenie programistyczne platformy Azure.

Gdy usługi Visual Studio 2017 jest gotowy, wymagane są również następujące narzędzia i składniki:

- Pobierz i zainstaluj [rozszerzenia usługi Azure IoT Edge (wersja zapoznawcza)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) z witryny marketplace programu Visual Studio, aby utworzyć projekt usługi IoT Edge w programie Visual Studio 2017.

- Pobierz i zainstaluj [platformę Docker Community Edition](https://docs.docker.com/install/) na komputerze deweloperskim w taki sposób, aby skompilować i uruchomić obrazów modułu. Należy ustawić Docker CE do uruchamiania w trybie kontenera systemu Linux lub Windows container trybu.

- Skonfiguruj lokalne Środowisko deweloperskie do debugowania, uruchom i przetestowanie rozwiązania usługi IoT Edge, instalując [narzędzia deweloperskiego EdgeHub IoT Azure](https://pypi.org/project/iotedgehubdev/). Zainstaluj [języka Python (w wersji 2.7/3.6) i narzędzie Pip](https://www.python.org/) , a następnie zainstaluj **iotedgehubdev** pakietu, uruchamiając następujące polecenie w terminalu. Upewnij się, że używana wersja narzędzia deweloperskiego EdgeHub IoT Azure jest większa niż 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub [usługi Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Można użyć lokalnego rejestru platformy Docker prototypów i testowania zamiast rejestru chmury.

- Aby przetestować modułu na urządzeniu, należy aktywnym Centrum IoT przy użyciu co najmniej jedno urządzenie usługi IoT Edge. Aby użyć komputera jako urządzenia usługi IoT Edge, postępuj zgodnie z instrukcjami w przewodniku Szybki Start dla [Linux](quickstart-linux.md) lub [Windows](quickstart.md). Jeśli demon usługi IoT Edge są uruchomione na komputerze deweloperskim, może być konieczne zatrzymanie EdgeHub i EdgeAgent przed rozpoczęciem tworzenia w programie Visual Studio.

### <a name="check-your-tools-version"></a>Sprawdź swoją wersję narzędzia

1. Z **narzędzia** menu, wybierz opcję **rozszerzenia i aktualizacje**. Rozwiń **zainstalowane > narzędzia** możesz znaleźć **Azure IoT Edge narzędzia** i **Eksplorator chmury dla programu Visual Studio**.

1. Zwróć uwagę na wersję zainstalowanego. Możesz porównać tej wersji do najnowszej wersji w Visual Studio Marketplace ([programu Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge))

1. Jeśli używana wersja jest starsza niż dostępna w Visual Studio Marketplace, zaktualizuj swoje narzędzia w programie Visual Studio, jak pokazano w poniższej sekcji.

### <a name="update-your-tools"></a>Aktualizowanie narzędzi

1. W **rozszerzenia i aktualizacje** okna dialogowego, rozwiń węzeł **aktualizacje > Visual Studio Marketplace**, wybierz opcję **Azure IoT Edge narzędzia** lub **Eksplorator chmury dla języka Visual Studio** i wybierz **aktualizacji**.

1. Po pobraniu aktualizacji narzędzi, zamknij program Visual Studio, aby wywoływany narzędzia aktualizacji za pomocą Instalatora VSIX.

1. W oknie Instalatora wybierz **OK** można uruchomić i następnie **Modyfikuj** Aby zaktualizować narzędzia.

1. Po zakończeniu aktualizacji wybierz **Zamknij** i uruchom ponownie program Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Tworzenie projektu usługi Azure IoT Edge

Szablon projektu usługi Azure IoT Edge w programie Visual Studio tworzy projekt, który można wdrożyć na urządzeniach z usługą Azure IoT Edge w usłudze Azure IoT Hub. Najpierw należy utworzyć rozwiązanie Azure IoT Edge, a następnie wygenerować pierwszego modułu C# w ramach tego rozwiązania. Każde rozwiązanie IoT Edge może zawierać więcej niż jeden moduł.

1. W programie Visual Studio wybierz pozycję **Nowy** > **Projekt** z menu **Plik**.

1. W **nowy projekt** okno dialogowe, wybierz opcję **zainstalowane**, rozwiń węzeł **Visual C# > chmury**, wybierz opcję **usługi Azure IoT Edge**, wprowadź nazwę z projektu i określ lokalizację, a następnie wybierz **OK**. Domyślna nazwa projektu to **AzureIoTEdgeApp1**.

   ![Nowy projekt](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

1. W **konfiguracji modułu usługi IoT Edge** wybierz  **C# modułu** i określ nazwę modułu i repozytorium obrazów w module. Visual Studio autopopulates modułu nazwa z **localhost:5000 / < Twoja nazwa modułu\>**. Zastąp go własną informacje rejestru. Jeśli używasz lokalnego rejestru platformy Docker do testowania, następnie **localhost** jest w dobrym stanie. Jeśli korzystasz z usługi Azure Container Registry, Użyj serwera logowania z ustawień w rejestrze. Serwer logowania wygląda jak ***\<nazwa rejestru\>*. azurecr.io**. Jedynie zastąpić **localhost:5000** część ciągu, więc, że wynik końcowy wygląda jak **\<* nazwa rejestru*\>.azurecr.io/* \<swoją nazwę modułu\>***. Domyślna nazwa modułu to **IoTEdgeModule1**

1. Wybierz **OK** do tworzenia projektu usługi Azure IoT Edge przy użyciu C# modułu.

Teraz masz **AzureIoTEdgeApp1** projektu i **IoTEdgeModule1** projektu w rozwiązaniu. **AzureIoTEdgeApp1** projekt ma **deployment.template.json** pliku. Ten plik Określa moduły, w których chcesz tworzyć i wdrażać rozwiązania usługi IoT Edge i definiuje również trasy między modułami. Ma domyślnego rozwiązania **tempSensor** modułu i **IoTEdgeModule1** modułu. **TempSensor** moduł generuje symulowane dane do **IoTEdgeModule1** modułu podczas domyślny kod w **IoTEdgeModule1** modułu bezpośrednio potoki odebrane komunikaty do usługi Azure IoT Hub.

**IoTEdgeModule1** projekt to aplikacja konsoli .NET Core 2.1. Zawiera ona wymaganych plików Docker, czego potrzebujesz do urządzenia usługi IoT Edge z kontenerów Windows albo kontenera systemu Linux. **Module.json** pliku opisuje metadane modułu. **Program.cs** jest kod rzeczywiste moduł, który przyjmuje zestawu SDK urządzenia IoT Azure jako zależność.

## <a name="develop-your-module"></a>Tworzenie modułu

Domyślny kod C# modułu dołączoną do rozwiązania znajduje się w **IoTEdgeModule1** > **Program.cs**. W module, plik deployment.template.json są konfigurowane tak, aby skompilować rozwiązanie, Wypchnij go do rejestru kontenerów i wdrożyć ją na urządzeniu do rozpoczęcia testowania bez dotykania żadnego kodu. Moduł został opracowany pod kątem pobierać dane wejściowe ze źródła (w tym przypadku **tempSensor** modułu, która symuluje sieć danych) i przekazać go do usługi Azure IoT Hub.

Gdy wszystko będzie gotowe dostosować szablon języka C# przy użyciu własnego kodu, należy użyć [zestawami SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) do tworzenia modułów ten adres klucz dla rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodności.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Inicjowanie iotedgehubdev przy użyciu parametrów połączenia urządzenia usługi IoT Edge

1. Skopiuj parametry połączenia z dowolnego urządzenia IoT Edge z **podstawowe parametry połączenia** w programie Visual Studio Cloud Explorer. Upewnij się, nie skopiować parametry połączenia urządzenia bez krawędzi, jak ikona urządzenia usługi IoT Edge różni się od ikonę urządzenia bez krawędzi.

   ![Skopiuj parametry połączenia urządzenia usługi Edge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Kliknij prawym przyciskiem myszy **AzureIoTEdgeApp1** projektu, a następnie kliknij przycisk **Ustaw parametry połączenia urządzenia usługi Edge** aby otworzyć okno Ustawienia usługi Azure IoT Edge.

   ![Otwórz okno ciąg połączenia krawędzi zestawu](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Wprowadź parametry połączenia z pierwszego kroku, a następnie wybierz pozycję **OK**.

> [!NOTE]
> Należy wykonać następujące kroki, tylko wtedy, gdy na komputerze deweloperskim jako wyniki są automatycznie stosowane do wszystkich kolejnych rozwiązań Azure IoT Edge. Tę procedurę można wykonać ponownie, jeśli trzeba zmienić parametry połączenia różnych.

## <a name="build-and-debug-single-c-module"></a>Kompilowanie i debugowanie jednym modułu C#

Zazwyczaj należy testować i debugować każdy moduł przed uruchomieniem jej w ramach całego rozwiązania z wieloma modułami.

1. Kliknij prawym przyciskiem myszy **IoTEdgeModule1** i wybierz **Ustaw jako projekt startowy** z menu kontekstowego.

   ![Ustaw projekt uruchamiania](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Naciśnij klawisz **F5** lub kliknij przycisk poniżej, aby uruchomić moduł; może potrwać 10&ndash;20 sekund po raz pierwszy możesz to zrobić.

   ![Uruchom moduł](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Powinien zostać wyświetlony aplikacji konsolowej .NET Core, uruchom, jeśli moduł został pomyślnie zainicjowany.

   ![Moduł uruchamiania](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Ustaw punkt przerwania `PipeMessage()` działa w programach **Program.cs** , a następnie przetestować, wysyłając wiadomość, uruchamiając następujące polecenie w **powłoki Git Bash** lub **WSL Bash** powłoki . (Nie można uruchomić `curl` polecenia programu PowerShell lub wierszu polecenia.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Debugowanie jednego modułu](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Powinna być wyzwolona punkt przerwania. Możesz obejrzeć zmiennych w programie Visual Studio **lokalne** okna.

   > [!TIP]
   > Można również użyć [PostMan](https://www.getpostman.com/) lub innych narzędzi interfejsu API do wysyłania komunikatów zamiast `curl`.

1. Naciśnij klawisz **klawiszy Ctrl + F5** lub kliknij przycisk Zatrzymaj, aby zatrzymać debugowanie.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Kompilowanie i debugowanie rozwiązania usługi IoT Edge z wieloma modułami

Po zakończeniu tworzenia pojedynczy moduł, możesz chcieć uruchamiać i debugować całego rozwiązania z wieloma modułami.

1. Dodaj drugą C# modułu do rozwiązania, klikając prawym przyciskiem myszy **AzureIoTEdgeApp1** i wybierając polecenie **Dodaj** > **nowy moduł usługi IoT Edge**. Domyślna nazwa drugiego modułu to **IoTEdgeModule2** i będzie działać jako inny moduł potoku.

1. Otwórz plik `deployment.template.json` zostaną wyświetlone **IoTEdgeModule2** został dodany w **modułów** sekcji. Zastąp **trasy** sekcję poniższym kodem. Jeśli dostosowano nazwy modułu, upewnij się, że możesz zaktualizować te nazwy, aby dopasować.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. Kliknij prawym przyciskiem myszy **AzureIoTEdgeApp1** i wybierz **Ustaw jako projekt startowy** z menu kontekstowego.

1. Tworzenie punktów przerwania, a następnie naciśnij klawisz **F5** można uruchamiać i debugować wiele modułów jednocześnie. Powinien zostać wyświetlony wielu platformy .NET Core app okna konsoli, każde okno reprezentująca różne C# modułu.

   ![Debuguj wiele modułów](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Naciśnij klawisz **klawiszy Ctrl + F5** lub wybierz przycisk Zatrzymaj, aby zatrzymać debugowanie.

## <a name="build-and-push-images"></a>Kompilowanie i wypychanie obrazów

1. Upewnij się, że **AzureIoTEdgeApp1** projekt startowy. Wybierz opcję **debugowania** lub **wersji** jako Konfiguracja kompilacji dla obrazów modułu.

    > [!NOTE]
    > Podczas wybierania **debugowania**, program Visual Studio używa `Dockerfile.debug` kompilowanie obrazów platformy Docker. W tym VSDBG debuger wiersza polecenia platformy .NET Core w obrazie kontenera podczas jego tworzenia. Moduły usługi IoT Edge gotowe do produkcji, zaleca się używanie **wersji** konfigurację, która wykorzystuje `Dockerfile` bez VSDBG.

1. Jeśli używasz rejestru prywatnego, takich jak usługa Azure Container Registry, użyj następującego polecenia Docker, aby zalogować się do niego. Jeśli używasz lokalnego rejestru możesz [uruchamiania lokalnego rejestru](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Jeśli używasz rejestru prywatnego, takich jak usługa Azure Container Registry, należy dodać informacje logowania rejestru do ustawienia środowiska uruchomieniowego, znajdującą się w pliku `deployment.template.json`. Zastąp symbole zastępcze nazwą rzeczywistego ACR administratora nazwa użytkownika, hasło i rejestru.

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

1. Kliknij prawym przyciskiem myszy **AzureIoTEdgeApp1** i wybierz **kompilacji i wypychania nowoczesne rozwiązanie** do tworzenia i wypychanie obrazu platformy Docker dla każdego modułu.

   ![Kompilowanie i wypychanie obrazów](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

W artykule Szybki start, który był używany do skonfigurowania urządzenia usługi IoT Edge, wdrożono moduł za pomocą witryny Azure Portal. Można także wdrożyć modułów za pomocą Eksploratora chmury dla programu Visual Studio. Masz już manifest wdrożenia, jest przygotowane do scenariusza `deployment.json` plików i wszystko, czego potrzebujesz, aby zrobić to wybierz urządzenie, aby otrzymywać wdrożenia.

1. Otwórz **programu Cloud Explorer** , klikając **widoku** > **programu Cloud Explorer**. Upewnij się, że logujesz się do programu Visual Studio 2017.

1. W **programu Cloud Explorer**rozwiń subskrypcji, usługi Azure IoT Hub i urządzenia usługi Azure IoT Edge, które chcesz wdrożyć.

1. Kliknij prawym przyciskiem myszy na urządzeniu usługi IoT Edge w celu utworzenia wdrożenia dla niego, musisz wybrać plik manifestu wdrożenia w obszarze `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json`.

   > [!NOTE]
   > Nie musisz wybrać `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Kliknij przycisk Odśwież, aby zobaczyć nowe moduły uruchomiony wraz z **TempSensor** modułu i **$edgeAgent** i **$edgeHub**.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

1. Do monitorowania komunikatu D2C dla określonego urządzenia, wybierz urządzenie, na liście, a następnie kliknij przycisk **rozpocząć monitorowanie komunikatów D2C** w **akcji** okna.

1. Aby zatrzymać monitorowanie danych, wybierz urządzenie, na liście, a następnie wybierz pozycję **zatrzymać monitorowanie komunikatów D2C** w **akcji** okna.

## <a name="next-steps"></a>Kolejne kroki

Aby opracować moduły niestandardowe dla urządzeń usługi IoT Edge, [poznawanie i używanie usługi Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).
