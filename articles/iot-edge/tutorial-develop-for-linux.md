---
title: Samouczek — tworzenie modułu dla urządzeń z systemem Linux przy użyciu usługi Azure IoT Edge
description: W tym samouczku opracowywany jest proces konfigurowania komputera dewelopera i zasobów w chmurze w celu tworzenia modułów usługi IoT Edge przy użyciu kontenerów linuksa dla urządzeń z systemem Linux
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 478d9c0485125870f8d5ffb4132f46476b4bb4ef
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384368"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Samouczek: Tworzenie modułów IoT Edge dla urządzeń z systemem Linux

Użyj programu Visual Studio Code do tworzenia i wdrażania kodu na urządzeniach z systemem Linux z uruchomiona aplikacją IoT Edge.

W przewodniku Szybki start utworzono urządzenie usługi IoT Edge przy użyciu maszyny wirtualnej systemu Linux i wdrożono moduł z portalu Azure Marketplace. W tym samouczku opracowywanie i wdrażanie własnego kodu na urządzeniu Z krawędzią IoT. Ten artykuł jest przydatnym warunkiem wstępnym dla innych samouczków, które przechodzą do bardziej szczegółowych informacji na temat określonych języków programowania lub usług platformy Azure.

W tym samouczku użyto przykładu wdrażania **modułu C# na urządzeniu z systemem Linux.** W tym przykładzie został wybrany, ponieważ jest to najbardziej typowy scenariusz deweloperski dla rozwiązań IoT Edge. Nawet jeśli planujesz przy użyciu innego języka lub wdrażania usługi platformy Azure, ten samouczek jest nadal przydatne, aby dowiedzieć się więcej o narzędzia programistyczne i pojęcia. Ukończ to wprowadzenie do procesu tworzenia, a następnie wybierz preferowany język lub usługę platformy Azure, aby zapoznać się ze szczegółami.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Skonfiguruj komputer dewelopera.
> * Użyj narzędzi usługi IoT Edge dla programu Visual Studio Code, aby utworzyć nowy projekt.
> * Skompiluj swój projekt jako kontener i przechowuj go w rejestrze kontenerów platformy Azure.
> * Wdrażanie kodu na urządzeniu z krawędzią IoT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Kluczowe pojęcia

W tym samouczku opracowywany jest moduł IoT Edge. *Moduł usługi IoT Edge*, a czasami tylko *moduł* w skrócie, jest kontenerem z kodem wykonywalnym. Można wdrożyć jeden lub więcej modułów na urządzeniu IoT Edge. Moduły wykonują określone zadania, takie jak pojmowanie danych z czujników, czyszczenie i analizowanie danych lub wysyłanie wiadomości do centrum IoT hub. Aby uzyskać więcej informacji, zobacz [Opis modułów usługi Azure IoT Edge](iot-edge-modules.md).

Podczas tworzenia modułów IoT Edge, ważne jest, aby zrozumieć różnicę między komputera dewelopera i docelowego urządzenia IoT Edge, gdzie moduł zostanie ostatecznie wdrożony. Kontener, który można utworzyć do przechowywania kodu modułu musi odpowiadać systemowi operacyjnemu (OS) *urządzenia docelowego*. Na przykład najbardziej typowym scenariuszem jest ktoś opracowujący moduł na komputerze z systemem Windows zamierza kierować urządzenie z systemem Linux z uruchomieniem usługi IoT Edge. W takim przypadku kontenerowym systemem operacyjnym będzie Linux. Podczas przechodzenia przez ten samouczek, należy pamiętać o różnicy między *systemu operacyjnego maszyny deweloperskiej* i *systemu operacyjnego kontenera*.

Ten samouczek dotyczy urządzeń z systemem Linux z systemem IoT Edge. Preferowanego systemu operacyjnego można używać tak długo, jak długo na komputerze deweloperskim są uruchamiane kontenery systemu Linux. Zalecamy użycie programu Visual Studio Code do opracowania dla urządzeń z systemem Linux, więc to, co ten samouczek będzie używany. Można również użyć programu Visual Studio, chociaż istnieją różnice w obsłudze między tymi dwoma narzędziami.

W poniższej tabeli wymieniono obsługiwane scenariusze rozwoju **kontenerów systemu Linux** w programie Visual Studio Code i Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Architektura urządzenia z systemem Linux** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Usługi platformy Azure** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Języki** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Więcej informacji** | [Usługa Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Narzędzia usługi Azure IoT Edge dla programu Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Narzędzia usługi Azure IoT Edge dla programu Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Obsługa urządzeń Arm64 z systemem Linux jest dostępna w [publicznej wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Aby uzyskać więcej informacji, zobacz [Tworzenie i debugowanie modułów usługi ARM64 IoT Edge w programie Visual Studio Code (wersja zapoznawcza)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

W tym samouczku uczy się kroki rozwoju dla programu Visual Studio Code. Jeśli wolisz używać programu Visual Studio, zapoznaj się z instrukcjami w [programie Visual Studio 2019 w celu opracowania i debugowania modułów dla usługi Azure IoT Edge.](how-to-visual-studio-develop-module.md)

## <a name="prerequisites"></a>Wymagania wstępne

Maszyna deweloperska:

* W zależności od preferencji programistów można używać własnego komputera lub maszyny wirtualnej.
  * Upewnij się, że komputer deweloperów obsługuje zagnieżdżoną wirtualizację. Ta funkcja jest niezbędna do uruchamiania aparatu kontenera, który można zainstalować w następnej sekcji.
* Większość systemów operacyjnych, które można uruchomić aparat kontenera może służyć do tworzenia modułów IoT Edge dla urządzeń z systemem Linux. Ten samouczek używa komputera z systemem Windows, ale wskazuje znane różnice w systemie MacOS lub Linux.
* Zainstaluj [Git](https://git-scm.com/), aby wyciągnąć pakiety szablonów modułów w dalszej części tego samouczka.  
* [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).

Urządzenie usługi Azure IoT Edge w systemie Linux:

* Firma Microsoft zaleca, aby nie uruchamiać IoT Edge na komputerze deweloperskim, ale zamiast tego należy użyć oddzielnego urządzenia. To rozróżnienie między maszyną deweloperającą a urządzeniem usługi IoT Edge dokładniej odzwierciedla prawdziwy scenariusz wdrażania i pomaga zachować proste różne pojęcia.
* Jeśli nie masz drugiego urządzenia, użyj artykułu szybki start, aby utworzyć urządzenie usługi IoT Edge na platformie Azure za pomocą [maszyny wirtualnej systemu Linux.](quickstart-linux.md)

Zasoby w chmurze:

* Bezpłatne lub standardowe [centrum IoT na](../iot-hub/iot-hub-create-through-portal.md) platformie Azure.

## <a name="install-container-engine"></a>Instalacja silnika kontenerowego

Moduły usługi IoT Edge są pakowane jako kontenery, więc potrzebujesz aparatu kontenerów na komputerze deweloperskim do tworzenia i zarządzania nimi. Firma Microsoft zaleca do tworzenia programu Docker Desktop ze względu na jego obsługę funkcji i popularność. Pulpit platformy Docker w systemie Windows umożliwia przełączanie między kontenerami systemu Linux a kontenerami systemu Windows, dzięki czemu można łatwo tworzyć moduły dla różnych typów urządzeń usługi IoT Edge.

Użyj dokumentacji platformy Docker, aby zainstalować na komputerze deweloperskim:

* [Instalowanie programu Docker Desktop dla systemu Windows](https://docs.docker.com/docker-for-windows/install/)

  * Podczas instalowania programu Docker Desktop dla systemu Windows jest pytany, czy chcesz używać kontenerów systemu Linux czy Windows. Decyzję tę można zmienić w dowolnym momencie za pomocą łatwego przełącznika. W tym samouczku używamy kontenerów Systemu Linux, ponieważ nasze moduły są przeznaczone dla urządzeń z systemem Linux. Aby uzyskać więcej informacji, zobacz [Przełączanie między kontenerami systemu Windows i Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Instalowanie platformy Docker Desktop dla komputerów Mac](https://docs.docker.com/docker-for-mac/install/)

* Przeczytaj [informacje o programie Docker CE,](https://docs.docker.com/install/) aby uzyskać informacje o instalacji na kilku platformach Linux.
  * Dla podsystemu Windows dla systemu Linux (WSL) zainstaluj program Docker Desktop dla systemu Windows.

## <a name="set-up-vs-code-and-tools"></a>Konfigurowanie kodu i narzędzi vs

Użyj rozszerzeń IoT dla programu Visual Studio Code do tworzenia modułów IoT Edge. Rozszerzenia te zapewniają szablony projektów, automatyzują tworzenie manifestu wdrażania i umożliwiają monitorowanie urządzeń usługi IoT Edge i zarządzanie nimi. W tej sekcji należy zainstalować program Visual Studio Code i rozszerzenie IoT, a następnie skonfigurować konto platformy Azure do zarządzania zasobami Usługi IoT Hub z poziomu kodu programu Visual Studio.

1. Zainstaluj [kod programu Visual Studio](https://code.visualstudio.com/) na komputerze deweloperskim.

2. Po zakończeniu instalacji wybierz opcję **Wyświetl** > **rozszerzenia**.

3. Wyszukaj **narzędzia Azure IoT Tools**, która w rzeczywistości jest zbiorem rozszerzeń, które ułatwiają interakcję z urządzeniami Usługi IoT Hub i IoT, a także tworzenie modułów usługi IoT Edge.

4. Wybierz pozycję **Zainstaluj**. Każde dołączone rozszerzenie instaluje się indywidualnie.

5. Po zakończeniu instalacji rozszerzenia otwórz paletę poleceń, wybierając **polecenie Wyświetl** > **paletę poleceń**.

6. W palecie poleceń wyszukaj i wybierz **pozycję Azure: Zaloguj się**. Postępuj zgodnie z monitami, aby zalogować się na koncie platformy Azure.

7. Ponownie w palecie poleceń wyszukaj i wybierz pozycję **Centrum Usługi Azure IoT: Wybierz centrum IoT Hub**. Postępuj zgodnie z instrukcjami, aby wybrać subskrypcję platformy Azure i centrum IoT.

8. Otwórz sekcję eksploratora programu Visual Studio Code, wybierając ikonę na pasku aktywności po lewej stronie lub wybierając **pozycję Wyświetl** > **Eksploratora**.

9. U dołu sekcji eksplorator rozwiń zwinięte menu **Usługi Azure IoT Hub.** Powinny być widoczne urządzenia i urządzenia usługi IoT Edge skojarzone z centrum IoT hub wybrany za pośrednictwem palety poleceń.

   ![Wyświetlanie urządzeń w centrum IoT](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Tworzenie nowego projektu modułu

Rozszerzenie Narzędzia Usługi Azure IoT zawiera szablony projektów dla wszystkich obsługiwanych języków modułów usługi IoT Edge w programie Visual Studio Code. Te szablony mają wszystkie pliki i kod, które należy wdrożyć działający moduł do testowania usługi IoT Edge lub daje punkt wyjścia do dostosowania szablonu z własnej logiki biznesowej.

W tym samouczku używamy szablonu modułu C#, ponieważ jest to najczęściej używany szablon.

### <a name="create-a-project-template"></a>Tworzenie szablonu projektu

W palecie poleceń kodu programu Visual Studio wyszukaj i wybierz pozycję **Azure IoT Edge: New IoT Edge Solution**. Postępuj zgodnie z instrukcjami i użyj następujących wartości, aby utworzyć rozwiązanie:

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz moduł **C# Module**. |
   | Podaj nazwę modułu | Zaakceptuj domyślny **samplemodule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br> Ostateczne repozytorium obrazów \<wygląda\>jak nazwa rejestru .azurecr.io/samplemodule. |

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-develop-for-linux/image-repository.png)

Po załadowaniu nowego rozwiązania w oknie Kod programu Visual Studio poświęć chwilę na zapoznanie się z utworzonymi plikami:

* Folder **.vscode** zawiera plik o nazwie **launch.json**, który jest używany do debugowania modułów.
* Folder **modułów** zawiera folder dla każdego modułu w rozwiązaniu. W tej chwili powinno to być tylko **SampleModule**, lub niezależnie od nazwy, którą nadałeś modułowi. Folder SampleModule zawiera kod głównego programu, metadane modułu i kilka plików platformy Docker.
* Plik **.env** przechowuje poświadczenia w rejestrze kontenerów. Te poświadczenia są współużytkowane z urządzeniem usługi IoT Edge, dzięki czemu ma dostęp do ściągania obrazów kontenerów.
* Plik **deployment.debug.template.json** i plik **deployment.template.json** to szablony ułatwiające utworzenie manifestu wdrożenia. *Manifest wdrażania* to plik, który definiuje dokładnie, które moduły mają być wdrażane na urządzeniu, jak powinny być skonfigurowane i jak mogą komunikować się ze sobą i chmurą. Pliki szablonów używają wskaźników dla niektórych wartości. Po przekształceniu szablonu w manifest wdrożenia true wskaźniki są zastępowane wartościami pobranymi z innych plików rozwiązania. Znajdź dwa typowe symbole zastępcze w szablonie wdrożenia:

  * W sekcji poświadczenia rejestru adres jest automatycznie wypełniany na podstawie informacji podanych podczas tworzenia rozwiązania. Jednak nazwa użytkownika i hasło odwołują się do zmiennych przechowywanych w pliku .env. Ta konfiguracja jest dla zabezpieczeń, ponieważ plik .env jest ignorowany, ale szablon wdrożenia nie jest.
  * W sekcji SampleModule obraz kontenera nie jest wypełniona, nawet jeśli podano repozytorium obrazów podczas tworzenia rozwiązania. Ten symbol zastępczy wskazuje plik **module.json** wewnątrz folderu SampleModule. Jeśli przejdziesz do tego pliku, zobaczysz, że pole obrazu zawiera repozytorium, ale także wartość znacznika, która składa się z wersji i platformy kontenera. Wersję można iterować ręcznie w ramach cyklu programowania i wybrać platformę kontenera przy użyciu przełącznika, który wprowadzimy w dalszej części tej sekcji.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Podaj poświadczenia rejestru agentowi IoT Edge

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko wykonawcze potrzebuje tych poświadczeń, aby wyciągnąć obrazy kontenera na urządzenie usługi IoT Edge.

Rozszerzenie usługi IoT Edge próbuje wyciągnąć poświadczenia rejestru kontenera z platformy Azure i wypełnić je w pliku środowiska. Sprawdź, czy poświadczenia są już uwzględnione. Jeśli nie, dodaj je teraz:

1. Otwórz plik **.env** w rozwiązaniu modułu.
2. Dodaj wartości **nazwy użytkownika** i **hasła** skopiowane z rejestru kontenerów platformy Azure.
3. Zapisz zmiany w pliku env.

### <a name="select-your-target-architecture"></a>Wybierz architekturę docelową

Obecnie Visual Studio Code można tworzyć moduły Języka C# dla urządzeń AMD64 i ARM32v7 Linux. Należy wybrać architekturę, która jest przeznaczona dla każdego rozwiązania, ponieważ ma to wpływ na sposób tworzenia i działania kontenera. Domyślnie jest Linux AMD64.

1. Otwórz paletę poleceń i wyszukaj **usługę Azure IoT Edge: Set Default Target Platform for Edge Solution**lub wybierz ikonę skrótu na pasku bocznym u dołu okna.

   ![Wybierz ikonę architektury na pasku bocznym](./media/tutorial-develop-for-linux/select-architecture.png)

2. W palecie poleceń wybierz architekturę docelową z listy opcji. W tym samouczku używamy maszyny wirtualnej Ubuntu jako urządzenia IoT Edge, więc zachowamy domyślny **amd64**.

### <a name="review-the-sample-code"></a>Przejrzyj przykładowy kod

Utworzony szablon rozwiązania zawiera przykładowy kod modułu IoT Edge. Ten przykładowy moduł po prostu odbiera wiadomości, a następnie przekazuje je dalej. Funkcja potoku demonstruje ważną koncepcję w uwolnienia IoT, czyli sposób komunikowania się modułów ze sobą.

Każdy moduł może mieć wiele kolejek *wejściowych* i *wyjściowych* zadeklarowanych w ich kodzie. Centrum usługi IoT Edge działające na urządzeniu kieruje komunikaty z danych wyjściowych jednego modułu do wejścia jednego lub więcej modułów. Określony język do deklarowania danych wejściowych i wyjściowych różni się w zależności od języka, ale koncepcja jest taka sama we wszystkich modułach. Aby uzyskać więcej informacji na temat routingu między modułami, zobacz [Deklarowanie tras](module-composition.md#declare-routes).

Przykładowy kod języka C# dostarczany z szablonem projektu używa [klasy ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) z zestawie SDK usługi IoT Hub dla platformy .NET.

1. Otwórz plik **Program.cs,** który znajduje się wewnątrz **folderu modules/SampleModule/.**

2. W program.cs znajdź Metodę **SetInputMessageHandlerAsync.**

3. [Metoda SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) konfiguruje kolejkę wejściową do odbierania wiadomości przychodzących. Przejrzyj tę metodę i zobacz, jak inicjuje kolejkę wejściową o nazwie **input1**.

   ![Znajdź nazwę danych wejściowych w konstruktorze SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Następnie znajdź **SendEventAsync** metody.

5. [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) procesów metody odebrane wiadomości i konfiguruje kolejkę danych wyjściowych, aby przekazać je razem. Przejrzyj tę metodę i zobacz, że inicjuje kolejkę wyjściową o nazwie **output1**.

   ![Znajdź nazwę danych wyjściowych w SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Otwórz plik **deployment.template.json.**

7. Znajdź właściwość **modułów** $edgeAgent żądanych właściwości.

   W tym miejscu powinny znajdować się dwa moduły. Pierwszym z nich jest **SimulatedTemperatureSensor**, który jest domyślnie zawarty we wszystkich szablonach, aby zapewnić symulowane dane temperatury, których można użyć do testowania modułów. Drugi to **SampleModule** moduł, który został utworzony w ramach tego rozwiązania.

8. W dolnej części pliku znajdź żądane właściwości **modułu $edgeHub.**

   Jedną z funkcji modułu centrum usługi IoT Edge jest kierowanie komunikatów między wszystkimi modułami we wdrożeniu. Przejrzyj wartości we właściwości **routes.** Pierwsza trasa, **SampleModuleToIoTHub**, używa symbolu**\*** wieloznacznego ( ) do wskazania wszelkich komunikatów pochodzących z wszelkich kolejek wyjściowych w module SampleModule. Te komunikaty są dostępne w *$upstream*, który jest zastrzeżoną nazwą wskazującą Centrum IoT. Druga trasa, sensorToSampleModule, pobiera komunikaty pochodzące z modułu SimulatedTemperatureSensor i kieruje je do kolejki *wejściowej input1,* który został zainicjowany w kodzie SampleModule.

   ![Przeglądanie tras w pliku deployment.template.json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Kompilowanie i wypychanie rozwiązania

Przejrzano kod modułu i szablon wdrożenia, aby zrozumieć niektóre pojęcia dotyczące wdrażania kluczy. Teraz możesz przystąpić do tworzenia obrazu kontenera SampleModule i wypychania go do rejestru kontenerów. Dzięki rozszerzeniu narzędzi IoT dla programu Visual Studio Code ten krok generuje również manifest wdrożenia na podstawie informacji w pliku szablonu i informacji o module z plików rozwiązania.

### <a name="sign-in-to-docker"></a>Zaloguj się do platformy Docker

Podaj poświadczenia rejestru kontenerów do platformy Docker, aby można było wypchnąć obraz kontenera do przechowywania w rejestrze.

1. Otwórz terminal zintegrowany z programem Visual Studio Code, wybierając **pozycję View** > **Terminal**.

2. Zaloguj się do platformy Docker przy użyciu poświadczeń rejestru kontenerów platformy Azure zapisanych po utworzeniu rejestru.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie . `--password-stdin` Chociaż to najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz odwołanie do [logowania docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

### <a name="build-and-push"></a>Buduj i wypychaj

Visual Studio Code ma teraz dostęp do rejestru kontenerów, więc nadszedł czas, aby włączyć kod rozwiązania do obrazu kontenera.

1. W Eksploratorze kodu programu Visual Studio kliknij prawym przyciskiem myszy plik **deployment.template.json** i wybierz polecenie **Skompiluj i wypychaj rozwiązanie usługi IoT Edge**.

   ![Tworzenie i wypychanie modułów IoT Edge](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config,** który zawiera manifest pełnego wdrożenia, zbudowany z informacji w szablonie wdrożenia i innych plików rozwiązania. Po drugie `docker build` uruchamia się do tworzenia obrazu kontenera na podstawie odpowiedniego pliku dockerfile dla architektury docelowej. Następnie uruchamia `docker push` się, aby wypchnąć repozytorium obrazów do rejestru kontenerów.

   Ten proces może potrwać kilka minut za pierwszym razem, ale jest szybszy przy następnym uruchomieniu poleceń.

2. Otwórz plik **deployment.amd64.json** w nowo utworzonym folderze konfiguracyjnym. Nazwa pliku odzwierciedla architekturę docelową, więc będzie inna, jeśli wybierzesz inną architekturę.

3. Należy zauważyć, że dwa parametry, które miały symbole zastępcze są teraz wypełnione ich odpowiednie wartości. Sekcja **registryCredentials** zawiera nazwę użytkownika rejestru i hasło pobrane z pliku .env. **SampleModule** ma pełne repozytorium obrazów z nazwą, wersją i tagiem architektury z pliku module.json.

4. Otwórz plik **module.json** w folderze SampleModule.

5. Zmień numer wersji obrazu modułu. (Wersja, a nie $schema wersja.) Na przykład przyrost numeru wersji poprawki do **0.0.2** tak, jakbyśmy dokonali małej poprawki w kodzie modułu.

   >[!TIP]
   >Wersje modułów umożliwiają kontrolę wersji i umożliwiają testowanie zmian na małym zestawie urządzeń przed wdrożeniem aktualizacji w wersji produkcyjnej. Jeśli nie zwiększasz wersji modułu przed tworzeniem i wypychaniem, należy zastąpić repozytorium w rejestrze kontenerów.

6. Zapisz zmiany w pliku module.json.

7. Kliknij ponownie plik **deployment.template.json** i ponownie wybierz polecenie **Build and Push IoT Edge Solution**.

8. Ponownie otwórz plik **deployment.amd64.json.** Należy zauważyć, że nowy plik nie został utworzony po uruchomieniu polecenia kompilacji i wypychania ponownie. Zamiast tego ten sam plik został zaktualizowany w celu odzwierciedlenia zmian. Obraz SampleModule wskazuje teraz wersję kontenera 0.0.2.

9. Aby jeszcze bardziej zweryfikować, co zrobił polecenie kompilacji i wypychania, przejdź do [witryny Azure portal](https://portal.azure.com) i przejdź do rejestru kontenerów.

10. W rejestrze kontenerów wybierz **pozycję Repozytoria,** a następnie **samplemodule**. Sprawdź, czy obie wersje obrazu zostały wypchnięte do rejestru.

    ![Wyświetlanie obu wersji obrazu w rejestrze kontenerów](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli wystąpią błędy podczas tworzenia i wypychania obrazu modułu, często ma to do czynienia z konfiguracją platformy Docker na komputerze deweloperskim. Aby przejrzeć konfigurację, użyj następujących kontroli:

* Czy `docker login` zostało uruchomione polecenie przy użyciu poświadczeń skopiowanych z rejestru kontenerów? Te poświadczenia różnią się od tych, których używasz do logowania się na platformie Azure.
* Czy używasz właściwego repozytorium kontenerów? Czy ma poprawną nazwę rejestru kontenera i poprawną nazwę modułu? Otwórz plik **module.json** w folderze SampleModule, aby sprawdzić. Wartość repozytorium powinna wyglądać jak ** \<nazwa\>rejestru .azurecr.io/samplemodule**.
* Jeśli użyto innej nazwy niż **SampleModule dla modułu,** czy ta nazwa jest spójna w całym rozwiązaniu?
* Czy twoja maszyna działa tego samego typu kontenerów, które budujesz? Ten samouczek jest dla urządzeń Linux IoT Edge, więc Visual Studio Code powinien powiedzieć **amd64** lub **arm32v7** na pasku bocznym, a Docker Desktop powinien być uruchomiony kontenery Systemu Linux.  

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Zweryfikowano, że wbudowane obrazy kontenerów są przechowywane w rejestrze kontenerów, więc nadszedł czas, aby wdrożyć je na urządzeniu. Upewnij się, że urządzenie IoT Edge jest uruchomione.

1. W Eksploratorze kodu programu Visual Studio rozwiń sekcję Urządzenia usługi Azure IoT Hub.

2. Kliknij prawym przyciskiem myszy urządzenie Usługi IoT Edge, na której chcesz wdrożyć, a następnie wybierz polecenie **Utwórz wdrożenie dla pojedynczego urządzenia**.

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/tutorial-develop-for-linux/create-deployment.png)

3. W eksploratorze plików przejdź do folderu **konfiguracji,** a następnie wybierz plik **deployment.amd64.json.**

   Nie należy używać pliku deployment.template.json, który nie ma w nim poświadczeń rejestru kontenerów ani wartości obrazu modułu. Jeśli kierujesz urządzenie ARM32 z systemu Linux, manifest wdrożenia będzie miał nazwę deployment.arm32v7.json.

4. Rozwiń szczegóły urządzenia IoT Edge, a następnie rozwiń listę **Moduły** dla urządzenia.

5. Użyj przycisku odświeżania, aby zaktualizować widok urządzenia, dopóki nie zobaczysz modułów SimulatedTemperatureSensor i SampleModule uruchomionych na urządzeniu.

   Uruchomienie obu modułów może potrwać kilka minut. Środowisko uruchomieniowe usługi IoT Edge musi odbierać nowy manifest wdrażania, ściągać obrazy modułów ze środowiska wykonawczego kontenera, a następnie uruchamiać każdy nowy moduł.

   ![Wyświetlanie modułów uruchomionych na urządzeniu IoT Edge](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Wyświetlanie wiadomości z urządzenia

Kod SampleModule odbiera wiadomości za pośrednictwem kolejki wejściowej i przekazuje je za pośrednictwem kolejki wyjściowej. Manifest wdrożenia zadeklarował trasy, które przekazały komunikaty do SampleModule z Programu SimulatedTemperatureSensor, a następnie przesłały dalej wiadomości z SampleModule do usługi IoT Hub. Narzędzia Usługi Azure IoT dla programu Visual Studio Code umożliwiają wyświetlanie komunikatów po ich odebraniu w Centrum IoT z poszczególnych urządzeń.

1. W Eksploratorze kodu programu Visual Studio kliknij prawym przyciskiem myszy urządzenie Usługi IoT Edge, które chcesz monitorować, a następnie wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzeń**.

2. Obejrzyj okno danych wyjściowych w programie Visual Studio Code, aby wyświetlić komunikaty przychodzące do centrum IoT hub.

   ![Wyświetlanie komunikatów urządzenia przychodzącego do chmury](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wyświetlanie zmian na urządzeniu

Jeśli chcesz zobaczyć, co dzieje się na samym urządzeniu, użyj poleceń w tej sekcji, aby sprawdzić środowisko uruchomieniowe IoT Edge i moduły uruchomione na urządzeniu.

Polecenia w tej sekcji są dla urządzenia IoT Edge, a nie komputera deweloperującego. Jeśli używasz maszyny wirtualnej dla urządzenia usługi IoT Edge, połącz się z nią teraz. Na platformie Azure przejdź do strony przeglądu maszyny wirtualnej i wybierz pozycję **Połącz,** aby uzyskać dostęp do połączenia bezpiecznej powłoki.

* Wyświetl wszystkie moduły wdrożone na urządzeniu i sprawdź ich stan:

   ```bash
   iotedge list
   ```

   Powinny być widoczne cztery moduły: dwa moduły środowiska wykonawczego IoT Edge, SimulatedTemperatureSensor i SampleModule. Wszystkie cztery powinny być wymienione jako uruchomione.

* Sprawdź dzienniki pod kątem określonego modułu:

   ```bash
   iotedge logs <module name>
   ```

   W modułach IoT Edge rozróżniana jest wielkość liter.

   Dzienniki SimulatedTemperatureSensor i SampleModule powinny wyświetlać komunikaty, które przetwarzają. Moduł edgeAgent jest odpowiedzialny za uruchamianie innych modułów, więc jego dzienniki będą miały informacje o implementacji manifestu wdrażania. Jeśli dowolnego modułu nie ma na liście lub nie jest uruchomiony, nakreśleń agenta prawdopodobnie będą miały błędy. Moduł edgeHub jest odpowiedzialny za komunikację między modułami i Centrum IoT Hub. Jeśli moduły są uruchomione, ale komunikaty nie są przychodzące do centrum IoT, dzienniki edgeHub prawdopodobnie będą miały błędy.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano program Visual Studio Code na komputerze deweloperskim i wdrożono z niego pierwszy moduł IoT Edge. Teraz, gdy znasz podstawowe pojęcia, spróbuj dodać funkcje do modułu, aby mógł analizować przesyłane przez nie dane. Wybierz preferowany język:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)
