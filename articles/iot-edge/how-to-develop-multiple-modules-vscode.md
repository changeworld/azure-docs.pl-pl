---
title: Praca z wieloma modułami Azure IoT krawędzi w kodzie VS | Dokumentacja firmy Microsoft
description: Rozszerzenie IoT dla programu Visual Studio Code służy do opracowywania wiele modułów jednocześnie Edge IoT Azure
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 4e9aac5f19fa75613dee2aba3853a0243d7d966b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048264"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Tworzenie rozwiązania IoT krawędź z wielu modułów w programie Visual Studio Code

Za pomocą programu Visual Studio Code można tworzyć rozwiązania usługi Azure IoT Edge z wieloma modułami. W tym artykule przedstawiono sposób tworzenia, aktualizowania i wdrażania rozwiązania usługi IoT Edge, które przesyła potokiem dane czujnika na symulowanym urządzeniu usługi IoT Edge w programie VS Code. 

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne jest spełnienie następujących wymagań wstępnych:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
- Aktywne centrum IoT Hub z co najmniej jednym urządzeniem brzegowym usługi IoT Edge

Aby zarządzać obrazami i kontenerami, wymagana jest również integracja rozszerzenia [platformy Docker dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) z narzędziem Device Explorer w usłudze Azure IoT Hub.

## <a name="create-your-iot-edge-solution"></a>Tworzenie rozwiązania IoT krawędzi

1. W kodzie programu Visual Studio Otwórz terminal zintegrowanego, wybierając **widoku** > **zintegrowane terminal**. 

1. W kodzie VS **palety polecenia**, wprowadź i uruchom polecenie **krawędzi IoT Azure: rozwiązanie graniczne IoT nowe**. Wybierz folder obszaru roboczego i podaj nazwę rozwiązania (wartość domyślna to EdgeSolution). Utwórz moduł C# (o nazwie **PipeModule**) jako pierwszego modułu użytkownika w tym rozwiązaniu. Domyślny szablon modułu C# jest modułu potoku, bezpośrednio powoduje przekazanie w potoku komunikaty z nadrzędnego do poniżej. Należy również określić repozytorium obrazów platformy Docker dla pierwszego modułu. Domyślne repozytorium obrazów jest oparte na lokalnym rejestrze platformy Docker (**localhost:5000/<first module name>**). Można go zmienić na usługę Azure Container Registry lub Docker Hub. 

2. W oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Folder główny zawiera folder **modules** i **.vscode** oraz plik szablonu manifestu wdrożenia. Konfiguracje debugowania znajdują się w folderze .vscode. Cały kod modułu użytkownika znajduje się podfolderach folderu modułów. Plik deployment.template.json to szablon manifestu wdrożenia. Niektóre parametry w tym pliku są analizowane na podstawie pliku module.json, który istnieje w każdym folderze modułu.

3. Dodaj drugi moduł do tego projektu rozwiązania. Istnieje kilka sposobów, aby dodać nowy moduł do bieżącego rozwiązania. Wprowadź i uruchom polecenie **Azure IoT krawędzi: Dodaj krawędzi IoT modułu**. Wybierz plik szablonu wdrożenia do zaktualizowania. Lub kliknij prawym przyciskiem myszy folder modułów lub kliknij prawym przyciskiem myszy plik deployment.template.json i wybierz polecenie **Dodawanie modułu krawędzi IoT**. Następnie będzie można listy rozwijanej, aby wybrać typ modułu. Wybierz **usługi Azure Functions - C#** modułu o nazwie **PipeFunction** i jego Docker repozytorium obrazów. Domyślny szablon modułu funkcje C# jest modułu potoku, bezpośrednio powoduje przekazanie w potoku komunikaty z nadrzędnego do poniżej.

4. Otwórz plik deployment.template.json. Sprawdź, czy w pliku zadeklarowano trzy moduły i środowisko uruchomieniowe. Z modułu tempSensor zostanie wygenerowany komunikat. Komunikat ten jest bezpośrednio przesyłany potokiem za pośrednictwem modułów SampleModule i SampleFunction, a następnie wysyłany do centrum IoT Hub. 

5. Zaktualizuj trasy dla tych modułów za pomocą następującej treści:

   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/PipeModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/PipeModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/PipeFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/PipeFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Zapisz ten plik.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Tworzenie i wdrażanie rozwiązania usługi IoT Edge

1. W kodzie VS **palety polecenia**, wprowadź i uruchom polecenie **krawędzi IoT Azure: rozwiązanie graniczne IoT kompilacji**. Na podstawie pliku module.json znajdującego się w każdym folderze modułu polecenie rozpoczyna kompilowanie, konteneryzowanie i wypychanie obrazów platformy Docker poszczególnych modułów. Następnie polecenie przekazuje wymaganą wartość do pliku deployment.template.json i generuje plik deployment.json z informacjami z folderu konfiguracji. W zintegrowanym terminalu w programie VS Code wyświetlany jest postęp kompilacji. 

2. W narzędziu **Device Explorer** w usłudze Azure IoT Hub kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge, a następnie wybierz polecenie **Utwórz wdrożenie dla urządzenia usługi Edge**. Wybierz plik deployment.json w folderze konfiguracji. W zintegrowanym terminalu w programie VS Code wyświetlane jest pomyślnie utworzone wdrożenie wraz z jego identyfikatorem.

3. W przypadku symulacji urządzenia IoT na komputerze deweloperskim, możesz obserwować zobaczyć, czy uruchomić wszystkie kontenery obrazu modułu w ciągu kilku minut.

## <a name="view-the-generated-data"></a>Wyświetlanie wygenerowanych danych

1. Aby monitorować dane docierające do centrum IoT Hub, wybierz polecenie **View** (Widok) > **Command Palette** (Paleta poleceń). Następnie wybierz polecenie **IoT: rozpocznij monitorowania komunikatu D2C**. 
2. Aby zatrzymać monitorowanie danych, użyj polecenia **IoT: Stop monitoring D2C message** (IoT: Zatrzymaj monitorowanie komunikatu D2C) w obszarze **Command Palette** (Paleta poleceń). 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat innych scenariuszy dotyczących programowania przy użyciu usługi Azure IoT Edge w programie Visual Studio Code:

* [Tworzenie modułu C# w kodzie VS](how-to-develop-csharp-module.md)
* [Tworzenie funkcji języka C# w kodzie VS](how-to-develop-csharp-function.md)
