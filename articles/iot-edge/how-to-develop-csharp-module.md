---
title: Debugowanie modułów języka C# dla usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Użyj Visual Studio Code umożliwiające tworzenie, kompilowanie i debugowanie modułu C# dla usługi Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/04/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 7fec050f43ed6facef548ea19d1d67030458fbb1
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380254"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Używanie programu Visual Studio Code do tworzenia i debugowania modułów języka C# dla usługi Azure IoT Edge

Logikę biznesową można przekształcić w moduły, dla usługi Azure IoT Edge. W tym artykule pokazano, jak używać programu Visual Studio Code (VS Code) jako głównego narzędzia do tworzenia i debugowania języka C# modułów.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że używasz komputera lub maszyny wirtualnej z systemem Windows, macOS lub Linux jako komputerze deweloperskim. Urządzenia usługi IoT Edge może być inny urządzenia fizycznego.

> [!NOTE]
> W tym artykule debugowania przedstawiono dwie typowe sposoby debugowania modułu C# w programie VS Code. Jednym ze sposobów jest dołączanie procesu w kontenerze modułu, a drugi do lanuch kod modułu w trybie debugowania. Jeśli nie znasz możliwości debugowania programu Visual Studio Code, przeczytaj temat [debugowanie](https://code.visualstudio.com/Docs/editor/debugging).

Ponieważ w tym artykule używa programu Visual Studio Code, jako narzędzia programistyczne główny, zainstaluj program VS Code. Następnie dodaj rozszerzeniami niezbędnymi:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozszerzenie języka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Rozszerzenia platformy docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Aby utworzyć moduł, potrzebne są .NET do tworzenia folderu projektu, Docker, aby skompilować obraz modułu i rejestru kontenerów do przechowywania obrazu modułu:
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Platformę docker Community Edition](https://docs.docker.com/install/) na komputerze deweloperskim. 
* [Usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub [usługi Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Można użyć lokalnego rejestru platformy Docker prototypów i testowania zamiast rejestru chmury. 

Można skonfigurować lokalne Środowisko deweloperskie, debugowanie, uruchamianie i testowanie rozwiązania usługi IoT Edge należy [narzędzia deweloperskiego EdgeHub IoT Azure](https://pypi.org/project/iotedgehubdev/). Zainstaluj [języka Python (w wersji 2.7/3.6) i narzędzie Pip](https://www.python.org/). Następnie zainstaluj **iotedgehubdev** uruchamiając poniższe polecenie w terminalu.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Aby przetestować modułu na urządzeniu, należy aktywnym Centrum IoT przy użyciu co najmniej jeden identyfikator urządzenia usługi IoT Edge utworzone. Jeśli demon usługi IoT Edge są uruchomione na komputerze deweloperskim, może być konieczne zatrzymanie EdgeHub i EdgeAgent przed przejściem do następnego kroku. 

## <a name="create-a-new-solution-with-c-module"></a>Utwórz nowe rozwiązanie za pomocą modułu C#

Wykonaj następujące czynności, aby utworzyć moduł usługi IoT Edge, oparte na programie .NET Core 2.0 przy użyciu programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge. Najpierw należy utworzyć rozwiązanie, a następnie wygenerować pierwszego modułu w ramach tego rozwiązania. Każdy roztwór może zawierać więcej niż jeden moduł. 

1. W programie Visual Studio Code wybierz **widoku** > **zintegrowany Terminal**.
3. Wybierz **widoku** > **polecenia palety**. 
4. W palecie poleceń, należy wprowadzić, a następnie uruchom polecenie **usługi Azure IoT Edge: nowe rozwiązanie graniczne IoT**.

   ![Uruchamianie nowego rozwiązania usługi IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie. Wybierz **Wybieranie folderu**. 
6. Wprowadź nazwę dla swojego rozwiązania. 
7. Wybierz **modułu C#** jako szablon dla pierwszego modułu w rozwiązaniu.
8. Wprowadź nazwę dla modułu. Wybierz nazwę, która jest unikatowa w obrębie usługi container registry. 
9. Podaj nazwę modułu repozytorium obrazów. VS Code autopopulates modułu nazwa z **localhost:5000**. Zastąp go własną informacje rejestru. Jeśli używasz lokalnego rejestru platformy Docker do testowania, następnie **localhost** jest w dobrym stanie. Jeśli korzystasz z usługi Azure Container Registry, Użyj serwera logowania z ustawień w rejestrze. Serwer logowania wygląda  **\<nazwa rejestru\>. azurecr.io**. Zastąp tylko część localhost ciągu, nie usuwaj nazwy modułu.

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/how-to-develop-csharp-module/repository.png)

Program VS Code przyjmuje informacje podane lub tworzy rozwiązanie IoT Edge, a następnie ładuje go w nowym oknie.

   ![Wyświetl rozwiązania usługi IoT Edge](./media/how-to-develop-csharp-module/view-solution.png)

Istnieją cztery elementy w ramach rozwiązania: 
* A **.vscode** folder zawiera konfiguracji debugowania.
* A **modułów** folder zawiera podfoldery dla każdego modułu. W tym momencie masz tylko jedną licencję. Jednak możesz dodać więcej licencji w palecie poleceń za pomocą polecenia **usługi Azure IoT Edge: Dodaj moduł usługi IoT Edge**. 
* **ENV** plik zawiera listę zmiennych środowiskowych. Jeśli rejestru Azure Container Registry, będziesz mieć usługi Azure Container Registry użytkownika i hasło w nim. 

   >[!NOTE]
   >Plik środowiska jest tworzony tylko, jeśli podasz repozytorium obrazów w module. Jeżeli użytkownik zaakceptował domyślnie localhost, Testuj i Debuguj lokalnie, nie należy do deklarowania zmiennych środowiskowych. 

* A **deployment.template.json** nowego modułu wraz z przykładu zawiera listę plików **tempSensor** modułu, która symuluje sieć danych można używać do testowania. Aby uzyskać więcej informacji na temat sposobu wdrażania manifestów pracy, zobacz [Dowiedz się, jak wdrażać moduły oraz ustalenia tras za pomocą manifesty wdrożenia](module-composition.md). 

## <a name="develop-your-module"></a>Tworzenie modułu

Domyślny kod C# modułu dołączoną do rozwiązania znajduje się w **modułów** > ** [Nazwa modułu] ** > **Program.cs**. W module, plik deployment.template.json są konfigurowane tak, aby skompilować rozwiązanie, Wypchnij go do rejestru kontenerów i wdrożyć ją na urządzeniu do rozpoczęcia testowania bez dotykania żadnego kodu. Moduł został opracowany pod kątem wystarczy pobrać dane wejściowe ze źródła (w tym przypadku moduł tempSensor, która symuluje sieć danych) i przekazać go do usługi IoT Hub. 

Gdy wszystko będzie gotowe dostosować szablon języka C# przy użyciu własnego kodu, należy użyć [zestawami SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) do tworzenia modułów ten adres klucz dla rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodności. 

Obsługa języka C# w programie VS Code jest zoptymalizowany pod kątem projektowania na różne platformy .NET Core. Dowiedz się więcej o [sposób pracy w języku C# w programie VS Code](https://code.visualstudio.com/docs/languages/csharp).

## <a name="launch-and-debug-module-code-without-container"></a>Uruchamianie i debugowanie kodu modułu bez kontenera
Moduł IoT Edge C# jest.Net Core z aplikacji. I zależy od języka C# urządzenia zestawu SDK Azure IoT. W kodzie modułu domyślne, należy zainicjować **ModuleClient** z ustawieniami środowiska i wprowadź nazwę, co oznacza, że moduł IoT Edge C# wymaga ustawienia środowiska uruchomić i przeprowadzić i trzeba będzie również wysłać lub kierowanie komunikatów kanały danych wejściowych. Domyślnego języka C# modułu zawiera tylko jeden kanał wejściowy, a nazwa to **wejście1**.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Konfigurowanie usługi IoT Edge symulator modułu pojedynczej aplikacji

1. Aby skonfigurować i uruchomić symulator, program VS Code paletę poleceń, wpisz i wybierz **usługi Azure IoT Edge: Start IoT Edge Hub symulatora dla jednego modułu**. Należy także określić nazw wejściowych dla aplikacji jednej modułu, typu **wejście1** i naciśnij klawisz Enter. Polecenie spowoduje wyzwolenie **iotedgehubdev** interfejsu wiersza polecenia i uruchom symulator usługi IoT Edge i kontener modułu narzędzia testowania. Symulator został uruchomiony w trybie jednego modułu pomyślnie można wyświetlić dane wyjściowe poniżej w zintegrowanym terminalu. Można również wyświetlić `curl` polecenie, aby ułatwić wysłać. Użyjesz jej później.

   ![Konfigurowanie usługi IoT Edge symulator modułu pojedynczej aplikacji](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Można przenieść do Eksploratora platformy Docker i zobacz Moduł stan działania.

   ![Stan modułu symulatora](media/how-to-develop-csharp-module/simulator-status.png)

   **EdgeHubDev** kontener jest podstawowy symulatora lokalnego usługi IoT Edge. Można uruchomić na komputerze deweloperskim bez demona zabezpieczeń usługi IoT Edge i podaj ustawienia środowiska dla aplikacji modułu macierzystego lub kontenery modułu. **Wejściowych** kontenera widoczne restAPIs ułatwiające Mostek wiadomości pod kątem kanał wejściowy w module.

2. W palecie poleceń programu VS Code, wpisz i wybierz **usługi Azure IoT Edge: Ustawianie poświadczeń modułu do ustawień użytkownika** ustawienia modułu środowiska do `azure-iot-edge.EdgeHubConnectionString` i `azure-iot-edge.EdgeModuleCACertificateFile` do ustawień użytkownika. Możesz znaleźć te ustawienia środowiska są wywoływane w **.vscode** > **launch.json** i [ustawień użytkownika w programie VS Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="build-module-app-and-debug-in-launch-mode"></a>Tworzenie aplikacji w module i debugowanie w trybie uruchamiania

1. W zintegrowanym terminalu, zmień katalog na **CSharpModule** folder, uruchom następujące polecenie, aby skompilować.Net Core z aplikacji.

    ```cmd
    dotnet build
    ```

2. Przejdź do adresu `program.cs`. Dodaj punkt przerwania w tym pliku.

3. Przejdź do widoku debugowania programu VS Code. Wybierz konfigurację debugowania **ModuleName lokalnego debugowania (.NET Core)**. 

4. Kliknij przycisk **Rozpocznij debugowanie** lub naciśnij **F5**. Rozpocznie się sesji debugowania.

   > [!NOTE]
   > Jeśli Twoje.Net Core `TargetFramework` nie jest spójna ze swojej ścieżka programu w `launch.json`. Musisz ręcznie zaktualizować ścieżkę programu w `launch.json` przestrzegać `TargetFramework` w pliku csproj. Więc tego programu VS Code można pomyślnie uruchomić ten program.

5. W zintegrowanym terminalu programu VS Code, uruchom następujące polecenie, aby wysłać **Witaj, świecie** komunikatu do modułu. To polecenie pokazano w poprzednich krokach podczas instalacji usługi IoT Edge symulator pomyślnie.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Jeśli używasz Windows, upewnij się, że powłoka zintegrowanym terminalu programu VS Code jest **powłoki Git Bash** lub **WSL Bash**. Nie można uruchomić `curl` polecenia w programie PowerShell lub wierszu polecenia. 
   
   > [!TIP]
   > Można również użyć [PostMan](https://www.getpostman.com/) lub innych narzędzi interfejsu API, aby wysyłać komunikaty za pośrednictwem zamiast `curl`.

6. W widoku debugowania programu VS Code zobaczysz zmiennych w panelu po lewej stronie. 

    ![Obserwuj zmienne](media/how-to-develop-csharp-module/single-module-variables.png)

7. Aby zatrzymać sesję debugowania, kliknij przycisk Zatrzymaj lub naciśnij klawisz **Shift + F5**. Paleta poleceń programu VS Code, wpisz i wybierz pozycję **usługi Azure IoT Edge: Zatrzymaj IoT Edge symulator** zatrzymać i oczyścić symulatora.

## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Tworzenie kontenera modułu do debugowania i debugowania w dołączyć tryb

Rozwiązanie domyślny zawiera dwa moduły, jeden z nich jest moduł czujnika temperatury symulowane, a drugi to modułu potoku C#. Czujnik temperatury symulowane utrzymuje wysyłanie komunikatów do potoku modułu C#, a następnie komunikaty są potokiem do usługi IoT Hub. W folderze modułu, który został utworzony istnieje kilka plików Docker dla kontenera różnych typów. Użyć dowolnej z tych plików, które kończą się rozszerzeniem **.debug** do tworzenia modułu do testowania. Obecnie C# modułów obsługę debugowania tylko w kontenerach amd64 systemu Linux w dołączyć trybu. 

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurowanie usługi IoT Edge symulatora dla rozwiązania IoT Edge

W komputerze deweloperskim można uruchomić symulatora usługi IoT Edge, zamiast instalować ją z demona zabezpieczeń usługi IoT Edge do uruchomienia rozwiązania usługi IoT Edge. 

1. W Eksploratorze urządzenia po lewej stronie, kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge, wybierz **Instalatora IoT Edge symulator** można uruchomić symulatora przy użyciu parametrów połączenia urządzenia.

2. Widać, że symulator IoT Edge została pomyślnie skonfigurowana w zintegrowanym terminalu.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Kompilowanie i uruchamianie kontenerów do debugowania i debugowania w dołączyć tryb

1. W programie VS Code przejdź do `deployment.template.json` pliku. Aktualizacja usługi C# modułu obraz adresu URL, dodając **.debug** na końcu.

   ![Dodaj *** .debug do nazwy obrazu](./media/how-to-develop-csharp-module/image-debug.png)

2. Przejdź do adresu `program.cs`. Dodaj punkt przerwania w tym pliku.
3. W Eksploratorze programu VS Code, wybierz `deployment.template.json` pliku dla danego rozwiązania, w menu kontekstowym kliknij **rozwiązanie kompilacji i uruchomienia usługi IoT Edge w symulatorze**. Możesz obejrzeć dzienniki kontenera modułu, w tym samym oknie. Możesz także przejść do Eksploratora platformy Docker, aby obserwować stan kontenera.

   ![Obserwuj zmienne](media/how-to-develop-csharp-module/view-log.png)

5. Przejdź do widoku debugowania programu VS Code. Wybierz plik konfiguracji debugowania dla modułu. Nazwa opcji debugowania powinny być podobne do **ModuleName zdalnego debugowania (.NET Core)**

   ![Wybierz konfigurację](media/how-to-develop-csharp-module/debug-config.png)

6. Wybierz **Rozpocznij debugowanie** lub wybierz **F5**. Wybierz proces do dołączenia.

7. W widoku debugowania programu VS Code zobaczysz zmiennych w panelu po lewej stronie.

8. Aby zatrzymać sesję debugowania, kliknij przycisk Zatrzymaj lub naciśnij klawisz **Shift + F5**. Paleta poleceń programu VS Code, wpisz i wybierz pozycję **usługi Azure IoT Edge: Zatrzymaj IoT Edge symulator**.

> [!NOTE]
> W tym przykładzie pokazano, jak można debugować modułów programu .NET Core IoT Edge w kontenerach. Jest on oparty na wersji debugowania `Dockerfile.debug`, w tym VSDBG debuger wiersza polecenia platformy .NET Core w obrazie kontenera podczas jego tworzenia. Po debugowaniu modułów języka C#, firma Microsoft zaleca, aby bezpośrednio użyć lub dostosować `Dockerfile` bez VSDBG dla modułów usługi IoT Edge gotowe do produkcji.

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu modułu, Dowiedz się jak [wdrożyć moduły usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md).

Tworzenie modułów na urządzeniach usługi IoT Edge [poznawanie i używanie usługi Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).
