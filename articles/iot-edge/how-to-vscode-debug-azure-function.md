---
title: Debugowanie usługi Azure Functions krawędzi IoT Azure przy użyciu programu Visual Studio Code | Dokumentacja firmy Microsoft
description: Debugowanie C# usługę Azure Functions Azure IoT krawędzi w kodzie VS
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 3/20/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8c266a01375bf74fd4df9290255e84bc28e6089c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Użyj programu Visual Studio kodu do debugowania usługi Azure Functions krawędzi IoT Azure

Ten artykuł zawiera szczegółowe instrukcje dotyczące używania [Visual Studio Code](https://code.visualstudio.com/) jako Narzędzia główne programowanie debugowania funkcji Azure IoT krawędzi.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że używasz komputera lub maszyny wirtualnej z systemem Windows lub Linux jako komputerze deweloperskim. Urządzenia IoT krawędzi może być inne fizyczne urządzenie lub urządzenia IoT krawędzi można symulować na komputerze deweloperskim.

Przed wykonaniem wskazówki zawarte w tym artykule, wykonaj kroki [opracowywania rozwiązań IoT Edge z wieloma modułami w programie Visual Studio Code](tutorial-multiple-modules-in-vscode.md). Po wykonaniu tej powinny mieć gotowy następujące elementy:
- Lokalnego rejestru Docker uruchomiony na komputerze deweloperskim. Zalecane jest korzystanie lokalnego rejestru Docker prototypu i celów testowych. Można zaktualizować klucza rejestru kontenera `module.json` plik w folderze każdego modułu.
- Krawędź IoT rozwiązania obszar roboczy projektu z podfolderu moduł funkcji platformy Azure w nim.
- `run.csx` Pliku z kodu funkcji.
- Uruchomiony na komputerze deweloperskim środowiska uruchomieniowego krawędzi.

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>Tworzenie modułu funkcja krawędzi IoT cel debugowania
1. Aby rozpocząć debugowanie, należy użyć **Dockerfile.amd64.debug** Aby ponownie skompilować obraz docker i wykonaj ponowne wdrożenie rozwiązania krawędzi. W kodzie VS Eksploratora, przejdź do `deployment.template.json` pliku. Zaktualizuj adres URL obrazu funkcja przez dodanie `.debug` w końcu.

    ![Utwórz obraz do debugowania](./media/how-to-debug-csharp-function/build-debug-image.png)

2. Skompiluj ponownie rozwiązanie. W kodzie VS palety polecenia, wpisz i uruchom polecenie **krawędzi: rozwiązań IoT Edge kompilacji**.

3. W Eksploratorze urządzenia Centrum IoT Azure, kliknij prawym przyciskiem myszy identyfikator urządzenia IoT krawędzi, a następnie wybierz **tworzenie wdrożenia dla urządzenie brzegowe**. Wybierz `deployment.json` w obszarze `config` folderu. Następnie można zauważyć, że pomyślnie utworzono wdrożenie z wdrożeniem, który zintegrowane Identyfikatora w kodzie VS terminala.

> [!NOTE]
> Można sprawdzić stan kontenera w Eksploratorze Docker kodu programu VS lub uruchom `docker images` w terminalu.

## <a name="start-debugging-c-function-in-vs-code"></a>Rozpocznij debugowanie funkcji języka C# w kodzie VS
1. Informacje o konfiguracji w śledzi debugowanie kodu VS `launch.json` plik znajdujący się w `.vscode` folder w obszarze roboczym. To `launch.json` plik został wygenerowany podczas tworzenia nowego rozwiązania IoT krawędzi. I zostanie zaktualizowany po dodaniu każdego nowy moduł, który obsługuje debugowania. Przejdź do widoku debugowania, a następnie wybierz odpowiedni plik konfiguracji debugowania.
    ![Wybierz opcję debugowania konfiguracji](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Przejdź do adresu `run.csx`. Dodaj punkt przerwania w funkcji.

3. Kliknij przycisk Rozpocznij debugowanie lub naciśnij klawisz **F5**i wybierz można dołączyć do procesu.

4. W widoku debugowania kodu VS widać zmiennych w lewym panelu. 


> [!NOTE]
> Powyżej przykładzie przedstawiono sposób debugowania .net funkcja krawędzi IoT Core kontenerów. Jest on oparty na wersji do debugowania z `Dockerfile.amd64.debug`, w tym VSDBG (debuger wiersza polecenia platformy .NET Core) w obrazie kontenera podczas jego tworzenia. Firma Microsoft zaleca, możesz bezpośrednio użyć lub dostosować `Dockerfile` bez VSDBG dla funkcji krawędzi IoT gotowe do produkcji, po zakończeniu debugowania funkcji języka C#.

## <a name="next-steps"></a>Kolejne kroki


[Debugowanie modułu C# z krawędzią IoT Azure przy użyciu programu Visual Studio Code](how-to-vscode-debug-csharp-module.md)

