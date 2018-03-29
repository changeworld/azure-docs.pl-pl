---
title: Praca z wieloma modułami IoT krawędzi w programie Visual Studio Code | Dokumentacja firmy Microsoft
description: Wdrażanie usługi Azure Machine Learning jako moduł do urządzenia
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0ea2dc723c674e7119b6ef38771a73ff4c11e98d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Tworzenie rozwiązania IoT krawędź z wielu modułów w programie Visual Studio Code - preview
Visual Studio Code służy do opracowywania rozwiązania IoT krawędź z wielu modułów. Ten samouczek przeprowadzi Cię przez proces tworzenia, aktualizowania i wdrażanie rozwiązania IoT krawędzi, która po prostu przekazać dane czujników na symulowane urządzenie brzegowe IoT w programie Visual Studio Code. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Visual Studio Code umożliwiają utworzenie rozwiązania IoT krawędzi
> * Dodaj nowy moduł do pracy przy użyciu kodu VS rozwiązania IoT krawędzi. 
> * Wdrażanie rozwiązania IoT krawędzi (wiele modułów) na urządzeniu IoT krawędzi
> * Widok wygenerowany danych

## <a name="prerequisites"></a>Wymagania wstępne
* Zakończenie poniżej samouczki
  * [Wdrażanie modułu C#](tutorial-csharp-module.md)
  * [Wdrażanie funkcji języka C#](tutorial-deploy-function.md)
  * [Wdrażanie modułu Python](tutorial-python-module.md)
* [Docker dla kodu VS](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) z Eksploratora integracji zarządzania obrazami i kontenerów.


## <a name="prepare-your-first-iot-edge-solution"></a>Przygotowanie rozwiązania IoT krawędź pierwszego
1. W kodzie VS palety polecenia, wpisz i uruchom polecenie **krawędzi: rozwiązanie graniczne IoT nowe**. Następnie wybierz folder roboczy, podaj nazwę rozwiązania (nazwa domyślna to **EdgeSolution**) i Utwórz moduł C# (**SampleModule**) jako pierwszego modułu użytkownika w tym rozwiązaniu. Należy również określić repozytorium obrazów Docker dla pierwszego modułu. Repozytorium obrazów domyślna jest oparta na lokalnym rejestrze Docker (`localhost:5000/<first module name>`). Można również zmienić do rejestru kontenera platformy Azure lub Centrum Docker.

> [!NOTE]
> Jeśli korzystasz z lokalnego rejestru Docker, upewnij się, rejestru jest uruchomiona, wpisując polecenie `docker run -d -p 5000:5000 --restart=always --name registry registry:2` w oknie konsoli.

2. Okno kodzie VS załaduje rozwiązania IoT krawędzi obszaru roboczego. Brak `modules` folderu `.vscode` folder i wdrożenie manifestu szablonu pliku w folderze głównym. Można zobaczyć konfiguracje w debugowania `.vscode` folderu. Wszystkie kody modułu użytkownika będzie podfolderów w folderze `modules`. `deployment.template.json` Jest manifestu szablonu wdrożenia. Niektóre parametry w tym pliku zostaną wydzielone z `module.json`, który istnieje w folderze co moduł.

3. Dodaj drugi modułu do projektu tego rozwiązania. Wpisz w tym momencie, a następnie uruchom **krawędzi: Dodaj krawędzi IoT modułu** i wybierz plik szablonu wdrażania aktualizacji. Następnie wybierz **funkcji platformy Azure - C#** o nazwie **SampleFunction** i jego Docker repozytorium obrazów do dodania.

4. Rozwiązania IoT krawędzi pierwszy z dwóch podstawowych modułów jest teraz gotowy. Domyślny C# moduł działa jako moduł komunikat potoku podczas Funtion C# działa jako funkcja komunikat potoku. W `deployment.template.json`, pojawi się to rozwiązanie zawiera trzy moduły. Wiadomość zostanie wygenerowane z `tempSensor` modułu i będzie można bezpośrednio przetwarzana potokowo za pośrednictwem `SampleModule` i `SampleFunction`, następnie wysyłane do Centrum IoT. Aktualizowanie tras dla tych modułów z poniżej zawartości. 
   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
        },
   ```

5. Zapisz ten plik.

## <a name="build-and-deploy-your-iot-edge-solution"></a>Tworzenie i wdrażanie rozwiązania IoT krawędzi
1. W kodzie VS palety polecenia, wpisz i uruchom polecenie **krawędzi: rozwiązań IoT Edge kompilacji**. Na podstawie `module.json` plik w folderze każdego modułu, to polecenie sprawdzi i zacząć kompilacji, containerize i Wypchnij każdego obrazu docker modułu. Następnie będzie analizować wymagana wartość do `deployment.template.json`, generowanie `deployment.json` z wartością rzeczywistą w obszarze `config` folderu. Można wyświetlić postęp kompilacji w kodzie VS terminal zintegrowanego.

2. W Eksploratorze urządzenia Centrum IoT Azure, kliknij prawym przyciskiem myszy identyfikator urządzenia IoT krawędzi, a następnie wybierz **tworzenie wdrożenia dla urządzenie brzegowe**. Wybierz `deployment.json` w obszarze `config` folderu. Następnie można zauważyć, że pomyślnie utworzono wdrożenie z wdrożeniem, który zintegrowane Identyfikatora w kodzie VS terminala.

3. Jeśli jesteś [symulacji urządzenia IoT](tutorial-simulate-device-linux.md) na komputerze deweloperskim. Zobaczysz, że wszystkie kontenery obrazu modułu zostanie uruchomiony za kilka minut.

## <a name="view-generated-data"></a>Widok wygenerowany danych

1. Aby monitorować danych otrzymywanych przez Centrum IoT, wybierz **widoku** > **palety polecenia...**  i wyszukaj **IoT: rozpocząć monitorowanie komunikat D2C**. 
2. Aby zatrzymać monitorowanie danych, należy użyć **IoT: zatrzymać monitorowanie komunikat D2C** w palecie polecenia. 

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku rozwiązań IoT Edge utworzony przy użyciu języka C# modułu, później dodać moduł funkcji, zaktualizować trasy dla rozwiązania, wbudowane i wdrożony na urządzeniu IoT krawędzi symulowane. Można kontynuować do jednej z następujących samouczków, aby dowiedzieć się więcej o innych scenariuszy podczas opracowywania Azure IoT krawędź w kodzie VS.

> [!div class="nextstepaction"]
> [Debugowanie modułu C# w kodzie VS](how-to-vscode-debug-csharp-module.md)
> [debugowania funkcji języka C# w kodzie VS](how-to-vscode-debug-azure-function.md)