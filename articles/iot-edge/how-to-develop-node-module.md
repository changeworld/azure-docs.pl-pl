---
title: Debugowanie modułów Node.js dla usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Używanie programu Visual Studio Code do tworzenia i debugowania modułów Node.js dla usługi Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8032fd2a0150597c55178648511c80233e63a911
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054730"
---
# <a name="develop-and-debug-nodejs-modules-with-azure-iot-edge-for-visual-studio-code"></a>Programowanie i debugowanie modułów Node.js z usługą Azure IoT Edge dla programu Visual Studio Code

Możesz wysłać logikę biznesową do działania na urządzeniach brzegowych, włączając je do modułów dla usługi Azure IoT Edge. Ten artykuł zawiera szczegółowe instrukcje dotyczące korzystania z programu Visual Studio Code (VS Code) jako narzędzie do tworzenia głównego do tworzenia modułów języka C#.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że używasz komputera lub maszyny wirtualnej z systemem Windows lub Linux jako komputerze deweloperskim. Urządzenia usługi IoT Edge mogą być inne urządzenie fizyczne, lub można symulować urządzenia usługi IoT Edge na komputerze deweloperskim.

> [!NOTE]
> W tym samouczku debugowania opisuje sposób dołączenia procesu w kontenerze modułu i debugować ją z programem VS Code. Można debugować modułów Node.js w amd64 systemu linux, windows i arm32 kontenerów. Jeśli nie znasz możliwości debugowania programu Visual Studio Code, przeczytaj temat [debugowanie](https://code.visualstudio.com/Docs/editor/debugging). 

Ponieważ w tym artykule używa programu Visual Studio Code, jako narzędzia programistyczne głównego, należy zainstalować program VS Code, a następnie dodaj rozszerzeniami niezbędnymi:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Rozszerzenie usługi Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Rozszerzenia platformy docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

Aby utworzyć moduł, potrzebne są Node.js, w tym tworzenie folderu projektu, Docker, aby skompilować obraz modułu i rejestru kontenerów do przechowywania obrazu modułu programu npm:
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Usługa Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub [usługi Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   >[!TIP]
   >Można użyć lokalnego rejestru platformy Docker prototypów i testowania, zamiast rejestru chmury. 

Aby przetestować modułu na urządzeniu, należy aktywnym Centrum IoT przy użyciu co najmniej jedno urządzenie usługi IoT Edge. Jeśli chcesz korzystać z komputera jako urządzenia usługi IoT Edge, możesz to zrobić, wykonując kroki opisane w samouczków dotyczących [Windows](quickstart.md) lub [Linux](quickstart-linux.md). 

## <a name="create-a-new-solution-template"></a>Utwórz nowy szablon rozwiązania

Poniższe kroki przedstawiające utworzyć moduł usługi IoT Edge, w oparciu o zestaw .NET Core 2.0 przy użyciu programu Visual Studio Code i rozszerzenia usługi Azure IoT Edge. Możesz rozpocząć tworzenie rozwiązania, a następnie generowania pierwszego modułu w ramach tego rozwiązania. Każdy roztwór może zawierać wiele modułów. 

1. W programie Visual Studio Code wybierz **widoku** > **zintegrowany Terminal**.
2. W zintegrowanym terminalu wprowadź następujące polecenie, aby zainstalować (lub zaktualizować) najnowszą wersję szablonu modułu usługi Azure IoT Edge dla środowiska Node.js:

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```
3. W programie Visual Studio Code wybierz pozycję **Widok** > **Paleta poleceń**. 
4. W palecie poleceń typu, a następnie uruchom polecenie **usługi Azure IoT Edge: nowe rozwiązanie graniczne IoT**.

   ![Uruchamianie nowego rozwiązania usługi IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

5. Przejdź do folderu, w którym chcesz utworzyć nowe rozwiązanie, a następnie kliknij przycisk **Wybieranie folderu**. 
6. Podaj nazwę dla swojego rozwiązania. 
7. Wybierz **moduł Node.js** jako szablon dla pierwszego modułu w rozwiązaniu.
8. Podaj nazwę dla modułu. Wybierz nazwę, która jest unikatowa w obrębie usługi container registry. 
9. Podaj repozytorium obrazów w module. Program VS Code autopopulates moduł nazwy, dlatego należy po prostu zastąpić **localhost:5000** podając własne informacje do rejestru. Jeśli używasz lokalnego rejestru platformy Docker do testowania localhost funkcjonuje prawidłowo. Jeśli korzystasz z usługi Azure Container Registry, Użyj serwera logowania z ustawień w rejestrze. Serwer logowania wygląda  **\<nazwa rejestru\>. azurecr.io**.

Program VS Code przyjmuje informacje należy podać, tworzy rozwiązanie IoT Edge, a następnie ładuje go w nowym oknie.

W ramach rozwiązania znajdują się trzy elementy: 
* A **.vscode** folder zawiera konfiguracji debugowania.
* A **modułów** folder zawiera podfoldery dla każdego modułu. Teraz masz tylko jedną licencję, ale można dodać więcej w palecie poleceń za pomocą polecenia **usługi Azure IoT Edge: Dodaj moduł usługi IoT Edge**. 
* A **ENV** plik zawiera listę zmiennych środowiskowych. Jeśli jesteś ACR jako rejestru, prawo teraz mieć ACR nazwy użytkownika i hasła w nim. 

   >[!NOTE]
   >Plik środowiska jest tworzony tylko, jeśli podasz repozytorium obrazów w module. Jeżeli użytkownik zaakceptował domyślnie localhost, Testuj i Debuguj lokalnie, nie należy do deklarowania zmiennych środowiskowych. 

* A **deployment.template.json** nowego modułu wraz z przykładu zawiera listę plików **tempSensor** modułu, która symuluje sieć danych, które można używać do testowania. Aby uzyskać więcej informacji na temat sposobu wdrażania manifestów pracy, zobacz [zrozumieć, jak moduły usługi IoT Edge mogą być używane, skonfigurowania i ponownie](module-composition.md).

## <a name="devlop-your-module"></a>Devlop modułu

Domyślny kod funkcji platformy Azure w rozwiązaniu znajduje się w **modułów** > **\<swoją nazwę modułu\>**   >   **App.js**. W module, plik deployment.template.json są konfigurowane tak, aby skompilować rozwiązanie, Wypchnij go do rejestru kontenerów i wdrożyć ją na urządzeniu do rozpoczęcia testowania bez dotykania żadnego kodu. Moduł został opracowany pod kątem wystarczy pobrać dane wejściowe ze źródła (w tym przypadku moduł tempSensor, która symuluje sieć danych) i przekazać go do usługi IoT Hub. 

Gdy wszystko będzie gotowe dostosować szablon środowiska Node.js przy użyciu własnego kodu, należy użyć [zestawami SDK Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md) do tworzenia modułów ten adres klucz dla rozwiązań IoT, takich jak zabezpieczenia, zarządzanie urządzeniami i niezawodności. 

## <a name="build-and-deploy-your-module-for-debugging"></a>Tworzenie i wdrażanie modułu do debugowania

W każdym folderze modułu jest wiele plików Docker dla kontenera różnych typów. Możesz użyć dowolnego z tych plików, które kończą się rozszerzeniem **.debug** do tworzenia modułu do testowania. Obecnie modułów języka C# obsługują tylko, debugowanie w kontenerach systemu linux amd64.

1. W programie VS Code przejdź do `deployment.template.json` pliku. Zastąp Node.js modułu CreateOptions, można żądań w **deployment.template.json** z poniżej zawartości, a następnie zapisz ten plik: 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

2. W palecie poleceń programu VS Code, typ, a następnie uruchom polecenie **usługi Azure IoT Edge: Tworzenie usługi IoT Edge rozwiązania**.
3. Wybierz `deployment.template.json` pliku rozwiązania jest wyświetlany z palety poleceń. 
4. W Eksploratorze usługi Azure IoT Hub Devices, kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge, a następnie wybierz **tworzenie wdrożenia dla urządzenia usługi IoT Edge**. 
5. Otwórz **config** folderu rozwiązania, następnie wybierz pozycję `deployment.json` pliku. Kliknij pozycję **Wybierz manifest wdrożenia usługi Edge**. 

Następnie zobaczysz, że wdrożenia została pomyślnie utworzona z wdrożeniem zintegrowanego identyfikator w programie VS Code terminala.

Możesz sprawdzić stan usługi kontenera, w Eksploratorze programu VS Code Docker lub uruchom `docker ps` polecenia w terminalu.

## <a name="start-debugging-nodejs-module-in-vs-code"></a>Rozpocznij debugowanie modułów Node.Js w programie VS Code

Informacje o konfiguracji w utrzymuje funkcji debugowania kodu programu VS `launch.json` plik znajdujący się w `.vscode` folder w obszarze roboczym. To `launch.json` plik został wygenerowany podczas tworzenia nowego rozwiązania usługi IoT Edge. Aktualizuje po dodaniu każdego nowego modułu, który obsługuje debugowanie. 

1. Przejdź do widoku debugowania programu VS Code i wybierz plik konfiguracji debugowania dla modułu.

2. Przejdź do adresu `app.js`. Dodaj punkt przerwania w tym pliku.

3. Kliknij przycisk **Rozpocznij debugowanie** przycisk lub naciśnij klawisz **F5**i wybierz proces do dołączenia.

4. W widoku debugowania programu VS Code widać zmienne w panelu po lewej stronie. 

Poprzedni przykład pokazuje, jak debugowanie moduły usługi IoT Edge dla środowiska Node.js w kontenerach. Ujawnionych portów on dodany do Twojego modułu kontenera CreateOptions, można żądań. Po zakończeniu debugowania moduły Node.js, zalecane jest usunięcie tych ujawnionych portów dla modułów usługi IoT Edge gotowe do produkcji.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, gdy Twoje Moduł skompilowany, jak [modułów wdrożenia usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md)

Tworzenie modułów na urządzeniach usługi IoT Edge [poznawanie i używanie usługi Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).
