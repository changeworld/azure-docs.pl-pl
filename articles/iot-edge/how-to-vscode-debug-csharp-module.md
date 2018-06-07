---
title: Debugowanie C# modułów Azure IoT krawędzi | Dokumentacja firmy Microsoft
description: Debugowanie modułu C# z krawędzią IoT Azure w programie Visual Studio Code przy użyciu programu Visual Studio Code.
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5d521a4e5b22222f0b0a5505f14684598d7d5b0a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631398"
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Debugowanie modułu C# z krawędzią IoT Azure przy użyciu programu Visual Studio Code
Ten artykuł zawiera szczegółowe instrukcje dotyczące używania [kodu programu Visual Studio (VS)](https://code.visualstudio.com/) jako Narzędzia główne programowanie debugowania moduły Azure IoT krawędzi.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że używasz komputera lub maszyny wirtualnej z systemem Windows lub Linux jako komputerze deweloperskim. Urządzenie brzegowe IoT może być inne urządzenie fizyczne lub urządzenia IoT krawędzi można symulować na komputerze deweloperskim.

> [!NOTE]
> Umożliwia debugowanie tylko moduł C# w kontenerach linux amd64.

Przed wykonaniem wskazówki zawarte w tym artykule, wykonaj kroki [opracowywania rozwiązań IoT Edge z wieloma modułami w programie Visual Studio Code](tutorial-multiple-modules-in-vscode.md). Po wykonaniu tej powinny mieć gotowy następujące elementy:
- Lokalnego rejestru Docker uruchomiony na komputerze deweloperskim. Zalecane jest korzystanie lokalnego rejestru Docker prototypu i celów testowych. Można zaktualizować klucza rejestru kontenera `module.json` plik w folderze każdego modułu.
- Krawędź IoT rozwiązania obszar roboczy projektu z podfolderu modułu C# w nim.
- `Program.cs` Pliku z najnowszą kodu modułu.
- Uruchomiony na komputerze deweloperskim środowiska uruchomieniowego krawędzi.

## <a name="build-your-iot-edge-c-module-for-debugging"></a>Tworzenie modułu IoT krawędzi C# do debugowania
1. Aby rozpocząć debugowanie, należy użyć **Dockerfile.amd64.debug** Aby ponownie skompilować obraz docker i wykonaj ponowne wdrożenie rozwiązania krawędzi. W kodzie VS Eksploratora, przejdź do `deployment.template.json` pliku. Zaktualizuj adres URL obrazu funkcja przez dodanie `.debug` w końcu.

2. Skompiluj ponownie rozwiązanie. W kodzie VS palety polecenia, wpisz i uruchom polecenie **krawędzi: rozwiązań IoT Edge kompilacji**.

3. W Eksploratorze urządzenia Centrum IoT Azure, kliknij prawym przyciskiem myszy identyfikator urządzenia IoT krawędzi, a następnie wybierz **tworzenie wdrożenia dla urządzenie brzegowe**. Wybierz `deployment.json` w pliku `config` folderu. Następnie można zauważyć, że pomyślnie utworzono wdrożenie z wdrożeniem, który zintegrowane Identyfikatora w kodzie VS terminala.

Można sprawdzić stan kontenera w Eksploratorze Docker kodu programu VS lub uruchom `docker images` w terminalu.

## <a name="start-debugging-c-module-in-vs-code"></a>Rozpocznij debugowanie modułu C# w kodzie VS
1. Informacje o konfiguracji w śledzi debugowanie kodu VS `launch.json` plik znajdujący się w `.vscode` folder w obszarze roboczym. To `launch.json` plik został wygenerowany podczas tworzenia nowego rozwiązania IoT krawędzi. Aktualizuje zawsze dodać nowy moduł, który obsługuje debugowania. Przejdź do widoku debugowania, a następnie wybierz odpowiedni plik konfiguracji debugowania.
    ![Wybierz opcję debugowania konfiguracji](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Przejdź do adresu `program.cs`. Dodaj punkt przerwania w tym pliku.

3. Kliknij przycisk **Rozpocznij debugowanie** przycisk lub naciśnij przycisk **F5**i wybierz można dołączyć do procesu.

4. W widoku debugowania kodu VS widać zmiennych w lewym panelu. 

> [!NOTE]
> Poprzednim przykładzie pokazano, jak można debugować modułów krawędzi IoT Core .NET do kontenerów. Jest on oparty na wersji do debugowania z `Dockerfile.debug`, w tym VSDBG (debuger wiersza polecenia platformy .NET Core) w obrazie kontenera podczas jego tworzenia. Po zakończeniu debugowania modułów C#, zaleca się bezpośrednio użyć lub dostosować `Dockerfile` bez VSDBG dla modułów krawędzi IoT gotowe do produkcji.

## <a name="next-steps"></a>Kolejne kroki

[Użyj programu Visual Studio kodu do debugowania usługi Azure Functions krawędzi IoT Azure](how-to-vscode-debug-azure-function.md)

