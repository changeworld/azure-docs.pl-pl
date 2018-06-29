---
title: Debugowanie modułów Node.js Azure IoT Edge | Dokumentacja firmy Microsoft
description: Visual Studio Code umożliwia tworzenie i debugowanie modułów Node.js Edge IoT Azure
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6b217690b88f303268f5abe66abb7868711d3125
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045096"
---
# <a name="develop-and-debug-nodejs-modules-with-azure-iot-edge-for-visual-studio-code"></a>Tworzenie i debugowanie modułów Node.js z krawędzią IoT Azure dla programu Visual Studio Code

Możesz wysłać logiki biznesowej do działania na krawędzi, wyłączając je w modułach Azure IoT Edge. Ten artykuł zawiera szczegółowe instrukcje dotyczące korzystania z programu Visual Studio (kod VS) jako narzędzie do projektowania głównego do opracowywania C# modułów.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że używasz komputera lub maszyny wirtualnej z systemem Windows lub Linux jako komputerze deweloperskim. Urządzenie brzegowe IoT może być inne urządzenie fizyczne lub urządzenia IoT krawędzi można symulować na komputerze deweloperskim.

> [!NOTE]
> W tym samouczku debugowania opisuje sposób dołączenia procesu w kontenerze modułu i debugowania kodu programu VS. Można debugować modułów Node.js w linux amd64, windows i arm32 kontenerów. Jeśli nie znasz możliwości debugowania programu Visual Studio Code, przeczytaj o [debugowanie](https://code.visualstudio.com/Docs/editor/debugging). 

Ponieważ w tym artykule wykorzystano Visual Studio Code jako narzędzie do projektowania główny, zainstaluj kod programu VS, a następnie dodaj niezbędne rozszerzenia:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie krawędzi IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozszerzenie docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Aby utworzyć moduł, należy Node.js, w tym npm, aby utworzyć folder projektu Docker do tworzenia obrazu modułu i rejestru kontenera, aby pomieścić obraz modułu:
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Rejestru kontenera platformy Azure](https://docs.microsoft.com/azure/container-registry/) lub [Centrum Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   >[!TIP]
   >Można użyć lokalnego rejestru Docker prototypu i testowania, zamiast rejestru chmury. 

Aby przetestować modułu na urządzeniu, konieczne jest aktywnym Centrum IoT z co najmniej jedno urządzenie brzegowe IoT. Jeśli chcesz korzystać z komputera jako urządzenia IoT, możesz to zrobić, wykonując kroki opisane w samouczków dotyczących [Windows](quickstart.md) lub [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Utwórz nowy szablon rozwiązania

Poniższych krokach przedstawiono sposób utworzyć moduł krawędzi IoT oparte na .NET Core 2.0 przy użyciu programu Visual Studio Code i rozszerzenie Azure IoT krawędzi. Należy rozpocząć od tworzenia rozwiązania, a następnie generowania pierwszego modułu, w tym rozwiązaniu. Każde rozwiązanie może zawierać wiele modułów. 

1. W programie Visual Studio Code, wybierz **widoku** > **zintegrowane Terminal**.
2. W terminalu zintegrowanego wprowadź następujące polecenie, aby zainstalować (lub aktualizację) najnowszej wersji szablonu modułu Azure IoT krawędzi dla środowiska Node.js:

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```
3. W programie Visual Studio Code wybierz pozycję **Widok** > **Paleta poleceń**. 
4. Wpisz w palecie polecenia i uruchom polecenie **Azure IoT krawędzi: nowe rozwiązanie graniczne IoT**.

   ![Uruchamianie nowego rozwiązania IoT krawędzi](./media/how-to-develop-csharp-module/new-solution.png)

5. Przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie, a następnie kliknij przycisk **wybierz folder**. 
6. Podaj nazwę dla swojego rozwiązania. 
7. Wybierz **modułu Node.js** jako szablon dla pierwszego modułu w rozwiązaniu.
8. Podaj nazwę dla modułu. Wybierz nazwę, która jest unikatowa w rejestrze kontenera. 
9. Podaj repozytorium obrazów dla modułu. VS kodu autopopulates modułu nazwy, dlatego należy zastąpić **localhost:5000** z informacjami rejestru. Jeśli korzystasz z lokalnego rejestru Docker do testowania, localhost funkcjonuje prawidłowo. Jeśli używasz rejestru kontenera platformy Azure, należy użyć serwera logowania z ustawień rejestru systemu. Serwer logowania wygląda jak  **\<nazwa rejestru\>. azurecr.io**.

VS kod pobiera informacje, można podać, tworzy rozwiązanie IoT krawędzi, a następnie ładuje go w nowym oknie.

W ramach rozwiązania są trzy elementy: 
* A **.vscode** folder zawiera konfiguracji debugowania.
* A **modułów** folder zawiera podfoldery dla każdego modułu. Obecnie masz tylko jedną, ale można dodać więcej w palecie polecenia przy użyciu polecenia **Azure IoT krawędzi: Dodawanie modułu krawędzi IoT**. 
* A **.env** zawiera listę plików zmienne środowiskowe. Jeśli jesteś ACR jako rejestru, prawy teraz masz ACR nazwy użytkownika i hasła w nim. 
* A **deployment.template.json** nowego modułu oraz przykład zawiera listę plików **tempSensor** modułu, która symuluje danych, która służy do testowania. Aby uzyskać więcej informacji na temat sposobu pracy manifesty wdrożenia, zobacz [zrozumieć, jak moduły krawędzi IoT może być używany, skonfigurowane i ponownie](module-composition.md).

## <a name="build-and-deploy-your-module-for-debugging"></a>Tworzenie i wdrażanie modułu do debugowania

W każdym folderze modułu jest wiele plików Docker na różne typy kontenera. Można użyć dowolnego z tych plików, które kończy się rozszerzeniem **.debug** do tworzenia modułu do testowania. C# modułów tylko obsługuje obecnie debugowania w kontenerach linux amd64.

1. W kodzie VS, przejdź do `deployment.template.json` pliku. Zastąp Node.js modułu CreateOptions, można żądań w **deployment.template.json** z poniżej zawartości, a następnie zapisz ten plik: 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

2. W kodzie VS palety polecenia, wpisz i uruchom polecenie **krawędzi IoT Azure: rozwiązanie graniczne IoT kompilacji**.
3. Wybierz `deployment.template.json` pliku rozwiązania z palety polecenia. 
4. W Eksploratorze urządzenia Centrum IoT Azure, kliknij prawym przyciskiem myszy identyfikator urządzenia IoT krawędzi, a następnie wybierz **tworzenie wdrożenia dla urządzenia IoT**. 
5. Otwórz **config** folderu rozwiązania, następnie wybierz `deployment.json` pliku. Kliknij przycisk **wybierz Manifest rozmieszczenia krawędzi**. 

Następnie można zauważyć, że pomyślnie utworzono wdrożenie z wdrożeniem, który zintegrowane Identyfikatora w kodzie VS terminala.

Można sprawdzić stan kontenera w Eksploratorze Docker kodu programu VS lub uruchom `docker ps` w terminalu.

## <a name="start-debugging-nodejs-module-in-vs-code"></a>Rozpocznij debugowanie modułu Node.Js w kodzie VS

Informacje o konfiguracji w śledzi debugowanie kodu VS `launch.json` plik znajdujący się w `.vscode` folder w obszarze roboczym. To `launch.json` plik został wygenerowany podczas tworzenia nowego rozwiązania IoT krawędzi. Aktualizuje zawsze dodać nowy moduł, który obsługuje debugowania. 

1. Przejdź do widoku debugowania kodzie VS i wybierz plik konfiguracji debugowania dla modułu.

2. Przejdź do adresu `app.js`. Dodaj punkt przerwania w tym pliku.

3. Kliknij przycisk **Rozpocznij debugowanie** przycisk lub naciśnij przycisk **F5**i wybierz można dołączyć do procesu.

4. W widoku debugowania kodu VS widać zmiennych w lewym panelu. 

Poprzednim przykładzie pokazano, jak można debugować modułów Node.js IoT krawędzi kontenerów. Porty narażonych ona dodana w Twojej modułu kontenera CreateOptions, można żądań. Po zakończeniu debugowania własnych modułów Node.js, firma Microsoft zaleca się, że należy usunąć te uwidocznione porty dla modułów krawędzi IoT gotowe do produkcji.

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu modułu wbudowane, Dowiedz się jak [wdrażanie Azure IoT krawędzi modułów z programu Visual Studio Code](how-to-deploy-modules-vscode.md)

