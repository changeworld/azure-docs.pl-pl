---
title: Debugowanie funkcji modułów Azure IoT Edge | Dokumentacja firmy Microsoft
description: Użyj programu Visual Studio kodu do debugowania usługi Azure Functions C# z krawędzią IoT Azure
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ee5d368bde0f176442a01ba266791f8a9c2cbc3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052831"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Użyj programu Visual Studio Code do opracowywania i debugowania usługi Azure Functions Edge IoT Azure

Ten artykuł zawiera szczegółowe instrukcje dotyczące używania [kodu programu Visual Studio (VS)](https://code.visualstudio.com/) debugowania funkcji Azure IoT krawędzi.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że używasz komputera lub maszyny wirtualnej z systemem Windows lub Linux jako komputerze deweloperskim. Urządzenia IoT krawędzi może być inne fizyczne urządzenie lub urządzenia IoT krawędzi można symulować na komputerze deweloperskim.

> [!NOTE]
> W tym samouczku debugowania opisuje sposób dołączenia procesu w kontenerze modułu i debugowania kodu programu VS. Umożliwia debugowanie tylko moduły C# w kontenerach linux amd64. Jeśli nie znasz możliwości debugowania programu Visual Studio Code, przeczytaj o [debugowanie](https://code.visualstudio.com/Docs/editor/debugging). 

W tym artykule używa Visual Studio Code jako narzędzie do projektowania głównego. Zainstaluj kod programu VS, a następnie dodaj niezbędne rozszerzenia: 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie krawędzi IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozszerzenia języka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Rozszerzenie docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Aby utworzyć moduł, należy .NET do tworzenia folderu projektu, Docker do tworzenia obrazu modułu i rejestru kontenera, aby pomieścić obraz modułu:
* [.NET core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) na komputerze deweloperskim. 
* [Rejestru kontenera platformy Azure](https://docs.microsoft.com/azure/container-registry/) lub [Centrum Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Można użyć lokalnego rejestru Docker prototypu i testowania, zamiast rejestru chmury. 

Aby przetestować modułu na urządzeniu, konieczne jest aktywnym Centrum IoT z co najmniej jedno urządzenie brzegowe IoT. Jeśli chcesz korzystać z komputera jako urządzenia IoT, możesz to zrobić, wykonując kroki opisane w samouczków dotyczących [Windows](quickstart.md) lub [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Utwórz nowy szablon rozwiązania

Poniższe kroki pokazują, jak tworzenie rozwiązań IoT Edge, które zawiera moduł funkcji jednego języka C#. Każde rozwiązanie może zawierać wiele modułów.

1. W programie Visual Studio Code, wybierz **widoku** > **zintegrowane Terminal**.
3. Wybierz **widoku** > **polecenia palety**.
4. Wpisz w palecie polecenia i uruchom polecenie **Azure IoT krawędzi: nowe rozwiązanie graniczne IoT**. 

   ![Uruchamianie nowego rozwiązania IoT krawędzi](./media/how-to-develop-csharp-module/new-solution.png)

5. Przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie, a następnie kliknij przycisk **wybierz folder**. 
6. Podaj nazwę dla swojego rozwiązania. 
7. Wybierz **usługi Azure Functions - C#** jako szablon dla pierwszego modułu w rozwiązaniu.
8. Podaj nazwę dla modułu. Wybierz nazwę, która jest unikatowa w rejestrze kontenera. 
9. Podaj repozytorium obrazów dla modułu. VS kodu autopopulates modułu nazwy, dlatego należy zastąpić **localhost:5000** z informacjami rejestru. Jeśli korzystasz z lokalnego rejestru Docker do testowania, localhost funkcjonuje prawidłowo. Jeśli używasz rejestru kontenera platformy Azure, należy użyć serwera logowania z ustawień rejestru systemu. Serwer logowania wygląda jak  **\<nazwa rejestru\>. azurecr.io**.

VS kod pobiera informacje, pod warunkiem, tworzy rozwiązań IoT Edge z projektem funkcja następnie ładuje go w nowym oknie.

W ramach rozwiązania są trzy elementy: 

* A **.vscode** folder zawierający konfiguracji debugowania.
* A **modułów** folder zawierający podfoldery dla każdego modułu. Obecnie masz tylko jedną, ale można dodać więcej za pośrednictwem palety polecenia przy użyciu polecenia **Azure IoT krawędzi: Dodawanie modułu krawędzi IoT**.
* A **.env** zawiera listę plików zmienne środowiskowe. Jeśli jesteś ACR jako rejestru, prawy teraz masz ACR nazwy użytkownika i hasła w nim. 
* A **deployment.template.json** nowego modułu oraz przykład zawiera listę plików **tempSensor** moduł, który symuluje danych, która służy do testowania. Aby uzyskać więcej informacji na temat sposobu pracy manifesty wdrożenia, zobacz [zrozumieć, jak moduły krawędzi IoT może być używany, skonfigurowane i ponownie](module-composition.md).

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Tworzenie modułu funkcja krawędzi IoT cel debugowania
1. Aby rozpocząć debugowanie, należy użyć **Dockerfile.amd64.debug** Aby ponownie skompilować obraz docker i wykonaj ponowne wdrożenie rozwiązania krawędzi. W kodzie VS Eksploratora, przejdź do `deployment.template.json` pliku. Zaktualizuj adres URL obrazu funkcja przez dodanie `.debug` w końcu.

    ![Utwórz obraz do debugowania](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Skompiluj ponownie rozwiązanie. W kodzie VS palety polecenia, wpisz i uruchom polecenie **krawędzi IoT Azure: rozwiązanie graniczne IoT kompilacji**.
3. W Eksploratorze urządzenia Centrum IoT Azure, kliknij prawym przyciskiem myszy identyfikator urządzenia IoT krawędzi, a następnie wybierz **tworzenie wdrożenia dla urządzenie brzegowe**. Wybierz `deployment.json` plików w obszarze `config` folderu. Następnie można zauważyć, że pomyślnie utworzono wdrożenie z wdrożeniem, który zintegrowane Identyfikatora w kodzie VS terminala.

Można sprawdzić stan kontenera w Eksploratorze Docker kodu programu VS lub uruchamiając `docker images` w terminalu.

## <a name="start-debugging-c-function-in-vs-code"></a>Rozpocznij debugowanie funkcji języka C# w kodzie VS
1. Informacje o konfiguracji w śledzi debugowanie kodu VS `launch.json` plik znajdujący się w `.vscode` folder w obszarze roboczym. To `launch.json` plik został wygenerowany podczas tworzenia nowego rozwiązania IoT krawędzi. Aktualizuje zawsze dodać nowy moduł, który obsługuje debugowania. Przejdź do widoku debugowania, a następnie wybierz odpowiedni plik konfiguracji debugowania. Nazwa opcji debugowania powinien być podobny do **Nazwa_modułu zdalne debugowanie (.NET Core)** ![konfiguracji wybierz opcję debugowania](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Przejdź do adresu `run.csx`. Dodaj punkt przerwania w funkcji.
3. Kliknij przycisk **Rozpocznij debugowanie** przycisk lub naciśnij przycisk **F5**i wybierz można dołączyć do procesu.
4. W widoku debugowania kodu VS widać zmiennych w lewym panelu. 


> [!NOTE]
> W powyższym przykładzie pokazano sposób debugowania .net funkcja krawędzi IoT Core kontenerów. Jest on oparty na wersji do debugowania z `Dockerfile.amd64.debug`, w tym VSDBG (debuger wiersza polecenia platformy .NET Core) w obrazie kontenera podczas jego tworzenia. Firma Microsoft zaleca, możesz bezpośrednio użyć lub dostosować `Dockerfile` bez VSDBG dla funkcji krawędzi IoT gotowe do produkcji, po zakończeniu debugowania funkcji języka C#.

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu modułu wbudowane, Dowiedz się jak [wdrażanie Azure IoT krawędzi modułów z programu Visual Studio Code](how-to-deploy-modules-vscode.md)
