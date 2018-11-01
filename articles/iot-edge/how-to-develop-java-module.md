---
title: Debugowanie modułów języka Java dla usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Używanie programu Visual Studio Code do tworzenia i debugowania modułów języka Java dla usługi Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: af37822429098f904e08404462deb134d4370898
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50421207"
---
# <a name="use-visual-studio-code-to-develop-and-debug-java-modules-for-azure-iot-edge"></a>Używanie programu Visual Studio Code do tworzenia i debugowania modułów języka Java dla usługi Azure IoT Edge

Możesz wysłać logikę biznesową do działania na urządzeniach brzegowych, włączając je do modułów dla usługi Azure IoT Edge. Ten artykuł zawiera szczegółowe instrukcje dotyczące programu Visual Studio Code (VS Code) jako narzędzie do tworzenia głównego do tworzenia i debugowania modułów języka Java.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że używasz komputera lub maszyny wirtualnej z systemem Windows, macOS lub Linux jako komputerze deweloperskim. Urządzenia usługi IoT Edge może być inny urządzenia fizycznego.

> [!NOTE]
> W tym artykule debugowania przedstawiono dwie typowe sposoby debugowania modułu języka Java w programie VS Code. Jednym ze sposobów jest dołączanie procesu w kontenerze modułu, a drugi do lanuch kod modułu w trybie debugowania. Jeśli nie znasz możliwości debugowania programu Visual Studio Code, przeczytaj temat [debugowanie](https://code.visualstudio.com/Docs/editor/debugging).

Ponieważ w tym artykule używa programu Visual Studio Code, jako narzędzia programistyczne głównego, należy zainstalować program VS Code, a następnie dodaj rozszerzeniami niezbędnymi:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Pakietu rozszerzenia języka Java dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
* [Rozszerzenia platformy docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Aby utworzyć moduł, potrzebne są języka Java i Maven, aby skompilować i uruchomić moduł kodu, Docker, aby skompilować obraz modułu i rejestru kontenerów do przechowywania obrazu modułu:
* [Java SE Development Kit 10](https://aka.ms/azure-jdks) i [ustaw zmienną środowiskową `JAVA_HOME`](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) tak, aby wskazywała instalację zestawu JDK.
* [Maven](https://maven.apache.org/)
* [Docker](https://docs.docker.com/engine/installation/)
* [Usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub [usługi Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Można użyć lokalnego rejestru platformy Docker prototypów i testowania, zamiast rejestru chmury. 

Można skonfigurować lokalne Środowisko deweloperskie, debugowanie, uruchamianie i testowanie rozwiązania usługi IoT Edge należy [narzędzia deweloperskiego EdgeHub IoT Azure](https://pypi.org/project/iotedgehubdev/). Zainstaluj [języka Python (w wersji 2.7/3.6) i narzędzie Pip](https://www.python.org/). Następnie zainstaluj **iotedgehubdev** uruchamiając poniższe polecenie w terminalu.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

Aby przetestować modułu na urządzeniu, należy aktywnym Centrum IoT przy użyciu co najmniej jeden identyfikator urządzenia usługi IoT Edge utworzone. Jeśli demon usługi IoT Edge są uruchomione na komputerze deweloperskim, może być konieczne zatrzymanie EdgeHub i EdgeAgent przed przejściem do następnego kroku. 


## <a name="create-a-new-solution-template"></a>Utwórz nowy szablon rozwiązania

Poniższe kroki przedstawiające utworzyć moduł usługi IoT Edge, oparte na języku Java za pomocą programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge. Możesz rozpocząć tworzenie rozwiązania, a następnie generowania pierwszego modułu w ramach tego rozwiązania. Każdy roztwór może zawierać wiele modułów. 

1. W programie Visual Studio Code wybierz **widoku** > **zintegrowany Terminal**.

3. W programie Visual Studio Code wybierz pozycję **Widok** > **Paleta poleceń**. 
4. W palecie poleceń typu, a następnie uruchom polecenie **usługi Azure IoT Edge: nowe rozwiązanie graniczne IoT**.

   ![Uruchamianie nowego rozwiązania usługi IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie, a następnie kliknij przycisk **Wybieranie folderu**. 
6. Podaj nazwę dla swojego rozwiązania. 
7. Wybierz **modułu Java** jako szablon dla pierwszego modułu w rozwiązaniu.
8. Podaj nazwę dla modułu. Wybierz nazwę, która jest unikatowa w obrębie usługi container registry. 
8. Podaj wartość dla elementu groupId lub zaakceptuj wartość domyślną **com.edgemodule**.
9. Podaj repozytorium obrazów w module. Program VS Code autopopulates moduł nazwy, dlatego należy po prostu zastąpić **localhost:5000** podając własne informacje do rejestru. Jeśli używasz lokalnego rejestru platformy Docker do testowania localhost funkcjonuje prawidłowo. Jeśli korzystasz z usługi Azure Container Registry, Użyj serwera logowania z ustawień w rejestrze. Serwer logowania wygląda  **\<nazwa rejestru\>. azurecr.io**. Zastąp tylko część localhost ciągu, nie usuwaj nazwy modułu.

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/how-to-develop-node-module/repository.png)

Program VS Code przyjmuje informacje należy podać, tworzy rozwiązanie IoT Edge, a następnie ładuje go w nowym oknie.

W ramach rozwiązania znajdują się trzy elementy: 
* A **.vscode** folder zawiera konfiguracji debugowania.
* A **modułów** folder zawiera podfoldery dla każdego modułu. Teraz masz tylko jedną licencję, ale można dodać więcej w palecie poleceń za pomocą polecenia **usługi Azure IoT Edge: Dodaj moduł usługi IoT Edge**. 
* A **ENV** plik zawiera listę zmiennych środowiskowych. Jeśli rejestru Azure Container Registry, będziesz mieć usługi Azure Container Registry użytkownika i hasło w nim.

   >[!NOTE]
   >Plik środowiska jest tworzony tylko, jeśli podasz repozytorium obrazów w module. Jeżeli użytkownik zaakceptował domyślnie localhost, Testuj i Debuguj lokalnie, nie należy do deklarowania zmiennych środowiskowych. 

* A **deployment.template.json** nowego modułu wraz z przykładu zawiera listę plików **tempSensor** modułu, która symuluje sieć danych, które można używać do testowania. Aby uzyskać więcej informacji na temat sposobu wdrażania manifestów pracy, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).

## <a name="develop-your-module"></a>Tworzenie modułu

Domyślny kod języka Java w rozwiązaniu znajduje się w **modułów > [Nazwa modułu] > src > główny > java > com > edgemodulemodules > App.java**. W module, plik deployment.template.json są konfigurowane tak, aby skompilować rozwiązanie, Wypchnij go do rejestru kontenerów i wdrożyć ją na urządzeniu do rozpoczęcia testowania bez dotykania żadnego kodu. Moduł został opracowany pod kątem wystarczy pobrać dane wejściowe ze źródła (w tym przypadku moduł tempSensor, która symuluje sieć danych) i przekazać go do usługi IoT Hub. 

Gdy wszystko będzie gotowe dostosować szablon Java przy użyciu własnego kodu, należy użyć [zestawami SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) do tworzenia modułów ten adres klucz dla rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodności. 

Visual Studio Code obsługuje dla języka Java. Dowiedz się więcej o [sposób pracy z językiem Java w programie VS Code](https://code.visualstudio.com/docs/java/java-tutorial).

## <a name="launch-and-debug-module-code-without-container"></a>Uruchamianie i debugowanie kodu modułu bez kontenera
Moduł IoT Edge w języku Java jest zależny od zestawu SDK urządzenia Java usługi Azure IoT. W kodzie modułu domyślne, należy zainicjować **ModuleClient** z ustawieniami środowiska i wprowadź nazwę, co oznacza, że moduł IoT Edge w języku Java wymaga ustawienia środowiska uruchomić i przeprowadzić i trzeba będzie również wysłać lub kierowanie komunikatów kanały danych wejściowych. Domyślne modułu języka Java zawiera tylko jeden kanał wejściowy, a nazwa to **wejście1**.

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>Konfigurowanie usługi IoT Edge symulator modułu pojedynczej aplikacji

1. Aby skonfigurować i uruchomić symulator, program VS Code paletę poleceń, wpisz i wybierz **usługi Azure IoT Edge: Start IoT Edge Hub symulatora dla jednego modułu**. Należy także określić nazw wejściowych dla aplikacji jednej modułu, typu **wejście1** i naciśnij klawisz Enter. Polecenie spowoduje wyzwolenie **iotedgehubdev** interfejsu wiersza polecenia i uruchom symulator usługi IoT Edge i kontener modułu narzędzia testowania. Symulator został uruchomiony w trybie jednego modułu pomyślnie można wyświetlić dane wyjściowe poniżej w zintegrowanym terminalu. Można również wyświetlić `curl` polecenie, aby ułatwić wysłać. Użyjesz jej później.

   ![Konfigurowanie usługi IoT Edge symulator modułu pojedynczej aplikacji](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Można przenieść do Eksploratora platformy Docker i zobacz Moduł stan działania.

   ![Stan modułu symulatora](media/how-to-develop-csharp-module/simulator-status.png)

   **EdgeHubDev** kontener jest podstawowy symulatora lokalnego usługi IoT Edge. Można uruchomić na komputerze deweloperskim bez demona zabezpieczeń usługi IoT Edge i podaj ustawienia środowiska dla aplikacji modułu macierzystego lub kontenery modułu. **Wejściowych** kontenera widoczne restAPIs ułatwiające Mostek wiadomości pod kątem kanał wejściowy w module.

2. W palecie poleceń programu VS Code, wpisz i wybierz **usługi Azure IoT Edge: Ustawianie poświadczeń modułu do ustawień użytkownika** ustawienia modułu środowiska do `azure-iot-edge.EdgeHubConnectionString` i `azure-iot-edge.EdgeModuleCACertificateFile` do ustawień użytkownika. Możesz znaleźć te ustawienia środowiska są wywoływane w **.vscode** > **launch.json** i [ustawień użytkownika w programie VS Code](https://code.visualstudio.com/docs/getstarted/settings).

### <a name="debug-java-module-in-launch-mode"></a>Debugowanie modułu języka Java w trybie uruchamiania

2. Przejdź do adresu `App.java`. Dodaj punkt przerwania w tym pliku.

3. Przejdź do widoku debugowania programu VS Code. Wybierz konfigurację debugowania **ModuleName lokalnego debugowania (java)**. 

4. Kliknij przycisk **Rozpocznij debugowanie** lub naciśnij **F5**. Rozpocznie się sesji debugowania.

5. W zintegrowanym terminalu programu VS Code, uruchom następujące polecenie, aby wysłać **Witaj, świecie** komunikatu do modułu. To polecenie pokazano w poprzednich krokach podczas instalacji usługi IoT Edge symulator pomyślnie. Może być konieczne, Utwórz lub przełącz się do innego zintegrowany terminal, jeśli bieżący klucz zostanie zablokowany.

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Jeśli używasz Windows, upewnij się, że powłoka zintegrowanym terminalu programu VS Code jest **powłoki Git Bash** lub **WSL Bash**. Nie można uruchomić `curl` polecenia w programie PowerShell lub wierszu polecenia. 
   
   > [!TIP]
   > Można również użyć [PostMan](https://www.getpostman.com/) lub innych narzędzi interfejsu API, aby wysyłać komunikaty za pośrednictwem zamiast `curl`.

6. W widoku debugowania programu VS Code zobaczysz zmiennych w panelu po lewej stronie. 

7. Aby zatrzymać sesję debugowania, kliknij przycisk Zatrzymaj lub naciśnij klawisz **Shift + F5**. Paleta poleceń programu VS Code, wpisz i wybierz pozycję **usługi Azure IoT Edge: Zatrzymaj IoT Edge symulator** Aby zatrzymać i oczyścić symulatora.


## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>Tworzenie kontenera modułu do debugowania i debugowania w dołączyć tryb

Rozwiązanie domyślny zawiera dwa moduły, jest jeden moduł czujnika temperatury symulowanego i drugi to modułu potoku Java. Czujnik temperatury symulowane utrzymuje wysyłanie komunikatów do modułu potoku Java, a następnie komunikaty są potokiem do usługi IoT Hub. W folderze modułu, który został utworzony istnieje kilka plików Docker dla kontenera różnych typów. Użyć dowolnej z tych plików, które kończą się rozszerzeniem **.debug** do tworzenia modułu do testowania. Obecnie modułów języka Java obsługują tylko, debugowanie w kontenerach systemu linux amd64 i arm32v7 systemu linux.

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurowanie usługi IoT Edge symulatora dla rozwiązania IoT Edge

W komputerze deweloperskim można uruchomić symulatora usługi IoT Edge, zamiast instalować ją z demona zabezpieczeń usługi IoT Edge do uruchomienia rozwiązania usługi IoT Edge. 

1. W Eksploratorze urządzenia po lewej stronie, kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge, wybierz **Instalatora IoT Edge symulator** można uruchomić symulatora przy użyciu parametrów połączenia urządzenia.

2. Widać, że symulator IoT Edge została pomyślnie skonfigurowana w zintegrowanym terminalu.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Kompilowanie i uruchamianie kontenerów do debugowania i debugowania w dołączyć tryb

1. W programie VS Code przejdź do `deployment.template.json` pliku. Zaktualizuj adres URL obrazu modułu, dodając **.debug** na końcu.

2. Zastąp Java modułu CreateOptions, można żądań w **deployment.template.json** z poniżej zawartości, a następnie zapisz ten plik: 
    ```json
    "createOptions":"{\"HostConfig\":{\"PortBindings\":{\"5005/tcp\":[{\"HostPort\":\"5005\"}]}}}"
    ```

5. Przejdź do widoku debugowania programu VS Code. Wybierz plik konfiguracji debugowania dla modułu. Nazwa opcji debugowania powinny być podobne do **ModuleName zdalne debugowanie (Java)**.

6. Wybierz **Rozpocznij debugowanie** lub wybierz **F5**. Wybierz proces do dołączenia.

7. W widoku debugowania programu VS Code zobaczysz zmiennych w panelu po lewej stronie.

8. Aby zatrzymać sesję debugowania, kliknij przycisk Zatrzymaj lub naciśnij klawisz **Shift + F5**. Paleta poleceń programu VS Code, wpisz i wybierz pozycję **usługi Azure IoT Edge: Zatrzymaj IoT Edge symulator**.

> [!NOTE]
> Poprzedni przykład pokazuje, jak można debugować moduły usługi IoT Edge w języku Java w kontenerach. Ujawnionych portów on dodany do Twojego modułu kontenera CreateOptions, można żądań. Po zakończeniu debugowania moduły języka Java, zalecane jest usunięcie tych ujawnionych portów dla modułów usługi IoT Edge gotowe do produkcji.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, gdy Twoje Moduł skompilowany, jak [modułów wdrożenia usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md)

Tworzenie modułów na urządzeniach usługi IoT Edge [poznawanie i używanie usługi Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).
