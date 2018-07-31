---
title: Debugowanie modułów języka C# dla usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Użyj Visual Studio Code umożliwiające tworzenie, kompilowanie i debugowanie modułu C# dla usługi Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a895f21bc061763b1d5d45b2bedb44fc932190dc
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345226"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Używanie programu Visual Studio Code do tworzenia i debugowania modułów języka C# dla usługi Azure IoT Edge

Logikę biznesową można przekształcić w moduły, dla usługi Azure IoT Edge. W tym artykule pokazano, jak używać programu Visual Studio Code (VS Code) jako głównego narzędzia do tworzenia i debugowania języka C# modułów.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że używasz komputera lub maszyny wirtualnej z systemem Windows lub Linux jako komputerze deweloperskim. Urządzenia usługi IoT Edge może być inny urządzenia fizycznego. Lub można symulować urządzenia usługi IoT Edge na komputerze deweloperskim.

> [!NOTE]
> W tym artykule debugowania pokazuje, jak dołączyć procesu w kontenerze modułu i debugować ją z programem VS Code. Można debugować tylko moduły języka C# w kontenerach amd64 systemu Linux. Jeśli nie znasz możliwości debugowania programu Visual Studio Code, przeczytaj temat [debugowanie](https://code.visualstudio.com/Docs/editor/debugging). 

Ponieważ w tym artykule używa programu Visual Studio Code, jako narzędzia programistyczne główny, zainstaluj program VS Code. Następnie dodaj rozszerzeniami niezbędnymi:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozszerzenie języka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Rozszerzenia platformy docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Aby utworzyć moduł, potrzebne są .NET do tworzenia folderu projektu, Docker, aby skompilować obraz modułu i rejestru kontenerów do przechowywania obrazu modułu:
* [Zestaw .NET Core 2.1 SDK](https://www.microsoft.com/net/download).
* [Platformę docker Community Edition](https://docs.docker.com/install/) na komputerze deweloperskim. 
* [Usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub [usługi Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Można użyć lokalnego rejestru platformy Docker prototypów i testowania zamiast rejestru chmury. 

Aby przetestować modułu na urządzeniu, należy aktywnym Centrum IoT przy użyciu co najmniej jedno urządzenie usługi IoT Edge. Aby użyć komputera jako urządzenia usługi IoT Edge, postępuj zgodnie z instrukcjami w przewodniku Szybki Start dla [Windows](quickstart.md) lub [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Utwórz nowy szablon rozwiązania

Wykonaj następujące czynności, aby utworzyć moduł usługi IoT Edge, oparte na programie .NET Core 2.0 przy użyciu programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge. Najpierw należy utworzyć rozwiązanie, a następnie wygenerować pierwszego modułu w ramach tego rozwiązania. Każdy roztwór może zawierać więcej niż jeden moduł. 

1. W programie Visual Studio Code wybierz **widoku** > **zintegrowany Terminal**.
3. Wybierz **widoku** > **polecenia palety**. 
4. W palecie poleceń, należy wprowadzić, a następnie uruchom polecenie **usługi Azure IoT Edge: nowe rozwiązanie graniczne IoT**.

   ![Uruchamianie nowego rozwiązania usługi IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie. Wybierz **Wybieranie folderu**. 
6. Wprowadź nazwę dla swojego rozwiązania. 
7. Wybierz **modułu C#** jako szablon dla pierwszego modułu w rozwiązaniu.
8. Wprowadź nazwę dla modułu. Wybierz nazwę, która jest unikatowa w obrębie usługi container registry. 
9. Podaj nazwę modułu repozytorium obrazów. VS Code autopopulates modułu nazwa z **localhost:5000**. Zastąp go własną informacje rejestru. Jeśli używasz lokalnego rejestru platformy Docker do testowania, następnie **localhost** jest w dobrym stanie. Jeśli korzystasz z usługi Azure Container Registry, Użyj serwera logowania z ustawień w rejestrze. Serwer logowania wygląda  **\<nazwa rejestru\>. azurecr.io**.

Program VS Code przyjmuje informacje podane lub tworzy rozwiązanie IoT Edge, a następnie ładuje go w nowym oknie.

   ![Wyświetl rozwiązania usługi IoT Edge](./media/how-to-develop-csharp-module/view-solution.png)

Istnieją cztery elementy w ramach rozwiązania: 
* A **.vscode** folder zawiera konfiguracji debugowania.
* A **modułów** folder zawiera podfoldery dla każdego modułu. W tym momencie masz tylko jedną licencję. Jednak możesz dodać więcej licencji w palecie poleceń za pomocą polecenia **usługi Azure IoT Edge: Dodaj moduł usługi IoT Edge**. 
* **ENV** plik zawiera listę zmiennych środowiskowych. Jeśli rejestru Azure Container Registry, będziesz mieć usługi Azure Container Registry użytkownika i hasło w nim. 

   >[!NOTE]
   >Plik środowiska jest tworzony tylko, jeśli podasz repozytorium obrazów w module. Jeżeli użytkownik zaakceptował domyślnie localhost, Testuj i Debuguj lokalnie, nie należy do deklarowania zmiennych środowiskowych. 

* A **deployment.template.json** nowego modułu wraz z przykładu zawiera listę plików **tempSensor** modułu, która symuluje sieć danych można używać do testowania. Aby uzyskać więcej informacji na temat sposobu wdrażania manifestów pracy, zobacz [Dowiedz się, jak wdrażać moduły oraz ustalenia tras za pomocą manifesty wdrożenia](module-composition.md). 

## <a name="devlop-your-module"></a>Devlop modułu

Domyślny kod funkcji platformy Azure w rozwiązaniu znajduje się w **modułów** > **\<swoją nazwę modułu\>**   >   **Plik program.cs**. W module, plik deployment.template.json są konfigurowane tak, aby skompilować rozwiązanie, Wypchnij go do rejestru kontenerów i wdrożyć ją na urządzeniu do rozpoczęcia testowania bez dotykania żadnego kodu. Moduł został opracowany pod kątem wystarczy pobrać dane wejściowe ze źródła (w tym przypadku moduł tempSensor, która symuluje sieć danych) i przekazać go do usługi IoT Hub. 

Gdy wszystko będzie gotowe dostosować szablon języka C# przy użyciu własnego kodu, należy użyć [zestawami SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) do tworzenia modułów ten adres klucz dla rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodności. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Tworzenie i wdrażanie modułu do debugowania

W każdym folderze modułu istnieje kilka plików Docker dla kontenera różnych typów. Użyć dowolnej z tych plików, które kończą się rozszerzeniem **.debug** do tworzenia modułu do testowania. Obecnie modułów języka C# obsługuje debugowanie tylko w kontenerach amd64 systemu Linux.

1. W programie VS Code przejdź do `deployment.template.json` pliku. Zaktualizuj adres URL obrazu funkcję, dodając **.debug** na końcu.

   ![Dodaj *** .debug do nazwy obrazu](./media/how-to-develop-csharp-module/image-debug.png)

2. W palecie poleceń programu VS Code, należy wprowadzić, a następnie uruchom polecenie **Edge: Tworzenie usługi IoT Edge rozwiązania**.
3. Wybierz `deployment.template.json` pliku rozwiązania jest wyświetlany z palety poleceń. 
4. W usłudze Azure IoT Hub Device Explorer kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge. Następnie wybierz pozycję **tworzenie wdrożenia dla urządzenia usługi IoT Edge**. 
5. Otwórz swoje rozwiązanie **config** folderu. Następnie wybierz pozycję `deployment.json` pliku. Wybierz **wybierz Manifest wdrożenia krawędzi**. 

Zobaczysz wdrożenie z Identyfikatorem wdrożenia w terminalu programu VS Code, zintegrowane pomyślnie utworzona.

Sprawdź stan kontenera w Eksploratorze programu VS Code Docker lub uruchamiając `docker ps` polecenia w terminalu.

## <a name="start-debugging-c-module-in-vs-code"></a>Rozpocznij debugowanie modułu C# w programie VS Code
Informacje o konfiguracji w utrzymuje funkcji debugowania kodu programu VS `launch.json` plik znajdujący się w `.vscode` folder w obszarze roboczym. To `launch.json` plik został wygenerowany podczas tworzenia nowego rozwiązania usługi IoT Edge. Aktualizuje po dodaniu każdego nowego modułu, który obsługuje debugowanie. 

1. Przejdź do widoku debugowania programu VS Code. Wybierz plik konfiguracji debugowania dla modułu. Nazwa opcji debugowania powinny być podobne do **ModuleName zdalnego debugowania (.NET Core)**

   ![Wybierz opcję debugowania konfiguracji](./media/how-to-develop-csharp-module/debug-config.png).

2. Przejdź do adresu `program.cs`. Dodaj punkt przerwania w tym pliku.

3. Wybierz **Rozpocznij debugowanie** lub wybierz **F5**. Wybierz proces do dołączenia.

4. W widoku debugowania programu VS Code zobaczysz zmiennych w panelu po lewej stronie. 

> [!NOTE]
> W tym przykładzie pokazano, jak można debugować modułów programu .NET Core IoT Edge w kontenerach. Jest on oparty na wersji debugowania `Dockerfile.debug`, w tym VSDBG debuger wiersza polecenia platformy .NET Core w obrazie kontenera podczas jego tworzenia. Po debugowaniu modułów języka C#, firma Microsoft zaleca, aby bezpośrednio użyć lub dostosować `Dockerfile` bez VSDBG dla modułów usługi IoT Edge gotowe do produkcji.

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu modułu, Dowiedz się jak [wdrożyć moduły usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md).

Tworzenie modułów na urządzeniach usługi IoT Edge [poznawanie i używanie usługi Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).
