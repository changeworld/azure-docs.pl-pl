---
title: Tworzenie modułu dla urządzeń z systemem Linux — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono konfigurowanie rozwoju zasoby komputera i w chmurze do tworzenia modułów usługi IoT Edge dla urządzeń z systemem Linux przy użyciu kontenerów systemu Linux
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 11fa72f5853350c76b2a8d0aa4fd7b96b598b670
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303850"
---
# <a name="tutorial-develop-iot-edge-modules-for-linux-devices"></a>Samouczek: Twórz moduły usługi IoT Edge dla urządzeń z systemem Linux

Tworzenie i wdrażanie kodu systemu Linux dla urządzeń z systemem usługi IoT Edge, należy użyć programu Visual Studio Code. 

W artykułach Szybki Start utworzono urządzenia usługi IoT Edge przy użyciu maszyny wirtualnej systemu Linux i wdrożony moduł wstępnie utworzone w portalu Azure Marketplace. Ten samouczek przeprowadzi od tego, co jest potrzebne do opracowywania i wdrażania własnego kodu na urządzeniu usługi IoT Edge. W tym samouczku jest wymaganiem wstępnym przydatne dla wszystkich innych samouczków, które zostaną umieszczone w szczegółowe informacje o konkretnych języków programowania lub usług platformy Azure. 

W tym samouczku użyto przykładu wdrażanie **modułu C na urządzeniu z systemem Linux**. W tym przykładzie wybrano, ponieważ ma ona najmniejszą liczbą wymagań wstępnych, tak, aby informacje na temat narzędzi deweloperskich, nie martwiąc się o nieważne, czy masz odpowiednie biblioteki, zainstalowane. Po zrozumieniu pojęć programowania, można wybrać z preferowanego języka lub Poznaj szczegółowe informacje z usługi Azure. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie komputera deweloperskiego.
> * Narzędzia usługi IoT Edge dla programu Visual Studio Code, aby utworzyć nowy projekt.
> * Kompilowanie projektu jako kontener i zapisz go w usłudze Azure container registry.
> * Wdróż swój kod na urządzeniu usługi IoT Edge. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Kluczowe pojęcia

W tym samouczku przedstawiono tworzenie moduł usługi IoT Edge. *Moduł usługi IoT Edge*, lub czasami po prostu *modułu* w skrócie, jest kontenerem, który zawiera kod wykonywalny. Urządzenia usługi IoT Edge można wdrożyć przynajmniej jeden moduł. Moduły wykonywania określonych zadań, takich jak pozyskiwania danych z czujników i przeprowadzania analizy danych lub danych operacji czyszczenia lub wysyłanie komunikatów do Centrum IoT hub. Aby uzyskać więcej informacji, zobacz [modułów Omówienie usługi Azure IoT Edge](iot-edge-modules.md).

Podczas tworzenia modułów usługi IoT Edge, należy zrozumieć różnicę między komputerem deweloperskim i docelowego urządzenia IoT Edge, na którym moduł po pewnym czasie zostanie wdrożony. Kontener, który tworzysz do przechowywania kodu modułu musi być zgodna systemu operacyjnego (OS) *urządzenie docelowe*. Na przykład najbardziej typowym scenariuszem jest ktoś tworzenia modułu na komputerze Windows, Linux urządzenie z IoT Edge docelowe pomyślny przebieg operacji. W takim przypadku system operacyjny kontenera będzie systemu Linux. Podczas wykonywania kroków tego samouczka, należy pamiętać o różnicy między *maszyna deweloperska z systemu operacyjnego* i *kontenera systemu operacyjnego*.

Ten samouczek jest przeznaczony dla systemu Linux z systemem usługi IoT Edge. Można użyć systemu operacyjnego maszyny tworzenia, tak długo, jak komputerze deweloperskim umożliwia uruchamianie kontenerów systemu Linux. Zaleca się przy użyciu programu Visual Studio Code do tworzenia dla urządzeń z systemem Linux, więc to, czego użyje tego samouczka. Visual Studio można użyć, mimo że istnieją różnice w obsłudze dwa narzędzia.

W poniższej tabeli wymieniono scenariusze rozwoju obsługiwane **kontenerów systemu Linux** w programie Visual Studio Code i Visual Studio.

|   | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Architektura urządzenia systemu Linux** | AMD64 systemu Linux <br> Linux ARM32 | AMD64 systemu Linux <br> Linux ARM32 |
| **Usługi platformy Azure** | Azure Functions <br> Usługa Azure Stream Analytics <br> Azure Machine Learning |   |
| **Języki** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Więcej informacji** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools), [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

W tym samouczku pokazano kroki programowania dla programu Visual Studio Code. Jeśli zostanie wykorzystany raczej programu Visual Studio, zapoznaj się z instrukcjami wyświetlanymi w [używać programu Visual Studio 2019 r do tworzenia i debugowania modułów dla usługi Azure IoT Edge](how-to-visual-studio-develop-module.md).

## <a name="prerequisites"></a>Wymagania wstępne

Komputerze deweloperskim:

* Można użyć własnego komputera lub maszyny wirtualnej, w zależności od preferencji użytkownika dotyczących projektowania.
* Większość systemów operacyjnych, które można uruchomić aparatu kontener może służyć do tworzenia modułów usługi IoT Edge dla urządzeń z systemem Linux. W tym samouczku korzysta z komputerów Windows, ale wykazuje znane różnice w systemie MacOS lub Linux. 
* Zainstaluj [Git](https://git-scm.com/), aby ściągnąć modułu pakiety szablonów w dalszej części tego samouczka.  

Urządzenia z systemem Azure IoT Edge w systemie Linux:

* Firma Microsoft zaleca, nie uruchamiaj usługi IoT Edge na komputerze deweloperskim, ale zamiast tego użyć osobnego urządzenia. Wykonywania tego rozróżnienia między komputerem deweloperskim i urządzenie usługi IoT Edge dokładnie odzwierciedla scenariusz wdrażania true i pomaga w zabezpieczeniu różne koncepcje proste.
* Jeśli nie masz drugiego urządzenia dostępne, umożliwia tworzenie urządzenia usługi IoT Edge na platformie Azure z artykułem Szybki Start [maszyny wirtualnej systemu Linux](quickstart-linux.md).

Zasoby w chmurze:

* Bezpłatna lub standardowa [usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) na platformie Azure. 

## <a name="install-container-engine"></a>Instalowanie aparatu kontenera

Moduły usługi IoT Edge są spakowane w postaci kontenerów, więc należy aparatu kontenera na komputerze deweloperskim tworzenie kontenerów i zarządzanie nimi. Firma Microsoft zaleca używanie pulpitu platformy Docker do tworzenia aplikacji z powodu wielu funkcji i popularność jako aparat kontenera. Za pomocą programu Desktop platformy Docker na urządzeniu z systemem Windows można przełączać się między kontenerów systemu Linux i kontenerów Windows tak, aby umożliwiają łatwe tworzenie modułów na potrzeby różnego rodzaju urządzenia usługi IoT Edge. 

Zapoznaj się z dokumentacją platformy Docker do zainstalowania na komputerze deweloperskim: 

* [Instalowanie programu Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)

  * Po zainstalowaniu pulpitu platformy Docker for Windows, pojawi się prośba czy chcesz używać kontenerów systemu Linux lub Windows. Ta decyzja można zmienić w dowolnym momencie za pomocą przełącznika łatwe. W tym samouczku używamy kontenerów systemu Linux, ponieważ naszych modułów przeznaczonych do pracy urządzeniami z systemem Linux. Aby uzyskać więcej informacji, zobacz [przełączać się między kontenerów Windows i Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Instalowanie programu Desktop platformy Docker dla komputerów Mac](https://docs.docker.com/docker-for-mac/install/)

* Odczyt [Docker CE](https://docs.docker.com/install/) informacje dotyczące instalacji na wiele platform dla systemu Linux.

## <a name="set-up-vs-code-and-tools"></a>Konfigurowanie programu VS Code i narzędzia

Użyj rozszerzenia IoT dla programu Visual Studio Code do tworzenia modułów usługi IoT Edge. Te rozszerzenia zapewnia szablony projektu, aby zautomatyzować tworzenie manifestu wdrażania i pozwala na monitorowanie i zarządzanie urządzeniami usługi IoT Edge. W tej sekcji możesz zainstalować Visual Studio Code i rozszerzenia usługi IoT, a następnie skonfigurować konto platformy Azure do zarządzania zasobami usługi IoT Hub z w ramach programu Visual Studio Code. 

1. Zainstaluj [programu Visual Studio Code](https://code.visualstudio.com/) na komputerze deweloperskim. 

2. Po zakończeniu instalacji wybierz **widoku** > **rozszerzenia**. 

3. Wyszukaj **narzędzia IoT Azure**, który jest faktycznie Kolekcja rozszerzeń, które ułatwiają wchodzić w interakcję z Centrum IoT Hub i urządzeniach IoT, a także tworzenie modułów usługi IoT Edge. 

4. Wybierz pozycję **Zainstaluj**. Każde rozszerzenie uwzględnione instaluje indywidualnie. 

5. Gdy gotowe rozszerzenia instalacji, otwórz paletę poleceń, wybierając pozycję **widoku** > **paletę poleceń**. 

6. Paleta poleceń, wyszukiwanie i wybieranie **platformy Azure: Sign in** (Azure: zaloguj). Postępuj zgodnie z monitami, aby zalogować się na koncie platformy Azure. 

7. W palecie poleceń, wyszukiwanie i wybieranie **usługi Azure IoT Hub: Select IoT Hub**  (Azure IoT Hub: wybierz centrum IoT Hub). Postępuj zgodnie z monitami, aby wybrać subskrypcję platformy Azure i usługi IoT hub. 

7. Otwórz Eksplorator części programu Visual Studio Code, wybierając ikonę na pasku działań po lewej stronie, lub wybierając **widoku** > **Explorer**. 

8. W dolnej części Eksploratora, rozwiń zwiniętą **Azure IoT Hub Devices** menu. Powinien zostać wyświetlony urządzenia i urządzenia usługi IoT Edge skojarzonych z usługą IoT hub, wybranej przez paletę poleceń. 

   ![Wyświetl urządzenia w usłudze IoT hub](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Utwórz nowy projekt modułu

Rozszerzenia Azure IoT Tools udostępnia szablony projektów dla wszystkich obsługiwanych IoT Edge, moduł języków w programie Visual Studio Code. Te szablony mają wszystkie pliki i kod, który należy zainstalować moduł pracy do testowania usługi IoT Edge lub zapewniają punkt wyjścia, aby dostosować szablon z własną logiką biznesową. 

W tym samouczku używamy szablon modułu C, ponieważ ma ona najmniejszą liczbą wymagania wstępne dotyczące instalacji. 

### <a name="create-a-project-template"></a>Tworzenie szablonu projektu

Paleta poleceń programu Visual Studio Code, wyszukiwanie i wybieranie **usługi Azure IoT Edge: Nowe rozwiązanie IoT Edge**. Postępuj zgodnie z monitami i utworzyć rozwiązania za pomocą następujących wartości: 

   | Pole | Wartość |
   | ----- | ----- |
   | Wybierz folder | Wybierz lokalizację na maszynie deweloperskiej dla programu VS Code, aby utworzyć pliki rozwiązania. |
   | Podaj nazwę rozwiązania | Wprowadź opisową nazwę rozwiązania lub zaakceptuj nazwę domyślną **EdgeSolution**. |
   | Wybierz szablon modułu | Wybierz pozycję **C Module** (Moduł języka C). |
   | Podaj nazwę modułu | Zaakceptuj wartość domyślną **SampleModule**. |
   | Podaj repozytorium obrazów platformy Docker dla modułu | Repozytorium obrazów zawiera nazwę rejestru kontenerów oraz nazwę obrazu kontenera. Obraz kontenera jest wstępnie wypełniany na podstawie nazwy podanej w ostatnim kroku. Zastąp ciąg **localhost:5000** wartością serwera logowania z rejestru kontenerów platformy Azure. Serwer logowania możesz pobrać ze strony Przegląd rejestru kontenerów w witrynie Azure Portal. <br><br> Repozytorium obrazów końcowego wygląda \<nazwa rejestru\>.azurecr.io/samplemodule. |
 
   ![Udostępnianie repozytorium obrazów platformy Docker](./media/tutorial-develop-for-linux/image-repository.png)

Po załadowaniu nowego rozwiązania w oknie programu Visual Studio Code, chwilę na zapoznanie się z plikami, utworzonych przez siebie: 

* **.Vscode** folder zawiera plik o nazwie **launch.json**, która jest używana do debugowania modułów.
* **Modułów** zawiera folder dla każdego modułu w rozwiązaniu. Teraz, prawo, które powinny być tylko **SampleModule**, lub dowolną nazwę, jak nadaje się do modułu. SampleModule folder zawiera kod głównego programu, metadane modułu i kilka plików Docker. 
* **ENV** plik przechowuje poświadczenia do rejestru kontenerów. Te poświadczenia są udostępniane urządzenia usługi IoT Edge, tak aby mieć dostęp do ściągania obrazów kontenera. 
* **Deployment.debug.template.json** pliku i **deployment.template.json** pliku są szablony, które ułatwiają tworzenie manifestu wdrażania. A *manifest wdrożenia* jest plikiem, który definiuje dokładnie które moduły mają być wdrożone na urządzeniu, jak powinny być skonfigurowane i jak mogą się komunikować i każdej chmury. Pliki szablonów stosowanie wskaźników dla niektórych wartości. Kiedy przekształcasz szablon do manifestu wdrażania true wskaźniki są zastępowane wartości z innych plików rozwiązania. Znajdź dwie typowe symbole zastępcze w szablonie wdrożenia: 

  * W sekcji poświadczenia rejestru adres jest autofilled z informacji podanych podczas tworzenia rozwiązania. Jednak nazwy użytkownika i hasła odwoływać się do zmiennych przechowywanych w pliku env. Te informacje dotyczą zabezpieczeń, zgodnie z pliku ENV jest ignorowane w usłudze git, ale szablonu wdrożenia nie jest. 
  * W sekcji SampleModule obraz kontenera nie jest wypełnione, nawet jeśli repozytorium obrazów jest podane podczas tworzenia rozwiązania. Ten symbol zastępczy wskazuje **module.json** plików znajdujące się w folderze SampleModule. Po przejściu do tego pliku, zobaczysz, że pole obrazu zawierają repozytorium, ale wartość tagu, który składa się z wersją i platformy kontenera. Można wykonać iterację wersji ręcznie w ramach cyklu rozwoju, a następnie wybierz platformę kontenerów za pomocą przełącznika, który możemy wprowadzić później w tej sekcji. 

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Podaj swoje poświadczenia rejestru agenta usługi IoT Edge

W pliku środowiska przechowywane są poświadczenia rejestru kontenerów udostępniane środowisku uruchomieniowemu usługi IoT Edge. Środowisko uruchomieniowe potrzebuje tych poświadczeń do ściągania obrazów kontenerów na urządzeniu usługi IoT Edge. 

Rozszerzenia usługi IoT Edge próbuje ściągania przez kontener poświadczeń rejestru na platformie Azure i wypełnić je w pliku środowiska. Sprawdź, czy poświadczenia są już uwzględniane. Jeśli nie, dodaj je teraz:

1. Otwórz **ENV** plików w rozwiązaniu do modułu. 
2. Dodaj **username** i **hasło** wartości, które zostały skopiowane z usługi Azure container registry.
3. Zapisz zmiany w pliku env. 

### <a name="select-your-target-architecture"></a>Wybierz swoje Architektura docelowa

Obecnie usługa Visual Studio Code można tworzyć moduły języka C dla urządzeń z AMD64 systemu Linux i ARM32v7 systemu Linux. Musisz wybrać architektury, które zostaną objęci z poszczególnych rozwiązań, ponieważ to ma wpływ na sposób kontenera jest oparta i działa. Wartość domyślna to AMD64 systemu Linux. 

1. Otwórz paletę poleceń i wyszukaj **usługi Azure IoT Edge: Ustaw domyślne platformę docelową dla nowoczesne rozwiązanie**, lub wybierz ikonę skrótu prowadzącą w pasku bocznym, w dolnej części okna. 

   ![Wybierz ikonę architektury w pasku bocznym](./media/tutorial-develop-for-linux/select-architecture.png)

2. W palecie poleceń Wybierz architektury docelowej z listy opcji. W tym samouczku używamy maszynę wirtualną Ubuntu jako urządzenie usługi IoT Edge, dzięki czemu będzie Zachowaj ustawienie domyślne **amd64**. 

### <a name="review-the-sample-code"></a>Przejrzyj przykładowy kod

Utworzony szablon rozwiązania zawiera przykładowy kod dla modułu usługi IoT Edge. Ten przykładowy moduł po prostu odbiera komunikaty i przekazuje je na. Funkcje potoku przedstawia bardzo ważnym pojęciem usługi IoT Edge, czyli jak moduły komunikują się ze sobą.

Każdy moduł może mieć wiele *wejściowych* i *dane wyjściowe* kolejek zadeklarowana w ich kodzie. Centrum usługi IoT Edge uruchomionej na urządzeniu kieruje komunikaty z danych wyjściowych jednego modułu do wejścia przynajmniej jeden moduł. Określony język do deklarowania dane wejściowe i wyjściowe różni się między językami, ale koncepcja jest taka sama we wszystkich modułach. Aby uzyskać więcej informacji na temat routingu między modułami, zobacz [zadeklarować trasy](module-composition.md#declare-routes).

1. Otwórz **main.c** pliku, który znajduje się wewnątrz **modułów/SampleModules/** folderu. 

2. Funkcja zestawu SDK C usługi IoT Hub [SetInputMessageCallback](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-setinputmessagecallback) można zainicjować modułu kolejki danych wejściowych. Wyszukaj w pliku main.c dla tej funkcji.

3. Przejrzyj Konstruktor funkcji SetInputMessageCallback i zobacz, czy kolejka danych wejściowych o nazwie **wejście1** jest inicjowany w kodzie. 

   ![Znajdź nazwę wejściową w Konstruktorze SetInputMessageCallback](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Moduł danych wyjściowych kolejki są inicjowane w podobny sposób. Wyszukaj [SendEventToOutputAsync](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-ll-h/iothubmoduleclient-ll-sendeventtooutputasync) funkcji w pliku main.c. 

5. Przejrzyj Konstruktor funkcji SendEventToOutputAsync i zobacz, że kolejki wyjściowej o nazwie **output1** jest inicjowany w kodzie. 

   ![Znajdź nazwę wyjściowego w SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Otwórz **deployment.template.json** pliku.

7. Znajdź **modułów** właściwość $edgeAgent żądane właściwości. 

   Powinna to być dwa moduły wymienione w tym miejscu. Pierwsza to **tempSensor**, która jest domyślnie we wszystkich szablonach zapewnienie symulowane dane dotyczące temperatury używanej do testowania moduły. Drugi to **SampleModule** modułu, który został utworzony w ramach tego rozwiązania.

7. W dolnej części pliku Znajdź żądane właściwości dla **$edgeHub** modułu. 

   Jedną z funkcji modułu Centrum IoT Edge jest do przesyłania wiadomości między wszystkie moduły w danym wdrożeniu. Sprawdź wartości w **trasy** właściwości. Pierwsza trasa **SampleModuleToIoTHub**, korzysta z symbolem wieloznacznym ( **\*** ) do wskazania komunikaty pochodzące z dowolnym kolejki danych wyjściowych w SampleModule module. Te komunikaty są przekazywane do *$nadrzędne*, która jest nazwą zastrzeżoną, który wskazuje usługę IoT Hub. Druga trasa sensorToSampleModule, przyjmuje komunikaty pochodzące z modułu tempSensor i kieruje je do *wejście1* danych wejściowych w kolejce przedstawionego zainicjowane w kodzie SampleModule. 

   ![Przejrzyj trasy w deployment.template.json](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Kompilowanie i wypychanie rozwiązania

Po przejrzeniu kod modułu i Szablon wdrożenia, aby poznać niektóre pojęcia związane z wdrażaniem klucza. Teraz możesz przystąpić do tworzenia SampleModule obraz kontenera i Wypchnij go do rejestru kontenerów. Za pomocą rozszerzenia IoT tools for Visual Studio Code w tym kroku generuje manifest wdrożenia, w oparciu o informacje zawarte w pliku szablonu i informacje zawarte w module pliki rozwiązania. 

### <a name="sign-in-to-docker"></a>Zaloguj się do platformy Docker

Poświadczenia kontenera rejestru do platformy Docker tak, aby można go wypchnąć obraz kontenera mają być przechowywane w rejestrze. 

1. Otworzyć zintegrowany terminal programu Visual Studio Code, wybierając **widoku** > **terminalu**.

2. Zaloguj się do platformy Docker przy użyciu poświadczeń rejestru kontenerów platformy Azure, które zostały zapisane po utworzeniu rejestru. 

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Może pojawić się ostrzeżenie o zabezpieczeniach zalecające użycie `--password-stdin`. Gdy na tym najlepszym rozwiązaniem jest zalecane na potrzeby scenariuszy produkcyjnych, znajduje się poza zakres tego samouczka. Aby uzyskać więcej informacji, zobacz [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) odwołania.

### <a name="build-and-push"></a>Zbuduj i Wypchnij 

Visual Studio Code ma teraz dostęp do usługi container registry, więc nadszedł czas, aby włączyć kod rozwiązania do obrazu kontenera. 

1. W Eksploratorze programu Visual Studio Code kliknij prawym przyciskiem myszy **deployment.template.json** plik i wybierz **kompilacji i wypychania IoT nowoczesne rozwiązanie**. 

   ![Zbuduj i Wypchnij moduły usługi IoT Edge](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Polecenie kompilacji i wypychania uruchamia trzy operacje. Najpierw tworzy nowy folder w rozwiązaniu o nazwie **config** przechowuje pliki manifestu, wbudowanego się z informacjami o szablon wdrożenia i inne rozwiązania pełne wdrożenie. Po drugie, uruchamia `docker build` do utworzenia obrazu kontenera, w oparciu o plik dockerfile odpowiednie dla architektury docelowej. Następnie uruchamia `docker push` wypychanie repozytorium obrazów do rejestru kontenerów. 

   Ten proces może potrwać kilka minut po raz pierwszy, ale jest szybsza podczas następnego uruchamiania polecenia. 

2. Otwórz **deployment.amd64.json** plik config nowo utworzonego folderu. Nazwa pliku odzwierciedla Architektura docelowa, dzięki czemu będzie on być inny, jeśli została wybrana opcja innej architektury.

3. Należy zauważyć, że dwa parametry, z których obecnie symbole zastępcze są wypełniane ich odpowiednimi wartościami. **RegistryCredentials** sekcja zawiera nazwy i pobierane z pliku ENV hasła użytkownika rejestru. **SampleModule** ma repozytorium pełny obraz z tagiem nazwę, wersję i architektura z pliku module.json. 

4. Otwórz **module.json** pliku w folderze SampleModule. 

5. Zmień numer wersji obrazu modułu. (Wersja, nie $schema-wersji.) Na przykład zwiększenie numeru wersji poprawki do **0.0.2** tak, jakby było wprowadziliśmy poprawki małe w kodzie modułu. 

   >[!TIP]
   >Wersje modułu Włącz kontroli wersji i pozwalają na testowanie zmian na niewielkim zestawie urządzeń przed wdrożeniem aktualizacji do środowiska produkcyjnego. Jeśli przed kompilowanie i wypychanie nie zwiększa się wersja modułu, należy zastąpić repozytorium w rejestrze kontenera. 

6. Zapisz zmiany w pliku module.json.

7. Kliknij prawym przyciskiem myszy **deployment.template.json** ponownie plik, a następnie ponownie **kompilacji i wypychania IoT nowoczesne rozwiązanie**.

8. Otwórz **deployment.amd64.json** plik ponownie. Należy zauważyć, że plik nie został utworzony po uruchomieniu polecenia kompilacji i wypychania ponownie. Zamiast tego samego pliku został zaktualizowany w celu odzwierciedlenia zmian. Obraz SampleModule wskazuje teraz 0.0.2 wersję kontenera. 

9. Dodatkowo sprawdź, czy polecenie kompilacji i wypychania, przejdź do [witryny Azure portal](https://portal.azure.com) i przejdź do rejestru kontenerów. 

10. W rejestrze kontenerów, wybierz **repozytoriów** następnie **samplemodule**. Upewnij się, że obie wersje obrazu zostały wypchnięte do rejestru.

   ![Wyświetl obie wersje obrazów w rejestrze kontenerów](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Jeśli wystąpią błędy podczas kompilowania i wypychanie obrazu modułu, często ma z konfiguracji platformy Docker na komputerze deweloperskim. Aby przejrzeć konfigurację, użyj następujące testy: 

* Zostało uruchomione `docker login` polecenia przy użyciu poświadczeń, które zostały skopiowane z rejestru kontenerów? Te poświadczenia są inne niż te, których używasz do logowania się na platformie Azure. 
* Czy używasz właściwego repozytorium kontenerów? Ma nazwę rejestru poprawny kontener i nazwy modułu? Otwórz **module.json** pliku w folderze SampleModule do sprawdzenia. Wartość repozytorium powinno wyglądać  **\<nazwa rejestru\>.azurecr.io/samplemodule**. 
* Jeśli używasz inną nazwę niż **SampleModule** dla modułu, jest wpisywanych spójne w całym rozwiązaniu?
* Komputer działa ten sam typ kontenerów, które tworzysz? Niniejszy samouczek jest urządzeń usługi IoT Edge w systemie Linux, więc należy zadeklarować programu Visual Studio Code **amd64** lub **arm32v7** w pasku bocznym, a pulpitu platformy Docker, powinny zostać uruchomione kontenery systemu Linux. Moduły języka C w programie Visual Studio Code obsługuje kontenery Windows. 

## <a name="deploy-modules-to-device"></a>Wdrażanie modułów na urządzeniu

Możesz zweryfikować, że obrazy kontenera utworzone są przechowywane w usługi container registry, nadszedł czas, aby wdrażać je na urządzeniu. Upewnij się, że urządzenia usługi IoT Edge jest uruchomiona. 

1. W Eksploratorze programu Visual Studio Code rozwiń sekcję usługi Azure IoT Hub Devices. 

2. Kliknij prawym przyciskiem myszy urządzenie usługi IoT Edge, który chcesz wdrożyć, a następnie wybierz **tworzenie wdrażania dla jednego urządzenia**. 

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/tutorial-develop-for-linux/create-deployment.png)

3. W Eksploratorze plików przejdź do **config** następnie wybierz folder **deployment.amd64.json** pliku. 

   Nie należy używać pliku deployment.template.json, który nie ma w nim poświadczeń rejestru kontenera lub wartości obrazu modułu. Jeśli zostaną objęci urządzenia ARM32 systemu Linux, manifest wdrożenia będzie miał nazwę deployment.arm32v7.json. 

4. Rozwiń szczegóły urządzenia usługi IoT Edge, a następnie rozwiń węzeł **modułów** lista dla danego urządzenia. 

5. Użyj przycisku odświeżania, aby zaktualizować widoku urządzenia, dopóki nie zobaczysz tempSensor i moduły SampleModule na urządzeniu z. 

   Może upłynąć kilka minut, zanim obu modułów rozpocząć. Środowisko uruchomieniowe usługi IoT Edge wymaga aby otrzymać nowy manifest wdrożenia ściągnąć obrazy modułu z kontener środowiska uruchomieniowego, a następnie uruchom każdego modułu. 

   ![Wyświetlanie modułów na urządzeniu usługi IoT Edge z](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Przeglądanie komunikatów z urządzenia

Kod SampleModule odbiera komunikaty za pośrednictwem jego danych wejściowych w kolejce i przekazuje je za pośrednictwem swojej kolejki danych wyjściowych. Manifest wdrażania zadeklarowany trasy przekazanego z tempSensor SampleModule wiadomości, a następnie SampleModule przesyłane komunikaty do usługi IoT Hub. Narzędzia usługi Azure IoT dla programu Visual Studio Code, pozwalają wyświetlić komunikaty przychodzące w usłudze IoT Hub z poszczególnych urządzeń. 

1. W Eksploratorze programu Visual Studio Code kliknij prawym przyciskiem myszy urządzenie usługi IoT Edge, który chcesz monitorować, a następnie wybierz **rozpocząć monitorowanie wbudowany zdarzeń punkt końcowy**. 

2. Obejrzyj w oknie danych wyjściowych programu Visual Studio Code, aby wyświetlić komunikaty przychodzące w Twoim Centrum IoT hub. 

   ![Wyświetl urządzenie przychodzących komunikatów w chmurze](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Wyświetl zmiany na urządzeniu

Jeśli chcesz zobaczyć, co dzieje się na twoim urządzeniu, sam, użyj poleceń w tej sekcji, aby sprawdzić środowisko uruchomieniowe usługi IoT Edge i moduły na urządzeniu z. 

Polecenia w tej sekcji dotyczą urządzenia usługi IoT Edge nie komputerze deweloperskim. Jeśli używasz maszyny wirtualnej do urządzenia usługi IoT Edge, połącz się do niej teraz. Na platformie Azure, przejdź do strony Przegląd maszyny wirtualnej, a następnie wybierz pozycję **Connect** na dostęp do połączenia protokołu secure shell. 

* Aby wyświetlić wszystkie moduły wdrożone na urządzeniu i sprawdzać ich stan:

   ```bash
   iotedge list
   ```

   Powinny zostać wyświetlone cztery moduły: dwa moduły środowiska uruchomieniowego usługi IoT Edge, tempSensor i SampleModule. Powinny zostać wyświetlone wszystkie cztery uruchomione.

* Przejrzyj dzienniki dla określonego modułu:

   ```bash
   iotedge logs <module name>
   ```

   Moduły usługi IoT Edge jest rozróżniana wielkość liter. 

   Dzienniki SamplModule i tempSensor powinny pokazywać wiadomości, które są ich przetwarzania. Moduł edgeAgent jest odpowiedzialna za uruchamianie innych modułów, dzięki czemu jej dzienników będzie miał informacji na temat wdrażania manifestu wdrażania. Jeśli dowolny moduł nie ma na liście lub nie jest uruchomiona, dzienniki edgeAgent prawdopodobnie ma błędy. Moduł edgeHub jest odpowiedzialny za komunikację między modułami i Centrum IoT Hub. Jeśli moduły są włączone i uruchomione, ale komunikaty nie są dotarciem do Centrum IoT hub, dzienniki edgeHub prawdopodobnie będziesz mieć błędy. 

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku Konfigurowanie programu Visual Studio Code na komputerze deweloperskim i wdrożyć swoje pierwsze moduł usługi IoT Edge z niego. Skoro już wiesz, podstawowych pojęć, wypróbuj Dodawanie funkcji do modułu, dzięki czemu można analizować, danych, przekazując przy jego użyciu. Wybierz preferowany język: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module.md)
> [C#](tutorial-csharp-module.md)
> [Java](tutorial-java-module.md)
> [Node.js](tutorial-node-module.md)
> [Python](tutorial-python-module.md)