---
title: Zarządzanie wieloma modułami usługi Azure IoT Edge w programie VS Code | Microsoft Docs
description: Użyj programu Visual Studio Code, aby tworzyć rozwiązania usługi Azure IoT Edge korzystające z wielu modułów.
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a624994f93e7a3cbb04db2a0ec0b2b823a12ee7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763038"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code-preview"></a>Tworzenie rozwiązania usługi IoT Edge z wieloma modułami w programie Visual Studio Code (wersja zapoznawcza)

Za pomocą programu Visual Studio Code można tworzyć rozwiązania usługi Azure IoT Edge z wieloma modułami. W tym artykule przedstawiono sposób tworzenia, aktualizowania i wdrażania rozwiązania usługi IoT Edge, które przesyła potokiem dane czujnika na symulowanym urządzeniu usługi IoT Edge w programie VS Code. 

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne jest spełnienie następujących wymagań wstępnych:

- [Visual Studio Code](https://code.visualstudio.com/)
- [Rozszerzenie usługi Azure IoT Edge dla programu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [Rozszerzenie C# for Visual Studio Code (obsługiwane przez technologię OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [Zestaw .NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)
- Szablon AzureIoTEdgeModule (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- Aktywne centrum IoT Hub z co najmniej jednym urządzeniem brzegowym usługi IoT Edge

Aby zarządzać obrazami i kontenerami, wymagana jest również integracja rozszerzenia [platformy Docker dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) z narzędziem Device Explorer w usłudze Azure IoT Hub.

## <a name="prepare-your-first-iot-edge-solution"></a>Przygotowanie pierwszego rozwiązania usługi IoT Edge

1. W obszarze **Command Palette** (Paleta poleceń) programu VS Code wprowadź i uruchom polecenie **Edge: New IoT Edge solution** (Edge: Nowe rozwiązanie usługi IoT Edge). Wybierz folder obszaru roboczego i podaj nazwę rozwiązania (wartość domyślna to EdgeSolution). Utwórz moduł w języku C# (o nazwie **SampleModule**) jako pierwszy moduł użytkownika w tym rozwiązaniu. Należy również określić repozytorium obrazów platformy Docker dla pierwszego modułu. Domyślne repozytorium obrazów jest oparte na lokalnym rejestrze platformy Docker (**localhost:5000/<first module name>**). Można go zmienić na usługę Azure Container Registry lub Docker Hub.

   > [!NOTE]
   > Jeśli korzystasz z lokalnego rejestru platformy Docker, sprawdź, czy rejestr jest uruchomiony. W oknie konsoli wprowadź następujące polecenie:
   > 
   > `docker run -d -p 5000:5000 --restart=always --name registry registry:2`

2. W oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Folder główny zawiera folder **modules** i **.vscode** oraz plik szablonu manifestu wdrożenia. Konfiguracje debugowania znajdują się w folderze .vscode. Cały kod modułu użytkownika znajduje się podfolderach folderu modułów. Plik deployment.template.json to szablon manifestu wdrożenia. Niektóre parametry w tym pliku są analizowane na podstawie pliku module.json, który istnieje w każdym folderze modułu.

3. Dodaj drugi moduł do tego projektu rozwiązania. Wprowadź i uruchom polecenie **Edge: Add IoT Edge module** (Edge: Dodaj moduł usługi IoT Edge). Wybierz plik szablonu wdrożenia do zaktualizowania. Wybierz moduł **Azure Function - C#** o nazwie **SampleFunction** i jego repozytorium obrazów platformy Docker.

4. Otwórz plik deployment.template.json. Sprawdź, czy w pliku zadeklarowano trzy moduły i środowisko uruchomieniowe. Z modułu tempSensor zostanie wygenerowany komunikat. Komunikat ten jest bezpośrednio przesyłany potokiem za pośrednictwem modułów SampleModule i SampleFunction, a następnie wysyłany do centrum IoT Hub. 

5. Zaktualizuj trasy dla tych modułów za pomocą następującej treści:

   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. Zapisz ten plik.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Tworzenie i wdrażanie rozwiązania usługi IoT Edge

1. W obszarze **Command Palette** (Paleta poleceń) programu VS Code wprowadź i uruchom polecenie **Edge: Build IoT Edge solution** (Edge: Skompiluj rozwiązanie usługi IoT Edge). Na podstawie pliku module.json znajdującego się w każdym folderze modułu polecenie rozpoczyna kompilowanie, konteneryzowanie i wypychanie obrazów platformy Docker poszczególnych modułów. Następnie polecenie przekazuje wymaganą wartość do pliku deployment.template.json i generuje plik deployment.json z informacjami z folderu konfiguracji. W zintegrowanym terminalu w programie VS Code wyświetlany jest postęp kompilacji.

2. W narzędziu **Device Explorer** w usłudze Azure IoT Hub kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge, a następnie wybierz polecenie **Utwórz wdrożenie dla urządzenia usługi Edge**. Wybierz plik deployment.json w folderze konfiguracji. W zintegrowanym terminalu w programie VS Code wyświetlane jest pomyślnie utworzone wdrożenie wraz z jego identyfikatorem.

3. Jeśli urządzenie usługi IoT Edge jest symulowane na maszynie deweloperskiej, sprawdź, czy wszystkie kontenery obrazów modułów zostaną uruchomione w ciągu kilku minut.

## <a name="view-the-generated-data"></a>Wyświetlanie wygenerowanych danych

1. Aby monitorować dane docierające do centrum IoT Hub, wybierz polecenie **View** (Widok) > **Command Palette** (Paleta poleceń). Następnie wybierz polecenie **IoT: rozpocznij monitorowania komunikatu D2C**. 
2. Aby zatrzymać monitorowanie danych, użyj polecenia **IoT: Stop monitoring D2C message** (IoT: Zatrzymaj monitorowanie komunikatu D2C) w obszarze **Command Palette** (Paleta poleceń). 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat innych scenariuszy dotyczących programowania przy użyciu usługi Azure IoT Edge w programie Visual Studio Code:

* [Debugowanie modułu w języku C# w programie VS Code](how-to-vscode-debug-csharp-module.md)
* [Debugowanie funkcji w języku C# w programie VS Code](how-to-vscode-debug-azure-function.md)
