---
title: Programowanie i debugowanie modułów języka C# dla usługi Azure IoT Edge w programie Visual Studio 2017 | Dokumentacja firmy Microsoft
description: Używanie programu Visual Studio 2017 do tworzenia i debugowania modułu w języku C# dla usługi Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/24/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9cc8e1db577859ad7637902a5ccd5a044efcd033
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978526"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Używanie programu Visual Studio 2017 do tworzenia i debugowania modułów języka C# dla usługi Azure IoT Edge (wersja zapoznawcza)

Logikę biznesową można przekształcić w moduły, dla usługi Azure IoT Edge. W tym artykule pokazano, jak używać programu Visual Studio 2017 jako głównego narzędzia do tworzenia i debugowania języka C# modułów.

Narzędzia usługi Azure IoT Edge dla programu Visual Studio zapewnia następujące korzyści:

- Tworzenie, edytowanie, tworzenie, uruchamianie i debugowanie rozwiązań Azure IoT Edge oraz modułów na lokalnym komputerze deweloperskim.
- Wdrażanie rozwiązania Azure IoT Edge na urządzeniu usługi Azure IoT Edge za pośrednictwem usługi Azure IoT Hub.
- Kodu moduły usługi Azure IoT w języku C#, a jednocześnie ma wszystkie korzyści wynikające z programowania Visual Studio.
- Zarządzanie urządzeniami usługi Azure IoT Edge i modułów przy użyciu interfejsu użytkownika. 

W tym artykule pokazano, jak tworzyć moduły usługi IoT Edge w języku C# za pomocą narzędzi usługi Azure IoT Edge dla programu Visual Studio 2017. Poznasz również sposób wdrażania projektu do Twojego urządzenia usługi Azure IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że używasz komputera lub maszyny wirtualnej z systemem Windows jako komputerze deweloperskim. Urządzenia usługi IoT Edge może być inny urządzenia fizycznego.

Ponieważ w tym artykule używa programu Visual Studio 2017, jako narzędzia programistyczne główny, zainstaluj program Visual Studio. I upewnij się, że dołączasz **obciążenie programistyczne platformy Azure** w instalacji programu Visual Studio 2017. Możesz [modyfikowanie programu Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) i Dodaj obciążenie programistyczne platformy Azure.

Gdy usługi Visual Studio 2017 jest gotowy, wymagane są również:

- Pobierz i zainstaluj [rozszerzenia usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.azureiotedgetools) z witryny marketplace programu Visual Studio, aby utworzyć usługi IoT Edge projektu w programie Visual Studio 2017.
- [Platformę docker Community Edition](https://docs.docker.com/install/) na komputerze deweloperskim w taki sposób, aby skompilować i uruchomić obrazów modułu. Należy poprawnie ustawić Docker CE uruchomiony w trybie kontenera systemu Linux lub Windows kontenera.
- Aby skonfigurować lokalne Środowisko deweloperskie do debugowania, uruchamiania i przetestowanie rozwiązania usługi IoT Edge, musisz mieć [narzędzia deweloperskiego EdgeHub IoT Azure](https://pypi.org/project/iotedgehubdev/). Zainstaluj [języka Python (w wersji 2.7/3.6) i narzędzie Pip](https://www.python.org/). Następnie zainstaluj **iotedgehubdev** uruchamiając poniższe polecenie w terminalu. Upewnij się, że używana wersja narzędzia deweloperskiego EdgeHub IoT Azure jest większa niż 0.3.0.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub [usługi Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   - Można użyć lokalnego rejestru platformy Docker prototypów i testowania zamiast rejestru chmury. 

- Aby przetestować modułu, konieczne jest aktywnym Centrum IoT z co najmniej jeden identyfikator urządzenia usługi IoT Edge utworzone. Demon zabezpieczeń usługi IoT Edge są uruchomione na komputerze deweloperskim, może być konieczne zatrzymanie EdgeHub i EdgeAgent przed rozpoczęciem tworzenia w programie Visual Studio. 

### <a name="check-your-tools-version"></a>Sprawdź swoją wersję narzędzia

1. Z **narzędzia** menu, wybierz **rozszerzenia i aktualizacje**. Rozwiń **zainstalowane > narzędzia** możesz znaleźć **usługi Azure IoT Edge** i **programu Cloud Explorer**.

2. Zwróć uwagę na wersję zainstalowanego. Możesz porównać tej wersji do najnowszej wersji w Visual Studio Marketplace ([programu Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS), [usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.azureiotedgetools))

3. W przypadku starszych wersji aktualizacji z narzędzi w programie Visual Studio, jak pokazano w poniższej sekcji.

### <a name="update-your-tools"></a>Aktualizowanie narzędzi

1. W **rozszerzenia i aktualizacje** okna dialogowego, rozwiń węzeł **aktualizacje > Visual Studio Marketplace**, wybierz **usługi Azure IoT Edge** lub **programu Cloud Explorer**i wybierz **aktualizacji**.

2. Po pobraniu aktualizacji narzędzi, zamknij program Visual Studio, aby wywoływany narzędzia aktualizacji za pomocą Instalatora VSIX.

3. W oknie Instalatora wybierz **OK** można uruchomić, a następnie zmodyfikuj, aby zaktualizować narzędzia.

4. Po zakończeniu aktualizacji, wybierz polecenie Zamknij i ponownie uruchom program Visual Studio.

### <a name="create-an-azure-iot-edge-project"></a>Tworzenie projektu usługi Azure IoT Edge

Szablon projektu usługi Azure IoT Edge w programie Visual Studio tworzy projekt, który można wdrożyć na urządzeniach z usługą Azure IoT Edge w usłudze Azure IoT Hub. Najpierw należy utworzyć rozwiązanie Azure IoT Edge, a następnie wygenerować pierwszego modułu C# w ramach tego rozwiązania. Każde rozwiązanie IoT Edge może zawierać więcej niż jeden moduł. 

1. W programie Visual Studio wybierz pozycję **Nowy** > **Projekt** z menu **Plik**.

2. W **nowy projekt** okno dialogowe, wybierz opcję **zainstalowane**, rozwiń węzeł **Visual C# > chmury**, wybierz opcję **usługi Azure IoT Edge**, wpisz  **Nazwa** dla projektu i określ lokalizację i kliknij **OK**. Domyślna nazwa projektu to **AzureIoTEdgeApp1**. 

   ![Nowy projekt](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

3. W **konfiguracji modułu usługi IoT Edge** wybierz **modułu C#** wpisz i określ nazwę modułu i repozytorium obrazów w module.  VS autopopulates modułu nazwa z **localhost:5000**. Zastąp go własną informacje rejestru. Jeśli używasz lokalnego rejestru platformy Docker do testowania localhost funkcjonuje prawidłowo. Jeśli korzystasz z usługi Azure Container Registry, Użyj serwera logowania z ustawień w rejestrze. Serwer logowania wygląda  **<registry name>. azurecr.io**. Zastąp tylko część localhost ciągu, nie usuwaj nazwy modułu. Domyślna nazwa modułu to **IoTEdgeModule1**

4. Kliknij przycisk **OK** do tworzenia projektu usługi Azure IoT Edge przy użyciu modułu C#.

Teraz masz **AzureIoTEdgeApp1** projektu i **IoTEdgeModule1** projektu w naszym rozwiązaniu. **AzureIoTEdgeApp1** projekt ma **deployment.template.json** pliku. Ten plik Określa moduły, w których chcesz tworzyć i wdrażać rozwiązania usługi IoT Edge i definiuje trasy między modułami. Ma domyślnego rozwiązania **tempSensor** modułu i **IoTEdgeModule1** modułu. **TempSensor** moduł generuje symulowane dane do **IoTEdgeModule1** modułu podczas domyślny kod w **IoTEdgeModule1** jest to moduł komunikat w potoku, który bezpośrednio potoku Odebrane komunikaty wyjściowe do Centrum IoT Hub.

**IoTEdgeModule1** projekt jest.Net Core 2.1 aplikacji konsoli. Zawiera on wymaganego **plików Dockerfile** potrzebne dla urządzenia usługi IoT Edge z kontenera Windows lub kontenera systemu Linux. **Module.json** pliku opisuje metadane modułu. **Program.cs** jest kod rzeczywiste moduł, który przyjmuje zestawu SDK urządzenia IoT Azure jako zależność.

## <a name="develop-your-module"></a>Tworzenie modułu

Domyślny kod C# modułu dołączoną do rozwiązania znajduje się w **IoTEdgeModule1** > **Program.cs**. W module, plik deployment.template.json są konfigurowane tak, aby skompilować rozwiązanie, Wypchnij go do rejestru kontenerów i wdrożyć ją na urządzeniu do rozpoczęcia testowania bez dotykania żadnego kodu. Moduł został opracowany pod kątem wystarczy pobrać dane wejściowe ze źródła (w tym przypadku moduł tempSensor, która symuluje sieć danych) i przekazać go do usługi IoT Hub. 

Gdy wszystko będzie gotowe dostosować szablon języka C# przy użyciu własnego kodu, należy użyć [zestawami SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) do tworzenia modułów ten adres klucz dla rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodności. 

## <a name="build-and-debug-single-c-module"></a>Kompilowanie i debugowanie jednym modułu C#

Zazwyczaj chcemy testów/debug każdego modułu, aby firma Microsoft będzie działać w ramach całego rozwiązania z wieloma modułami.

1. Wybierz **IoTEdgeModule1** jako projekt startowy z menu kontekstowego.

   ![Ustaw projekt uruchamiania](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

2. Naciśnij klawisz **F5** lub kliknij przycisk poniżej, aby uruchomić moduł, może potrwać 10 ~ 20 sekund dla pierwszego czasu.

   ![Uruchom moduł](./media/how-to-visual-studio-develop-csharp-module/run-module.png)


3. .Net Core powinna zostać wyświetlona aplikacja konsolowa pracę, jeśli moduł został pomyślnie zainicjowany.

   ![Moduł uruchamiania](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

4. Teraz możesz ustawić punkt przerwania **PipeMessage** w **Program.cs**, następnie wysyłania komunikatu, uruchamiając następujące polecenie w swojej **powłoki Git Bash** lub **WSL Bash**  (nie uruchamiaj go w CMD lub programu Powershell) (tego polecenia można również znaleźć w oknie danych wyjściowych programu VS):

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Debugowanie jednego modułu](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Powinna być wyzwolona punkt przerwania. Możesz obejrzeć zmienne w oknie programu Visual Studio zmiennych lokalnych.

   > [!TIP]
   > Można również użyć [PostMan](https://www.getpostman.com/) lub innych narzędzi interfejsu API, aby wysyłać komunikaty za pośrednictwem zamiast `curl`.

5. Naciśnij klawisz **klawiszy Ctrl + F5** lub kliknij przycisk Zatrzymaj, aby zatrzymać debugowanie.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Kompilowanie i debugowanie rozwiązania usługi IoT Edge z wieloma modułami

Po zakończeniu tworzenia pojedynczy moduł dalej, chcemy uruchamiać i debugować całe rozwiązanie z wieloma modułami.

1. Dodaj drugi modułu C# do rozwiązania. Kliknij prawym przyciskiem myszy **AzureIoTEdgeApp1** i wybierz **Dodaj** -> **nowy moduł usługi IoT Edge**. Domyślna nazwa drugiego modułu to **IoTEdgeModule2** i nadal jest to moduł potoku.

2. Przejdź do **deployment.template.json**. Zostanie wyświetlony **IoTEdgeModule2** został dodany w **modułów** sekcji. Zastąp **trasy** sekcję poniższym kodem. Jeśli dostosowano nazwy modułu, upewnij się, że aktualizacja nazwy w następującym po zastąpienia.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

3. Ustaw **AzureIoTEdgeApp1** projektu jako projektu startowego w menu kontekstowym.

4. Ustawianie punktów przerwania i naciśnij klawisz F5, a następnie można uruchomić i debugować wiele modułów jednocześnie. Powinien zostać wyświetlony.Net Core wiele okien aplikacji konsoli, każde okno wskazuje modułu C#. 

   ![Debuguj wiele modułów](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

5. Naciśnij klawisz **klawiszy Ctrl + F5** lub kliknij przycisk Zatrzymaj, aby zatrzymać debugowanie.

## <a name="build-and-push-images"></a>Kompilowanie i wypychanie obrazów

1. Upewnij się, że **AzureIoTEdgeApp1** jest projekt startowy. Wybierz **debugowania** lub **wersji** konfigurację dla obrazów modułu do kompilacji.

    > [!NOTE]
    > Podczas wybierania **debugowania**, użyje VS `Dockerfile.debug` kompilowanie obrazów platformy Docker. W tym VSDBG debuger wiersza polecenia platformy .NET Core w obrazie kontenera podczas jego tworzenia. Firma Microsoft zaleca użycie **wersji** konfiguracji, który używa `Dockerfile` bez VSDBG dla modułów usługi IoT Edge gotowe do produkcji.

2. Jeśli są przy użyciu rejestru prywatnego, takich jak usługa Azure Container Registry, należy uruchomić Docker Zaloguj się przy użyciu następującego polecenia w terminalu. Jeśli używasz lokalnego rejestru możesz [uruchamiania lokalnego rejestru](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server> 
    ```

3. Jeśli są przy użyciu rejestru prywatnego, takich jak usługa Azure Container Registry, musisz umieścić nazwę rejestru użytkownika i hasło `deployment.template.json` w obszarze ustawień środowiska uruchomieniowego o następującej zawartości. Pamiętaj, aby Zamień symbol zastępczy administratora rzeczywiste nazwy użytkownika i hasła.

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

4. Kliknij prawym przyciskiem myszy **AzureIoTEdgeApp1** i wybierz element menu kontekstowego **kompilacji i wypychania nowoczesne rozwiązanie**, będzie on kompilacji i wypychanie obrazu platformy docker dla każdego modułu.

   ![Kompilowanie i wypychanie obrazów](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)


## <a name="deploy-the-solution"></a>Wdrażanie rozwiązania

W artykule Szybki start, który był używany do skonfigurowania urządzenia usługi IoT Edge, wdrożono moduł za pomocą witryny Azure Portal. Można także wdrożyć modułów za pomocą Eksploratora chmury dla programu Visual Studio. Masz już manifest wdrożenia, jest przygotowane do scenariusza `deployment.json` pliku. Teraz wystarczy wybrać urządzenie, które ma otrzymać wdrożenie.

1. Otwórz **programu Cloud Explorer** , klikając **widoku** > **programu Cloud Explorer**. Upewnij się, że logujesz się w programie Visual Studio 2017 z Twoim kontem.

2. W **programu Cloud Explorer**rozwiń subskrypcji, usługi Azure IoT Hub i urządzenia usługi Azure IoT Edge, które chcesz wdrożyć.

3. Kliknij prawym przyciskiem myszy, IoT Edge na urządzeniu w celu utworzenia wdrożenia dla niego, musisz wybrać plik manifestu wdrożenia w obszarze `$AzureIoTEdgeApp1\config\Debug|Release\deployment.json`.

4. Kliknij przycisk Odśwież. Powinny zostać wyświetlone nowe moduły uruchomiony wraz z **TempSensor** modułu i **$edgeAgent** i **$edgeHub**.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

1. Do monitorowania komunikatu D2C dla określonego urządzenia, kliknij urządzenie na liście, a następnie kliknij przycisk **rozpocząć monitorowanie komunikatów D2C** w **akcji** okna.

2. Aby zatrzymać monitorowanie danych, kliknij urządzenie na liście, a następnie wybierz **zatrzymać monitorowanie komunikatów D2C** w **akcji** okna.

## <a name="next-steps"></a>Kolejne kroki

Tworzenie modułów na urządzeniach usługi IoT Edge [poznawanie i używanie usługi Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).
