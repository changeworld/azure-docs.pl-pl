---
title: Debugowanie modułów funkcji platformy Azure dla usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Debugowanie języka C# usługi Azure functions z usługą Azure IoT Edge przy użyciu programu Visual Studio Code
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 343264f90ecf067786db9c0096625b87b2dbd319
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004412"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Używanie programu Visual Studio Code do tworzenia i debugowania usługi Azure functions dla usługi Azure IoT Edge

W tym artykule dowiesz się, jak używać [programu Visual Studio Code (VS Code)](https://code.visualstudio.com/) do debugowania funkcji platformy Azure w usłudze Azure IoT Edge.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że używasz komputera lub maszyny wirtualnej z systemem Windows lub Linux jako komputerze deweloperskim. Urządzenia usługi IoT Edge może być inny urządzenia fizycznego. Lub można symulować urządzenia usługi IoT Edge na komputerze deweloperskim.

> [!NOTE]
> W tym artykule debugowania pokazuje, jak dołączyć procesu w kontenerze modułu i debugować ją z programem VS Code. Można debugować tylko funkcji języka C# w kontenerach amd64 systemu Linux. Jeśli nie znasz możliwości debugowania programu Visual Studio Code, przeczytaj temat [debugowanie](https://code.visualstudio.com/Docs/editor/debugging). 

W tym artykule używa programu Visual Studio Code, jako narzędzia programistyczne głównego. Instalowanie programu VS Code. Następnie dodaj rozszerzeniami niezbędnymi: 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozszerzenie języka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Rozszerzenia platformy docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Aby utworzyć moduł, potrzebne są .NET do tworzenia folderu projektu, Docker, aby skompilować obraz modułu i rejestru kontenerów do przechowywania obrazu modułu:

* [.NET core 2.1 SDK](https://www.microsoft.com/net/download)
* [Platformę docker Community Edition](https://docs.docker.com/install/) na komputerze deweloperskim 
* [Usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub [usługi Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * Można użyć lokalnego rejestru platformy Docker prototypów i testowania zamiast rejestru chmury. 

Aby przetestować modułu na urządzeniu, należy aktywnym Centrum IoT przy użyciu co najmniej jedno urządzenie usługi IoT Edge. Aby użyć komputera jako urządzenia usługi IoT Edge, postępuj zgodnie z instrukcjami w przewodniku Szybki Start dla [Windows](quickstart.md) lub [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Utwórz nowy szablon rozwiązania

Wykonaj następujące czynności, aby utworzyć rozwiązanie IoT Edge, które zawiera moduł funkcji jednego języka C#. Każde z tych rozwiązań może mieć kilka modułów.

1. W programie Visual Studio Code wybierz **widoku** > **zintegrowany Terminal**.
3. Wybierz **widoku** > **polecenia palety**.
4. W palecie poleceń, należy wprowadzić, a następnie uruchom polecenie **usługi Azure IoT Edge: nowe rozwiązanie graniczne IoT**. 

   ![Uruchamianie nowego rozwiązania usługi IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie. Wybierz **Wybieranie folderu**. 
6. Wprowadź nazwę dla swojego rozwiązania. 
7. Wybierz **usługi Azure Functions — C#** jako szablon dla pierwszego modułu w rozwiązaniu.
8. Wprowadź nazwę dla modułu. Wybierz nazwę, która jest unikatowa w obrębie usługi container registry. 
9. Podaj repozytorium obrazów w module. VS Code autopopulates modułu nazwa z **localhost:5000**. Zastąp go własną informacje rejestru. Jeśli używasz lokalnego rejestru platformy Docker do testowania, następnie **localhost** jest w dobrym stanie. Jeśli korzystasz z usługi Azure Container Registry, Użyj serwera logowania z ustawień w rejestrze. Serwer logowania wygląda  **\<nazwa rejestru\>. azurecr.io**. Zastąp tylko część localhost ciągu, nie usuwaj nazwy modułu.

   ![Udostępnianie repozytorium obrazów platformy Docker](./media/how-to-develop-csharp-function/repository.png)

Program VS Code przyjmuje informacje podane, tworzy rozwiązanie IoT Edge przy użyciu projektu usługi Azure Functions i ładuje go w nowym oknie.

Istnieją cztery elementy w ramach rozwiązania: 

* A **.vscode** folder zawiera konfiguracji debugowania.
* A **modułów** folder zawiera podfoldery dla każdego modułu. W tym momencie masz tylko jedną licencję. Jednak możesz dodać więcej licencji za pośrednictwem paletę poleceń za pomocą polecenia **usługi Azure IoT Edge: Dodaj moduł usługi IoT Edge**.
* **ENV** plik zawiera listę zmiennych środowiskowych. Jeśli rejestru Azure Container Registry, będziesz mieć usługi Azure Container Registry użytkownika i hasło w nim. 

   >[!NOTE]
   >Plik środowiska jest tworzony tylko, jeśli podasz repozytorium obrazów w module. Jeżeli użytkownik zaakceptował domyślnie localhost, Testuj i Debuguj lokalnie, nie należy do deklarowania zmiennych środowiskowych. 

* A **deployment.template.json** nowego modułu wraz z przykładu zawiera listę plików **tempSensor** modułu, która symuluje sieć danych można używać do testowania. Aby uzyskać więcej informacji na temat sposobu wdrażania manifestów pracy, zobacz [Dowiedz się, jak wdrażać moduły oraz ustalenia tras za pomocą manifesty wdrożenia](module-composition.md).

## <a name="develop-your-module"></a>Tworzenie modułu

Domyślny kod funkcji platformy Azure w rozwiązaniu znajduje się w **modułów** > [Nazwa modułu] > **modulename.cs**. W module, plik deployment.template.json są konfigurowane tak, aby skompilować rozwiązanie, Wypchnij go do rejestru kontenerów i wdrożyć ją na urządzeniu do rozpoczęcia testowania bez dotykania żadnego kodu. Moduł został opracowany pod kątem wystarczy pobrać dane wejściowe ze źródła (w tym przypadku moduł tempSensor, która symuluje sieć danych) i przekazać go do usługi IoT Hub. 

Gdy wszystko będzie gotowe dostosować szablon funkcji platformy Azure przy użyciu własnego kodu, należy użyć [zestawami SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) do tworzenia modułów ten adres klucz dla rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodności. 

## <a name="build-your-module-for-debugging"></a>Tworzenie modułu do debugowania
1. Aby rozpocząć debugowanie, użyj **Dockerfile.amd64.debug** Aby ponownie skompilować obraz platformy docker i ponownie wdrożyć swoje rozwiązanie krawędzi. W Eksploratorze programu VS Code przejdź do `deployment.template.json` pliku. Zaktualizuj adres URL obrazu funkcję, dodając `.debug` na końcu.

    ![Zbuduj obraz debugowania](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Ponownie skompiluj rozwiązanie. W palecie poleceń programu VS Code, należy wprowadzić, a następnie uruchom polecenie **usługi Azure IoT Edge: Tworzenie usługi IoT Edge rozwiązania**.
3. W Eksploratorze usługi Azure IoT Hub Devices, kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge, a następnie wybierz **tworzenie wdrożenia dla urządzenia Edge**. Wybierz `deployment.json` w pliku `config` folderu. Zobaczysz wdrożenie z Identyfikatorem wdrożenia w terminalu programu VS Code, zintegrowane pomyślnie utworzona.

Sprawdź stan kontenera w Eksploratorze programu VS Code Docker lub uruchamiając `docker ps` polecenia w terminalu.

## <a name="start-debugging-c-functions-in-vs-code"></a>Rozpocznij debugowanie funkcji języka C# w programie VS Code
1. Informacje o konfiguracji w utrzymuje funkcji debugowania kodu programu VS `launch.json` plik znajdujący się w `.vscode` folder w obszarze roboczym. To `launch.json` plik został wygenerowany podczas tworzenia nowego rozwiązania usługi IoT Edge. Aktualizuje po dodaniu każdego nowego modułu, który obsługuje debugowanie. Przejdź do widoku debugowania. Wybierz odpowiedni plik konfiguracji debugowania. Nazwa opcji debugowania powinny być podobne do **ModuleName zdalnego debugowania (.NET Core)**.

   ![Wybierz opcję debugowania konfiguracji](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Przejdź do adresu `modulename.cs`. Dodaj punkt przerwania w funkcji.
3. Wybierz **Rozpocznij debugowanie** lub wybierz **F5**. Wybierz proces do dołączenia.
4. W widoku debugowania programu VS Code zobaczysz zmiennych w panelu po lewej stronie. 


> [!NOTE]
> Ten przykład pokazuje, jak można debugować.Net Core funkcje usługi IoT Edge w kontenerach. Jest on oparty na wersji debugowania `Dockerfile.amd64.debug`, w tym VSDBG debuger wiersza polecenia platformy .NET Core w obrazie kontenera podczas jego tworzenia. Firma Microsoft zaleca bezpośrednio użyć lub dostosować `Dockerfile` bez VSDBG dla gotowych do produkcji usługi IoT Edge działa po debugowaniu funkcji języka C#.

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu modułu, Dowiedz się jak [modułów wdrożenia usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md).

Tworzenie modułów na urządzeniach usługi IoT Edge [poznawanie i używanie usługi Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).
