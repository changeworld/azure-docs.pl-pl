---
title: Debugowanie C# modułów Azure IoT Edge | Dokumentacja firmy Microsoft
description: Użyj Visual Studio Code umożliwiające tworzenie, kompilowanie i debugowanie modułu C# Edge IoT Azure
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c237b1eb9874357afa922f809109cf8f2181561e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048190"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Visual Studio Code umożliwia tworzenie i debugowanie C# modułów Edge IoT Azure

Możesz wysłać logiki biznesowej do działania na krawędzi, wyłączając je w modułach Azure IoT Edge. Ten artykuł zawiera szczegółowe instrukcje dotyczące korzystania z programu Visual Studio (kod VS) jako narzędzie do projektowania głównego do opracowywania C# modułów.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że używasz komputera lub maszyny wirtualnej z systemem Windows lub Linux jako komputerze deweloperskim. Urządzenie brzegowe IoT może być inne urządzenie fizyczne lub urządzenia IoT krawędzi można symulować na komputerze deweloperskim.

> [!NOTE]
> W tym samouczku debugowania opisuje sposób dołączenia procesu w kontenerze modułu i debugowania kodu programu VS. Umożliwia debugowanie tylko funkcje C# w kontenerach linux amd64. Jeśli nie znasz możliwości debugowania programu Visual Studio Code, przeczytaj o [debugowanie](https://code.visualstudio.com/Docs/editor/debugging). 

Ponieważ w tym artykule wykorzystano Visual Studio Code jako narzędzie do projektowania główny, zainstaluj kod programu VS, a następnie dodaj niezbędne rozszerzenia:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie krawędzi IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozszerzenia języka C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Rozszerzenie docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Aby utworzyć moduł, należy .NET, która tworzy folder projektu Docker do tworzenia obrazu modułu i rejestru kontenera, aby pomieścić obraz modułu:
* [.NET core 2.1 SDK](https://www.microsoft.com/net/download).
* [Docker CE](https://docs.docker.com/install/) na komputerze deweloperskim. 
* [Rejestru kontenera platformy Azure](https://docs.microsoft.com/azure/container-registry/) lub [Centrum Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > Można użyć lokalnego rejestru Docker prototypu i testowania, zamiast rejestru chmury. 

Aby przetestować modułu na urządzeniu, konieczne jest aktywnym Centrum IoT z co najmniej jedno urządzenie brzegowe IoT. Jeśli chcesz korzystać z komputera jako urządzenia IoT, możesz to zrobić, wykonując kroki opisane w samouczków dotyczących [Windows](quickstart.md) lub [systemu Linux](quickstart-linux.md) 

## <a name="create-a-new-solution-template"></a>Utwórz nowy szablon rozwiązania

Poniższych krokach przedstawiono sposób utworzyć moduł krawędzi IoT oparte na .NET Core 2.0 przy użyciu programu Visual Studio Code i rozszerzenie Azure IoT krawędzi. Należy rozpocząć od tworzenia rozwiązania, a następnie generowania pierwszego modułu, w tym rozwiązaniu. Każde rozwiązanie może zawierać wiele modułów. 

1. W programie Visual Studio Code, wybierz **widoku** > **zintegrowane Terminal**.
3. Wybierz **widoku** > **polecenia palety**. 
4. Wpisz w palecie polecenia i uruchom polecenie **Azure IoT krawędzi: nowe rozwiązanie graniczne IoT**.

   ![Uruchamianie nowego rozwiązania IoT krawędzi](./media/how-to-develop-csharp-module/new-solution.png)

5. Przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie, a następnie kliknij przycisk **wybierz folder**. 
6. Podaj nazwę dla swojego rozwiązania. 
7. Wybierz **C# modułu** jako szablon dla pierwszego modułu w rozwiązaniu.
8. Podaj nazwę dla modułu. Wybierz nazwę, która jest unikatowa w rejestrze kontenera. 
9. Podaj repozytorium obrazów dla modułu. VS kodu autopopulates modułu nazwy, dlatego należy zastąpić **localhost:5000** z informacjami rejestru. Jeśli korzystasz z lokalnego rejestru Docker do testowania, localhost funkcjonuje prawidłowo. Jeśli używasz rejestru kontenera platformy Azure, należy użyć serwera logowania z ustawień rejestru systemu. Serwer logowania wygląda jak  **\<nazwa rejestru\>. azurecr.io**.

VS kod pobiera informacje, można podać, tworzy rozwiązanie IoT krawędzi, a następnie ładuje go w nowym oknie.

   ![Rozwiązania IoT krawędzi widoku](./media/how-to-develop-csharp-module/view-solution.png)

W ramach rozwiązania są trzy elementy: 
* A **.vscode** folder zawiera konfiguracji debugowania.
* A **modułów** folder zawiera podfoldery dla każdego modułu. Obecnie masz tylko jedną, ale można dodać więcej w palecie polecenia przy użyciu polecenia **Azure IoT krawędzi: Dodawanie modułu krawędzi IoT**. 
* A **.env** zawiera listę plików zmienne środowiskowe. Jeśli jesteś ACR jako rejestru, prawy teraz masz ACR nazwy użytkownika i hasła w nim. 
* A **deployment.template.json** nowego modułu oraz przykład zawiera listę plików **tempSensor** modułu, która symuluje danych, która służy do testowania. Aby uzyskać więcej informacji na temat sposobu pracy manifesty wdrożenia, zobacz [zrozumieć, jak moduły krawędzi IoT może być używany, skonfigurowane i ponownie](module-composition.md).

## <a name="build-and-deploy-your-module-for-debugging"></a>Tworzenie i wdrażanie modułu do debugowania

W każdym folderze modułu jest wiele plików Docker na różne typy kontenera. Można użyć dowolnego z tych plików, które kończy się rozszerzeniem **.debug** do tworzenia modułu do testowania. C# modułów tylko obsługuje obecnie debugowania w kontenerach linux amd64.

1. W kodzie VS, przejdź do `deployment.template.json` pliku. Zaktualizuj adres URL obrazu funkcja przez dodanie **.debug** na końcu.

   ![Dodaj .debug do nazwy obrazu](./media/how-to-develop-csharp-module/image-debug.png)

2. W kodzie VS palety polecenia, wpisz i uruchom polecenie **krawędzi: rozwiązań IoT Edge kompilacji**.
3. Wybierz `deployment.template.json` pliku rozwiązania z palety polecenia. 
4. W Eksploratorze urządzenia Centrum IoT Azure, kliknij prawym przyciskiem myszy identyfikator urządzenia IoT krawędzi, a następnie wybierz **tworzenie wdrożenia dla urządzenia IoT**. 
5. Otwórz **config** folderu rozwiązania, następnie wybierz `deployment.json` pliku. Kliknij przycisk **wybierz Manifest rozmieszczenia krawędzi**. 

Następnie można zauważyć, że pomyślnie utworzono wdrożenie z wdrożeniem, który zintegrowane Identyfikatora w kodzie VS terminala.

Można sprawdzić stan kontenera w Eksploratorze Docker kodu programu VS lub uruchom `docker ps` w terminalu.

## <a name="start-debugging-c-module-in-vs-code"></a>Rozpocznij debugowanie modułu C# w kodzie VS
Informacje o konfiguracji w śledzi debugowanie kodu VS `launch.json` plik znajdujący się w `.vscode` folder w obszarze roboczym. To `launch.json` plik został wygenerowany podczas tworzenia nowego rozwiązania IoT krawędzi. Aktualizuje zawsze dodać nowy moduł, który obsługuje debugowania. 

1. Przejdź do widoku debugowania kodzie VS i wybierz plik konfiguracji debugowania dla modułu. Nazwa opcji debugowania powinien być podobny do **Nazwa_modułu zdalne debugowanie (.NET Core)** ![konfiguracji wybierz opcję debugowania](./media/how-to-develop-csharp-module/debug-config.png)

2. Przejdź do adresu `program.cs`. Dodaj punkt przerwania w tym pliku.

3. Kliknij przycisk **Rozpocznij debugowanie** przycisk lub naciśnij przycisk **F5**i wybierz można dołączyć do procesu.

4. W widoku debugowania kodu VS widać zmiennych w lewym panelu. 

> [!NOTE]
> Poprzednim przykładzie pokazano, jak można debugować modułów krawędzi IoT Core .NET do kontenerów. Jest on oparty na wersji do debugowania z `Dockerfile.debug`, w tym VSDBG (debuger wiersza polecenia platformy .NET Core) w obrazie kontenera podczas jego tworzenia. Po zakończeniu debugowania modułów C#, zaleca się bezpośrednio użyć lub dostosować `Dockerfile` bez VSDBG dla modułów krawędzi IoT gotowe do produkcji.

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu modułu wbudowane, Dowiedz się jak [wdrażanie Azure IoT krawędzi modułów z programu Visual Studio Code](how-to-deploy-modules-vscode.md)

