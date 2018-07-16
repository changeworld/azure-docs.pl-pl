---
title: Praca z wieloma modułami usługi Azure IoT Edge w programie VS Code | Dokumentacja firmy Microsoft
description: Używanie rozszerzenia IoT dla programu Visual Studio Code umożliwiające tworzenie wiele modułów jednocześnie dla usługi Azure IoT Edge
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 31fe210b87a052438956d813db0d104e0f2cdb6e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041259"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Tworzenie rozwiązania usługi IoT Edge z wieloma modułami w programie Visual Studio Code

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

## <a name="create-your-iot-edge-solution"></a>Utwórz swoje rozwiązanie IoT Edge

1. W programie Visual Studio code, należy otworzyć zintegrowany terminal, wybierając **widoku** > **zintegrowany terminal**. 

1. W programie VS Code **paletę poleceń**, wprowadź i uruchom polecenie **usługi Azure IoT Edge: rozwiązanie nowej usługi IoT Edge**. Wybierz folder obszaru roboczego i podaj nazwę rozwiązania (wartość domyślna to EdgeSolution). Tworzenie modułu C# (o nazwie **PipeModule**) jako pierwszego modułu, w tym rozwiązaniu. Domyślny szablon modułu C# jest modułem potoku, który bezpośrednio przekazuje komunikaty z nadrzędnych do podrzędnych. Należy również określić repozytorium obrazów platformy Docker dla pierwszego modułu. Domyślne repozytorium obrazów jest oparte na lokalnym rejestrze platformy Docker (**localhost:5000/<first module name>**). Można go zmienić na usługę Azure Container Registry lub Docker Hub. 

2. W oknie programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Folder główny zawiera folder **modules** i **.vscode** oraz plik szablonu manifestu wdrożenia. Konfiguracje debugowania znajdują się w folderze .vscode. Cały kod modułu użytkownika znajduje się podfolderach folderu modułów. Plik deployment.template.json to szablon manifestu wdrożenia. Niektóre parametry w tym pliku są analizowane na podstawie pliku module.json, który istnieje w każdym folderze modułu.

3. Dodaj drugi moduł do tego projektu rozwiązania. Istnieje kilka sposobów, aby dodać nowy moduł do bieżącego rozwiązania. Należy wprowadzić, a następnie uruchom polecenie **usługi Azure IoT Edge: Dodaj moduł IoT Edge**. Wybierz plik szablonu wdrożenia do zaktualizowania. Kliknij prawym przyciskiem myszy folder modułów lub kliknij prawym przyciskiem myszy plik deployment.template.json lub wybierz **Dodaj moduł usługi IoT Edge**. Następnie będzie istnieć na liście rozwijanej, aby wybrać typ modułu. Wybierz **usługi Azure Functions — C#** modułu o nazwie **PipeFunction** i jego repozytorium obrazów platformy Docker. Domyślny szablon funkcji modułu C# jest modułu potoku, który bezpośrednio przekazuje komunikaty z nadrzędnych do podrzędnych.

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

1. W programie VS Code **paletę poleceń**, wprowadź i uruchom polecenie **usługi Azure IoT Edge: Tworzenie usługi IoT Edge rozwiązania**. Na podstawie pliku module.json znajdującego się w każdym folderze modułu polecenie rozpoczyna kompilowanie, konteneryzowanie i wypychanie obrazów platformy Docker poszczególnych modułów. Następnie polecenie przekazuje wymaganą wartość do pliku deployment.template.json i generuje plik deployment.json z informacjami z folderu konfiguracji. W zintegrowanym terminalu w programie VS Code wyświetlany jest postęp kompilacji. 

2. W narzędziu **Device Explorer** w usłudze Azure IoT Hub kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge, a następnie wybierz polecenie **Utwórz wdrożenie dla urządzenia usługi Edge**. Wybierz plik deployment.json w folderze konfiguracji. W zintegrowanym terminalu w programie VS Code wyświetlane jest pomyślnie utworzone wdrożenie wraz z jego identyfikatorem.

3. Jeśli masz symulowanie urządzenia usługi IoT Edge na komputerze deweloperskim, możesz obejrzeć zobaczyć, że wszystkie identyfikatory kontenerów obrazu modułu rozpoczynają się w ciągu kilku minut.

## <a name="view-the-generated-data"></a>Wyświetlanie wygenerowanych danych

1. Aby monitorować dane docierające do centrum IoT Hub, wybierz polecenie **View** (Widok) > **Command Palette** (Paleta poleceń). Następnie wybierz polecenie **IoT: rozpocznij monitorowania komunikatu D2C**. 
2. Aby zatrzymać monitorowanie danych, użyj polecenia **IoT: Stop monitoring D2C message** (IoT: Zatrzymaj monitorowanie komunikatu D2C) w obszarze **Command Palette** (Paleta poleceń). 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat innych scenariuszy dotyczących programowania przy użyciu usługi Azure IoT Edge w programie Visual Studio Code:

* Tworzenie modułów w programie VS Code z [C#](how-to-develop-csharp-module.md) lub [Node.js](how-to-develop-node-module.md).
* Funkcje platformy Azure w programie VS Code za pomocą tworzyć [C#](how-to-develop-csharp-function.md).

Tworzenie modułów na urządzeniach usługi IoT Edge [poznawanie i używanie usługi Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).