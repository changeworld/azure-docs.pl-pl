---
title: Zarządzanie wiele modułów Azure IoT krawędzi w kodzie VS | Dokumentacja firmy Microsoft
description: Użyj programu Visual Studio Code umożliwiające tworzenie rozwiązań IoT Edge, które użycia wielu modułów.
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4d9caa7aa95c99fa30e8ec76c5b6362615725622
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Tworzenie rozwiązania IoT krawędź z wielu modułów w programie Visual Studio Code - preview
Visual Studio Code służy do opracowywania rozwiązania IoT krawędź z wielu modułów. W tym artykule przedstawiono tworzenie, aktualizowanie i wdrażanie rozwiązań IoT Edge potoków danych czujnika na symulowane urządzenie brzegowe IoT w programie Visual Studio Code. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać wszystkie kroki opisane w tym artykule, w obowiązują następujące wymagania wstępne:

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Rozszerzenie krawędzi IoT Azure dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [C# dla rozszerzenia Visual Studio Code (obsługiwane przez OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- Szablon AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Aktywnym Centrum IoT z co najmniej jedno urządzenie brzegowe IoT


* [Docker dla kodu VS](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) z Eksploratora integracji zarządzania obrazami i kontenerów.


## <a name="prepare-your-first-iot-edge-solution"></a>Przygotowanie rozwiązania IoT krawędź pierwszego
1. W kodzie VS palety polecenia, wpisz i uruchom polecenie **krawędzi: rozwiązanie graniczne IoT nowe**. Następnie wybierz folder roboczy, podaj nazwę rozwiązania (nazwa domyślna to **EdgeSolution**) i Utwórz moduł C# (**SampleModule**) jako pierwszego modułu użytkownika w tym rozwiązaniu. Należy również określić repozytorium obrazów Docker dla pierwszego modułu. Repozytorium obrazów domyślna jest oparta na lokalnym rejestrze Docker (`localhost:5000/<first module name>`). Można również zmienić do rejestru kontenera platformy Azure lub Centrum Docker.

   > [!NOTE]
   > Jeśli korzystasz z lokalnego rejestru Docker, upewnij się, rejestru jest uruchomiona, wpisując polecenie `docker run -d -p 5000:5000 --restart=always --name registry registry:2` w oknie konsoli.

2. Okno kodzie VS ładuje rozwiązania IoT krawędzi obszaru roboczego. Folder główny zawiera `modules` folderu, `.vscode` folderu i pliku manifestu szablonu wdrożenia. Można zobaczyć konfiguracje w debugowania `.vscode` folderu. Wszystkie kody modułu użytkownika będzie podfolderów w folderze `modules`. `deployment.template.json` Jest manifestu szablonu wdrożenia. Niektóre parametry w tym pliku zostaną wydzielone z `module.json`, który istnieje w folderze co moduł.

3. Dodaj drugi modułu do projektu tego rozwiązania. Wpisz w tym momencie, a następnie uruchom **krawędzi: Dodaj krawędzi IoT modułu** i wybierz plik szablonu wdrażania aktualizacji. Następnie wybierz **funkcji platformy Azure - C#** o nazwie **SampleFunction** i jego Docker repozytorium obrazów.

4. Otwórz `deployment.template.json` i sprawdzić deklaruje trzy modułów oprócz środowiska uruchomieniowego. Wiadomość zostanie wygenerowane z `tempSensor` modułu i będzie można bezpośrednio przetwarzana potokowo za pośrednictwem `SampleModule` i `SampleFunction`, następnie wysyłane do Centrum IoT. 
5. Zaktualizuj trasy dla tych modułów o następującej zawartości:
   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Zapisz ten plik.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Tworzenie i wdrażanie rozwiązania IoT krawędzi
1. W kodzie VS palety polecenia, wpisz i uruchom polecenie **krawędzi: rozwiązań IoT Edge kompilacji**. Na podstawie `module.json` plik w folderze każdego modułu, to polecenie start do tworzenia, containerize i push każdego obrazu docker modułu. Następnie przekazuje wymagana wartość do `deployment.template.json` i generuje `deployment.json` pliku z informacjami z `config` folderu. Można wyświetlić postęp kompilacji w kodzie VS terminal zintegrowanego.

2. W Eksploratorze urządzenia Centrum IoT Azure, kliknij prawym przyciskiem myszy identyfikator urządzenia IoT krawędzi, a następnie wybierz **tworzenie wdrożenia dla urządzenie brzegowe**. Wybierz `deployment.json` w obszarze `config` folderu. Następnie można zauważyć, że pomyślnie utworzono wdrożenie z wdrożeniem, który zintegrowane Identyfikatora w kodzie VS terminala.

3. Urządzenia IoT są symulację na komputerze deweloperskim, pojawi się że wszystkich kontenerów obrazu modułu zostanie uruchomiony w ciągu kilku minut.

## <a name="view-generated-data"></a>Wyświetlanie wygenerowanych danych

1. Aby monitorować dane otrzymywane przez centrum IoT Hub, wybierz pozycję **Widok** > **Paleta poleceń...** , a następnie wyszukaj ciąg **IoT: rozpocznij monitorowania komunikatu D2C**. 
2. Aby zatrzymać monitorowanie danych, użyj polecenia **IoT: zatrzymaj monitorowanie komunikatu D2C** w palecie poleceń. 

## <a name="next-steps"></a>Kolejne kroki

Więcej informacji na temat innych scenariuszy programowania brzegowe IoT Azure w programie Visual Studio Code:

* [Debugowanie modułu C# w kodzie VS](how-to-vscode-debug-csharp-module.md)
* [Debugowanie funkcji języka C# w kodzie VS](how-to-vscode-debug-azure-function.md)