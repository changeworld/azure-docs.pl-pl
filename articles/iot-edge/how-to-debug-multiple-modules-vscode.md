---
title: Debuguj wiele modułów w programie Visual Studio Code — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Debugowanie wielu modułów za pomocą usługi Azure IoT Edge przy użyciu programu Visual Studio Code
author: shizn
manager: philmea
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0c421eb1532536a3d11013073f0474f5ac37311b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100457"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Debugowanie wielu modułów za pomocą usługi Azure IoT Edge przy użyciu programu Visual Studio Code
Ten artykuł zawiera szczegółowe instrukcje dotyczące korzystania z [kodu programu Visual Studio (VS)](https://code.visualstudio.com/) debugowania wiele modułów na brzegowych urządzeniach IoT.

## <a name="prerequisites"></a>Wymagania wstępne
Ukończ samouczek [tworzenia rozwiązań usługi IoT Edge z wieloma modułami w programie Visual Studio Code](tutorial-multiple-modules-in-vscode.md) i upewnij się, że masz co najmniej dwa moduły działające na urządzeniu usługi IoT Edge.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>Wiele docelowych i zdalne debugowanie w programie VS Code
Z rozszerzeniem programu VS Code i usługi Azure IoT Edge możesz dołączyć procesu modułu w kontenerze, czy kontener jest uruchomiony na maszynie deweloperskiej lub w zdalnym fizyczne urządzenia IoT Edge. Moduły niepustych debugowania działających w kontenerach jest faktycznie dołączanie więcej niż jeden proces w oddzielnych kontenerów. Program VS Code [wielu docelowego debugowania](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging) mogą być używane podczas debugowania wiele modułów.

   > [!TIP]
   > Jeśli kontener modułu jest uruchomiona w zdalnym fizyczne urządzenia IoT Edge, może być konieczne instalacji [maszyny platformy Docker](https://docs.docker.com/machine/overview/) tak, aby aparat platformy Docker na komputerze deweloperskim może komunikować się zdalny hostów platformy Docker.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>Twórz moduły usługi IoT Edge w celu debugowania
1. Aby rozpocząć debugowanie w wielu modułu, należy użyć **Dockerfile.amd64.debug** Aby ponownie skompilować obrazy usługi docker i ponownie wdrożyć swoje rozwiązanie krawędzi. W Eksploratorze programu VS Code przejdź do `deployment.template.json` pliku. Aktualizuj obraz adresami URL, dodając `.debug` końcowy. Potrzebujesz dwóch obrazów modułu za pomocą `.debug` co najmniej. Jeśli pracujesz w rozwiązaniu z poprzedniego samouczka, powinien mieć C# funkcji modułu i C# modułu. Zaktualizować te adresy URL dwóch obrazu, dodając `.debug` w końcu, a następnie zapisz ten plik. 
2. Ponownie skompiluj rozwiązanie. W palecie poleceń programu VS Code, typ, a następnie uruchom polecenie **usługi Azure IoT Edge: Tworzenie usługi IoT Edge rozwiązania**.
3. W Eksploratorze usługi Azure IoT Hub Devices, kliknij prawym przyciskiem myszy identyfikator urządzenia usługi IoT Edge, a następnie wybierz **tworzenie wdrożenia dla urządzenia Edge**. Wybierz `deployment.json` plik `config` folderu. Następnie zobaczysz, że wdrożenia została pomyślnie utworzona z wdrożeniem zintegrowanego identyfikator w programie VS Code terminala.

Możesz sprawdzić stan usługi kontenera, w Eksploratorze programu VS Code Docker lub uruchamiając `docker ps` polecenia w terminalu.

### <a name="start-debugging-c-function-in-vs-code"></a>Rozpocznij debugowanie C# funkcji w programie VS Code
1. Informacje o konfiguracji w utrzymuje funkcji debugowania kodu programu VS `launch.json` plik znajdujący się w `.vscode` folder w obszarze roboczym. To `launch.json` plik został wygenerowany podczas tworzenia nowego rozwiązania usługi IoT Edge. Aktualizuje po dodaniu każdego nowego modułu, który obsługuje debugowanie. Przejdź do widoku debugowania, a następnie wybierz odpowiedni plik konfiguracji debugowania dla C# funkcji modułu zdalnego debugowania.
2. Przejdź do adresu `run.csx`. Dodaj punkt przerwania w funkcji.
3. Kliknij przycisk **Rozpocznij debugowanie** przycisk lub naciśnij klawisz **F5**i wybierz proces do dołączenia.
4. W widoku debugowania programu VS Code widać zmienne w panelu po lewej stronie. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>Rozpocznij debugowanie C# modułu, w tym samym czasie w programie VS Code
1. W palecie poleceń programu VS Code wpisz i uruchom polecenie "Obszaru roboczego: zduplikowany obszar roboczy w nowym oknie". Nowe okno programu VS Code rozpoczyna się od tego samego obszaru roboczego.
2. Przejdź do widoku debugowania, a następnie wybierz odpowiedni plik konfiguracji debugowania dla C# modułu zdalnego debugowania.
3. Przejdź do adresu `program.cs`. Dodaj punkt przerwania w C# modułu.
4. Kliknij przycisk **Rozpocznij debugowanie** przycisk lub naciśnij klawisz **F5**i wybierz proces do dołączenia.
5. W widoku debugowania programu VS Code widać zmienne w panelu po lewej stronie. 

### <a name="see-variables-in-multiple-debugging-windows"></a>Zobacz zmiennych w wielu okien debugowania
1. Teraz masz co najmniej dwa uruchomione w dwa okna programu VS Code sesji debugowania. Zostanie osiągnięty jednego punktu przerwania.
2. Naciśnij klawisz `F10` lub kliknij przycisk Step Over w **narzędzi debugowania**.
3. Należy trafiony punkt przerwania w innym oknie programu VS Code. 
4. Nadal nad dwa kroki, widać zmienne z wiele modułów w wielu programu VS Code debugowania systemu windows.

> [!NOTE]
> Powyżej przykładzie pokazano, jak debugowanie wiele modułów przy użyciu usługi Azure IoT Edge. Jest on oparty na wersji debugowania `Dockerfile.amd64.debug`, która obejmuje VSDBG (debuger wiersza polecenia platformy .NET Core) w obrazie kontenera podczas jego tworzenia. Firma Microsoft zaleca bezpośrednio użyć lub dostosować `Dockerfile` bez VSDBG dla funkcji usługi IoT Edge gotowa do użycia w środowisku produkcyjnym po zakończeniu debugowania usługi C# funkcji.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, gdy Twoje Moduł skompilowany, jak [modułów wdrożenia usługi Azure IoT Edge z programu Visual Studio Code](how-to-deploy-modules-vscode.md) 0
