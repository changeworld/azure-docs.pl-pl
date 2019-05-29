---
title: Tworzenie modułu w przypadku urządzeń Windows — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono konfigurowanie rozwoju zasoby komputera i w chmurze do tworzenia modułów usługi IoT Edge przy użyciu kontenerów Windows dla urządzeń Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/20/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 891b64b8e31266360d718255dcd8e8a1f9fb597c
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306587"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Samouczek: Twórz moduły usługi IoT Edge dla urządzeń Windows

Visual Studio umożliwia tworzenie i wdrażanie kodu Windows dla urządzeń z systemem usługi IoT Edge.

W przewodniku Szybki Start utworzono urządzenia usługi IoT Edge przy użyciu maszyny wirtualnej Windows i wdrożyć wstępnie skompilowane moduł w portalu Azure Marketplace. Ten samouczek przeprowadzi od tego, co jest potrzebne do opracowywania i wdrażania własnego kodu na urządzeniu usługi IoT Edge. W tym samouczku jest wymaganiem wstępnym przydatne dla wszystkich innych samouczków, które zostaną umieszczone w szczegółowe informacje o konkretnych języków programowania lub usług platformy Azure. 

W tym samouczku użyto przykładu wdrażanie **modułu C na urządzeniu Windows**. W tym przykładzie został wybrany dla jego uproszczenia, aby informacje na temat narzędzi deweloperskich, nie martwiąc się o nieważne, czy masz odpowiednie biblioteki, zainstalowane. Po zrozumieniu pojęć programowania, można wybrać z preferowanego języka lub Poznaj szczegółowe informacje z usługi Azure. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie komputera deweloperskiego.
> * Narzędzia usługi IoT Edge dla programu Visual Studio, aby utworzyć nowy projekt.
> * Kompilowanie projektu jako kontener i zapisz go w usłudze Azure container registry.
> * Wdróż swój kod na urządzeniu usługi IoT Edge. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Kluczowe pojęcia

W tym samouczku przedstawiono tworzenie moduł usługi IoT Edge. *Moduł usługi IoT Edge*, lub czasami po prostu *modułu* w skrócie, jest kontenerem, który zawiera kod wykonywalny. Urządzenia usługi IoT Edge można wdrożyć przynajmniej jeden moduł. Moduły wykonywania określonych zadań, takich jak pozyskiwania danych z czujników i przeprowadzania analizy danych lub danych operacji czyszczenia lub wysyłanie komunikatów do Centrum IoT hub. Aby uzyskać więcej informacji, zobacz [modułów Omówienie usługi Azure IoT Edge](iot-edge-modules.md).

Podczas tworzenia modułów usługi IoT Edge, należy zrozumieć różnicę między komputerem deweloperskim i docelowego urządzenia IoT Edge, na którym moduł po pewnym czasie zostanie wdrożony. Kontener, który tworzysz do przechowywania kodu modułu musi być zgodna systemu operacyjnego (OS) *urządzenie docelowe*. Do tworzenia kontenerów Windows takie podejście jest prostsze, ponieważ Windows kontenery są uruchamiane tylko w systemach operacyjnych Windows. Ale komputerze deweloperskim Windows można na przykład użyć do tworzenia modułów dla urządzenia usługi IoT Edge w systemie Linux. W tym scenariuszu należy musi upewnij się, że na komputerze deweloperskim był uruchomiony kontenerów systemu Linux. Podczas wykonywania kroków tego samouczka, należy pamiętać o różnicy między *maszyna deweloperska z systemu operacyjnego* i *kontenera systemu operacyjnego*.

Ten samouczek jest przeznaczony dla usługi IoT Edge systemem Windows. Urządzenia usługi IoT Edge Windows korzystaj z kontenerów Windows. Zalecamy używanie programu Visual Studio do tworzenia dla urządzeń Windows w taki sposób, aby co ten samouczek korzysta. Visual Studio Code można użyć, mimo że istnieją różnice w obsłudze dwa narzędzia.

W poniższej tabeli wymieniono scenariusze rozwoju obsługiwane **kontenery Windows** w programie Visual Studio Code i Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Usługi platformy Azure** | Azure Functions <br> Usługa Azure Stream Analytics |   |
| **Języki** | C#(debugowanie nie jest obsługiwane) | C <br> C# |
| **Więcej informacji** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools), [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

W tym samouczku pokazano kroki programowania dla programu Visual Studio 2019 r. Jeśli zostanie wykorzystany raczej programu Visual Studio Code, zapoznaj się z instrukcjami wyświetlanymi w [używać programu Visual Studio Code do tworzenia i debugowania modułów dla usługi Azure IoT Edge](how-to-vs-code-develop-module.md). Jeśli używasz programu Visual Studio 2017 (w wersji 15.7 lub nowszej) plrease Pobierz i zainstaluj [narzędzia usługi Azure IoT Edge dla programu Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

## <a name="prerequisites"></a>Wymagania wstępne

Komputerze deweloperskim:

* Windows 10 z aktualizacją update 1809 lub nowszej.
* Można użyć własnego komputera lub maszyny wirtualnej, w zależności od preferencji użytkownika dotyczących projektowania.
* [Zainstaluj oprogramowanie Git](https://git-scm.com/). 
* Instalowanie zestawu SDK C usługi Azure IoT dla Windows x64 za pośrednictwem vcpkg:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

<!--vcpkg only required for C development-->

Urządzenia z systemem Azure IoT Edge na Windows:

* Firma Microsoft zaleca, nie uruchamiaj usługi IoT Edge na komputerze deweloperskim, ale zamiast tego użyć osobnego urządzenia. Wykonywania tego rozróżnienia między komputerem deweloperskim i urządzenie usługi IoT Edge dokładnie odzwierciedla scenariusz wdrażania true i pomaga w zabezpieczeniu różne koncepcje proste.
* Jeśli nie masz drugiego urządzenia dostępne, umożliwia tworzenie urządzenia usługi IoT Edge na platformie Azure z artykułem Szybki Start [maszynę wirtualną Windows](quickstart.md).

Zasoby w chmurze:

* Bezpłatna lub standardowa [usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) na platformie Azure. 

## <a name="install-container-engine"></a>Instalowanie aparatu kontenera

Moduły usługi IoT Edge są spakowane w postaci kontenerów, więc należy aparatu kontenera na komputerze deweloperskim tworzenie kontenerów i zarządzanie nimi. Firma Microsoft zaleca używanie pulpitu platformy Docker do tworzenia aplikacji z powodu wielu funkcji i popularność jako aparat kontenera. Za pomocą programu Desktop platformy Docker na komputerze Windows możesz przełączać się między kontenerów systemu Linux i kontenerów Windows tak, aby umożliwiają łatwe tworzenie modułów na potrzeby różnego rodzaju urządzenia usługi IoT Edge. 

Zapoznaj się z dokumentacją platformy Docker do zainstalowania na komputerze deweloperskim: 

* [Instalowanie programu Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)

  * Po zainstalowaniu pulpitu platformy Docker for Windows, pojawi się prośba czy chcesz używać kontenerów systemu Linux lub Windows. W tym samouczku używane **kontenery Windows**. Aby uzyskać więcej informacji, zobacz [przełączać się między kontenerów Windows i Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).


## <a name="set-up-visual-studio-and-tools"></a>Konfigurowanie programu Visual Studio i narzędzia

Tworzenie modułów usługi IoT Edge, należy użyć rozszerzenia IoT dla programu Visual Studio 2019 r. Te rozszerzenia zapewnia szablony projektu, aby zautomatyzować tworzenie manifestu wdrażania i pozwala na monitorowanie i zarządzanie urządzeniami usługi IoT Edge. W tej sekcji możesz zainstalować program Visual Studio i rozszerzenia usługi IoT Edge, a następnie skonfiguruj konto platformy Azure do zarządzania zasobami usługi IoT Hub z poziomu programu Visual Studio. 

1. Jeśli nie masz jeszcze programu Visual Studio na komputerze deweloperskim, [instalacji programu Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) z następującymi pakietami roboczymi: 

   * Programowanie na platformie Azure
   * Programowanie aplikacji klasycznych w języku C++
   * Programowanie dla wielu platform .NET core

1. Jeśli masz już program Visual Studio 2019 r na komputerze deweloperskim. Postępuj zgodnie z instrukcjami w [modyfikowanie programu Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) dodać wymagane obciążenia, jeśli ich nie masz już.

2. Pobierz i zainstaluj [Azure IoT Edge narzędzia](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) rozszerzenie dla programu Visual Studio 2019 r. 

3. Po ukończeniu instalacji, Otwórz program Visual Studio 2019 r i wybierz **Kontynuuj bez konieczności pisania kodu**.

4. Wybierz **widoku** > **Eksplorator chmury**. 

5. Wybierz ikonę profilu w programie cloud explorer, a następnie zaloguj się do konta platformy Azure, jeśli użytkownik nie zostało to zrobione. 

6. Po zalogowaniu się subskrypcji platformy Azure są wyświetlane. Wybierz subskrypcje, z którymi chcesz uzyskać dostęp za pośrednictwem programu cloud explorer, a następnie wybierz pozycję **Zastosuj**. 

7. Rozwiń swoją subskrypcję, następnie **centra IoT Hub**, następnie Centrum IoT hub. Powinien zostać wyświetlony na liście urządzeń IoT i użyć Eksploratora do zarządzania nimi. 

   ![Uzyskaj dostęp do zasobów usługi IoT Hub w Eksploratorze chmury](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Utwórz nowy projekt modułu

Rozszerzenie Azure IoT Edge narzędzia udostępnia szablony projektów, dla wszystkich obsługiwanych IoT Edge językami modułu w programie Visual Studio. Te szablony mają wszystkie pliki i kod, który należy zainstalować moduł pracy do testowania usługi IoT Edge lub zapewniają punkt wyjścia, aby dostosować szablon z własną logiką biznesową. 

1. Wybierz **pliku** > **nowe** > **projektu...**

2. W nowym oknie projektu, 2. W nowym oknie projektu, wyszukiwanie **usługi IoT Edge** projektu, a następnie wybierz **usługi Azure IoT Edge (Windows amd64)** projektu. Kliknij przycisk **Dalej**. 

   ![Utwórz nowy projekt usługi Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. W konfiguracji okno nowego projektu, Zmień nazwę projektu i rozwiązania do opisu coś podobnego **CTutorialApp**. Kliknij przycisk **Utwórz** do tworzenia projektu.

   ![Skonfiguruj nowy projekt usługi Azure IoT Edge](./media/tutorial-develop-for-windows/configure-project.png)
 

4. W oknie aplikacji IoT Edge i moduł należy skonfigurować projekt z następującymi wartościami: 

   | Pole | Wartość |
   | ----- | ----- |

   | Wybierz szablon | Wybierz **modułu C**. | | Nazwa projektu modułu | Zaakceptuj wartość domyślną **IoTEdgeModule1**. | | Repozytorium obrazów platformy docker | Repozytorium obrazów zawiera nazwę rejestru kontenerów i nazwę obrazu kontenera. Obraz kontenera jest wypełniana wstępnie z wartości Nazwa projektu modułu. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br> Repozytorium obrazów końcowego wygląda \<nazwa rejestru\>.azurecr.io/iotedgemodule1. |

   ![Konfigurowanie projektu dla urządzeń docelowych, typ modułu i container registry](./media/tutorial-develop-for-windows/add-application-and-module.png)

5. Wybierz **OK** Aby zastosować zmiany. 

Po załadowaniu nowego projektu w oknie programu Visual Studio, chwilę na zapoznanie się z plikami, utworzonych przez siebie: 

* Wywołuje się, to projekt usługi IoT Edge **AzureIoTEdgeApp1.Windows.Amd64**.
    * **Modułów** folder zawiera wskaźniki do modułów, zawarty w projekcie. W takim przypadku należy po prostu IoTEdgeModule1. 
    * **Deployment.template.json** plik znajduje się szablon, aby pomóc w tworzeniu manifestu wdrażania. A *manifest wdrożenia* jest plikiem, który definiuje dokładnie które moduły mają być wdrożone na urządzeniu, jak powinny być skonfigurowane i jak mogą się komunikować i każdej chmury. 
* Projekt modułu usługi IoT Edge nazywany **IoTEdgeModule1**.
    * **Main.c** plik zawiera kod modułu C domyślny, który pochodzi z szablonem projektu. Domyślny moduł akceptuje dane wejściowe z lokalizacji źródłowej i przekazuje je wzdłuż do usługi IoT Hub. 
    * **Module.json** pliku wstrzymanie szczegóły moduł, w tym repozytorium pełnego obrazu, obrazów wersji i Dockerfile, które będą używane dla każdego z obsługiwanych platform.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Podaj swoje poświadczenia rejestru agenta usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge wymaga poświadczeń rejestru do ściągania obrazów kontenerów na urządzeniu usługi IoT Edge. Te poświadczenia należy dodać do szablonu wdrożenia. 

1. Otwórz **deployment.template.json** pliku.

2. Znajdź **registryCredentials** właściwość $edgeAgent żądane właściwości. 

3. Aktualizacja właściwości przy użyciu swoich poświadczeń, zgodnie z następującym formacie: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }

4. Save the deployment.template.json file. 

### Review the sample code

The solution template that you created includes sample code for an IoT Edge module. This sample module simply receives messages and then passes them on. The pipeline functionality demonstrates an important concept in IoT Edge, which is how modules communicate with each other.

Each module can have multiple *input* and *output* queues declared in their code. The IoT Edge hub running on the device routes messages from the output of one module into the input of one or more modules. The specific language for declaring inputs and outputs varies between languages, but the concept is the same across all modules. For more information about routing between modules, see [Declare routes](module-composition.md#declare-routes).

1. In the **main.c** file, find the **SetupCallbacksForModule** function.

2. This function sets up an input queue to receive incoming messages. It calls the C SDK module client function [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback). Review this function and see that it initializes an input queue called **input1**. 

   ![Find the input name in the SetInputMessageCallback constructor](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Next, find the **InputQueue1Callback** function.

4. This function processes received messages and sets up an output queue to pass them along. It calls the C SDK module client function [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync). Review this function and see that it initializes an output queue called **output1**. 

   ![Find the output name in the SendEventToOutputAsync constructor](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Open the **deployment.template.json** file.

6. Find the **modules** property of the $edgeAgent desired properties. 

   There should be two modules listed here. The first is **tempSensor**, which is included in all the templates by default to provide simulated temperature data that you can use to test your modules. The second is the **IotEdgeModule1** module that you created as part of this project.

   This modules property declares which modules should be included in the deployment to your device or devices. 

7. Find the **routes** property of the $edgeHub desired properties. 

   One of the functions if the IoT Edge hub module is to route messages between all the modules in a deployment. Review the values in the routes property. The first route, **IotEdgeModule1ToIoTHub**, uses a wildcard character (**\***) to include any message coming from any output queue in the IoTEdgeModule1 module. These messages go into *$upstream*, which is a reserved name that indicates IoT Hub. The second route, **sensorToIotEdgeModule1**, takes messages coming from the tempSensor module and routes them to the *input1* input queue of the IotEdgeModule1 module. 

   ![Review routes in deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)


## Build and push your solution

You've reviewed the module code and the deployment template to understand some key deployment concepts. Now, you're ready to build the IotEdgeModule1 container image and push it to your container registry. With the IoT tools extension for Visual Studio, this step also generates the deployment manifest based on the information in the template file and the module information from the solution files. 

### Sign in to Docker

Provide your container registry credentials to Docker on your development machine so that it can push your container image to be stored in the registry. 

1. Open PowerShell or a command prompt.

2. Sign in to Docker with the Azure container registry credentials that you saved after creating the registry. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie `--password-stdin`. Gdy na tym najlepszym rozwiązaniem jest zalecane na potrzeby scenariuszy produkcyjnych, znajduje się poza zakres tego samouczka. Aby uzyskać więcej informacji, zobacz [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odwołania.

### <a name="build-and-push"></a>Zbuduj i Wypchnij

Komputerze deweloperskim ma teraz dostęp do usługi container registry i urządzeniach usługi IoT Edge będzie zbyt. Nadszedł czas, aby włączyć kod projektu do obrazu kontenera. 

1. Kliknij prawym przyciskiem myszy **AzureIotEdgeApp1.Windows.Amd64** folderu projektu i wybierz pozycję **kompilacji i wypychania IoT Edge modułów**. 

   ![Zbuduj i Wypchnij moduły usługi IoT Edge](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config** przechowuje pliki manifestu, wbudowanego się z informacjami o szablon wdrożenia i inne rozwiązania pełne wdrożenie. Po drugie, uruchamia `docker build` do utworzenia obrazu kontenera, w oparciu o plik dockerfile odpowiednie dla architektury docelowej. Następnie uruchamia `docker push` wypychanie repozytorium obrazów do rejestru kontenerów. 

   Ten proces może potrwać kilka minut po raz pierwszy, ale jest szybsza podczas następnego uruchamiania polecenia. 

2. Otwórz **deployment.windows amd64.json** pliku w folderze nowo utworzonej konfiguracji. (Folderu konfiguracji nie może występować w Eksploratorze rozwiązań w programie Visual Studio. Jeśli tak jest, wybierz opcję **Pokaż wszystkie pliki** ikonę na pasku zadań w Eksploratorze rozwiązań.)

3. Znajdź **obraz** parametr sekcji IotEdgeModule1. Należy zauważyć, że obraz zawiera repozytorium pełnego obrazu nazwę, wersję i architektura znacznika z pliku module.json.

4. Otwórz **module.json** pliku w folderze IotEdgeModule1. 

5. Zmień numer wersji obrazu modułu. (Wersja, nie $schema-wersji.) Na przykład zwiększenie numeru wersji poprawki do **0.0.2** tak, jakby było wprowadziliśmy poprawki małe w kodzie modułu. 

   >[!TIP]
   >Wersje modułu Włącz kontroli wersji i pozwalają na testowanie zmian na niewielkim zestawie urządzeń przed wdrożeniem aktualizacji do środowiska produkcyjnego. Jeśli przed kompilowanie i wypychanie nie zwiększa się wersja modułu, należy zastąpić repozytorium w rejestrze kontenera. 

6. Zapisz zmiany w pliku module.json.

7. Kliknij prawym przyciskiem myszy **AzureIotEdgeApp1.Windows.Amd64** folderu projektu ponownie, a następnie ponownie **modułów kompilacji i wypychania usługi IoT Edge**. 

8. Otwórz **deployment.windows amd64.json** plik ponownie. Należy zauważyć, że plik nie został utworzony po uruchomieniu polecenia kompilacji i wypychania ponownie. Zamiast tego samego pliku został zaktualizowany w celu odzwierciedlenia zmian. Obraz IotEdgeModule1 wskazuje teraz 0.0.2 wersję kontenera. Ta zmiana w pliku manifestu wdrożenia to, jak sprawdzić urządzenia usługi IoT Edge, że dostępna jest nowa wersja modułu, aby ściągnąć. 

9. Dodatkowo sprawdź, czy polecenie kompilacji i wypychania, przejdź do [witryny Azure portal](https://portal.azure.com) i przejdź do rejestru kontenerów. 

10. W rejestrze kontenerów, wybierz **repozytoriów** następnie **iotedgemodule1**. Upewnij się, że obie wersje obrazu zostały wypchnięte do rejestru.

    ![Wyświetl obie wersje obrazów w rejestrze kontenerów](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli wystąpią błędy podczas kompilowania i wypychanie obrazu modułu, często ma z konfiguracji platformy Docker na komputerze deweloperskim. Aby przejrzeć konfigurację, użyj następujące testy: 

* Zostało uruchomione `docker login` polecenia przy użyciu poświadczeń, które zostały skopiowane z rejestru kontenerów? Te poświadczenia są inne niż te, których używasz do logowania się na platformie Azure. 
* Czy używasz właściwego repozytorium kontenerów? Ma nazwę rejestru poprawny kontener i nazwy modułu? Otwórz **module.json** pliku w folderze IotEdgeModule1 do sprawdzenia. Wartość repozytorium powinno wyglądać  **\<nazwa rejestru\>.azurecr.io/iotedgemodule1**. 
* Jeśli używasz inną nazwę niż **IotEdgeModule1** dla modułu, jest wpisywanych spójne w całym rozwiązaniu?
* Komputer działa ten sam typ kontenerów, które tworzysz? Niniejszy samouczek jest w przypadku urządzeń Windows IoT Edge, więc pliki programu Visual Studio powinny mieć **windows-amd64** rozszerzenie i pulpitu platformy Docker powinna być uruchomiona kontenery Windows. 

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Możesz zweryfikować, że obrazy kontenera utworzone są przechowywane w usługi container registry, nadszedł czas, aby wdrażać je na urządzeniu. Upewnij się, że urządzenia usługi IoT Edge jest uruchomiona. 

1. Otwórz program Cloud Explorer w programie Visual Studio, a następnie rozwiń szczegóły Centrum IoT. 

2. Wybierz nazwę urządzenia, które mają zostać wdrożone na. W **akcje** listy wybierz **tworzenia wdrożenia**.

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/tutorial-develop-for-windows/create-deployment.png)


3. W Eksploratorze plików przejdź do folderu konfiguracji projektu i wybierz **deployment.windows amd64.json** pliku. Ten plik często znajduje się w `C:\Users\<username>\source\repos\AzureIotEdgeApp1\AzureIotEdgeApp1.Windows.Amd64\config\deployment.windows-amd64.json`

   Nie należy używać pliku deployment.template.json, który nie ma modułu pełnego obrazu wartości w nim. 

4. Rozwiń szczegóły urządzenia usługi IoT Edge w Eksploratorze chmury, aby zobaczyć modułów na twoim urządzeniu.

5. Użyj **Odśwież** przycisk, aby zaktualizować stan urządzenia, aby zobaczyć, są wdrażane tempSensor i moduły IotEdgeModule1 urządzenia. 


   ![Wyświetlanie modułów na urządzeniu usługi IoT Edge z](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Przeglądanie komunikatów z urządzenia

Kod IotEdgeModule1 odbiera komunikaty za pośrednictwem jego danych wejściowych w kolejce i przekazuje je za pośrednictwem swojej kolejki danych wyjściowych. Manifest wdrożenia zadeklarowany trasy, przekazywanie komunikatów z tempSensor IotEdgeModule1, a następnie przesyłane komunikaty IotEdgeModule1 usługi IoT Hub. Narzędzia usługi Azure IoT Edge dla programu Visual Studio umożliwiają wyświetlone komunikaty przychodzące w usłudze IoT Hub z poszczególnych urządzeń. 

1. W programie cloud explorer programu Visual Studio wybierz nazwę urządzenia usługi IoT Edge, która została wdrożona do. 

2. W **akcje** menu, wybierz opcję **rozpocząć monitorowanie wbudowany zdarzeń punkt końcowy**.

3. Obejrzyj **dane wyjściowe** sekcji w programie Visual Studio, aby wyświetlić komunikaty przychodzące w Twoim Centrum IoT hub. 

   Może upłynąć kilka minut, zanim obu modułów rozpocząć. Środowisko uruchomieniowe usługi IoT Edge wymaga aby otrzymać nowy manifest wdrożenia ściągnąć obrazy modułu z kontener środowiska uruchomieniowego, a następnie uruchom każdego modułu. Jeśli użytkownik 

   ![Wyświetl urządzenie przychodzących komunikatów w chmurze](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wyświetl zmiany na urządzeniu

Jeśli chcesz zobaczyć, co dzieje się na twoim urządzeniu, sam, użyj poleceń w tej sekcji, aby sprawdzić środowisko uruchomieniowe usługi IoT Edge i moduły na urządzeniu z. 

Polecenia w tej sekcji dotyczą urządzenia usługi IoT Edge nie komputerze deweloperskim. Jeśli używasz maszyny wirtualnej do urządzenia usługi IoT Edge, połącz się do niej teraz. Na platformie Azure, przejdź do strony Przegląd maszyny wirtualnej, a następnie wybierz pozycję **Connect** na dostęp do połączeń usług pulpitu zdalnego. Na urządzeniu Otwórz polecenie lub okno programu PowerShell do uruchamiania `iotedge` poleceń.

* Aby wyświetlić wszystkie moduły wdrożone na urządzeniu i sprawdzać ich stan:

   ```cmd
   iotedge list
   ```

   Powinny zostać wyświetlone cztery moduły: dwa moduły środowiska uruchomieniowego usługi IoT Edge, tempSensor i IotEdgeModule1. Powinny zostać wyświetlone wszystkie cztery uruchomione.

* Przejrzyj dzienniki dla określonego modułu:

   ```cmd
   iotedge logs <module name>
   ```

   Moduły usługi IoT Edge jest rozróżniana wielkość liter. 

   Dzienniki IotEdgeModule1 i tempSensor powinny pokazywać wiadomości, które są ich przetwarzania. Moduł edgeAgent jest odpowiedzialna za uruchamianie innych modułów, dzięki czemu jej dzienników będzie miał informacji na temat wdrażania manifestu wdrażania. Jeśli dowolny moduł nie ma na liście lub nie jest uruchomiona, dzienniki edgeAgent prawdopodobnie ma błędy. Moduł edgeHub jest odpowiedzialny za komunikację między modułami i Centrum IoT Hub. Jeśli moduły są włączone i uruchomione, ale komunikaty nie są dotarciem do Centrum IoT hub, dzienniki edgeHub prawdopodobnie będziesz mieć błędy. 

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku Konfigurowanie programu Visual Studio 2019 r na komputerze deweloperskim i wdrożyć swoje pierwsze moduł usługi IoT Edge z niego. Skoro już wiesz, podstawowych pojęć, wypróbuj Dodawanie funkcji do modułu, dzięki czemu można analizować, danych, przekazując przy jego użyciu. Wybierz preferowany język: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)