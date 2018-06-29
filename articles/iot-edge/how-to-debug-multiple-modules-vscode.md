---
title: Debugowanie wielu modułów Azure IoT Edge w kodzie VS | Dokumentacja firmy Microsoft
description: Debugowanie wielu modułów krawędzi IoT Azure przy użyciu programu Visual Studio Code
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 38c66129ac8244d18b0f94c1485c785015e29ab4
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049595"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Debugowanie wielu modułów krawędzi IoT Azure przy użyciu programu Visual Studio Code
Ten artykuł zawiera szczegółowe instrukcje dotyczące używania [kodu programu Visual Studio (VS)](https://code.visualstudio.com/) debugowania wiele modułów na krawędzi IoT.

## <a name="prerequisites"></a>Wymagania wstępne
Ukończ samouczek [opracowywania rozwiązań IoT Edge z wieloma modułami w programie Visual Studio Code](tutorial-multiple-modules-in-vscode.md) i upewnij się, że masz co najmniej dwa moduły uruchomione na urządzeniu IoT krawędzi.

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>Wiele docelowych i zdalnego debugowania w kodzie VS
Z rozszerzeniem kodzie VS i Azure IoT krawędzi możesz dołączyć procesu modułu w kontenerze, czy kontener jest uruchomiona na komputerze deweloperskim lub zdalnego urządzenia IoT krawędzi fizycznego. Debugowanie wielu modułów uruchomionych w kontenerach faktycznie jest dołączanie więcej niż jeden proces w osobnych kontenerów. Kod VS [wielu docelowego debugowania](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging) może być używana podczas debugowania wiele modułów.

   > [!TIP]
   > Jeśli Twoje kontener modułu jest uruchomiona w zdalnego urządzenia IoT krawędzi fizycznego, konieczne może być Instalator [maszyny Docker](https://docs.docker.com/machine/overview/) tak, aby aparatem platformy Docker na komputerze deweloperskim mogą komunikować się z hostów Docker zdalnych.

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>Tworzenie sieci krawędzi IoT modułów w celu debugowania
1. Aby rozpocząć debugowanie wielomodułowe, należy użyć **Dockerfile.amd64.debug** Aby ponownie skompilować obrazy usługi docker i wykonaj ponowne wdrożenie rozwiązania krawędzi. W kodzie VS Eksploratora, przejdź do `deployment.template.json` pliku. Zaktualizuj adresami URL obrazu, dodając `.debug` w końcu. Należy dwa obrazy modułu z `.debug` co najmniej. Podczas pracy w ramach rozwiązania z poprzednich samouczek, należy określić moduł funkcje C# i moduł C#. Zaktualizować te adresy URL obrazu dwóch przez dodanie `.debug` w końcu, a następnie zapisz ten plik. 
2. Skompiluj ponownie rozwiązanie. W kodzie VS palety polecenia, wpisz i uruchom polecenie **krawędzi IoT Azure: rozwiązanie graniczne IoT kompilacji**.
3. W Eksploratorze urządzenia Centrum IoT Azure, kliknij prawym przyciskiem myszy identyfikator urządzenia IoT krawędzi, a następnie wybierz **tworzenie wdrożenia dla urządzenie brzegowe**. Wybierz `deployment.json` plików w obszarze `config` folderu. Następnie można zauważyć, że pomyślnie utworzono wdrożenie z wdrożeniem, który zintegrowane Identyfikatora w kodzie VS terminala.

Można sprawdzić stan kontenera w Eksploratorze Docker kodu programu VS lub uruchamiając `docker ps` w terminalu.

### <a name="start-debugging-c-function-in-vs-code"></a>Rozpocznij debugowanie funkcji języka C# w kodzie VS
1. Informacje o konfiguracji w śledzi debugowanie kodu VS `launch.json` plik znajdujący się w `.vscode` folder w obszarze roboczym. To `launch.json` plik został wygenerowany podczas tworzenia nowego rozwiązania IoT krawędzi. Aktualizuje zawsze dodać nowy moduł, który obsługuje debugowania. Przejdź do widoku debugowania, a następnie wybierz odpowiedni plik konfiguracji debugowania dla C# funkcji modułu zdalnego debugowania.
2. Przejdź do adresu `run.csx`. Dodaj punkt przerwania w funkcji.
3. Kliknij przycisk **Rozpocznij debugowanie** przycisk lub naciśnij przycisk **F5**i wybierz można dołączyć do procesu.
4. W widoku debugowania kodu VS widać zmiennych w lewym panelu. 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>Rozpocznij debugowanie C# modułu w tym samym czasie w kodzie VS
1. W kodzie VS palety polecenia wpisz i uruchom polecenie "Obszaru roboczego: zduplikowana obszaru roboczego w nowym oknie". Nowe okno kodzie VS rozpoczyna się od tego samego obszaru roboczego.
2. Przejdź do widoku debugowania, a następnie wybierz odpowiedni plik konfiguracji debugowania dla C# modułu zdalnego debugowania.
3. Przejdź do adresu `program.cs`. Dodaj punkt przerwania w module języka C#.
4. Kliknij przycisk **Rozpocznij debugowanie** przycisk lub naciśnij przycisk **F5**i wybierz można dołączyć do procesu.
5. W widoku debugowania kodu VS widać zmiennych w lewym panelu. 

### <a name="see-variables-in-multiple-debugging-windows"></a>Zobacz zmiennych w wielu debugowania systemu windows
1. Teraz masz co najmniej dwa uruchomienie w oknie kodzie VS dwóch sesji debugowania. Są osiągane jednego punktu przerwania.
2. Naciśnij klawisz `F10` lub kliknij przycisk Step Over w **narzędzi debugowania**.
3. Powinien być trafiony punkt przerwania, w innym oknie kodzie VS. 
4. Nadal powyżej dwa kroki widać zmiennych z wiele modułów w wielu kodzie VS debugowania systemu windows.

> [!NOTE]
> Powyżej przykładzie przedstawiono sposób debugowania wiele modułów Azure IoT krawędzi. Jest on oparty na wersji do debugowania z `Dockerfile.amd64.debug`, w tym VSDBG (debuger wiersza polecenia platformy .NET Core) w obrazie kontenera podczas jego tworzenia. Firma Microsoft zaleca, możesz bezpośrednio użyć lub dostosować `Dockerfile` bez VSDBG dla funkcji krawędzi IoT gotowe do produkcji, po zakończeniu debugowania funkcji języka C#.

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu modułu wbudowane, Dowiedz się jak [wdrażanie Azure IoT krawędzi modułów z programu Visual Studio Code](how-to-deploy-modules-vscode.md) 0
