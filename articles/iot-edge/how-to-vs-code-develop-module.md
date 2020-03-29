---
title: Tworzenie i debugowanie modułów dla usługi Azure IoT Edge | Dokumenty firmy Microsoft
description: Tworzenie, tworzenie i debugowanie modułu usługi Azure IoT Edge przy użyciu języka C#, Python, Node.js, Java lub C służy do tworzenia, tworzenia i debugowania modułu usługi Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
ms.author: xshi
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 10c8008d73390174c44ec503f708c1e2c0011e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944314"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Tworzenie i debugowanie modułów usługi Azure IoT Edge za pomocą programu Visual Studio Code

Możesz przekształcić logikę biznesową w moduły dla usługi Azure IoT Edge. W tym artykule pokazano, jak używać programu Visual Studio Code jako głównego narzędzia do tworzenia i debugowania modułów.

Istnieją dwa sposoby debugowania modułów napisanych w języku C#, Node.js lub Java w programie Visual Studio Code: Można dołączyć proces w kontenerze modułu lub uruchomić kod modułu w trybie debugowania. Aby debugować moduły napisane w Pythonie lub C, można dołączyć tylko do procesu w kontenerach linux amd64.

Jeśli nie znasz możliwości debugowania programu Visual Studio Code, przeczytaj o [debugowaniu](https://code.visualstudio.com/Docs/editor/debugging).

Ten artykuł zawiera instrukcje dotyczące tworzenia i debugowania modułów w wielu językach dla wielu architektur. Obecnie visual studio code zapewnia obsługę modułów napisanych w językach C#, C, Python, Node.js i Java. Obsługiwane architektury urządzeń to X64 i ARM32. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych, języków i architektur, zobacz [Obsługa języka i architektury](module-development.md#language-and-architecture-support).

>[!NOTE]
>Tworzenie i debugowanie wsparcie dla urządzeń Arm64 Linux jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Aby uzyskać więcej informacji, zobacz [Tworzenie i debugowanie modułów usługi ARM64 IoT Edge w programie Visual Studio Code (wersja zapoznawcza)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Wymagania wstępne

Jako komputerze deweloperskim można użyć komputera lub maszyny wirtualnej z systemem Windows, macOS lub Linux. Na komputerach z systemem Windows można tworzyć moduły systemu Windows lub Linux. Aby opracować moduły systemu Windows, należy użyć komputera z systemem Windows w wersji 1809/build 17763 lub nowszej. Aby opracować moduły systemu Linux, należy użyć komputera z systemem Windows, który spełnia [wymagania platformy Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Najpierw zainstaluj [program Visual Studio Code,](https://code.visualstudio.com/) a następnie dodaj następujące rozszerzenia:

- [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Rozszerzenie platformy Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Rozszerzenia programu Visual Studio specyficzne dla języka, w który tworzysz:
  - C#, w tym rozszerzenie usługi Azure Functions: [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Rozszerzenie Pythona](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Pakiet rozszerzeń Java dla kodu programu Visual Studio](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [Rozszerzenie C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Aby opracować moduł, musisz również zainstalować dodatkowe narzędzia specyficzne dla języka:

- C#, w tym usługi Azure Functions: [zestaw SDK .NET Core 2.1](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) i [Pip](https://pip.pypa.io/en/stable/installing/#installation) do instalowania pakietów Pythona (zazwyczaj dołączonych do instalacji Języka Python).

- Node.js: [Node.js](https://nodejs.org). Warto również zainstalować [program Yeoman](https://www.npmjs.com/package/yo) i [generator modułów usługi Azure IoT Edge Node.js.](https://www.npmjs.com/package/generator-azure-iot-edge-module)

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) i [Maven](https://maven.apache.org/). Należy [ustawić zmienną `JAVA_HOME` środowiskową,](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) aby wskazywała instalację zestawu JDK.

Aby utworzyć i wdrożyć obraz modułu, należy platformy Docker do tworzenia obrazu modułu i rejestru kontenerów do przechowywania obrazu modułu:

- [Docker Community Edition](https://docs.docker.com/install/) na komputerze deweloperskim.

- [Usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub [Centrum platformy Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Lokalnego rejestru platformy Docker można używać do celów prototypowych i testowych zamiast rejestru w chmurze.

Jeśli nie tworzysz modułu w języku C, potrzebujesz również [narzędzia deweloperskiego Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/) oparte na języku Python, aby skonfigurować lokalne środowisko programistyczne do debugowania, uruchamiania i testowania rozwiązania usługi IoT Edge. Jeśli jeszcze tego nie zrobiłeś, zainstaluj [Pythona (2.7/3.6/3.7) i Pip,](https://www.python.org/) a następnie zainstaluj **iotedgehubdev,** uruchamiając to polecenie w terminalu.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> Obecnie iotedgehubdev używa biblioteki docker-py, która nie jest zgodna z Python 3.8.
>
> Jeśli masz wiele Python, w tym preinstalowany python 2.7 (na przykład na Ubuntu lub macOS), upewnij się, że używasz poprawnego `pip` lub `pip3` zainstalować **iotedgehubdev**

Aby przetestować moduł na urządzeniu, musisz aktywnego centrum IoT hub z co najmniej jednym urządzeniem Usługi IoT Edge. Aby używać komputera jako urządzenia usługi IoT Edge, wykonaj czynności opisane w przewodniku Szybki start dla [systemu Linux](quickstart-linux.md) lub [Windows](quickstart.md). Jeśli na komputerze deweloperskim jest uruchomiony demon IoT Edge, może być konieczne zatrzymanie aplikacji EdgeHub i EdgeAgent przed przejściem do następnego kroku.

## <a name="create-a-new-solution-template"></a>Tworzenie nowego szablonu rozwiązania

Poniższe kroki pokazują, jak utworzyć moduł usługi IoT Edge w preferowanym języku programowania (w tym usługi Azure Functions, napisane w języku C#) przy użyciu programu Visual Studio Code i narzędzi Azure IoT. Możesz rozpocząć od utworzenia rozwiązania, a następnie wygenerowanie pierwszego modułu w tym rozwiązaniu. Każde rozwiązanie może zawierać wiele modułów.

1. Wybierz **polecenie Wyświetl** > **paletę poleceń**.

1. W palecie poleceń wprowadź i uruchom polecenie **Azure IoT Edge: New IoT Edge Solution**.

   ![Uruchom nowe rozwiązanie IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

1. Przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie, a następnie wybierz pozycję **Wybierz folder**.

1. Wprowadź nazwę rozwiązania.

1. Wybierz szablon modułu dla preferowanego języka programowania, aby był pierwszym modułem w rozwiązaniu.

1. Wprowadź nazwę modułu. Wybierz nazwę, która jest unikatowa w rejestrze kontenerów.

1. Podaj nazwę repozytorium obrazów modułu. Visual Studio Code autopopuluje nazwę modułu za pomocą **localhost:5000/<nazwę\>modułu**. Zastąp go własnymi informacjami rejestru. Jeśli używasz lokalnego rejestru platformy Docker do testowania, **localhost** jest w porządku. Jeśli używasz usługi Azure Container Registry, użyj serwera logowania z ustawień rejestru. Serwer logowania wygląda jak ** _ \<nazwa\>rejestru_.azurecr.io**. Zastąp tylko **localhost:5000** część ciągu tak, aby wynik końcowy wygląda jak ** \< *nazwa*\>rejestru .azurecr.io/_\<\>nazwę modułu_**.

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code pobiera podane informacje, tworzy rozwiązanie usługi IoT Edge, a następnie ładuje je w nowym oknie.

Istnieją cztery elementy w ramach rozwiązania:

- Folder **vscode** zawiera konfiguracje debugowania.

- Folder **modułów** ma podfoldery dla każdego modułu.  W folderze dla każdego modułu znajduje się plik, **module.json**, który kontroluje sposób, w jaki moduły są budowane i wdrażane.  Ten plik musiałby zostać zmodyfikowany, aby zmienić rejestr kontenerów wdrażania modułu z localhost na rejestr zdalny. W tym momencie masz tylko jeden moduł.  Ale można dodać więcej w palecie poleceń za pomocą polecenia **Azure IoT Edge: Dodaj moduł IoT Edge**.

- Plik **.env** zawiera listę zmiennych środowiskowych. Jeśli usługa Azure Container Registry jest twoim rejestrem, będziesz mieć w nim nazwę użytkownika i hasło rejestru kontenerów platformy Azure.

  > [!NOTE]
  > Plik środowiska jest tworzony tylko wtedy, gdy podasz repozytorium obrazów dla modułu. Jeśli zaakceptowano domyślne ustawienia hosta lokalnego, aby przetestować i debugować lokalnie, nie musisz deklarować zmiennych środowiskowych.

- Plik **deployment.template.json** wyświetla listę nowego modułu wraz z przykładowym **modułem SimulatedTemperatureSensor,** który symuluje dane, których można użyć do testowania. Aby uzyskać więcej informacji na temat działania manifestów wdrażania, zobacz [Dowiedz się, jak używać manifestów wdrażania do wdrażania modułów i ustanawiania tras](module-composition.md).

Aby zobaczyć, jak działa symulowany moduł temperatury, wyświetl [kod źródłowy SimulatedTemperatureSensor.csproj](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>Dodawanie dodatkowych modułów

Aby dodać dodatkowe moduły do rozwiązania, uruchom polecenie **Azure IoT Edge: Dodaj moduł usługi IoT Edge** z palety poleceń. Można również kliknąć prawym przyciskiem `deployment.template.json` myszy folder **modułów** lub plik w widoku Eksploratora kodu programu Visual Studio, a następnie wybrać pozycję Dodaj moduł **IoT Edge**.

## <a name="develop-your-module"></a>Opracowanie modułu

Domyślny kod modułu dostarczany z rozwiązaniem znajduje się w następującej lokalizacji:

- Funkcja Azure (C#): **moduły > * &lt;&gt;nazwę* > *&lt;modułu nazwą&gt;modułu*.cs**
- C#: **moduły > * &lt;nazwę&gt; modułu* > Program.cs**
- Python: **moduły > * &lt;nazwę&gt; modułu* > main.py**
- Node.js: **moduły > * &lt;&gt; nazwę modułu* > app.js**
- Java: **moduły > * &lt;&gt; nazwę modułu* > src > głównym > java > com > edgemodulemodules > App.java**
- C: **moduły > * &lt;nazwę&gt; modułu* > main.c**

Moduł i plik deployment.template.json są skonfigurowane tak, aby można było utworzyć rozwiązanie, wypchnąć go do rejestru kontenerów i wdrożyć na urządzeniu, aby rozpocząć testowanie bez dotykania kodu. Moduł jest zbudowany, aby po prostu wziąć dane wejściowe ze źródła (w tym przypadku moduł SimulatedTemperatureSensor, który symuluje dane) i potoku go do Usługi IoT Hub.

Gdy chcesz dostosować szablon za pomocą własnego kodu, użyj [zestawów SDK usługi Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) do tworzenia modułów, które zaspokajają kluczowe potrzeby rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodność.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Debugowanie modułu bez kontenera (C#, Node.js, Java)

Jeśli tworzysz w języku C#, Node.js lub Java, moduł wymaga użycia **ModuleClient** obiektu w domyślnym kodzie modułu, dzięki czemu można uruchomić, uruchomić i rozsyłać komunikaty. Użyjesz również domyślnego wejścia kanału **wejściowego1,** aby wykonać akcję, gdy moduł odbiera wiadomości.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurowanie symulatora IoT Edge dla rozwiązania IoT Edge

Na komputerze deweloperskim można uruchomić symulator ioT Edge zamiast instalowania demona zabezpieczeń IoT Edge, aby można było uruchomić rozwiązanie IoT Edge.

1. W Eksploratorze urządzeń po lewej stronie kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge, a następnie wybierz pozycję **Setup IoT Edge Simulator,** aby uruchomić symulator z ciągiem połączenia urządzenia.
1. Możesz zobaczyć, że symulator ioT Edge został pomyślnie skonfigurowany, odczytając szczegóły postępu w zintegrowanym terminalu.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Konfigurowanie symulatora IoT Edge dla aplikacji z jednym modułem

Aby skonfigurować i uruchomić symulator, uruchom polecenie **Azure IoT Edge: Uruchom symulator usługi IoT Edge Hub dla pojedynczego modułu** z palety poleceń kodu programu Visual Studio. Po wyświetleniu monitu użyj **input1** wartości z domyślnego kodu modułu (lub równoważnej wartości z kodu) jako nazwy wejściowej aplikacji. Polecenie wyzwala **iotedgehubdev** CLI, a następnie uruchamia symulator usługi IoT Edge i kontener modułu narzędzia testowego. Możesz zobaczyć wyjścia poniżej w zintegrowanym terminalu, jeśli symulator został pomyślnie uruchomiony w trybie pojedynczego modułu. Można również wyświetlić polecenie, aby pomóc w wysyłaniu `curl` wiadomości za pośrednictwem. Użyjesz jej później.

   ![Konfigurowanie symulatora IoT Edge dla aplikacji z jednym modułem](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Można użyć widoku Eksploratora platformy Docker w programie Visual Studio Code, aby wyświetlić stan uruchomiony modułu.

   ![Stan modułu symulatora](media/how-to-develop-csharp-module/simulator-status.png)

   Kontener **edgeHubDev** jest rdzeniem lokalnego symulatora usługi IoT Edge. Może działać na komputerze deweloperskim bez demona zabezpieczeń usługi IoT Edge i udostępnia ustawienia środowiska dla aplikacji modułu macierzystego lub kontenerów modułów. Kontener **wejściowy** udostępnia interfejsy API REST, aby ułatwić mostkowanie komunikatów do docelowego kanału wejściowego w module.

### <a name="debug-module-in-launch-mode"></a>Moduł debugowania w trybie uruchamiania

1. Przygotuj środowisko do debugowania zgodnie z wymaganiami języka programowania, ustaw punkt przerwania w module i wybierz konfigurację debugowania do użycia:
   - **C #**
     - W terminalu zintegrowanym z programem Visual *** &lt;&gt; *** Studio Code zmień katalog na folder nazwy modułu, a następnie uruchom następujące polecenie, aby utworzyć aplikację .NET Core.

       ```cmd
       dotnet build
       ```

     - Otwórz plik `Program.cs` i dodaj punkt przerwania.

     - Przejdź do widoku debugowania kodu programu Visual Studio, wybierając **pozycję Widok > debugowanie**. Wybierz konfigurację debugowania ** * &lt;nazwę&gt; modułu* Local Debug (.NET Core)** z listy rozwijanej.

        > [!NOTE]
        > `TargetFramework` Jeśli rdzeń platformy .NET nie jest `launch.json`zgodny ze ścieżką programu w programie `launch.json` , `TargetFramework` należy ręcznie zaktualizować ścieżkę programu, aby dopasować go do pliku csproj w programie .csproj, aby program Visual Studio Code mógł pomyślnie uruchomić ten program.

   - **Node.js**
     - W zintegrowanym terminalu programu Visual Studio Code zmień katalog na folder *** &lt;nazwy&gt; modułu,*** a następnie uruchom następujące polecenie, aby zainstalować pakiety węzłów

       ```cmd
       npm install
       ```

     - Otwórz plik `app.js` i dodaj punkt przerwania.

     - Przejdź do widoku debugowania kodu programu Visual Studio, wybierając **pozycję Widok > debugowanie**. Wybierz konfigurację debugowania ** * &lt;&gt; nazwę modułu* Local Debug (Node.js)** z listy rozwijanej.
   - **Java**
     - Otwórz plik `App.java` i dodaj punkt przerwania.

     - Przejdź do widoku debugowania kodu programu Visual Studio, wybierając **pozycję Widok > debugowanie**. Wybierz konfigurację ** * &lt;&gt; * debugowania nazwę modułu Local Debug (Java)** z listy rozwijanej.

1. Kliknij **przycisk Rozpocznij debugowanie** lub naciśnij **klawisz F5,** aby rozpocząć sesję debugowania.

1. W terminalu zintegrowanym z programem Visual Studio Code uruchom następujące polecenie, aby wysłać wiadomość **Hello World** do modułu. Jest to polecenie pokazane w poprzednich krokach podczas konfigurowania symulatora IoT Edge.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Jeśli używasz systemu Windows, upewniając się, że powłoka programu Visual Studio Code zintegrowany terminal jest **Git Bash** lub **WSL Bash**. Nie można `curl` uruchomić polecenia z programu PowerShell lub wiersza polecenia.
   > [!TIP]
   > Można również użyć [PostMan](https://www.getpostman.com/) lub innych narzędzi interfejsu `curl`API do wysyłania wiadomości za pośrednictwem zamiast .

1. W widoku debugowania kodu programu Visual Studio zobaczysz zmienne w lewym panelu.

1. Aby zatrzymać sesję debugowania, wybierz przycisk Zatrzymaj lub naciśnij **klawisze Shift + F5**, a następnie uruchom **usługę Azure IoT Edge: Stop IoT Edge Simulator** na palecie poleceń, aby zatrzymać symulator i oczyścić.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Debugowanie w trybie dołączania za pomocą symulatora ioT Edge (C#, node.js, Java, Azure Functions)

Domyślne rozwiązanie zawiera dwa moduły, jeden to symulowany moduł czujnika temperatury, a drugi to moduł rury. Symulowany czujnik temperatury wysyła komunikaty do modułu potoku, a następnie komunikaty są przesyłane potokiem do centrum IoT Hub. W utworzonym folderze modułu istnieje kilka plików platformy Docker dla różnych typów kontenerów. Użyj dowolnego pliku, który kończy się **rozszerzeniem .debug,** aby utworzyć moduł do testowania.

Obecnie debugowanie w trybie dołączania jest obsługiwane tylko w następujący sposób:

- Moduły języka C#, w tym moduły dla usługi Azure Functions, obsługują debugowanie w kontenerach systemu Linux amd64
- Moduły Node.js obsługują debugowanie w kontenerach z systemem Linux amd64 i arm32v7 oraz kontenerach amd64 systemu Windows
- Moduły Java obsługują debugowanie w kontenerach Linux amd64 i arm32v7

> [!TIP]
> Można przełączać się między opcjami dla domyślnej platformy dla rozwiązania IoT Edge, klikając element na pasku stanu kodu programu Visual Studio.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurowanie symulatora IoT Edge dla rozwiązania IoT Edge

Na komputerze deweloperskim można uruchomić symulator ioT Edge zamiast instalowania demona zabezpieczeń IoT Edge, aby można było uruchomić rozwiązanie IoT Edge.

1. W Eksploratorze urządzeń po lewej stronie kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge, a następnie wybierz pozycję **Setup IoT Edge Simulator,** aby uruchomić symulator z ciągiem połączenia urządzenia.

1. Możesz zobaczyć, że symulator ioT Edge został pomyślnie skonfigurowany, odczytając szczegóły postępu w zintegrowanym terminalu.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Tworzenie i uruchamianie kontenera do debugowania i debugowania w trybie dołączania

1. Otwórz plik modułu `app.js` `App.java`(`Program.cs` `<your module name>.cs`, , , lub ) i dodaj punkt przerwania.

1. W widoku Eksplorator kodu programu `deployment.debug.template.json` Visual Studio kliknij prawym przyciskiem myszy plik rozwiązania, a następnie wybierz polecenie **Kompilacja i uruchom rozwiązanie IoT Edge w programie Simulator**. Można oglądać wszystkie dzienniki kontenera modułu w tym samym oknie. Można również przejść do widoku platformy Docker, aby obserwować stan kontenera.

   ![Obserwuj zmienne](media/how-to-vs-code-develop-module/view-log.png)

1. Przejdź do widoku debugowania kodu programu Visual Studio i wybierz plik konfiguracji debugowania dla modułu. Nazwa opcji debugowania powinna być ** * &lt;podobna&gt; * do nazwy modułu Remote Debug**

1. Wybierz **pozycję Rozpocznij debugowanie** lub naciśnij **klawisz F5**. Wybierz proces, do który chcesz dołączyć.

1. W widoku debugowania kodu programu Visual Studio zobaczysz zmienne w lewym panelu.

1. Aby zatrzymać sesję debugowania, najpierw wybierz przycisk Zatrzymaj lub naciśnij **klawisze Shift + F5**, a następnie wybierz pozycję **Azure IoT Edge: Stop IoT Edge Simulator** z palety poleceń.

> [!NOTE]
> W poprzednim przykładzie pokazano, jak debugować moduły IoT Edge na kontenerach. Dodano narażone porty `createOptions` do ustawień kontenera modułu. Po zakończeniu debugowania modułów zaleca się usunięcie tych narażonych portów dla gotowych do produkcji modułów usługi IoT Edge.
>
> Dla modułów napisanych w języku C#, w tym usługi Azure `Dockerfile.amd64.debug`Functions, w tym przykładzie jest oparty na wersji debugowania , który zawiera .NET Core debugera wiersza polecenia (VSDBG) w obrazie kontenera podczas tworzenia go. Po debugowaniu modułów języka C# zaleca się bezpośrednie użycie pliku Dockerfile bez vsdbg dla gotowych do produkcji modułów usługi IoT Edge.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Debugowanie modułu ze środowiska wykonawczego IoT Edge

W każdym folderze modułu istnieje kilka plików platformy Docker dla różnych typów kontenerów. Użyj dowolnego pliku, który kończy się **rozszerzeniem .debug,** aby utworzyć moduł do testowania.

Podczas debugowania modułów przy użyciu tej metody, moduły są uruchomione na górze środowiska uruchomieniowego usługi IoT Edge. Urządzenie IoT Edge i kod programu Visual Studio może znajdować się na tym samym komputerze lub bardziej typowo Visual Studio Code znajduje się na komputerze deweloperskim, a środowisko uruchomieniowe IoT Edge i moduły są uruchomione na innym komputerze fizycznym. Aby debugować z programu Visual Studio Code, należy:

- Skonfiguruj urządzenie IoT Edge, skompiluj moduły ioT Edge za pomocą pliku dockerfile **.debug,** a następnie wdrażaj na urządzeniu IoT Edge.
- Uwidaczniać adres IP i port modułu, tak aby można było dołączyć debuger.
- Zaktualizuj `launch.json` kod programu Visual Studio, aby kod programu Visual Studio mógł dołączyć do procesu w kontenerze na komputerze zdalnym. Ten plik znajduje `.vscode` się w folderze w obszarze roboczym i aktualizuje się za każdym razem, gdy dodajesz nowy moduł, który obsługuje debugowanie.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Tworzenie i wdrażanie modułu na urządzeniu IoT Edge

1. W programie Visual Studio `deployment.debug.template.json` Code otwórz plik, który zawiera wersję debugowania obrazów modułu z ustawionymi wartościami. `createOptions`

1. Jeśli tworzysz moduł w Pythonie, wykonaj następujące kroki przed kontynuowaniem:
   - Otwórz plik `main.py` i dodaj ten kod po sekcji importu:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Dodaj następujący pojedynczy wiersz kodu do wywołania zwrotnego, które chcesz debugować:

      ```python
      ptvsd.break_into_debugger()
      ```

     Na przykład, jeśli chcesz debugować `receive_message_listener` funkcję, należy wstawić ten wiersz kodu, jak pokazano poniżej:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. W palecie poleceń Kod programu Visual Studio:
   1. Uruchom polecenie **Azure IoT Edge: Build and Push Rozwiązanie usługi IoT Edge**.

   1. Wybierz `deployment.debug.template.json` plik dla rozwiązania.

1. W sekcji **Urządzenia usługi Azure IoT Hub** w widoku Eksploratora kodu programu Visual Studio:
   1. Kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge, a następnie wybierz polecenie **Utwórz wdrożenie dla pojedynczego urządzenia**.

      > [!TIP]
      > Aby potwierdzić, że wybrane urządzenie jest urządzeniem IoT Edge, wybierz je, aby rozwinąć listę modułów i sprawdzić obecność **$edgeHub** i **$edgeAgent**. Każde urządzenie IoT Edge zawiera te dwa moduły.

   1. Przejdź do folderu **konfiguracji** rozwiązania, `deployment.debug.amd64.json` wybierz plik, a następnie wybierz pozycję **Wybierz manifest wdrożenia krawędzi**.

Zobaczysz, że wdrożenie zostanie pomyślnie utworzone przy identyfikatorze wdrożenia w zintegrowanym terminalu.

Stan kontenera można sprawdzić, `docker ps` uruchamiając polecenie w terminalu. Jeśli program Visual Studio Code i środowisko uruchomieniowe IoT Edge są uruchomione na tym samym komputerze, można również sprawdzić stan w widoku platformy Docker kodu programu Visual Studio.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Uwidacznianie adresu IP i portu modułu dla debugera

Tę sekcję można pominąć, jeśli moduły są uruchomione na tym samym komputerze co program Visual Studio Code, ponieważ używasz localhost do dołączenia `createOptions` do kontenera i masz już poprawne ustawienia portu w pliku dockerfile **.debug,** ustawieniach kontenera modułu i `launch.json` pliku. Jeśli moduły i kod programu Visual Studio są uruchomione na oddzielnych komputerach, wykonaj kroki dla języka programowania.

- **C#, w tym usługi Azure Functions**

  [Skonfiguruj kanał SSH na komputerze deweloperskim i urządzeniu IoT Edge,](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) a następnie edytuj `launch.json` plik do dołączenia.

- **Node.js**

  - Upewnij się, że moduł na komputerze, który ma być debugowany jest uruchomiony i gotowy do debugerów do dołączenia, a ten port 9229 jest dostępny zewnętrznie. Można to sprawdzić, `http://<target-machine-IP>:9229/json` otwierając na komputerze debugera. Ten adres URL powinien wyświetlać informacje o module Node.js, który ma zostać debugowany.
  
  - Na komputerze deweloperskim otwórz program Visual `launch.json` Studio Code, a następnie edytuj tak, ** * &lt;aby&gt; * wartość adresu profilu zdalnego debugowania (Node.js)** nazwy modułu (lub ** * &lt;&gt; nazwy modułu* Remote Debug (Node.js w kontenerze systemu Windows),** jeśli moduł jest uruchomiony jako kontener systemu Windows) jest adresem IP debugowanego komputera.

- **Java**

  - Zbuduj tunel SSH do komputera, który ma `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N`być debugowany przez uruchomienie .
  
  - Na komputerze deweloperskim otwórz program Visual Studio Code i edytuj `launch.json` ** * &lt;profil&gt; * zdalnego debugowania (Java) nazwy modułu,** aby można było dołączyć do komputera docelowego. Aby dowiedzieć się `launch.json` więcej na temat edytowania i debugowania oprogramowania Java za pomocą programu Visual Studio Code, zobacz sekcję dotyczącą [konfigurowania debugera](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Upewnij się, że port 5678 na komputerze, który ma być debugowany, jest otwarty i dostępny.

  - W kodzie, `ptvsd.enable_attach(('0.0.0.0', 5678))` który został `main.py`wcześniej wstawiony do , zmień **0.0.0.0** na adres IP komputera, który ma zostać debugowany. Ponownie skompiluj, wypychaj i wdrażaj moduł IoT Edge.

  - Na komputerze deweloperskim otwórz program Visual `launch.json` Studio `host` Code, a następnie edytuj tak, `localhost` ** * &lt;aby&gt; * wartość profilu zdalnego debugowania (Python) nazwy modułu** używała adresu IP komputera docelowego zamiast .

### <a name="debug-your-module"></a>Debugowanie modułu

1. W widoku debugowania kodu programu Visual Studio wybierz plik konfiguracji debugowania dla modułu. Nazwa opcji debugowania powinna być ** * &lt;podobna&gt; * do nazwy modułu Remote Debug**

1. Otwórz plik modułu dla języka programowania i dodaj punkt przerwania:

   - **Funkcja platformy Azure (C#)**: Dodaj `<your module name>.cs`punkt przerwania do pliku .
   - **C#**: Dodaj punkt przerwania do pliku `Program.cs`.
   - **Node.js**: Dodaj punkt przerwania do pliku `app.js`.
   - **Java**: Dodaj punkt przerwania do pliku `App.java`.
   - **Python:** Dodaj punkt przerwania `main.py`do pliku w metodzie `ptvsd.break_into_debugger()` wywołania zwrotnego, w którym dodano wiersz.
   - **C**: Dodaj punkt przerwania do pliku `main.c`.

1. Wybierz **pozycję Rozpocznij debugowanie** lub wybierz **opcję F5**. Wybierz proces, do który chcesz dołączyć.

1. W widoku debugowania kodu programu Visual Studio zobaczysz zmienne w lewym panelu.

> [!NOTE]
> W poprzednim przykładzie pokazano, jak debugować moduły IoT Edge na kontenerach. Dodano narażone porty `createOptions` do ustawień kontenera modułu. Po zakończeniu debugowania modułów zaleca się usunięcie tych narażonych portów dla gotowych do produkcji modułów usługi IoT Edge.

## <a name="build-and-debug-a-module-remotely"></a>Zdalne tworzenie i debugowanie modułu

Dzięki niedawnym zmianom w aparatach Platformy Docker i Moby obsługującym połączenia SSH oraz nowemu ustawieniu w narzędziach Azure IoT Tools, które umożliwia wtryszenie ustawień środowiska do palety poleceń kodu programu Visual Studio i terminali usługi Azure IoT Edge, można teraz tworzyć i debugować modułów na urządzeniach zdalnych.

Zobacz ten [wpis blogu IoT Developer,](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) aby uzyskać więcej informacji i instrukcje krok po kroku.

## <a name="next-steps"></a>Następne kroki

Po sbudcie modułu dowiedz się, jak [wdrożyć moduły usługi Azure IoT Edge z programu Visual Studio Code.](how-to-deploy-modules-vscode.md)

Aby opracować moduły dla urządzeń usługi IoT Edge, [należy zrozumieć i używać zestawów SDK usługi Azure IoT Hub.](../iot-hub/iot-hub-devguide-sdks.md)
