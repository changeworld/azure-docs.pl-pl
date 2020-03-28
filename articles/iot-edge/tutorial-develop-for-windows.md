---
title: Samouczek — tworzenie modułu dla urządzeń z systemem Windows przy użyciu usługi Azure IoT Edge
description: W tym samouczku obrało się za pomocą ustawień konfigurowania komputera dewelopera i zasobów w chmurze w celu tworzenia modułów usługi IoT Edge przy użyciu kontenerów systemu Windows dla urządzeń z systemem Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 58a63c9e11cf86318f0e9f051d034cbbaf7c40a9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76772249"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Samouczek: Tworzenie modułów IoT Edge dla urządzeń z systemem Windows

Program Visual Studio służy do tworzenia i wdrażania kodu na urządzeniach z systemem Windows z aplikacją IoT Edge.

W przewodniku Szybki start utworzono urządzenie usługi IoT Edge przy użyciu maszyny wirtualnej systemu Windows i wdrożono wstępnie utworzony moduł z portalu Azure Marketplace. W tym samouczku opracowywają się informacje o tym, czego potrzeba do opracowania i wdrożenia własnego kodu na urządzeniu z aplikacją IoT Edge. Ten samouczek jest przydatnym warunkiem wstępnym dla innych samouczków, które przechodzą do bardziej szczegółowych informacji na temat określonych języków programowania lub usług platformy Azure.

W tym samouczku użyto przykładu wdrażania **modułu C# na urządzeniu z systemem Windows**. Ten przykład został wybrany, ponieważ jest to najbardziej typowy scenariusz rozwoju. Jeśli chcesz opracować w innym języku lub zaplanować wdrażanie usług platformy Azure jako modułów, ten samouczek będzie nadal przydatny, aby dowiedzieć się więcej o narzędziach programistycznych. Po zapoznaniu się z pojęciami dotyczącymi programowania możesz wybrać preferowany język lub usługę platformy Azure, aby zapoznać się ze szczegółami.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Skonfiguruj komputer dewelopera.
> * Użyj narzędzi usługi IoT Edge dla programu Visual Studio, aby utworzyć nowy projekt.
> * Skompiluj swój projekt jako kontener i przechowuj go w rejestrze kontenerów platformy Azure.
> * Wdrażanie kodu na urządzeniu z krawędzią IoT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Kluczowe pojęcia

W tym samouczku opracowywany jest moduł IoT Edge. *Moduł usługi IoT Edge*, lub czasami tylko *moduł* w skrócie, jest kontenerem, który zawiera kod wykonywalny. Można wdrożyć jeden lub więcej modułów na urządzeniu IoT Edge. Moduły wykonują określone zadania, takie jak pozyskiwania danych z czujników, wykonywania operacji analizy danych lub czyszczenia danych lub wysyłania wiadomości do centrum IoT Hub. Aby uzyskać więcej informacji, zobacz [Opis modułów usługi Azure IoT Edge](iot-edge-modules.md).

Podczas tworzenia modułów IoT Edge, ważne jest, aby zrozumieć różnicę między komputera dewelopera i docelowego urządzenia IoT Edge, gdzie moduł zostanie ostatecznie wdrożony. Kontener, który można utworzyć do przechowywania kodu modułu musi odpowiadać systemowi operacyjnemu (OS) *urządzenia docelowego*. W przypadku tworzenia kontenerów systemu Windows ta koncepcja jest prostsza, ponieważ kontenery systemu Windows działają tylko w systemach operacyjnych Windows. Ale można, na przykład, użyć komputera dewelopera systemu Windows do tworzenia modułów dla urządzeń Linux IoT Edge. W tym scenariuszu należy upewnić się, że na komputerze deweloperskim był uruchomiony kontenery systemu Linux. W trakcie przechodzenia przez ten samouczek należy pamiętać o różnicy między *programem operacyjnym maszyny deweloperskiej* a *kontenerowym os.*

Ten samouczek dotyczy urządzeń z systemem Windows z uruchomieniem usługi IoT Edge. Urządzenia Usługi IoT Edge systemu Windows używają kontenerów systemu Windows. Firma Microsoft zaleca korzystanie z programu Visual Studio do tworzenia dla urządzeń z systemem Windows, więc to, co ten poradnik będzie używany. Można również użyć programu Visual Studio Code, chociaż istnieją różnice w obsłudze między tymi dwoma narzędziami.

W poniższej tabeli wymieniono obsługiwane scenariusze rozwoju **kontenerów systemu Windows** w programie Visual Studio Code i Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Usługi platformy Azure** | Azure Functions <br> Azure Stream Analytics |   |
| **Języki** | C# (debugowanie nie jest obsługiwane) | C <br> C# |
| **Więcej informacji** | [Usługa Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Narzędzia usługi Azure IoT Edge dla programu Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Narzędzia usługi Azure IoT Edge dla programu Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Wymagania wstępne

Maszyna deweloperska:

* Windows 10 z aktualizacją 1809 lub nowszą.
* W zależności od preferencji programistów można używać własnego komputera lub maszyny wirtualnej.
  * Upewnij się, że komputer deweloperów obsługuje zagnieżdżoną wirtualizację. Ta funkcja jest niezbędna do uruchamiania aparatu kontenera, który można zainstalować w następnej sekcji.
* Zainstaluj [Git](https://git-scm.com/).

Urządzenie usługi Azure IoT Edge w systemie Windows:

* Firma Microsoft zaleca, aby nie uruchamiać IoT Edge na komputerze deweloperskim, ale zamiast tego należy użyć oddzielnego urządzenia. To rozróżnienie między maszyną deweloperającą a urządzeniem usługi IoT Edge dokładniej odzwierciedla prawdziwy scenariusz wdrażania i pomaga zachować proste różne pojęcia.
* Jeśli nie masz drugiego urządzenia, użyj artykułu przewodnika Szybki start, aby utworzyć urządzenie usługi IoT Edge na platformie Azure za pomocą [maszyny wirtualnej systemu Windows.](quickstart.md)

Zasoby w chmurze:

* Bezpłatne lub standardowe [centrum IoT na](../iot-hub/iot-hub-create-through-portal.md) platformie Azure.

## <a name="install-container-engine"></a>Instalacja silnika kontenerowego

Moduły usługi IoT Edge są pakowane jako kontenery, więc potrzebujesz aparatu kontenerów na komputerze deweloperskim do tworzenia kontenerów i zarządzania nimi. Firma Microsoft zaleca korzystanie z platformy Docker Desktop do tworzenia ze względu na jego wiele funkcji i popularność jako aparat kontenera. Za pomocą platformy Docker Desktop na komputerze z systemem Windows można przełączać się między kontenerami systemu Linux a kontenerami systemu Windows, dzięki czemu można łatwo tworzyć moduły dla różnych typów urządzeń usługi IoT Edge.

Użyj dokumentacji platformy Docker, aby zainstalować na komputerze deweloperskim:

* [Instalowanie programu Docker Desktop dla systemu Windows](https://docs.docker.com/docker-for-windows/install/)

  * Podczas instalowania programu Docker Desktop dla systemu Windows jest pytany, czy chcesz używać kontenerów systemu Linux czy Windows. W tym samouczku użyj **kontenerów systemu Windows**. Aby uzyskać więcej informacji, zobacz [Przełączanie między kontenerami systemu Windows i Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="set-up-visual-studio-and-tools"></a>Konfigurowanie programu Visual Studio i narzędzi

Rozszerzenia IoT dla programu Visual Studio ułatwią tworzenie modułów IoT Edge. Rozszerzenia te zapewniają szablony projektów, automatyzują tworzenie manifestu wdrażania i umożliwiają monitorowanie urządzeń usługi IoT Edge i zarządzanie nimi. W tej sekcji należy zainstalować program Visual Studio i rozszerzenie usługi IoT Edge, a następnie skonfigurować konto platformy Azure do zarządzania zasobami usługi IoT Hub z poziomu programu Visual Studio.

W tym samouczku uczy się kroki rozwoju programu Visual Studio 2019. Jeśli używasz programu Visual Studio 2017 (wersja 15.7 lub nowsza), kroki są podobne. Jeśli wolisz używać programu Visual Studio Code, zapoznaj się z instrukcjami w [programie Visual Studio Code w celu opracowania i debugowania modułów dla usługi Azure IoT Edge.](how-to-vs-code-develop-module.md)

1. Przygotuj program Visual Studio 2019 na komputerze deweloperskim.

   * Jeśli nie masz jeszcze programu Visual Studio na komputerze deweloperskim, [zainstaluj program Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) z następującymi obciążeniami:

      * Tworzenie aplikacji na platformie Azure
      * Programowanie aplikacji klasycznych w języku C++
      * Tworzenie aplikacji dla wielu platform w środowisku .NET Core

   * Jeśli masz już visual studio 2019 na komputerze deweloperskim, wykonaj kroki opisane w [programie Visual Studio,](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) aby dodać wymagane obciążenia.

2. Pobierz i zainstaluj rozszerzenie [Narzędzia Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) dla programu Visual Studio 2019.

   Jeśli używasz programu Visual Studio 2017 (wersja 15.7 lub nowsza), pobierz i zainstaluj [narzędzia Azure IoT Edge Tools for Visual Studio 2017.](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)

3. Po zakończeniu instalacji otwórz program Visual Studio 2019 i wybierz pozycję **Kontynuuj bez kodu.**

4. Wybierz **pozycję Wyświetl** > **Eksploratora chmury**.

5. Wybierz ikonę profilu w Eksploratorze chmury i zaloguj się do konta platformy Azure, jeśli nie jesteś jeszcze zalogowany.

6. Po zalogowaniu się subskrypcje platformy Azure są wyświetlane. Rozwiń subskrypcję, która ma centrum IoT Hub.

7. W ramach subskrypcji rozwiń centrum **IoT Hubs,** a następnie centrum IoT Hub. Powinna zostać wyświetlona lista urządzeń IoT i można użyć tego eksploratora do zarządzania nimi.

   ![Dostęp do zasobów usługi IoT Hub w Eksploratorze chmury](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Tworzenie nowego projektu modułu

Rozszerzenie Narzędzia Usługi Azure IoT Edge zawiera szablony projektów dla wszystkich obsługiwanych języków modułów usługi IoT Edge w programie Visual Studio. Te szablony mają wszystkie pliki i kod, które należy wdrożyć działający moduł do testowania usługi IoT Edge lub daje punkt wyjścia do dostosowania szablonu z własnej logiki biznesowej.

1. Wybierz **plik** > **nowy** > **projekt...**

2. W nowym oknie projektu wyszukaj usługę **IoT Edge** i wybierz projekt **usługi Azure IoT Edge (Windows amd64).** Kliknij przycisk **alej**.

   ![Tworzenie nowego projektu usługi Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. W skonfigurowaniu nowego okna projektu zmień nazwę projektu i rozwiązania na coś opisowego, takiego jak **CSharpTutorialApp**. Kliknij **przycisk Utwórz,** aby utworzyć projekt.

   ![Konfigurowanie nowego projektu usługi Azure IoT Edge](./media/tutorial-develop-for-windows/configure-project.png)

4. W oknie Dodaj moduł skonfiguruj projekt z następującymi wartościami:

   | Pole | Wartość |
   | ----- | ----- |
   | Szablon programu Visual Studio | Wybierz **moduł C#**. |
   | Nazwa modułu | Zaakceptuj domyślny **plik IotEdgeModule1**. |
   | Adres URL repozytorium | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniony z wartości nazwy projektu modułu. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Wartość **serwera logowania** można pobrać ze strony **Przegląd** rejestru kontenerów w witrynie Azure portal. <br><br> Ostateczne repozytorium obrazów \<wygląda\>jak nazwa rejestru .azurecr.io/iotedgemodule1. |

      ![Konfigurowanie projektu dla urządzenia docelowego, typu modułu i rejestru kontenerów](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Wybierz **dodaj,** aby utworzyć moduł.

Po załadowaniu nowego projektu w oknie programu Visual Studio poświęć chwilę na zapoznanie się z utworzonymi plikami:

* Projekt usługi IoT Edge o nazwie **CSharpTutorialApp**.
  * **Folder Moduły** zawiera wskaźniki do modułów uwzględnionych w projekcie. W tym przypadku powinno to być tylko IotEdgeModule1.
  * Ukryty plik **env** przechowuje poświadczenia w rejestrze kontenerów. Te poświadczenia są współużytkowane z urządzeniem usługi IoT Edge, dzięki czemu ma dostęp do ściągania obrazów kontenerów.
  * Plik **deployment.template.json** jest szablonem ułatwiacym tworzenie manifestu wdrożenia. *Manifest wdrażania* to plik, który definiuje dokładnie, które moduły mają być wdrażane na urządzeniu, jak powinny być skonfigurowane i jak mogą komunikować się ze sobą i chmurą.
    > [!TIP]
    > W sekcji poświadczenia rejestru adres jest automatycznie wypełniany na podstawie informacji podanych podczas tworzenia rozwiązania. Jednak zmienne odwołania do nazwy użytkownika i hasła przechowywane w pliku .env. Jest to dla bezpieczeństwa, ponieważ plik .env jest ignorowany, ale szablon wdrożenia nie jest.
* Projekt modułu IoT Edge o nazwie **IotEdgeModule1**.
  * Plik **program.cs** zawiera domyślny kod modułu C#, który jest dostarczany z szablonem projektu. Moduł domyślny pobiera dane wejściowe ze źródła i przekazuje go wraz z Centrum IoT Hub.
  * Plik **module.json** zawiera szczegółowe informacje o module, w tym pełne repozytorium obrazów, wersję obrazu i które dockerfile do użycia dla każdej obsługiwanej platformy.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Podaj poświadczenia rejestru agentowi IoT Edge

Środowisko wykonawcze usługi IoT Edge wymaga poświadczeń rejestru, aby pobierać obrazy kontenerów na urządzenie usługi IoT Edge. Rozszerzenie usługi IoT Edge próbuje wyciągnąć informacje rejestru kontenera z platformy Azure i wypełnić go w szablonie wdrożenia.

1. Otwórz plik **deployment.template.json** w rozwiązaniu modułu.

1. Znajdź **właściwość registryCredentials** w $edgeAgent żądanych właściwościach. Powinien mieć adres rejestru automatycznie wypełniony z informacji podanych podczas tworzenia projektu, a następnie pola nazwy użytkownika i hasła powinny zawierać nazwy zmiennych. Przykład:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Otwórz plik **.env** w rozwiązaniu modułu. (Jest domyślnie ukryty w Eksploratorze rozwiązań, więc może być konieczne wybranie przycisku **Pokaż wszystkie pliki,** aby go wyświetlić).

1. Dodaj wartości **nazwy użytkownika** i **hasła** skopiowane z rejestru kontenerów platformy Azure.

1. Zapisz zmiany w pliku env.

### <a name="review-the-sample-code"></a>Przejrzyj przykładowy kod

Utworzony szablon rozwiązania zawiera przykładowy kod modułu IoT Edge. Ten przykładowy moduł po prostu odbiera wiadomości, a następnie przekazuje je dalej. Funkcja potoku demonstruje ważną koncepcję w uwolnienia IoT, czyli sposób komunikowania się modułów ze sobą.

Każdy moduł może mieć wiele kolejek *wejściowych* i *wyjściowych* zadeklarowanych w ich kodzie. Centrum usługi IoT Edge działające na urządzeniu kieruje komunikaty z danych wyjściowych jednego modułu do wejścia jednego lub więcej modułów. Określony język do deklarowania danych wejściowych i wyjściowych różni się w zależności od języka, ale koncepcja jest taka sama we wszystkich modułach. Aby uzyskać więcej informacji na temat routingu między modułami, zobacz [Deklarowanie tras](module-composition.md#declare-routes).

Przykładowy kod języka C# dostarczany z szablonem projektu używa [klasy ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) z zestawie SDK usługi IoT Hub dla platformy .NET.

1. W pliku **program.cs** znajdź metodę **SetInputMessageHandlerAsync.**

2. [Metoda SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) konfiguruje kolejkę wejściową do odbierania wiadomości przychodzących. Przejrzyj tę metodę i zobacz, jak inicjuje kolejkę wejściową o nazwie **input1**.

   ![Znajdź nazwę danych wejściowych w konstruktorze SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Następnie znajdź **SendEventAsync** metody.

4. [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) procesów metody odebrane wiadomości i konfiguruje kolejkę danych wyjściowych, aby przekazać je razem. Przejrzyj tę metodę i zobacz, że inicjuje kolejkę wyjściową o nazwie **output1**.

   ![Znajdź nazwę danych wyjściowych w konstruktorze SendEventAsync](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Otwórz plik **deployment.template.json.**

6. Znajdź właściwość **modułów** $edgeAgent żądanych właściwości.

   W tym miejscu powinny znajdować się dwa moduły. Pierwszym z nich jest **SimulatedTemperatureSensor**, który jest domyślnie zawarty we wszystkich szablonach, aby zapewnić symulowane dane temperatury, których można użyć do testowania modułów. Drugi to moduł **IotEdgeModule1** utworzony w ramach tego projektu.

   Ta właściwość modułów deklaruje, które moduły powinny być uwzględnione we wdrożeniu na urządzeniu lub urządzeniach.

7. Znajdź właściwości **tras** $edgeHub żądanych właściwości.

   Jedną z funkcji modułu centrum usługi IoT Edge jest kierowanie komunikatów między wszystkimi modułami we wdrożeniu. Przejrzyj wartości we właściwości routes. Pierwsza trasa, **IotEdgeModule1ToIoTHub**, używa symbolu**\*** wieloznacznego ( ), aby uwzględnić dowolną wiadomość pochodzącą z dowolnej kolejki wyjściowej w module IotEdgeModule1. Te komunikaty są dostępne w *$upstream*, który jest zastrzeżoną nazwą wskazującą Centrum IoT. Druga trasa, **sensorToIotEdgeModule1,** pobiera komunikaty pochodzące z modułu SimulatedTemperatureSensor i kieruje je do kolejki *wejściowej input1* modułu IotEdgeModule1.

   ![Przeglądanie tras w pliku deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Kompilowanie i wypychanie rozwiązania

Przejrzano kod modułu i szablon wdrożenia, aby zrozumieć niektóre pojęcia dotyczące wdrażania kluczy. Teraz możesz przystąpić do tworzenia obrazu kontenera IotEdgeModule1 i wypychania go do rejestru kontenerów. Dzięki rozszerzeniu narzędzi IoT dla programu Visual Studio ten krok generuje również manifest wdrożenia na podstawie informacji w pliku szablonu i informacji o module z plików rozwiązania.

### <a name="sign-in-to-docker"></a>Zaloguj się do platformy Docker

Podaj poświadczenia rejestru kontenerów do platformy Docker na komputerze deweloperskim, aby można było wypchnąć obraz kontenera do przechowywania w rejestrze.

1. Otwórz program PowerShell lub wiersz polecenia.

2. Zaloguj się do platformy Docker przy użyciu poświadczeń rejestru kontenerów platformy Azure zapisanych po utworzeniu rejestru.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie . `--password-stdin` Chociaż to najlepsze rozwiązanie jest zalecane w scenariuszach produkcyjnych, jest poza zakresem tego samouczka. Aby uzyskać więcej informacji, zobacz odwołanie do [logowania docker.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

### <a name="build-and-push"></a>Buduj i wypychaj

Komputer deweloperski ma teraz dostęp do rejestru kontenerów, a urządzenia usługi IoT Edge również będą. Nadszedł czas, aby włączyć kod projektu do obrazu kontenera.

1. Kliknij prawym przyciskiem myszy folder projektu **CSharpTutorialApp** i wybierz polecenie **Zbuduj i wypychaj moduły brzegowe IoT**.

   ![Tworzenie i wypychanie modułów IoT Edge](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config,** który zawiera manifest pełnego wdrożenia, zbudowany z informacji w szablonie wdrożenia i innych plików rozwiązania. Po drugie `docker build` uruchamia się do tworzenia obrazu kontenera na podstawie odpowiedniego pliku dockerfile dla architektury docelowej. Następnie uruchamia `docker push` się, aby wypchnąć repozytorium obrazów do rejestru kontenerów.

   Ten proces może potrwać kilka minut za pierwszym razem, ale jest szybszy przy następnym uruchomieniu poleceń.

2. Otwórz plik **deployment.windows-amd64.json** w nowo utworzonym folderze konfiguracyjnym. (Folder konfiguracji może nie być wyświetlany w Eksploratorze rozwiązań w programie Visual Studio. W takim przypadku wybierz ikonę **Pokaż wszystkie pliki** na pasku zadań Eksploratora rozwiązań).

3. Znajdź parametr **obrazu** sekcji IotEdgeModule1. Należy zauważyć, że obraz zawiera pełne repozytorium obrazów z nazwą, wersją i tagiem architektury z pliku module.json.

4. Otwórz plik **module.json** w folderze IotEdgeModule1.

5. Zmień numer wersji obrazu modułu. (Wersja, a nie $schema wersja.) Na przykład przyrost numeru wersji poprawki do **0.0.2** tak, jakbyśmy dokonali małej poprawki w kodzie modułu.

   >[!TIP]
   >Wersje modułów umożliwiają kontrolę wersji i umożliwiają testowanie zmian na małym zestawie urządzeń przed wdrożeniem aktualizacji w wersji produkcyjnej. Jeśli nie zwiększasz wersji modułu przed tworzeniem i wypychaniem, należy zastąpić repozytorium w rejestrze kontenerów.

6. Zapisz zmiany w pliku module.json.

7. Kliknij prawym przyciskiem myszy folder projektu **CSharpTutorialApp** ponownie, a następnie ponownie wybierz polecenie **Zbuduj i wypychaj moduły usługi IoT Edge.**

8. Ponownie otwórz plik **deployment.windows-amd64.json.** Należy zauważyć, że nowy plik nie został utworzony po uruchomieniu polecenia kompilacji i wypychania ponownie. Zamiast tego ten sam plik został zaktualizowany w celu odzwierciedlenia zmian. Obraz IotEdgeModule1 wskazuje teraz wersję kontenera 0.0.2. Ta zmiana w manifeście wdrażania jest jak powiedzieć urządzeniu usługi IoT Edge, że istnieje nowa wersja modułu do wyciągnięcia.

9. Aby jeszcze bardziej zweryfikować, co zrobił polecenie kompilacji i wypychania, przejdź do [witryny Azure portal](https://portal.azure.com) i przejdź do rejestru kontenerów.

10. W rejestrze kontenerów wybierz **repozytoria,** a następnie **iotedgemodule1**. Sprawdź, czy obie wersje obrazu zostały wypchnięte do rejestru.

    ![Wyświetlanie obu wersji obrazu w rejestrze kontenerów](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli wystąpią błędy podczas tworzenia i wypychania obrazu modułu, często ma to do czynienia z konfiguracją platformy Docker na komputerze deweloperskim. Aby przejrzeć konfigurację, użyj następujących kontroli:

* Czy `docker login` zostało uruchomione polecenie przy użyciu poświadczeń skopiowanych z rejestru kontenerów? Te poświadczenia różnią się od tych, których używasz do logowania się na platformie Azure.
* Czy używasz właściwego repozytorium kontenerów? Czy ma poprawną nazwę rejestru kontenera i poprawną nazwę modułu? Otwórz plik **module.json** w folderze IotEdgeModule1, aby sprawdzić. Wartość repozytorium powinna wyglądać jak ** \<nazwa\>rejestru .azurecr.io/iotedgemodule1**.
* Jeśli używasz innej nazwy niż **IotEdgeModule1 dla modułu,** czy ta nazwa jest spójna w całym rozwiązaniu?
* Czy twoja maszyna działa tego samego typu kontenerów, które budujesz? Ten samouczek jest przeznaczony dla urządzeń usługi Windows IoT Edge, więc pliki programu Visual Studio powinny mieć rozszerzenie **windows-amd64,** a pulpit platformy Docker powinien być uruchomiony kontenery systemu Windows.

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Zweryfikowano, że wbudowane obrazy kontenerów są przechowywane w rejestrze kontenerów, więc nadszedł czas, aby wdrożyć je na urządzeniu. Upewnij się, że urządzenie IoT Edge jest uruchomione.

1. Otwórz Eksploratora chmury w programie Visual Studio i rozwiń szczegóły dotyczące centrum IoT Hub.

2. Wybierz nazwę urządzenia, na które chcesz wdrożyć. Na liście **Akcje** wybierz pozycję **Utwórz wdrożenie**.

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/tutorial-develop-for-windows/create-deployment.png)

3. W eksploratorze plików przejdź do folderu konfiguracji projektu i wybierz plik **deployment.windows-amd64.json.** Plik ten często znajduje się na`C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Nie należy używać pliku deployment.template.json, który nie zawiera pełnych wartości obrazu modułu.

4. Rozwiń szczegóły dotyczące urządzenia Usługi IoT Edge w Eksploratorze chmury, aby wyświetlić moduły na urządzeniu.

5. Użyj przycisku **Odśwież,** aby zaktualizować stan urządzenia, aby zobaczyć, że moduły SimulatedTemperatureSensor i IotEdgeModule1 są wdrażane na urządzeniu.

   ![Wyświetlanie modułów uruchomionych na urządzeniu IoT Edge](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Wyświetlanie wiadomości z urządzenia

Kod IotEdgeModule1 odbiera wiadomości za pośrednictwem kolejki wejściowej i przekazuje je za pośrednictwem kolejki wyjściowej. Manifest wdrażania zadeklarował trasy, które przekazały wiadomości z Programu SimulatedTemperatureSensor do IotEdgeModule1, a następnie przesłały dalej wiadomości z IotEdgeModule1 do centrum IoT. Narzędzia usługi Azure IoT Edge dla programu Visual Studio umożliwiają wyświetlanie komunikatów po ich odebraniu w Centrum IoT z poszczególnych urządzeń.

1. W Eksploratorze chmury programu Visual Studio wybierz nazwę urządzenia usługi IoT Edge, które zostało wdrożone.

2. W menu **Akcje** wybierz polecenie **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

3. Obejrzyj sekcję **Dane wyjściowe** w programie Visual Studio, aby wyświetlić komunikaty przychodzące do centrum IoT hub.

   Uruchomienie obu modułów może potrwać kilka minut. Środowisko uruchomieniowe usługi IoT Edge musi odbierać nowy manifest wdrażania, ściągać obrazy modułów ze środowiska wykonawczego kontenera, a następnie uruchamiać każdy nowy moduł.

   ![Wyświetlanie komunikatów urządzenia przychodzącego do chmury](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wyświetlanie zmian na urządzeniu

Jeśli chcesz zobaczyć, co dzieje się na samym urządzeniu, użyj poleceń w tej sekcji, aby sprawdzić środowisko uruchomieniowe IoT Edge i moduły uruchomione na urządzeniu.

Polecenia w tej sekcji są dla urządzenia IoT Edge, a nie komputera deweloperującego. Jeśli używasz maszyny wirtualnej dla urządzenia usługi IoT Edge, połącz się z nią teraz. Na platformie Azure przejdź do strony przeglądu maszyny wirtualnej i wybierz pozycję **Połącz,** aby uzyskać dostęp do połączenia pulpitu zdalnego. Na urządzeniu otwórz polecenie lub okno programu PowerShell, aby uruchomić `iotedge` polecenia.

* Wyświetl wszystkie moduły wdrożone na urządzeniu i sprawdź ich stan:

   ```cmd
   iotedge list
   ```

   Powinny zostać wyświetlone cztery moduły: dwa moduły środowiska wykonawczego IoT Edge, SimulatedTemperatureSensor i IotEdgeModule1. Wszystkie cztery powinny być wymienione jako uruchomione.

* Sprawdź dzienniki pod kątem określonego modułu:

   ```cmd
   iotedge logs <module name>
   ```

   W modułach IoT Edge rozróżniana jest wielkość liter.

   Dzienniki SimulatedTemperatureSensor i IotEdgeModule1 powinny wyświetlać komunikaty, które przetwarzają. Moduł edgeAgent jest odpowiedzialny za uruchamianie innych modułów, więc jego dzienniki będą miały informacje o implementacji manifestu wdrażania. Jeśli dowolnego modułu nie ma na liście lub nie jest uruchomiony, nakreśleń agenta prawdopodobnie będą miały błędy. Moduł edgeHub jest odpowiedzialny za komunikację między modułami i Centrum IoT Hub. Jeśli moduły są uruchomione, ale komunikaty nie są przychodzące do centrum IoT, dzienniki edgeHub prawdopodobnie będą miały błędy.

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurowano program Visual Studio 2019 na komputerze deweloperskim i wdrożono z niego pierwszy moduł IoT Edge. Teraz, gdy znasz podstawowe pojęcia, spróbuj dodać funkcje do modułu, aby mógł analizować przesyłane przez nie dane. Wybierz preferowany język:

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
